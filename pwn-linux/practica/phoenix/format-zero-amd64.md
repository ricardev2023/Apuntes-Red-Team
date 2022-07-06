# FORMAT-ZERO amd64

Éste nivel introduce la utilización de format-string bugs para afectar a la ejecución de un programa.

## CÓDIGO FUENTE

```c
/*
 * phoenix/format-zero, by https://exploit.education
 *
 * Can you change the "changeme" variable?
 *
 * 0 bottles of beer on the wall, 0 bottles of beer! You take one down, and
 * pass it around, 4294967295 bottles of beer on the wall!
 */

#include <err.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#define BANNER \
  "Welcome to " LEVELNAME ", brought to you by https://exploit.education"

int main(int argc, char **argv) {
  struct {
    char dest[32];
    volatile int changeme;
  } locals;
  char buffer[16];

  printf("%s\n", BANNER);

  if (fgets(buffer, sizeof(buffer) - 1, stdin) == NULL) {
    errx(1, "Unable to get buffer");
  }
  buffer[15] = 0;

  locals.changeme = 0;

  sprintf(locals.dest, buffer);

  if (locals.changeme != 0) {
    puts("Well done, the 'changeme' variable has been changed!");
  } else {
    puts(
        "Uh oh, 'changeme' has not yet been changed. Would you like to try "
        "again?");
  }

  exit(0);
}
```

Como ya se ha visto en la parte de teoría de [Format-Strings](../../teoria/format-string.md), la utilización de funciones como sprintf para formatear las cadenas de caracteres introducidas por el usuario puede desembocar en un problema de seguridad muy grave.

El programa inicializa unas variables como parte de una estructura (struct) llamada locals. Entre ellas encontramos la variable `locals.changeme` que se inicializa a 0. Nuestro objetivo es modificar el valor de esta variable haciendo uso de un **format-string bug**.

En este caso, la variable `locals.changeme` está almacenada en el buffer justo después de `locals.dest[32]`. Por lo tanto tendríamos que sobrescribir **32 bytes** para empezar a sobrescribir el contenido de `locals.changeme`. Sin embargo, buffer es solo de 16 bytes, por tanto, ¿Cómo podría sobrescribirlo?

## EXPLOTANDO LA VULNERABILIDAD

Como hemos visto, solo podemos introducir 16 bytes en el buffer pero debemos sobrescribir 32 + 1 al menos para ganar el reto.

De la teoría de **Format-String bug** sabemos que, de existir un bug de este tipo en el programa, al introducir `%x` en el **buffer**, nos devuelve una dirección de memoria de 8 bytes que después se almacenaría en `locals.dest[32]`.  Esto haría que 2 bytes del **buffer** ocuparan 8 bytes en `locals.dest[32]`.  Por esta razón, si introducimos `%x%x%x%x` en el buffer, solo ocupamos 8 bytes pero estamos escribiendo la totalidad de los 32 bytes en `locals.dest[32]`.

Por tanto, la solución a este ejercicio es introducir por stdin,  %x%x%x%x%x por ejemplo, de esta manera estamos introduciendo 40 bytes en `locals.dest[32]` lo que hace que se sobrescriba la variable `locals.changeme` con una dirección de memoria.

```bash
user@phoenix-amd64:~$ /opt/phoenix/amd64/format-zero 
Welcome to phoenix/format-zero, brought to you by https://exploit.education
%x%x%x
Uh oh, 'changeme' has not yet been changed. Would you like to try again?
 
user@phoenix-amd64:~$ /opt/phoenix/amd64/format-zero 
Welcome to phoenix/format-zero, brought to you by https://exploit.education
%x%x%x%x
Well done, the 'changeme' variable has been changed!
```
