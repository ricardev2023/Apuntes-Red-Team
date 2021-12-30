---
description: >-
  En este artículo crearemos un laboratorio sin mitigaciones frente a este tipo
  de ataques.
---

# CREANDO UN LABORATORIO SIN MITIGACIONES

{% hint style="info" %}
Artículo copiado de la Fundación Sadosky escrito por Teresa Alberto.&#x20;
{% endhint %}

{% embed url="https://fundacion-sadosky.github.io/guia-escritura-exploits/configuracion.html" %}

## INTRODUCCIÓN

A la hora de introducirse en el tema, es recomendable deshabilitar las técnicas de mitigación presentes por defecto en los sistemas modernos. A medida que se avanza con ataques más sofisticados es posible habilitar una a una estas mitigaciones para poner en práctica cómo subvertirlas y trabajar en el marco de escenarios más “reales”. Es por ello que se recomienda compilar los programas vulnerables con ciertos flags específicos que harán la tarea más fácil y se recomiendan ciertas configuraciones extras en el sistema.

## ENTORNO SIN MITIGACIONES <a href="#simular-entorno-sin-mitigaciones" id="simular-entorno-sin-mitigaciones"></a>

Como el **desbordamiento de búfer** es una vulnerabilidad de larga data, los sistemas operativos actuales desarrollaron prevenciones para este tipo de ataques, entre otras la **randomización del espacio de direcciones** y la **implementación de **_**canaries**_** de protección de la pila**. Por eso para introducir estos ataques es necesario deshabilitar una serie de configuraciones para, en una segunda instancia, explicar cómo superarlas. A continuación se hace un repaso de las mitigaciones y se usa el script `checksec` para detectar cuando se encuentran habilitadas o no.

### Deshabilitar ASLR <a href="#deshabilitar-aslr" id="deshabilitar-aslr"></a>

El **ASLR** (“Address Space Layout Randomization” o mapa de espacio de direcciones aleatorio) es una tecnología diseñada para impedir la explotación de ciertas vulnerabilidades. El **ASLR vuelve aleatorias las direcciones de memoria de un proceso y por ende, dficulta la tarea de adivinar direcciones de la pila**. Esta información es necesaria para la lectura y reescritura de sectores claves de la pila, para la inyección y ejecución de código malicioso y en el caso de técnicas de explotación más avanzadas, para la creación de **ROP gadgets**.

Es posible temporalmente deshabilitar esta protección desde la terminal configurando el espacio de memoria como “no random”.

```
user@abos:~$ sudo sysctl -w kernel.randomize_va_space=0 #no random
user@abos:~$ sudo sysctl -w kernel.randomize_va_space=1 #random
```

### Deshabilitar W^X <a href="#deshabilitar-mitigacion-wx" id="deshabilitar-mitigacion-wx"></a>

Es una **política en relación a la memoria que indica que nunca se debe tener una página de memoria escribible y ejecutable al mismo tiempo** (representado como _write XOR execute_ o **W^X**). Es por ello que se marcan los sectores escribibles dentro de las direcciones de un proceso como no ejecutables. **La pila entonces deviene en un área de memoria no ejecutable**.\
&#x20;Los ataques iniciales consistiran en parte en inyectar un código arbitrario en la pila (escribir en la pila) y ejecutarlo (ejecutar en la pila). Para ser capaces de escribir y ejecutar en la pila, es necesario inicialmente deshabilitar esta mitigación.

Por defecto `gcc` compila con esta mitigación habilitada. Para deshabilitar protección de ejecución en la pila, al compilar con `gcc` agregamos el flag `-z execstack`.

```
user@abos:~$ gcc -o no-exec programa.c
user@abos:~$ ./checksec.sh --file no-exec
NX           ...    FILE
NX enabled   ...    no-exec

user@abos:~$ gcc -z execstack -o exec programa.c
user@abos:~$ ./checksec.sh --file exec
NX           ...    FILE
NX disabled  ...    exec
```

