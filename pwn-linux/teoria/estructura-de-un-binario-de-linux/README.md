---
description: En éste artículo veremos la estructura de un Binario en formato ELF de Linux
---

# ESTRUCTURA DE UN BINARIO DE LINUX

{% hint style="info" %}
Artículo copiado de la Fundación Sadosky escrito por Teresa Alberto.
{% endhint %}

{% embed url="https://fundacion-sadosky.github.io/guia-escritura-exploits/buffer-overflow/3-got.html" %}

## ESTRUCTURA DEL FORMATO ELF <a href="#layout-del-formato-elf-executable-and-linking-format-de-gnulinux" id="layout-del-formato-elf-executable-and-linking-format-de-gnulinux"></a>

### Secciones de la estructura

El formato ELF de un binario de GNU/Linux se encuentra organizado en **secciones** que estructuran sus instrucciones, sus datos y otra información necesaria para el _linker_ en el proceso de enlazado. Desde la perspectiva del sistema operativo el formato ELF se estructura bajo la forma de **segmentos** que utilizará para cargar en memoria el proceso.

En esta ocasión sólo será importante considerar algunas de las secciones de un archivo ELF, a las que podremos categorizar dentro de dos grandes grupos: por un lado la sección de las instrucciones y por otro las de los datos del programa.

#### **INSTRUCCIONES:**

1.  **Sección .text**: corresponde a las instrucciones del programa.

    ```
    contenido en .text | instrucción     
    8b 03                mov eax, [ebx]
    ```

    Por ejemplo, si el programa cuenta con esta instrucción `mov`, en la sección `.text` del binario encontraremos el código máquina `0x8b03`.

#### **DATOS:**&#x20;

Compuesto por tres secciones que corresponden a los datos de un programa:

1. **Sección** **.data**: con las variables estáticas y globales inicializadas del proceso.
2. **Sección** **.rodata**: es idéntica a la sección .data pero únicamente para datos de sólo lectura (_Read Only data_).
3. **Sección** **.bss**: con las variables no inicializadas.

Así como las variables _estáticas_ (declaradas como `static` o por fuera de una función) se almacenan en la sección `.data` y persisten a lo largo de la ejecución del programa, en cambio las variables locales declaradas dentro de una función son consideradas _dinámicas_ en C y se almacenan en la **pila** como parte del frame de la función. Por último el **heap** es el área de memoria reservada para el almacenamiento de memoria dinámica, manipulada a través de malloc(), realloc(), free(), etc.

El gráfico a continuación ilustra cómo el sistema operativo carga en memoria el proceso teniendo en cuenta la estructura del ELF definida previamente:

Para ejemplificar el funcionamiento de las secciones en el siguiente programa de ejemplo se indica en qué sección estará cada variable:

```
//en .data: variable global inicializada
int a = 5;

//en .bss: variable global no inicializada
int b;

int main(){
	//en la pila: variable local
	int c=7;

	//en .bss: variable estática no inicializada
	static int d;

	//en la pila: ptr es un puntero local que apunta a memoria dinámica en el heap
	int *ptr = (int *) malloc(sizeof(int));
	//en el heap
	ptr[0]=3;

	free(ptr);

	return 1;
}
```

Es posible ver un detalle de las secciones de un binario con `readelf -S`:

