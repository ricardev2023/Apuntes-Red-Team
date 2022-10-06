# CONVENCIÓN DE LLAMADAS

La mayor parte de la teoría que se va a ver en la guía es aplicable tanto a arquitectura de 32bit como a 64bit. Sin embargo hay un detalle muy importante que varía y es necesario tenerlo en cuenta:

### Convención de llamadas: Argumentos de las funciones

La manera de pasar argumentos a las funciones varía en función de la arquitectura:

#### x86

En arquitectura de 32 bits, los argumentos se almacenan directamente en el stack siguiendo la convención de llamadas:

![](../../.gitbook/assets/stack-convention.png)

#### x64

Por otro lado, en arquitecturas más modernas (64bits), los parámetros se almacenan en los **registros** a no ser que haya más de 6 argumentos, que los últimos se almacenarían en el stack.

![Fijense que ésta imagen muestra el stack al revés que la imagen superior.](<../../.gitbook/assets/image (2) (2).png>)

Como se puede observar, los argumentos se almacenan en: **RDI, RSI, RDX, RCX, R8 y R9** y despúes se comienzan a almacenar en el **stack** desde el séptimo en adelante.