### Deshabilitar RELRO <a href="#deshabilitar-relro" id="deshabilitar-relro"></a>

Para **prevenir técnicas de explotación que sobreescriben la Global Offset Table, se obliga al linker a resolver todas las funciones linkeadas dinámicamente al iniciarse el programa y, una vez actualizada la tabla GOT, volverla de sólo lectura de modo que no pueda ser sobreescrita de manera arbitraria**. Es por ello que en exploits que involucran la GOT es necesario que la mitigación RELRO (en inglés _RELocation Read-Only_) se encuentre **deshabilitada o habilitada parcialmente con la variante “RELRO parcial”**. Esta última opción es usada por defecto en la mayoría de distribuciones de Linux modernas, por lo que no es necesaria ninguna configuración extra.\
&#x20;No obstante en casos donde un ataque utilice la sección DTORS será necesario deshabilitarlo completamente con el flag `-Wl,-z,norelro`.

```
user@abos:~$ gcc -o con-relro programa.c
user@abos:~$ ./checksec.sh --file con-relro
RELRO           ...    FILE
Partial RELRO   ...    con-relro

user@abos:~$ gcc -Wl,-z,norelro -o sin-relro programa.c
user@abos:~$ ./checksec.sh --file sin-relro
RELRO      ...    FILE
No RELRO   ...    sin-relro
```

### Deshabilitar _canary_  <a href="#deshabilitar-canary-de-proteccion-de-la-pila" id="deshabilitar-canary-de-proteccion-de-la-pila"></a>

En esta técnica de mitigación el **compilador inserta una marca o **_**canario**_** en la pila cuando detecta una función que accede a variables locales por referencia**. En estos casos inmediatamente después de almacenar la dirección de retorno, se almacena a su vez un valor (el _canario_ o la _cookie_) entre la variable local (datos) y la dirección de retorno (información de control).\
&#x20;**Frente a un ataque de reescritura de la dirección de retorno, el valor del canario se verá modificado levantando alertas de que se produjo un desbordamiento de búfer**. Y se detiene la ejecución del programa antes de que la función retorne a la dirección vulnerada por ejemplo con una excepción de violación de segmento. En una primera instancia, de esta manera se evitaría una reescritura de la dirección de retorno de una función y la consecuente ejecución de código malicioso.

Dado que inicialmente se trabajará con técnicas de corrupción de la dirección de retorno en memoria es necesario deshabilitar esta protección de la pila compilando con `gcc` con el `flag -fno-stack-protector`.

```
user@abos:~$ gcc -o con-canary programa.c
user@abos:~$ ./checksec.sh --file con-canary
STACK CANARY      ...    FILE
Canary found      ...    con-canary

user@abos:~$ gcc -fno-stack-protector -o sin-canary programa.c
user@abos:~$ ./checksec.sh --file sin-canary
STACK CANARY      ...    FILE
No canary found   ...    sin-canary
```

### Otros flags de GCC útiles <a href="#otros-flags-de-gcc-utiles" id="otros-flags-de-gcc-utiles"></a>

* `-m32` para compilar ejecutables de 32 bits.
* `-mpreferred-stack-boundary=2` para el alineamiento de la pila.
* `-ggdb` habilita información de debugging
* `-no-pie` para evitar compilar el binario como _Position Independent Executable_, sino cambiaría en cada ejecución la ubicación del código en el espacio de memoria virtual.

### ¿Cómo compilar los binarios? <a href="#como-compilar-los-binarios" id="como-compilar-los-binarios"></a>

Inicialmente se compilan con los flags mencionados previamente:

```
user@abos:~$ gcc -m32 -no-pie -fno-stack-protector -ggdb -mpreferred-stack-boundary=2 -z execstack -o abo1 abo1.c
```

No se utiliza el flag `-Wl,-z,norelro` para deshabilitar `RELRO`, dado que con una configuración de `RELRO parcial` es posible avanzar sin problemas.