```
user@abos:~$ readelf -S programa
There are 35 section headers, starting at offset 0x1120:

Section Headers:
     [Nr] Name              Type            Addr     Off    Size   ES Flg Lk Inf Al
     [ 0]                   NULL            00000000 000000 000000 00      0   0  0
     [ 1] .interp           PROGBITS        08048134 000134 000013 00   A  0   0  1
     [ 2] .note.ABI-tag     NOTE            08048148 000148 000020 00   A  0   0  4
     (...)
     [11] .init             PROGBITS        08048294 000294 000023 00  AX  0   0  4
     [12] .plt              PROGBITS        080482c0 0002c0 000040 04  AX  0   0 16
 =>  [13] .text             PROGBITS        08048300 000300 000192 00  AX  0   0 16
     [14] .fini             PROGBITS        08048494 000494 000014 00  AX  0   0  4
 =>  [15] .rodata           PROGBITS        080484a8 0004a8 000008 00   A  0   0  4
     [16] .eh_frame_hdr     PROGBITS        080484b0 0004b0 00002c 00   A  0   0  4
     [17] .eh_frame         PROGBITS        080484dc 0004dc 0000ac 00   A  0   0  4
     [18] .init_array       INIT_ARRAY      08049588 000588 000004 00  WA  0   0  4
     [19] .fini_array       FINI_ARRAY      0804958c 00058c 000004 00  WA  0   0  4
     [20] .jcr              PROGBITS        08049590 000590 000004 00  WA  0   0  4
     [21] .dynamic          DYNAMIC         08049594 000594 0000e8 08  WA  6   0  4
     [22] .got              PROGBITS        0804967c 00067c 000004 04  WA  0   0  4
     [23] .got.plt          PROGBITS        08049680 000680 000018 04  WA  0   0  4
 =>  [24] .data             PROGBITS        08049698 000698 000008 00  WA  0   0  4
 =>  [25] .bss              NOBITS          080496a0 0006a0 000004 00  WA  0   0  1
     [26] .comment          PROGBITS        00000000 0006a0 000039 01  MS  0   0  1
....
```

Y con `readelf -l` vemos la estructura del ELF desde la perspectiva de los segmentos utilizados por el sistema operativo para cargar el ejecutable en memoria:

```
user@abos:~$ readelf -l programa

Elf file type is EXEC (Executable file)
Entry point 0x8048300
There are 8 program headers, starting at offset 52

   Program Headers:
      Type           Offset   VirtAddr   PhysAddr   FileSiz MemSiz  Flg Align         ; Segmento   |  Permisos/Flags
      PHDR           0x000034 0x08048034 0x08048034 0x00100 0x00100 R E 0x4           ;    #00             R E
      INTERP         0x000134 0x08048134 0x08048134 0x00013 0x00013 R   0x1           ;    #01             R            
   +-    #02             R E
 +-|-    #03             RW 
 | |  DYNAMIC        0x000594 0x08049594 0x08049594 0x000e8 0x000e8 RW  0x4           ;    #04             RW 
 | |  NOTE           0x000148 0x08048148 0x08048148 0x00044 0x00044 R   0x4           ;    #05             R  
 | |  GNU_EH_FRAME   0x0004b0 0x080484b0 0x080484b0 0x0002c 0x0002c R   0x4           ;    #06             R  
 | |  GNU_STACK      0x000000 0x00000000 0x00000000 0x00000 0x00000 RW  0x10          ;    #07             RW 
 | |
 | | Section to Segment mapping:
 | |   Segment Sections...
 | |   00     
 | |   01     .interp 
 | +-> 02     .interp .note.ABI-tag .note.gnu.build-id .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rel.dyn .rel.plt .init .plt .text .fini
 |           .rodata .eh_frame_hdr .eh_frame 
 +---> 03     .init_array .fini_array .jcr .dynamic .got .got.plt .data .bss 
       04     .dynamic 
       05     .note.ABI-tag .note.gnu.build-id 
       06     .eh_frame_hdr 
       07 

```

En el ELF se agrupan las secciones `.rodata` y `.text` -entre otras- en un segmento con permisos de lectura y ejecución (`Flags: R E`), y en otro las secciones `.data` y `.bss` con permisos de lectura y escritura (`Flags: RW`).

Otras secciones importantes a la hora de pensar una estrategia de ataque son:

1. **Sección .got**: corresponde a la **Global Offset Table**, una tabla en cuyas entradas están las direcciones efectivas de las funciones de bibliotecas compartidas presentes en el programa.
2. **Sección .plt**: corresponde a la **Procedure Linkage Table**, otra tabla necesaria para la resolución de las direcciones de funciones de bibliotecas compartidas cuyo rol veremos a continuación.

### Global Offset Table (GOT) <a href="#global-offset-table-got" id="global-offset-table-got"></a>

Dada la relevancia que tendrá en los exploits es importante detenerse en la sección `.got` que corresponde a la _Global Offset Table_ o “Tabla global de offsets” en español.

