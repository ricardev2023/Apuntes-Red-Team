---
description: >-
  En este artículo vamos a ver la teoría básica detrás del lenguaje ensamblador
  orientada a Buffer Overflow.
---

# ENSAMBLADOR

{% hint style="info" %}
Artículo copiado de la Fundación Sadosky escrito por Teresa Alberto.
{% endhint %}

{% embed url="https://fundacion-sadosky.github.io/guia-escritura-exploits/buffer-overflow/1-introduccion.html" %}

## PRIMER PROGRAMA EN ASSEMBLER

Un programa en assembler se compone de una serie de instrucciones de máquina que al ejecutarse se almacenan en la memoria del proceso. Cada instrucción es un flujo de bytes que interpretados por el procesador modifican el estado del programa.\
&#x20;El código de un programa escrito en lenguaje ensamblador o assembler permite trabajar con una representación simbólica de ese flujo de bytes. Por ejemplo, la instrucción en assembler `add eax, 0x1` suma 1 al contenido del registro de memoria `eax`. Y `add eax, 0x1` es en realidad una representación simbólica del número: `83 c0 01` (en hexadecimal) o lo que es lo mismo `1000 0011 1100 0000 0000 0001`. Este último es el contenido binario efectivamente almacenado en la memoria.

```c
instrucción       | contenido binario en mem.       | contenido hexa en mem. 
add    eax, 0x1   | 1000 0011 1100 0000 0000 0001   | 83 c0 01                     
```

Por facilidad de lectura el contenido escrito en memoria se representará en hexadecimal y no en binario.

```c
dir. mem. | contenido en mem.   |   instrucción
08048334:   83 c0 01                add    eax,0x1
08048337:   83 c3 01                add    ebx,0x1
0804833a:   83 c0 02                add    eax,0x2
0804833c:   83 c3 02                add    ebx,0x2
```

### Registros de propósito general