## PROBLEMA DEL MODO DEBUG CON LAS DIRECCIONES <a href="#direcciones-en-la-pila-con-y-sin-modo-debugging" id="direcciones-en-la-pila-con-y-sin-modo-debugging"></a>

En muchos exploits es necesario hacer cálculos sobre direcciones de la pila (la dirección de variables, punteros, etc). Como `gdb` agrega variables de entorno que se almacenan en la pila y la modifican, los cálculos sobre las direcciones obtenidas en `gdb` no resultan útiles para explotar el binario por fuera de un entorno de debugging.

Por ejemplo, para ver las diferencias en variables de entorno:

```
user@abos:~$  /usr/bin/printenv
XDG_SESSION_ID=...
TERM=...
SHELL=...
(...)

user@abos:~$ gdb -q /usr/bin/printenv
XDG_SESSION_ID=...
TERM=...
SHELL=...
(...)
COLUMNS=111             ; env var de gdb
LINES=48                ; env var de gdb
```

Existen varias maneras de alinear la memoria dentro y fuera de `gdb`.

### Limpiar variables de entorno <a href="#limpiar-variables-de-entorno" id="limpiar-variables-de-entorno"></a>

Para limpiar las variables de entorno es posible ejecutar el programa con el _wrapper_ `env -i`. Como ejemplo podemos ver como se limpian las variables de entorno al ejecutar `env -i /usr/bin/printenv`: **Printenv**

```
user@abos:~$ env -i /usr/bin/printenv
user@abos:~$
```

No obstante, vemos que `gdb` igualmente agrega variables de entorno:

```
user@abos:~$ env -i gdb /usr/bin/printenv
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
(gdb) r
COLUMNS=72
PWD=/home/usuarix/abos/stack1
LINES=34
```

#### wrapper `env -i` <a href="#wrapper-env--i" id="wrapper-env--i"></a>

Una posible solución es usar el _wrapper_ `env -i` para limpiar las variables de entorno tanto usando `gdb` como a la hora de ejecutar el programa. Dentro de `gdb` con `show env` sabemos qué variables de entorno se mantienen y optar por sumarlas en la ejecución o eliminarlas de `gdb` con `unset env`.

Por ejemplo, ejecutamos `printenv` con las variables de entorno que incluye `gdb` que observamos recién (usando siempre el `path` completo para ejecutar el programa como hace el debugger):

```
user@abos:~$ env -i COLUMNS=72 PWD=$(pwd) LINES=34 /usr/bin/printenv
COLUMNS=72
PWD=/home/usuarix/carpeta
LINES=34
```

O debugeamos `printenv` con `env -i` y eliminamos dentro de `gbd` las variables de entorno que se mantienen con `unset env`:

```
user@abos:~$ env -i gdb /usr/bin/printenv
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
(gdb) show env
LINES=34
COLUMNS=95
(gdb) unset env LINES
(gdb) unset env COLUMNS
(gdb) show env
(gdb) r
Starting program: /usr/bin/printenv 
PWD=/home/usuarix/carpeta
[Inferior 1 (process 4546) exited normally]
```

Y si lo ejecutamos:

```
user@abos:~$ env -i PWD=$(pwd) /usr/bin/printenv
PWD=/home/usuarix/carpeta
```