En un **binario ELF linkeado dinámicamente** (ver en el siguiente apartado), cuando se produce un llamado a una función de una biblioteca compartida se recurre a la tabla GOT para resolverlo. Justamente esta tabla es un listado de punteros donde se indican las direcciones efectivas de esas funciones en tiempo de ejecución.

Por ejemplo podriamos ilustrar la tabla GOT de un programa que llama a `printf()` y `exit()` de la biblioteca `libc` como:

```
sección .got

 ----------------------- --------------------------
| Nombre de la función  | Dirección en biblioteca  |
|-----------------------|--------------------------|
| printf()              | 0xb7e89d80 (libc.so)     |
| exit()                | 0xb7e561b0 (libc.so)     | 
```

### Enlazado dinámico de binarios <a href="#enlazado-dinamico-de-binarios" id="enlazado-dinamico-de-binarios"></a>

El **proceso de enlazado** para resolver referencias a módulos o a funciones de bibliotecas compartidas se puede resolver a grandes rasgos de dos maneras. Por un lado, i**ncorporando al binario el código de esos módulos o bibliotecas utilizado (enlazado estático)**, o **manteniendo una referencia al código compartido que el sistema operativo se encargará de resolver en tiempo de ejecución (enlazado dinámico)**. El enlazado dinámico pospone el cálculo de las direcciones de esas funciones hasta que sean efectivamente llamadas en tiempo de ejecución, es decir, el proceso de enlace se produce “a demanda”.

Para evitar que la resolución de estas referencias implique modificar el código de un proceso, se crea una tabla aparte en el binario: la tabla GOT que se ubica en la seccion `.got`. Como la dirección de las bibliotecas compartidas es desconocida al momento de compilación, el compilador apunta esas funciones a entradas de la tabla GOT cuya ubicación es conocida y estática. Una vez calculadas sus direcciones efectivas solo será necesario actualizar las entradas de la GOT, sin modificar el código en `.text`. Esta estrategia trae enormes ventajas en el manejo de permisos de las secciones de un binario. Esta tabla -y su correspondiente sección- tendrá permisos de escritura (ya que debe actualizarse en tiempo de ejecución) y en cambio la sección de código `.text` podrá únicamente tener permisos de lectura y ejecución.

#### _Procedure Lookup Table_ o PLT <a href="#procedure-lookup-table-o-plt" id="procedure-lookup-table-o-plt"></a>

En muchos casos una función incluida en un programa puede ser llamada, o no, de acuerdo, por ejemplo, al input de un usuario. Para no llevar a cabo el proceso de enlazado dinámico de todas las funciones externas de un programa, se recurre a una etapa intermedia en la resolución de esas referencias: la tabla **PLT** o _Procedure Lookup Table_ en inglés.

Cada función de una biblioteca -presente en el programa- tiene una entrada en la tabla PLT. A su vez, cada una de esas entradas apunta a una entrada en la GOT. En tiempo de ejecución se inicia un proceso por el cual se resuelve la dirección efectiva en memoria de la función dentro de la biblioteca. En un primer llamado a la función, se aprovecha una función trampolin dentro de la sección `.plt` que invoca al _dynamic linker_. Este resuelve la dirección de la función en la biblioteca y la ejecuta. También actualiza la entrada en la GOT con la dirección efectiva de la función dentro de la biblioteca compartida para los subsiguientes llamados.

Por ejemplo, en el siguiente programa `imprimir.c`:

```
#include 
int main() {
  printf("you win!\n");
  exit(0);
}
```

```
user@abos:~$ gcc -m32 -no-pie -mpreferred-stack-boundary=2 -o imprimir imprimir.c
```

