---
description: Bypassing NX
---

# RETURN-ORIENTED PROGRAMMING

La base de ROP es encadenar pequeños fragmentos de código que se pueden encontrar dentro del binario (llamados gadgets) de tal forma que se realicen las acciones que uno desee.

Esto suele implicar pasar parámetros a funciones que ya se encuentran presentes en libc, como por ejemplo, system() que se encarga de ejecutar un comando en el sistema. Este comando podría ser cat flag.txt o uno más peligroso como /bin/sh que daría acceso al atacante a una shell.

Hacer esto sin embargo no es tan sencillo como podría parecer. Para llamar correctamente a una función, primero debemos entender la [**convención de llamadas**](../32-bits-vs-64-bits.md).
