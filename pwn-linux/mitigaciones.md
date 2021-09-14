---
description: >-
  En esté artículo nos centramos en dar nombre y definición a todas las posibles
  mitigaciones que encontramos para hacer frente al buffer overflow.
---

# MITIGACIONES

## BIT NX

Esta medida de seguridad garantiza que ciertas áreas de memoria \(como el _stack_ y el _heap_\) no sean ejecutables, y otras, como la sección del código, no puedan ser escritas. Dado que, hasta el momento, hemos aprovechado la explotación de _buffer overflows_ para ejecutar código arbitrario contenido en el _stack_, el bit NX ha tenido que estar apagado.

### Desactivación

Para desactivarla hay que entrrar en la linea de comandos de GRUB y en la linea del kernel \(empieza por linux\) incluir lo siguiente al final:  
 noexec=off noexex32=off

## ASLR

**Address Space Layout Randomization** es una técnica utilizada para mitigar los ataques contra los desbordamientos de buffer, haciendo que los segmentos de memoria no tengan una posición fija en memoria sino que esta varíe de forma aleatoria en cada ejecución.

###  Tipos

 **1.** **STACK ASLR**: Cada nueva ejecución de un programa hace que el stack tenga una disposición distinta sobre el espacio de direcciones. Lo que hace mucho más difícil encontrar en memoria dónde atacar o incrustar un payload para su posterior ejecución.

 **2.** **LIBS/MMAP \(Librerías /Memory Map\) ASLR**: Cada ejecución de un programa resulta en un nuevo espacio de direcciones distinto. Ésto significa que las librerías cargadas dinámicamente se cargan en diferente localización de cada vez. Esto evita la ejecución de técnicas como return-to-libc.

 **3. EXEC ASLR**: Cada ejecución de un programa compilado con la directiva _**“-fPIE -pie”**_ se cargará en una localización de memoria distinta. Por tanto, es más difícil localizar dónde saltar o atacar cuando se realizan técnicas de corrupción de memoria.

 **4.** **BRK ASLR**: De forma similar al **EXEC ASLR, BRK ASLR** ajusta las direcciones de memoria relativas entre la zona de memoria de ejecución y la brk  
 \(para pequeñas reservas de memoria\). Siendo sinceros, no entiendo muy bien que ventajas tiene este tipo de randomización de memoria, espero comprenderlas  
 más adelante.

 **5. VDSO \(Virtual Dinamically-linked Shared Object\) ASLR**: Por lo que entiendo, el **VDSO** es un objeto o librería compartida que se mapea en el espacio de kernel y que es compartido por todos los procesos. La utilidad de éste es permitir al espacio de usuario realizar ciertas acciones sobre el kernel \(todas de solo lectura , gracias @BatchDrake\).  pero sin el coste de las llamadas a sistema \(syscalls\). Con cada nueva ejecución del programa el **VDSO** tiene una dirección de memoria distinta.

### Desactivación

 Para desactivarlo debemos entrar en `/proc/sys/kernel/randomize_va_space` y cambiamos el valor 2 por 0.

## CANARY

**Proteccion del STACK o Canario** es la protección más básica contra desbordamientos de buffer que utiliza el sistema. La idea es alojar un pequeño número entero aleatorio en la zona de memoria justo antes del puntero de retorno de pila.

Si al llamar a una función se produce un desbordamiento de la pila, el valor del entero añadido o _**“canario”**_ se verá sobreescrito, abortando la ejecución del programa.

### Activación

Dicha protección no viene incluida por defecto en todos los sistemas operativos linux que existen.  
Si deseas activarla, al compilar basta con utilizar el flag **`-fstack-protector,`** y el programa disfrutará de dicha protección.

## PIE

**Position Independent Executables**. Es una técnica de randomización del espacio de direcciones que se especifica en tiempo de compilación. Ésta compila y linka ejecutables para que sean  
 independientes de la posición. Ésto significa que el código máquina se debe ejecutar correctamente sin tener en cuenta donde resida realmente en memoria.

Combinado con un kernel que sepa reconocer que está cargando un binario PIE, éste lo carga en una dirección de memoria aleatoria en vez de las direcciones estáticas tradicionales.

### Activación

Una forma sencilal de obtener un binario PIE con gcc es : `gcc -o pie -fpie pie.c`

## RELRO