1.  Al compilarlo de esta manera con `gcc`, `libc` va a estar linkeada dinámicamente al binario, es decir no va a estar incluida en él y por lo tanto -en tiempo de compilación- no se va a conocer la dirección de funciones como `printf()` o `exit()` contenidas en esa biblioteca.\


    Con `ldd` vemos las bibliotecas dinámicas enlazadas a este binario y el directorio en el que se encuentran:

    ```
    user@abos:~$ ldd imprimir
        linux-gate.so.1 (0xb7ffd000)
    =>  libc.so.6 => /lib/i386-linux-gnu/i686/cmov/libc.so.6 (0xb7e46000)
        /lib/ld-linux.so.2 (0x80000000)
    ```

    Y es posible con `objdump` ver las direcciones de cada entrada de la tabla GOT:

    ```
    user@abos:~$ objdump --dynamic-reloc imprimir      
       
    imprimir:     file format elf32-i386
       
    DYNAMIC RELOCATION RECORDS
    OFFSET   TYPE              VALUE 
    080496c8 R_386_JUMP_SLOT   puts
    080496d0 R_386_JUMP_SLOT   exit
    ```

    Como en casos anteriores la función llamada es `puts()` y no `printf()` porque se trata de un string fijo, sin parámetros.\
    &#x20;Es posible pensar que en este punto en la tabla GOT aún no está definida la dirección efectiva de las funciones en la biblioteca.

    ```
     ----------------- ----------------------- -------------------------
    | Entrada en .got | Nombre de la función  | Dirección en biblioteca |
    |-----------------|-----------------------|-------------------------|
    | 080496c8        | puts()                | ??? (no resuelto)       |
    | 080496d0        | exit()                | ??? (no resuelto)       | 
    ```
2.  Siguiendo con el programa de ejemplo, en el código assembler del llamado a `printf()` y a `exit()` vemos que el compilador genera dos llamados que apuntan a la tabla PLT: `puts@plt` y `exit@plt`.

    ```
    user@abos:~$ objdump -M intel -d imprimir
    ```

    ```
    Disassembly of section .text:
    0804842b <main>:
         804842b: 55                    push   ebp
         804842c: 89 e5                 mov    ebp,esp
         804842e: 68 e0 84 04 08        push   0x80484e0
      => 8048433: e8 b8 fe ff ff        call   80482f0 <puts@plt>   ; llamado a puts@plt en .plt
         8048438: 83 c4 04              add    esp,0x4
         804843b: 6a 00                 push   0x0
      => 804843d: e8 ce fe ff ff        call   8048310 <exit@plt>   ; llamado a exit@plt en .plt
    ```

    Si seguimos las direcciones de los respectivos `call` vemos que se produce un salto a la sección `.plt`.