> **Consideraciones:**
>
> * Tener en cuenta que a lo largo de esta guía se trabaja con la arquitectura Intel x86.
> * Y que el código assembler se presenta con la [sintaxis Intel](https://es.wikipedia.org/wiki/Lenguaje\_ensamblador\_x86#Sintaxis). Las diferencias con la sintaxis AT\&T son numerosas, una de las más importantes es que en la sintaxis Intel el registro destino se indica primero y luego el registro fuente, al revés que en AT\&T.

La CPU, dependiendo de su arquitectura, tiene una cierta cantidad de registros. Los registros pueden entenderse como celdas de memoria de muy alta velocidad que es utilizada para almacenar datos o direcciones durante la ejecución de un programa (como si fueran variables globales). A través de instrucciones en assembler se les asigna un valor y sobre ellos se realizan operaciones aritméticas.\
&#x20;En una computadora de 32 bits, el número más grande que se puede almacenar en un registro es de 32 bits y de 64 bits para una computadora de 64.\
Dentro de los denominados registros de propósito general de 32 bits están: `eax`, `ebx`, `ecx` y `edx`. (Por convención cuando los registros son de 64 bits se denominan: `rax`, `rbx`, `rcx` y `rdx`). A ellos se suman los registros `esi`, `edi` entre otros también considerados de propósito general de los que hablaremos más adelante.

```c
==========================
registro eax = 00 00 00 00
==========================

dir. mem. | instrucción       | ¿qué hace?
08048334:   mov eax, 0x4      ; almacena el valor 4 en el registro eax
08048339:   add eax, 0x3      ; suma 3 al valor almacenado en eax
0804833f:   mov ebx, eax      ; almacena el valor del registro eax en ebx

==========================
registro eax = 00 00 00 07
registro ebx = 00 00 00 07
==========================
```

Una instrucción puede, dado el caso, manipular sólo una porción de los 32 bits de un registro, para ello accede únicamente a esa porción de bytes utilizando nombres como: `ax`, `ah` y `al` según el criterio que indica la imagen:

![](<../../.gitbook/assets/imagen (90).png>)

Por ejemplo, para modificar el byte menos significativo de `eax` una instrucción usaría directamente `al`. Como se puede ver con ello la instrucción ocupa menos espacio en memoria. (Parece un detalle pero en la escritura de exploits la cuestión del espacio se vuelve muy relevante).

```c
dir. mem. | contenido en mem.   |   instrucción
08048330:   b8 04 00 00 00          mov    eax,0x4
08048335:   66 b8 04 00             mov    ax ,0x4
08048339:   b4 04                   mov    ah ,0x4
0804833b:   b0 04                   mov    al ,0x4
```

> **Consideraciones:** En el ejemplo anterior cuando se quería copiar el número 4 en `mov eax,0x4`, éste se almacenaba en formato **little endian** bajo la forma `04 00 00 00`. Cuando nos referimos a little endian o big endian hablamos del formato en el que una computadora almacena los datos en celdas de memoria. Justamente en la arquitectura x86 los datos se almacenan en el formato **little endian**, es decir, el byte menos significativo se almacena en una posición de memoria menor, y así hasta el byte más significativo.
>
>

![](<../../.gitbook/assets/imagen (92).png>)

> Por lo tanto, en _little endian_ el dato `0x12345678` se almacena en memoria:
>
> ```c
>   0x78 | 0x56 | 0x34 | 0x12
>   n    | n+1  | n+2  | n+3 
> ```
>
> El dato `ABCD`, es decir `\x41\x42\x43\x44` se almacena en memoria como `DCBA`:
>
> ```c
>   0x44 | 0x43 | 0x42 | 0x41
>   n    | n+1  | n+2  | n+3 
> ```

### Registro eip

El _**instruction pointer register**_ apunta a la siguiente instrucción a ser ejecutada por el programa. Cada vez que una instrucción se procesa, el procesador actualiza automáticamente este registro para que apunte a la siguiente instrucción a ser ejecutada. Para ello su valor se incrementa de acuerdo al tamaño de la instrucción (por ejemplo, la instrucción `add eax, 0x1` que se almacena en memoria como `83 c0 01`, ocupa 3 bytes).

Además de los registros para el almacenamiento de datos, en un programa se utilizan áreas de memoria que pueden ser accedidas con instrucciones de accesos a memoria del tipo `load/store` o con las operaciones de la pila `push/pop`.

### Instrucción MOV

En la arquitectura x86 `mov` es la instrucción encargada de los accesos a memoria.

1. `mov reg, [addr]`: permite copiar valores desde una dirección de memoria a un registro.
2. `mov [addr], reg`: permite copiar valores desde un registro a una dirección de memoria.

```c
==========================
registro ebx = 00 00 00 00
==========================

dir. mem. | contenido en mem.  | instrucción           | ¿qué hace?
08048334:   68 10 85 04 08       mov ebx, [0x08048510] ; almacena el valor 0x123 en el registro ebx
08048339: ....                                  | 
...                                             |
08048510:   23 01 00 00      <------------------+      ; 0x123 almacenado en little endian

==========================
registro ebx = 00 00 01 23
==========================
```

De manera similar a la dereferencia de punteros en C, un operando de una instrucción puede ser dereferenciado como puntero si está rodeado de corchetes. El uso de corchetes en las direcciones como `[0x08048510]` es similar a su uso en los arrays como `array[2]`. En vez de un índice se usa una dirección de memoria para localizar un valor.

1. Con \[ ]: `mov eax, [ebx]`\
   &#x20;Al segundo operando de `mov` se lo trata como un puntero, se sigue esa dirección y se copia el valor almacenado en ella en `eax`.

```c
==========================
registro eax = 00 00 00 00
registro ebx = 08 04 85 10    ; 0x08048510
==========================
   
dir. mem. | contenido en mem. | instrucción     | ¿qué hace?
08048334:   8b 03               mov eax, [ebx]  ; ebx = 0x08048510. [ebx] = 0x123. eax = 0x123. 
...                                        |    ; guardo en eax el contenido almacenado en 0x08048510
08048510:   23 01 00 00         <----------+

==========================
registro eax = 00 00 01 23     ; Notar que el valor está invertido en relación a cómo se almacena en little endian en memoria
==========================
```

1. Sin \[ ]: `mov eax, ebx`\
   &#x20;Al no usar corchetes, el segundo operando consiste en el contenido de `ebx` (no entendido ya como puntero), por lo que se lo copia directamente en `eax`.

```c
==========================
registro eax = 00 00 00 00
registro ebx = 08 04 85 10    ; 0x08048510
==========================
   
dir. mem. | contenido en mem. | instrucción      | ¿qué hace?
08048336:   89 d8               mov eax, ebx     ; ebx = 0x08048510. eax = 0x08048510. 
...                                              ; guardo en eax el contenido almacenado en ebx
08048510:   23 01 00 00 
   
==========================
registro eax = 08 04 85 10
==========================
```

## LA PILA <a href="#la-pila" id="la-pila"></a>

### PUSH & POP <a href="#push--pop" id="push--pop"></a>

Las operaciones `push/pop` manipulan un área de la memoria de un proceso denominada pila o stack, que responde a una estructura de datos _LIFO_ (_last in, first out_: último en entrar, primero en salir) donde los elementos se almacenan con `push` y se desapilan con `pop`.\
&#x20;La pila se utiliza para almacenar: valores de registros de manera temporal, variables locales, parámetros de funciones y direcciones de retorno.

Uno de los registros especiales vinculados a la pila es el puntero de pila.

### Puntero de pila (registro esp). <a href="#puntero-de-pila-registro-esp" id="puntero-de-pila-registro-esp"></a>

El _**stack pointer register**_ (o _extended stack pointer_) apunta al tope de la pila, es decir al último elemento almacenado en ella. Cuando se almacena un nuevo valor en la pila con `push`, el valor del puntero se actualiza para siempre apuntar al tope de la pila.

![](<../../.gitbook/assets/imagen (86).png>)

```c
==========================
registro esp = 0xbffff590
==========================

dir. mem. | contenido en mem. | instrucción | ¿qué hace?
08048334:   68 00 01 00 00      push 0x100  ; actualiza esp = esp - 4
...                                         ; y apila el valor 0x100
...                                                     |
bffff58c:   00 00 00 00                                 |
bffff590:   00 03 00 00         <= ESP                  |
                                                        |
                                                        |
                                                        |
==========================                              |
registro esp = 0xbffff58c                               |
==========================                              |
...                                                     |
...                                                     |
bffff58c:   00 01 00 00         <= ESP            <-----+ 
bffff590:   00 03 00 00  
```

Cuando se desapila un dato con `pop` lo apuntado por `esp` se almacena en el registro indicado en la instrucción. Y el valor de `esp` se actualiza con el nuevo tope de pila.

```c
==========================
registro esp = 0xbffff58c
==========================

dir. mem. | contenido en mem. | instrucción | ¿qué hace?
08048339:   5b                  pop ebx     ; desapila 0x100, lo guarda en ebx
...                                         ; y actualiza esp = esp + 4
bffff58c:   00 01 00 00         <= ESP
bffff590:   00 03 00 00  



==========================
registro esp = 0xbffff590
registro ebx = 0x00000100
==========================
...
bffff58c:   00 01 00 00      
bffff590:   00 03 00 00         <= ESP
```

Es interesante notar que con `pop ebx` el valor `0x100` se almacena en `ebx` quedando la dirección `0xbffff58c` disponible para su uso. Es posible ver cómo aún permanece `0x100` en `0xbffff58c` porque todavía no lo ha sobreescrito otra instrucción que utilice la pila.

### Crecimiento de la pila <a href="#crecimiento-de-la-pila" id="crecimiento-de-la-pila"></a>

En el ejemplo anterior, contraintuitivamente, al almacenar un nuevo valor en la pila con `push`, el tope de la pila que estaba en `0xbffff590` pasa a estar en `0xbffff58c`. Es decir que, al agregar un elemento, la pila creció hacia las direcciones numéricas menores.\
&#x20;Al desapilar un elemento el proceso fue inverso, la pila decreció desde `0xbffff58c` hasta `0xbffff590`, es decir decreció hacia direcciones mayores.\
&#x20;Esto se debe a que la pila crece desde direcciones numéricas mayores (que son usadas primero) hacia las direcciones de memoria menores. Es decir, crece desde `0xf...fff` hacia `0x0...000`. Como la pila crece desde su base -desde la dirección más alta- hacia direcciones menores de memoria, al apilar un nuevo elemento se debe decrementar el puntero de la pila y al desapilar un elemento se debe incrementar el puntero de la pila. Por eso con `push` se resta `esp = esp - 4` y con `pop` se suma `esp = esp + 4`.

![](<../../.gitbook/assets/imagen (71).png>)

> **Consideraciones**: Es posible pensar a las instrucciones `push` y `pop` como dos instrucciones concatenadas.
>
> ```c
>
> ; push eax puede pensarse como:
> sub esp, 4               ; actualizo tope de la pila (o registro esp)
> mov [esp], eax           ; almaceno contenido de eax allí
>
>  
> ; pop eax puede pensarse como:
> mov eax, [esp]           ; almaceno valor de esp en eax
> add esp, 4               ; actualizo tope de la pila
> ```

### Instrucciones de salto <a href="#instrucciones-de-salto" id="instrucciones-de-salto"></a>

En assembler instrucciones del tipo `jump`, `branch` o `call` modifican el valor del contador del programa. De esta manera instrucciones como `jmp`, `je`, `jne`, `call` provocan que el programa deje de ejecutarse de manera lineal modificando el flujo de ejecución.

La instrucción `jmp` es un ejemplo de un salto incondicional, es decir, siempre va a ejecutarse.\
&#x20;En cambio `jne` (`jump if not equal` o saltar si los operandos son distintos) es un salto condicional que depende del valor del flag zero. Existe un registro especial llamado **registro de estado** o registro eflags (rflags en 64 bits) donde cada bit almacena información de control que se modifica con las operaciones aritmético lógicas. Se compone de flags (o banderas en español) de 1 bit, como el `Z o zero flag` que se setea en 1 si la operación anterior resultó en 0, por ejemplo si el resultado de una resta como `sub ebx, eax` dió 0. Otros flags son `S o sign flag` si el resultado de la operación anterior da negativo y `O u overflow flag` si se produce un overflow.

![](<../../.gitbook/assets/imagen (89).png>)

La información del registro de estado es utilizada luego por instrucciones de salto condicional. Si el resultado de una resta es 0, eso implica que ambos operandos son iguales y que la condición de `jump if equal` debe considerarse como verdadera y por lo tanto el salto debe producirse.

Por ejemplo, considerando el siguiente programa:

```c
==========================
registro eax = 0x100
registro ebx = 0x100
registro de estado => Z=0
==========================

08048332: sub ebx, eax       ; el resultado es 0. ebx = 0. Z = 1
08048334: je  8048340        ; ¿Z==1? true. eip = 0x08048340

==========================
registro eax = 0x100
registro ebx = 0x0
registro de estado => Z=1
==========================

```

Una instrucción de salto condicional como `je 8048340` (`jump equal`) evalúa que los operandos de la última instrucción aritmético lógica sean iguales, si lo son el salto debe producirse. Es posible conocer si son iguales a partir de una resta con `sub`: si se antecede esa instrucción al `jump if equal` y si el resultado de esa resta da 0, es que ambos operandos son iguales (tal como indica el ejemplo); si el resultado no da 0 es que no lo son.\
&#x20;En el ejemplo la operación `sub ebx, eax` tiene como efecto setear Z=1, porque el resultado de la resta fue 0. La instrucción `jump if equal` evalúa el valor de `Z`, si es 1 significa que los operandos eran iguales, por ende `eip` se modifica por el nuevo valor y el salto se produce.

De esta manera, es posible evaluar saltos condicionales corroborando el estado del flag `zero`.

### CALL y convención del llamado a funciones <a href="#call-y-convencion-del-llamado-a-funciones" id="call-y-convencion-del-llamado-a-funciones"></a>

En la arquitectura x86, en el llamado a funciones la pila juega un rol fundamental. En este espacio de memoria se almacenan las variables locales de la función llamada, sus argumentos y su dirección de retorno. Justamente se habla de `frame` o marco de una función al sector de la pila donde ésta almacena sus argumentos y variables locales, entre otra información.\
&#x20;A medida que se llaman funciones y se retorna de ellas, en la pila se crean y destruyen `frames`, permaneciendo siempre en el tope de la pila el marco de la función en ejecución.

```c
void funcion_a(param_1, param_2) {
        int var_1 = 10;
        int var_2 = 11;
        funcion_b(arg_3, arg_4)
}
void funcion_b(param_3, param_4) {
        int var = 12;
        funcion_c(arg_5);
}
void funcion_c(param_5) {
        int var = 13;
        ...                         <= EIP
}

int main() {
        funcion_a(arg_1, arg_2);
        printf("Mensaje\n");
}
```

Después del llamado a las tres funciones (`funcion_a, funcion_b, funcion_c`) y cuando se están ejecutando instrucciones dentro de la `funcion_c` (`eip` apunta al cuerpo de esa función), el layout de la pila -en una versión simplificada- es:

![](<../../.gitbook/assets/imagen (91).png>)

Por convención los parámetros de una función se encuentran disponibles en la pila y se almacenan en orden inverso: desde el último al primero, de esta manera se encontrarán disponibles en el orden correcto. (Bajo otras convenciones los parámetros se almacenan en registros).\
&#x20;En el ejemplo anterior, el llamado a `funcion_a(arg_1, arg_2)` haría que primero se apile `arg_2` y después `arg_1`.

![](<../../.gitbook/assets/imagen (88).png>)

Este gráfico es una versión simplificada del layout de la pila como se verá a continuación.

### Frame pointer (registro ebp)

En tiempo de compilación no es posible conocer la dirección de memoria que tendrán los argumentos y variables locales de una función al ejecutar un programa. Por eso para acceder a ellos se usa el registro especial `ebp` o _frame pointer_ (también llamado _base pointer register_) que apunta a una ubicación fija dentro del marco de una función, para que la dirección de variables y argumentos pueda ser accedida como offsets utilizando este registro.

```c
ebp-0x8     ; variable local #2  
ebp-0x4     ; variable local #1  
ebp         ; valor ebp de función llamadora  
ebp+0x4     ; dirección de retorno  
ebp+0x8     ; parámetro #1  
ebp+0xc     ; parámetro #2  
```

Entonces en el llamado a una función sus parámetros y variables locales son accedidos como un offset de `ebp`, siempre negativo en el caso de las variables locales (`ebp-0x4: var local #1`, `ebp-0x8: var local #2`) y siempre positivo para los argumentos (`ebp+0x8: param #1`, `ebp+0xc: param #2`) ya que fueron apilados con anterioridad.

Considerando el mismo programa de ejemplo anterior, cuando la ejecución se encuentra en la `funcion_a` sin haber llamado todavía la `funcion_b`:

```c
void funcion_a(param_1, param_2) {
        int var_1 = 10;
        int var_2 = 11;
        funcion_b(arg_3, arg_4)   <= EIP
}
```

En la pila se observa el stack frame de la `funcion_a` después de ser llamada, con los respectivos offsets de `ebp`:

![](<../../.gitbook/assets/imagen (87).png>)

Este es el layout resultante de la pila y ya no una versión simplificada, lo cual puede ser comprobado fácilmente debuggeando un programa como el del ejemplo.

### Prologo de una funcion

A las instrucciones iniciales que se pueden observar tanto en `main()` como en `funcion_a()` se las denomina **prólogo**:

```c
; prólogo

push    ebp                                     
mov     ebp, esp                                
```

El prólogo de una función son las instrucciones en assembler que modifican los registros para la creación del marco de la función llamada.

1. `push ebp`: el valor del registro `ebp` se modifica en cada llamado a una función (es diferente para cada frame), por eso su valor se almacena en la pila. El valor del registro `ebp` almacenado permite acceder a las variables y parámetros de la función llamadora. Se lo almacena en la pila porque -al llamar a una función- se modifica el valor de `ebp` para acceder a las variables y parámetros de la función llamada.
2. `mov ebp, esp`: se apunta el registro `ebp` al puntero de la pila, de esta manera se establece una dirección de referencia para el nuevo marco de la función llamada. Como inmediatamente antes se habían apilado los parámetros, estos serán accedidos; `[ebp+0x8] parámetro #1` y `[ebp+0xc] parámetro #2`. Como a posteriori se apilan las variables locales, éstas serán accedidas: `[ebp-0x4] variable local #1` y `[ebp-0x8] variable local #2`.

Resultando en el layout ya mencionado:

```
ebp-0x8     ; variable local #2  
ebp-0x4     ; variable local #1  
ebp         ; valor ebp de función llamadora  
ebp+0x4     ; dirección de retorno  
ebp+0x8     ; parámetro #1  
ebp+0xc     ; parámetro #2  
```

### Epilogo de una funcion

Se denomina **epílogo** a las instrucciones que, al finalizar la ejecución de la función llamada, vuelven la pila al estado inicial antes del llamado a la función:

La instrucción `leave` puede pensarse como dos instrucciones:

```bash
mov esp, ebp  ; leave, parte 1
pop ebp       ; leave, parte 2       
ret               
```

1. `mov esp, ebp` deja de lado las variables locales de la función llamada y reestablece el tope de la pila
2. `pop ebp` actualiza el registro `ebp` a la base del marco de la función llamadora anterior.
3. `ret` es un retorno desde una función llamada. Desapila una dirección del tope de la pila (apuntada por `esp`) y la almacena en el registro `eip` para ejecutar a continuación esa instrucción. (Al desapilarla, actualiza el valor de `esp` al nuevo tope de la pila)

### ¿Qué es la dirección de retorno?

Al igual que con los saltos condicionales e incondicionales, el llamado a una función modifica el flujo de ejecución de un programa. No obstante, a diferencia de los saltos, cuando la función llamada termina de ejecutarse el control debe retornar a la función llamadora. El punto al que se debe retornar es la instrucción exactamente posterior al llamado a la función (dentro de la función llamadora).

Considerando una versión modificada del ejemplo anterior que imprime dos mensajes:

```c
void funcion_a(int param_1, int param_2) {
        int var_1 = 3;
        int var_2 = 4;
        printf("Mensaje en funcion_a()");
}                                          <= eip debe retornar a main()

int main() {
        funcion_a(1,2);
        printf("Mensaje en main()");
}
```

En `main()` se llama a `funcion_a(1,2)`. Estando al final de la `funcion_a` una vez finalizada su ejecución (es decir, ya impreso el mensaje “Mensaje en funcion\_a()”), el contador del programa debe retornar a `main()` y continuar con la ejecución de `printf("Mensaje en main()")`.

¿Cómo se sabe dónde retornar dentro de main? Para conocer en qué punto exacto de `main()` debe continuar el flujo de ejecución se usa la dirección de retorno y la instrucción `call`.

Viendo el pseudo assembler (código assembler levemente modificado para que resulte más fácil su lectura) es posible identificar el llamado de `funcion_a(1,2)` dentro de `main()` con el desensamblador OBJDUMP.

(El significado de los flags para la compilación del programa se detalla en la sección de CREANDO UN LABORATORIO SIN MITIGACIONES).

```bash
user@u:~$ sudo sysctl -w kernel.randomize_va_space=0
user@u:~$ gcc -g -m32 -no-pie -fno-stack-protector -fno-asynchronous-unwind-tables 
-mpreferred-stack-boundary=2 -o funcion_a funcion_a.c

user@u:~$ objdump -M intel -S funcion_a
```

```c
0804841e <main>:

int main() {
 804841e: 55                    push   ebp
 804841f: 89 e5                 mov    ebp,esp
        
    funcion_a(1,2);
    8048421: 6a 02                 push   0x2                  ; param_2
    8048423: 6a 01                 push   0x1                  ; param_1
    8048425: e8 d1 ff ff ff        call   80483fb <funcion_a>  ; call funcion_a(1,2)
    804842a: 83 c4 08              add    esp,0x8
    
    printf("Mensaje en main()");
    804842d: 68 da 84 04 08        push   0x80484da            ; addr "Mensaje en main()"
    8048432: e8 99 fe ff ff        call   80482d0 <puts@plt>   ; call printf("Mensaje en main()");
    8048437: 83 c4 04              add    esp,0x4
}
 804843a: c9                    leave  
 804843b: c3                    ret  
```

> **Consideraciones:** como `printf("Mensaje..." )` tiene como argumento un string fijo sin parámetros por una optimización del compilador se llama a la función `puts()` y no a `printf()`.

Al llamar a una función con la instrucción `call`, después de haber apilado los parámetros en la pila en sentido inverso, se almacena la dirección de retorno (de la instrucción siguiente a `call` para saber a dónde retornar) y el valor del registro `ebp` usado en el marco actual. Si observamos el código en assembler de la `funcion_a`:

```c
080483fb <funcion_a>:

void funcion_a(int param_1, int param_2) {
 ;
 ; prólogo: apilo ebp del frame de main() para preservar su valor
 ; 
 80483fb: 55                    push   ebp
 ;
 ; prólogo: ahora ebp apunta al tope de la pila: será el nuevo ebp del frame de funcion_a
 ;
 80483fc: 89 e5                 mov    ebp,esp
 ;
 ; hago espacio para las variables locales
 ;
 80483fe: 83 ec 08              sub    esp,0x8                 
        
    ; int var_1 = 3;
    8048401: c7 45 fc 03 00 00 00  mov    [ebp-0x4],0x3        ; [ebp-0x4]: var_1
    
    ; int var_2 = 4;
    8048408: c7 45 f8 04 00 00 00  mov    [ebp-0x8],0x4        ; [ebp-0x8]: var_2
    ;
    ; apilo argumento de printf() y llamo a:
    ; printf("Mensaje en funcion_a()");
    ;
    804840f: 68 d0 84 04 08        push   0x80484d0            ; addr "Mensaje en funcion_a()"
    8048414: e8 b7 fe ff ff        call   80482d0 <puts@plt>   ; call printf("Mensaje en funcion_a()"); 
    ;
    ; desapilo argumento de printf()
    ;
    8048419: 83 c4 04              add    esp,0x4
}
 ;
 ;  epílogo: leave puede pensarse como: mov esp, ebp; pop ebp
 ;  mov esp, ebp: llevo tope de la pila al frame pointer. 
 ;  pop ebp: sobreescribo el frame pointer por el valor de ebp del frame de main(). 
 ;
 804841c: c9                    leave
 
 ;  epílogo: retorno a main() usando la dirección de retorno en el tope de la pila
 ;  
 804841d: c3                    ret    
```

### Punto de entrada del binario \_start <a href="#punto-de-entrada-del-binario-_start" id="punto-de-entrada-del-binario-_start"></a>

Cuando trabajamos en GNU/Linux el formato de los ejecutables es ELF (Executable and Linking Format). Con `readelf -h` es posible ver los campos de la cabecera del archivo, que nos dan información relevante del binario:

```c
user@abos:~$ gcc -g -m32 -no-pie -fno-stack-protector -fno-asynchronous-unwind-tables 
-mpreferred-stack-boundary=2 -o programa programa.c
user@abos:~$ readelf -h programa
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Intel 80386
  Version:                           0x1
  Entry point address:               0x80482d0
  ...
```

Esta información es útil ya que nos indica que es un binario de la arquitectura Intel x86 y nos informa el punto de entrada del programa (`Entry point adress: 0x80482d0`). Como podemos ver en el código desensamblado esa exacta dirección del punto de entrada corresponde a `_start` y no a `main`:

```c
user@abos:~$ objdump -M intel -d suma
080482d0 <_start>:
 80482d0: 31 ed                 xor    ebp,ebp
 80482d2: 5e                    pop    esi
 ...
 80482ec: e8 cf ff ff ff        call   80482c0 <__libc_start_main@plt>
 
080483d8 <main>:
 80483d8: 55                    push   ebp
 80483d9: 89 e5                 mov    ebp,esp
 ...
 80483ed: c9                    leave  
 80483ee: c3                    ret    
```

Efectivamente, la entrada al programa es `_start` que se encarga de llamar a funciones de inicialización y hace un llamado a `__libc_start_main` que -a su vez- se encarga de hacer el call a `main` con los parámetros correspondientes.\
&#x20;Cuando se hable del frame anterior a `main()` nos referiremos de manera simplificada al frame de `_start`.

### REFERENCIAS <a href="#material-consultado" id="material-consultado"></a>

\[1]. Tanenbaum, Andrew S. (2005). _Structured computer organization._

\[2]. Intel Corporation. (Mayo de 2011). _Intel 64 and IA-32 Architectures Software Developer’s Manual: Combined volumes_. Disponible en: https://software.intel.com/sites/default/files/managed/39/c5/325462-sdm-vol-1-2abcd-3abcd.pdf

\[3]. Younan, Y., Piessens, F., Joosen, W. (Sin fecha). _Protecting global and static variables from buffer overflow attacks_. Disponible en: http://fort-knox.org/files/globstat.pdf

\[4]. Aleph One. (Noviembre de 1996). Smashing the Stack for Fun and Profit. _Phrack_, 7. Disponible en: http://phrack.org/issues/49/14.html

\[5]. Liveoverflow.com. (2018). Disponible en: http://liveoverflow.com/