**Stack 1** Probamos la primer solución con el programa [Stack 1](https://fundacion-sadosky.github.io/guia-escritura-exploits/buffer-overflow/1-practica#stack-1) y observamos cómo las direcciones de `buf` y `cookie` son idénticas al ejecutar y debuggear el programa:

```
user@abos:~$ env -i LINES=34 PWD=$(pwd) COLUMNS=72 /home/usuarix/abos/stack1
buf: bffffd94 cookie: bffffde4

user@abos:~$ env -i gdb ./stack1
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
>>> r
buf: bffffd94 cookie: bffffde4
```

Y sino, de otra manera, probamos sin variables de entorno en `gdb` y vemos como logramos las mismas direcciones para las variables:

```
user@abos:~$ env -i PWD=$(pwd) /home/usuarix/abos/stack1
buf: bffffdb4 cookie: bffffe04

user@abos:~$ env -i gdb ./stack1
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
(gdb) show env
LINES=34
COLUMNS=95
(gdb) unset env LINES
(gdb) unset env COLUMNS
(gdb) show env
(gdb) r
buf: bffffdb4 cookie: bffffe04
```

Si es necesario enviarle un input por stdin al programa vulnerable (que cuenta por ejemplo con una función como `gets()`) esta solución también funciona:

```
user@abos:~$ python exploit.py | env -i PWD=$(pwd) /home/usuarix/abos/stack1
buf: bffffdb4 cookie: bffffe04
...

user@abos:~$ python exploit.py > in
user@abos:~$ env -i gdb ./stack1
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
(gdb) show env
LINES=34
COLUMNS=95
(gdb) unset env LINES
(gdb) unset env COLUMNS
(gdb) show env
(gdb) r < in
buf: bffffdb4 cookie: bffffe04
```

Y también cuendo el programa vulnerable espera que se le pasen argumentos por ejemplo con una función como `strcpy(buf,argv[1])`):

```
user@abos:~$ env -i PWD=$(pwd) /home/usuarix/abos/abo1 "$(./arg-exploit.py)"
buf: bffff558

user@abos:~$ env -i gdb ./abo1
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
(gdb) show env
LINES=34
COLUMNS=95
(gdb) unset env LINES
(gdb) unset env COLUMNS
(gdb) show env
(gdb) r "$(./arg-exploit.py)"
buf: bffff558
```

Para facilitar esta configuración es posible editar el archivo `.gdbinit`:

```
bash -c "echo 'unset env LINES' >> .gdbinit"
bash -c "echo 'unset env COLUMNS' >> .gdbinit"
```

### Script fixenv

Otra solución es utilizar un script como Fixenv de Hellman que fija las direcciones del stack. Es posible ver el funcionamiento del script de Hellman (usando `./r.sh` como _wrapper_) con el programa Stack 1.

```
; sin las direcciones de la pila consistentes

user@abos:~$ ./stack1 
buf: bffff6a4 cookie: bffff6f4

user@abos:~$ gdb ./stack1          
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
>>> r
buf: bffff654 cookie: bffff6a4
```

```
; con las direcciones de la pila consistentes

user@abos:~$ ./r.sh ./stack1
buf: bffff734 cookie: bffff784

user@abos:~$ ./r.sh gdb ./stack1         
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
>>> r
buf: bffff734 cookie: bffff784
```

A diferencia de la solución anterior con `env -i` cuando el programa vulnerable toma un input por stdin, este script no resuelve las diferencias entre las direcciones con y sin gdb.

En cambio funciona correctamente cuando es necesario pasarle argumentos al programa vulnerable:

```
user@abos:~$ ./r.sh ./abo1 "$(./exploit.py)"
buf: bffff558

user@abos:~$ ./r.sh gdb ./abo1         
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
>>> r "$(./exploit.py)"
buf: bffff558
```

## EJECUCIÓN DE BINARIOS NO CONOCIDOS <a href="#ejecucion-de-binarios-no-conocidos" id="ejecucion-de-binarios-no-conocidos"></a>

Si bien en este caso nos ocupamos de compilar nosotrxs mismxs los binarios de los programas vulnerables, cuando se trabaja en estos temas es una buena práctica aislar en una máquina virtual la ejecución de binarios no conocidos.

## REFERENCIAS

\[1]. Reece, Alex. (02 de mayo de 2013). Introduction to format string exploits \[Post]. _Code Arcana_. Recuperado de http://codearcana.com/posts/2013/05/02/introduction-to-format-string-exploits.html