3.  Analizamos la sección `.plt` buscando las direcciones `80482f0` y `8048310`:

    ```
    user@abos:~$ objdump -M intel -d imprimir

    Disassembly of section .plt:
       
    080482f0 <puts@plt>:
      => 80482f0: ff 25 c8 96 04 08     jmp    DWORD PTR ds:0x80496c8   ; jmp 
         80482f6: 68 00 00 00 00        push   0x0
         80482fb: e9 e0 ff ff ff        jmp    80482e0 <_init+0x30>
       
    08048310 <exit@plt>:
      => 8048310: ff 25 d0 96 04 08     jmp    DWORD PTR ds:0x80496d0   ; jmp 
         8048316: 68 10 00 00 00        push   0x10
         804831b: e9 c0 ff ff ff        jmp    80482e0 <_init+0x30>
    ```

    En ambos casos, la primer instrucción es un salto dentro del segmento de datos (`ds:`) y si miramos detenidamente las direcciones del salto corresponden a las direcciones de cada entrada en la GOT que vimos con `objdump --dynamic-reloc`.\
    &#x20;Son entonces dos saltos que apuntan a entradas de la tabla GOT: `jmp puts@GOT` (`jmp ds:0x80496c8`) y el segundo `jmp exit@GOT` (`jmp ds:0x80496d0`). Corroboramos que efectivamente esas direcciones a dónde se salta corresponden a la tabla GOT, es decir, en este caso pertenecen a la sección `.got.plt`.

    ```
    user@abos:~$ objdump -s imprimir

    Contents of section .got.plt:     
     80496bc d0950408 00000000 00000000 f6820408  ................
     80496cc 06830408 16830408 26830408    ^       ........&...   
     ;                   ^                 |
     ;                   |                 | 
     ;                   |        0x80496c8: 0804286f en little endian
     ;          0x80496d0: 08043816  en little endian
    ```

    En la primer columna `objdump` nos muestra las direcciones correspondientes a la sección `.got.plt`, en las siguientes cuatro columnas sus valores en hexa y en la última su representación en ASCII (se indica un punto en el caso de ser caracteres no imprimibles).\
    &#x20;Efectivamente ambos saltos apuntan a la sección .got (o lo que es lo mismo a `.got.plt`). Por comodidad graficamos el contenido de la sección `.got.plt` que nos mostró `objdump -s` de la siguiente manera:

    ```
     ----------------- ----------------------- ---------------------------
    | Entrada en .got | Nombre de la función  | Dirección en ¿biblioteca? |
    |-----------------|-----------------------|---------------------------|
    | 080496c8        | printf()              | 0x080482f6 (sección .plt) |
    | 080496d0        | exit()                | 0x08048316 (sección .plt) | 
    ```

    En este punto, viendo el output anterior de `objdump -s` nos damos cuenta que aún la tabla GOT no tiene las direcciones efectivas de las funciones sino que almacena direcciones que apuntan nuevamente a la sección `.plt`.\
    &#x20;Es posible corroborarlo viendo el contenido de la dirección `0x080482f6` en el caso de `printf()` y de `0x08048316` en el caso de `exit()`:

    ```
    user@abos:~$ objdump -M intel -d imprimir

    Disassembly of section .plt:
       
    080482f0 <puts@plt>:
         80482f0: ff 25 c8 96 04 08     jmp    DWORD PTR ds:0x80496c8   ; jmp 
      => 80482f6: 68 00 00 00 00        push   0x0                      ; addr 0x080482f6
         80482fb: e9 e0 ff ff ff        jmp    80482e0 <_init+0x30>
       
    08048310 <exit@plt>:
         8048310: ff 25 d0 96 04 08     jmp    DWORD PTR ds:0x80496d0   ; jmp 
      => 8048316: 68 10 00 00 00        push   0x10                     ; addr 0x08048316
         804831b: e9 c0 ff ff ff        jmp    80482e0 <_init+0x30>
    ```

    En ambas entradas de la GOT se apunta a una instrucción dentro de `.plt` que hace `push` de un valor y un salto a la misma dirección de memoria (`jmp 80482e0`). Como se verá a continuación, en ambos casos, se produce un salto a una función trampolín dentro de `.plt`.\


    **¿Por qué las entradas en la GOT apuntan a la sección `.plt` nuevamente?**\
    \
    &#x20;En un primer llamado a una función de una biblioteca compartida, en la entrada de la GOT correspondiente se apunta a un sector de la tabla PLT que hace un llamado a una función trampolín. Esa función se encarga de transferirle el control al _dynamic linker_. Este es el encargado de hacer un llamado a la función invocada y después actualizar la entrada en la GOT con su dirección efectiva en la biblioteca correspondiente.\
    &#x20;A partir de entonces la entrada en la GOT se encuentra actualizada, y ya no apuntará a la función trampolín dentro de `.plt` sino directamente a una dirección en una biblioteca compartida.

    En el ejemplo, el llamado al _linker_ sucede con el `jmp 80482e0` que es un salto a una función trampolin también dentro de la sección `.plt` que invoca al _dynamic linker_. Este será el encargado de actualizar la GOT, es decir, reemplazar las direcciones `0x080482f6` en el caso de `printf()` y `0x08048316` en el caso de `exit()` por sus direcciones efectivas en la `libc.so` cargada en memoria.

    Es posible ver estos cambios si consultamos el contenido de la GOT después de ejecutar por primera vez cada una de las funciones:

    ```
          0x0804842c main+1  mov    ebp,esp
          0x0804842e main+3  push   0x80484e0
          0x08048433 main+8  call   0x80482f0 <puts@plt>
    eip=> 0x08048438 main+13 add    esp,0x4              ; 1er instrucción después de ejecutar printf()
          0x0804843b main+16 push   0x0
          0x0804843d main+18 call   0x8048310 <exit@plt>
       ───────────────────────────────────────────────────────────────────────────────────────────────
       >>> x/xw 0x80496c8                                ; consultamos la entrada de printf() en la GOT:
       0x80496c8 <puts@got.plt>: 0xb7e89d80              ; la entrada de printf() se actualizó
       >>> x/xw 0x80496d0                                ; consultamos la entrada de exit() en la GOT:
       0x80496d0 <exit@got.plt>: 0x08048316              ; la entrada de exit() aún no se actualizó
       >>> 
    ```

    Después de ejecutar `printf()` vemos como en la tabla GOT se especifica la dirección de esa función dentro de `libc` (`0xb7e89d80`). Podriamos ilustrar la tabla GOT en este punto de la siguiente manera:

    ```
     ----------------- ----------------------- -----------------------------
    | Entrada en .got | Nombre de la función  | Dirección en biblioteca     |
    |-----------------|-----------------------|-----------------------------|
    | 080496c8        | printf()              | 0xb7e89d80 (libc.so)        |
    | 080496d0        | exit()                | 0x08048316 (.plt)           | 
    ```

    Si avanzamos con la ejecución de `exit()` vemos que también su valor se actualiza en la tabla. Utilizamos un _watchpoint_ en gdb para que la ejecución se detenga cuando el valor de `exit` en la GOT se modifique:

    ```
    >>> x/xw 0x80496d0                        ; valor anterior de exit() en la GOT
    0x80496d0 <exit@got.plt>: 0x08048316 
    >>> watch *0x80496d0
    ─── Output/messages ───────────────────────────────────────────────────────────────────────────
    Hardware watchpoint 5: *0x80496d0 ...     ; la ejecución de detiene
    >>> x/xw 0x80496d0
    0x80496d0 <exit@got.plt>: 0xb7e561b0      ; valor actualizado de exit() en la GOT
    ```

    Ya en este punto la tabla GOT contiene las direcciones efectivas de las dos funciones en `libc`:

    ```
     ----------------   ---------------------   ------------------------
    | Entrada en .got | Nombre de la función  | Dirección en biblioteca |
    |-----------------|-----------------------|-------------------------|
    | 080496c8        | printf()              | 0xb7e89d80 (libc.so)    |
    | 080496d0        | exit()                | 0xb7e561b0 (libc.so)    | 
    ```

