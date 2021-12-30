---
description: >-
  En esta sección veremos que es, para que se utiliza y el uso de las
  herramientas que nos facilitan los ataques de fuerza bruta.
---

# FUERZA BRUTA

## **TEORIA FUERZA BRUTA**

Un ataque de fuerza bruta es un intento de descifrar una contraseña o nombre de usuario, de buscar una página web oculta o de descubrir la clave utilizada para cifrar un mensaje, que consiste en aplicar el método de prueba y error con la esperanza de dar con la combinación correcta finalmente. Se trata de antiguo método de ataque, pero sigue siendo eficaz y goza de popularidad entre los hackers.

En función de la longitud y complejidad de la contraseña, descifrarla puede llevar desde unos segundos hasta varios años. De hecho, apunta a que algunos hackers tienen los mismos sistemas como objetivo a diario durante meses e, incluso, años.

&#x20;Existen dos tipos de ataques de fuerza bruta:

* **Ataque de diccionario.**

&#x20;Se utiliza un diccionario con nombres de usuario y passwords obtenidas en la fase de reconocimiento o las predefinidas del servicio que queremos explotar.

* **Ataque por espacios clave.**

Consiste en pasar por todas la combinaciones posibles. Por un lado, al final consigues la clave, por otro, el diccionario sería infinito.

### &#x20;**DICCIONARIOS**

&#x20;Podemos encontrar los diccionarios predefinidos de Kali Linux o Parrot OS en **`/usr/share/wordlist`**

#### &#x20;**CRUNCH**

&#x20;Es una aplicacion que nos permite crear diccionarios para ambos tipos de ataques.

&#x20;**`crunch 4 4 -f /usr/share/crunch/charset.lst ualpha`**

&#x20;Genera un diccionario de 4 caracteres (minimos y maximos) con el **charset** (**-f**) ualpha

&#x20;Permite marcar el tipo de caracteres que queremos con la opcion **-t**

&#x20;**- Minusculas @** \
&#x20;**- Mayusculas ,** \
&#x20;**- Numeros %** \
&#x20;**- Caracter Especial ^**&#x20;

&#x20;**`crunch 6 6 -t @,%^%% -f ./charset.lst mixalpha-numeric-all-space`**

## **TIPOS DE HASHES**

```
DES: rEK1ecacw.7.c (Algunos UNIX, Solaris, PHP)

BSDI: _J9..K0AyUubDrfOgO4s (*BSD, algunos Linux y PHP)

MD5: $1$O3JMY.Tw$AdLnLjQ/5jXF9.MTp3gHv/ (FreeBDS, Cisco IOS, Linux)

SHA-256: $5$MnfsQ4iN$ZMTppKN16y/tIsUYs/obHlhdP.Os80yXhTurpBMUbA5 (Ubuntu, Fedora, PHP, Solaris)

SHA-512: $6$zWwwXKNj$gLAOoZCjcr8p/.VgV/FkGC3NX7BsXys3KHYePfuIGMNjY83dVxugPYlxVg/evpcVEJLT/rSwZcDMlVVf/bhf.1 (Igual SHA-256)

LM: 855c3697d9979e78ac404c4ba2c66533 (Windows NT, 2000, XP y MacOS) (Lam Manager) Esta basado en DES

NTLM: $NT$7f8fe03093cc84b267b109625f6bbf4b (Windows NT, 2000, XP, Vista, 7 y Samba) (New Tecnology Lam Manager) Esta basado en MD4

MacOS X salted SHA-1: 0E6A48F765D0FFFFF6247FA80D748E615F91DD0C7431E4D9 (MacOS X 10.4+)

Oracle: 000EA4D72A142E29 (Oracle 10)

SAP: $7369F1C8ECD6FCA1C6413F07408CA281C7A85EF0 (SAP)
```

## &#x20;**AUTENTICACION EN WINDOWS**

### &#x20;**HOSTS INDEPENDIENTES**

&#x20;Para poder autenticarse en un sistema windows, las password deben estar almacenadas en local\
&#x20;Para ganar seguridad se hashean antes de almacenarse.

&#x20;**C:\system32\config\SAM**

&#x20;Es un archivo sin extension donde se almacenan las password para hosts aislados.

### &#x20;**ACTIVE DIRECTORY**

&#x20;En el caso de que se trate de un Server, la autenticacion se realiza en tres fases

&#x20;**1**. Mensaje de Negociacion (Cliente → Servidor)\
&#x20;**2**. ID Cliente (Servidor → Cliente)\
&#x20;**3**. Autenticacion (Cliente → Servidor)

### &#x20;**KERBEROS**

&#x20;A partir de WIN XP y de 2000 Server, se trabaja con **Kerberos**.

&#x20;Si hay un **desfase de mas de 5 minutos** entre la hora del Controlador de Dominido y la del cliente, no se puede autenticar ni el Administrador. (muy potente pero con algunas vulnerabilidades)

### &#x20;**SYSKEY**

&#x20;**A partir de Win 2000** **Cifra los hashes** almacenados en la base de datos de autenticacion.

## &#x20;**APLICACIONES PARA CONSEGUIR LOS HASHES DE WINDOWS**

### &#x20;**FGDUMP.EXE**

&#x20;**Obtiene los hashes de la base de datos de autenticacion en local**, es decir, hay que instalarlo en local para que funcione.

&#x20;Genera un archivo 127.0.0.1.pwdump con el contenido

### &#x20;**WCE.EXE -W**

### &#x20;**MIMIKATZ**

&#x20;Obtiene los hashes en local. Lo bueno que tiene es que con una sesion meterpreter de msf, podemos ejecutarlo directamente sin tener que descargarlo.

## **REFERENCIAS**

****[https://www.kaspersky.es/resource-center/definitions/brute-force-attack](https://www.kaspersky.es/resource-center/definitions/brute-force-attack)****\
****