**RElocation Read Only**. se basa en resolver todas las direcciones de las funciones llamadas por la PLT al principio de la ejecución del programa y convertir la GOT en solo lectura. De modo que así se evita que las direcciones cargadas en la **GOT** se puedan sobreescribir, permitiendo por ejemplo saltar a una dirección de memoria de nuestra elección.

{% hint style="info" %}
Para saber más acerca de la GOT y la PLT, puede leer éste [artículo](https://ajcruz15.gitbook.io/red-team/pwn-linux/estructura-de-un-binario-de-linux#global-offset-table-got). 
{% endhint %}

## PROTECCIÓN DEL HEAP

La **librería de protección del heap de GNU C** realiza distintas comprobaciones de forma automática sobre:

 1. Listas corruptas  
 2.  Llamadas a la función unlink\(\).  
 3. Problemas de double **free\(\)**.  
 4.  Overflows básicos \(Que se intente escribir en un bloque contiguo a la memoria reservada con malloc\(\) por ejemplo\).

## OFUSCACIÓN DE PUNTEROS

Tal y como yo entiendo esta medida de seguridad, se trata de “cifrar” de alguna forma los punteros a función dentro del código.

Ésto es, en vez de guardarlos tal cual, aparecerían en el desensamblado como el resultado de XORizar el valor del puntero con un entero de 32/64 bits, y recuperando los valores verdaderos en tiempo de  
ejecución \(eso creo\).

## FORTIFY\_SOURCE

Es una **protección en tiempo de compilación** que activa una serie de protecciones  
 en la glibc:  
 1. Expande las llamadas a funciones inseguras como **sprintf** o **strcpy** a las funciones seguras en las que el tamaño del buffer es conocido.  
 2. Evita los ataques de cadena de formato de tipo “%n” donde la cadena de formato está en un segmento de memoria escribible.  
 3. Fuerza la comprobación de los valores de retorno de funciones importantes como **system** ,**write** u **open**.  
 4. Se requiere declarar explícitamente la máscara de archivo \(para los permisos\) al crear nuevos ficheros.

### Activación

se puede activar añadiendo la directiva `-D_FORTIFY_SOURCE=2` al gcc. 

## BIND\_NOW

De nuevo otra técnica en tiempo de compilación, que en combinación con **RELRO** hace que el ejecutable resuelva todos los símbolos dinámicos al inicio de la ejecución en vez de en el momento en que se necesiten. Eso permite marcar la **GOT** como solo lectura\(todas las llamadas fueron resueltas ya, no es necesaria su escritura\) y evitar su posterior sobreescritura.

## PROTECCIÓN SOBRE EL FICHERO /PROC/$PID/MAPS

Dicho fichero muestra en caliente el mapa de memoria de un proceso determinado por su pid. El fichero se marca como solo lectura excepto para el propio proceso, evitando su modificacion por parte de un atacante \(por ejemplo indicar desde ahi­ que el stack es ejecutable\).

## RESTRICCIONES EN SIMLINKS

 Se basa en limitar el acceso a links simbólicos en directorios con permisos de escritura globales , como _**“/tmp”**_. Los links no se pueden seguir si el usuario que accede al link simbólico no es el propietario. Dicho comportamiento se puede controlar desde: `/proc/sys/kernel/yama/protected_sticky_symlinks sysctl`

## RESTICCIONES EN HARDLINKS

De forma análoga a los links simbólicos, un atacante puede crear un enlace duro a `/etc/shadow` por ejemplo, si `/etc` ****está en la misma partición que el `/home` del usuario que realiza el ataque. La protección se basa en que los enlaces duros no se pueden crear en archivos sobre los que el usuario era incapaz de leer y escribir originalmente.

Dicho comportamiento se puede controlar desde `/proc/sys/kernel/yama/protected_nonaccess_hardlinks sysctl`

## ALCANCE DE PTRACE

Una debilidad problemática de las interfaces de proceso de linux es que un usuario es capaz de examinar la memoria y el estado de cualquiera de sus procesos. Por ejemplo, si una aplicación fuera comprometida, sería posible para un atacante adjuntarse a otros procesos en ejecución \(sesiones SSH , etc..\) y extraer credenciales adicionales y continuar expandiéndose.

La protección consiste en que los usuarios no pueden ptracear los procesos que no sean descendientes del depurador que se utilice en ese momento. El comportamiento se puede controlar desde `/proc/sys/kernel/yama/ptrace_scope sysctl`