### La utilidad de la GOT <a href="#la-utilidad-de-la-got" id="la-utilidad-de-la-got"></a>

**¿Cómo es posible aprovecharse de la GOT a la hora de escribir exploits?**\
&#x20;La dirección de una entrada en la GOT está definida para cada binario, de modo que es independiente de la pila y sus variables de entorno. Se almacena en una posición de memoria estática y conocida y debe ser un sector de memoria que se pueda escribir, porque como se vió antes su contenido se actualiza de manera “diferida” en tiempo de ejecución.

Estas cualidades hacen de la GOT un recurso valioso para la escritura de exploits. Sobretodo en los casos donde no es de utilidad sobreescribir la dirección de retorno de la pila, como por ejemplo en un escenario en el que `main()` nunca retorna porque el programa finaliza con `exit()` o se mantiene en un loop infinito.

> **Consideraciones:** es necesario recordar para abusar de los permisos de escritura de la tabla GOT la [mitigación RELRO](https://fundacion-sadosky.github.io/guia-escritura-exploits/configuracion.html#deshabilitar-relro) o _RELocation Read-Only_ en inglés) debe estar deshabilitada o parcialmente habilitada como sucede por defecto en la mayoría de las distribuciones de Linux.

Si nuestro objetivo es hacer una lectura o escritura arbitraria en memoria, la GOT toma relevancia de la siguiente manera:

1.  **En una escritura arbitraria**\
    &#x20;Supongamos que gracias a la vulnerabilidad de un programa podemos escribir un valor arbitrario en algún lugar de la memoria y como dijimos antes una escritura en la dirección de retorno de la pila no es de utilidad. Si optamos por sobreescribir la entrada de alguna función en la GOT (que se encuentra en una sección de memoria con permisos de escritura), al ser llamada esa función ya no se reenvía la ejecución a la biblioteca correspondiente sino a dónde hemos especificado (por ejemplo la dirección de un shellcode).

    ```
     ---------------------  -------------------------
    | Nombre de la función | Dirección en biblioteca |
    |----------------------|-------------------------|
    | printf()             | &shellcode()            |
    ```
2.  **En una lectura arbitraria**\
    &#x20;Si podemos filtrar direcciones contenidas en la GOT podemos conocer información relevante para atacar un proceso.\
    &#x20;Por ejemplo en un ataque más avanzado con la mitigación [ASLR](https://fundacion-sadosky.github.io/guia-escritura-exploits/configuracion.html#deshabilitar-aslr) habilitada, una lectura de memoria sería de gran utilidad ya que la dirección de `libc` en cada ejecución del binario es diferente.

    Los cambios en la dirección de `libc` se pueden observar si ejecutamos varias veces el programa anterior con ASLR habilitado:

    ```
    user@abos:~$ sudo sysctl -w kernel.randomize_va_space=1                ; habilitamos ASLR
       
    user@abos:~$ ldd imprimir
      libc.so.6 => /lib/i386-linux-gnu/i686/cmov/libc.so.6 (0xb75b6000)    ; libc.so => 0xb75b6000
    user@abos:~$ ldd imprimir
      libc.so.6 => /lib/i386-linux-gnu/i686/cmov/libc.so.6 (0xb75ae000)    ; libc.so => 0xb75ae000
    user@abos:~$ ldd imprimir
      libc.so.6 => /lib/i386-linux-gnu/i686/cmov/libc.so.6 (0xb7559000)    ; libc.so => 0xb7559000
    ```

    Pero la dirección de las entradas en la GOT es fija -sin ninguna mitigación extra como una compilación con [PIE](https://en.wikipedia.org/wiki/Position-independent\_code#PIE) por ejemplo- y por lo tanto idéntica en cada ejecución.

    (Primero habilitamos ASLR, deshabilitada por defecto en gdb)

    ```
     user@abos:~$ gdb imprimir
     (gdb) show disable-randomization 
           Disabling randomization of debuggee's virtual address space is on.
     (gdb) set disable-randomization off
     (gdb) show disable-randomization 
     Disabling randomization of debuggee's virtual address space is off.
    ```

    ```
     ; 1er ejecución

     (gdb) r 
     (gdb) x/2i $eip
     => 0x8048433 :  call   0x80482f0 
        0x8048438 : add    $0x4,%esp
     (gdb) si 
     0x080482f0 in puts@plt ()
     (gdb) x/i $eip
     => 0x80482f0 :  jmp    *0x80496c8                    ; jmp 

     (gdb) p puts
     $1 = {} 0xb7618d80 <_IO_puts>    ; 1er ejecución: 0xb7618d80 addr puts() in libc
     (gdb) x/i $eip
     => 0x80482f0 :  jmp    *0x80496c8                    ; 1er ejecución: 0x80496c8  addr puts() in GOT

     ; 2da ejecución   
     (gdb) r
     The program being debugged has been started already.
     Start it from the beginning? (y or n) y 
     (...)
     (gdb) si
     0x080482f0 in puts@plt ()
     (gdb) p puts 
     $2 = {} 0xb7604d80 <_IO_puts>    ; 2da ejecución: 0xb7604d80 addr puts() in libc
     (gdb) x/i $eip
     => 0x80482f0 :  jmp    *0x80496c8                    ; 2da ejecución: 0x80496c8  addr puts() in GOT
    ```

    Observamos como en dos ejecuciones diferentes la dirección de `libc` se modifica, no así la dirección de `puts()` dentro de la tabla GOT.

    En este escenario una lectura arbitraria de la GOT sería útil. Como vimos la dirección de una entrada en la GOT será idéntica aunque ASLR esté habilitada y si leyeramos su contenido obtendremos información de la dirección efectiva de una función en `libc` (que cambiará cada vez). Si logramos esa información es posible calcular -sumando siempre el mismo offset- la dirección de otras funciones dentro de esa biblioteca como `system()` por ejemplo, aunque la mitigación ASLR se encuentre habilitada.
