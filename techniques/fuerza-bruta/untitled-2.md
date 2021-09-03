---
description: Explicación detallada de la herramienta John the ripper.
---

# JOHN THE RIPPER

## **JOHN THE RIPPER**

**John the Ripper es una herramienta de crackeo de passwords** escrita en C y muy utilizada por los analistas de seguridad para comprobar la robustez de una clave frente a ataques de fuerza bruta.

## Ejemplo de funcionamiento

Lo primero que hacemos es generar un archivocomando incluido con John con los usuarios y las passwords con el siguiente :

 **`unshadow /etc/passwd /etc/shadow > passwd.txt`**

###  **ARGUMENTOS**

 **--show** → esta opción nos permite visualizar las contraseñas mientras la herramienta esta en ejecución.  
 Mientras estas realizando el crackeo puedes pulsar cualquier tecla para poder ver el proceso que lleva en todo momento, pero si pulsamos Ctrl+C, terminaríamos el  
 proceso de crackeo y se guarda en un archivo para así seguirlo posteriormente, pero si lo pulsamos dos veces seguidas, la herramienta abortara el proceso de crackeo  
 sin guardar ningún dato.

 **-restore** → esta opción sirve para cuando deseamos continuar con una sesión que hemos paralizado anteriormente.

 **--single** → esta opción permite a la herramienta realizar un crackeo mas rápido, ligero pero por contraposición con menor probabilidad de averiguar claves.

 **--incremental** → esta opción permite a la herramienta utilizar todas las herramientas posibles para asi tener el mayor porcentaje de acierto posibles.  
  
 **--wordlist:File** → esta opción permite utilizar un diccionario de claves para poder averiguar las claves guiándose a través de una lista de posibles claves.

 **-savemem** → esta opción es útil si el equipo cuenta con poca memoria por que le puedes indicar a la herramienta que no utilice demasiada memoria para así no afectar mucho  
 a los otros procesos del sistema. Por ejemplo el nivel 1 indica que no pierda memoria en nombre de los inicios de sesión por los que no los vera mientras realiza el  
 crackeo.

 **-users:UID** → esta opción es bastante útil por que sirve por si deseas crakear una cuenta de usuario introduciendo el numero de UID, en el caso en el que quieras hacer un filtro  
 invertido \(que crakees todos menos el que le indiques\) le tienes que indicar un – delante del numero que indiques.

 **-external: mode** → esta opción permite definir opciones externas que le hayas definido previamente en el fichero ~/john.ini’ s.

 **-groups: GID** → esta opción permite realizar lo mismo que con la opción de user pero con la diferencia que esta opción te permite crakear todos los usuarios de un grupo  
 indicado con la GID, y si deseas realizar un filtrado inverso tendremos que hacer lo mismo que con el de users que es el poner el signo menos delante del GID.

###  **EJEMPLOS DE ATAQUES**

 **`john -incremental --users=1001 passwd.txt`**

 **`john --wordlist:claves.lst passwd.txt`**

 **`zip2john hola.zip > hola.txt → john hola.txt → john --show hola.txt`**

## REFERENCIAS

[https://github.com/openwall/john](https://github.com/openwall/john)

