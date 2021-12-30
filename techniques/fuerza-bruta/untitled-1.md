---
description: Explicación detallada de la herramienta Medusa.
---

# MEDUSA

## **MEDUSA**

Medusa es una herramienta para realizar ataques por fuerza bruta desarrollada por JoMo-Kun cuyo objetivo es conseguir acceso a sistemas remotos probando pares de usuarios/contraseñas de manera eficaz (login cracker). Las características más importantes de medusa son:\


* Posee un diseño modular lo que nos permite modificar el módulo de un servicio concreto sin tener que modificar el core de medusa. Además esto se realiza de una manera muy cómoda mediante los archivos .mod que son absolutamente independientes entre si, pudiendo ampliar por tanto cualquier funcionalidad que Medusa traiga de serie.
* Permite una paralelización absoluta de los ataques pudiendo realizar ataques a múltiples host, usuarios o contraseñas al mismo tiempo sin restricciones.
* Tiene una entrada de parámetros muy flexible, pudiendo realizar la entrada de hosts, usuarios, contraseñas y opciones de múltiples maneras.
* Nos permite auditar la seguridad de los usuarios/contraseñas de acceso de los siguientes servicios: **AFP, CVS, FTP, HTTP, IMAP, MS-SQL, MySQL, NetWare NCP, NNTP, PcAnywhere, POP3, PostgreSQL, RDP, REXEC, RLOGIN, RSH, SMBNT, SMTP, SMTP-VRFY, SNMP, SSH, SVN, Telnet, vmauthd, VNC, Genérico Wrapper y Web Form**.

### **Funcionamiento básico**

**`medusa -h [ip] -u [usuario] -P [wordlist de password] -M [modulo de protocolo] -m [opciones de modulo]`**

&#x20;**-h** → IP del host

&#x20;**-H** → listado de hosts en archivo

&#x20;**-u** → usuario

&#x20;**-U** → listado de usuarios en archivo

&#x20;**-p** → password

&#x20;**-P** → wordlist de passwords

&#x20;**-e** → Prueba passwords especiales (n= vacia s=usuario ns= las dos)

&#x20;**-M** → selecciona el modulo

&#x20;**-d** → lista los protocolos

&#x20;**-m** → incluye las opciones de los modulos

&#x20;**-n** → seleccion de puerto

&#x20;**-s** → habilita SSL

&#x20;**-f** → se detiene cuando detecta una pareja user:password valida

&#x20;**-g** → marca el tiempo de espera de respuesta desde la solicitud en segundos

&#x20;**-r** → sleep entre intentos

## REFERENCIAS

[https://www.lapsusmentis.com/2015/08/ataque-por-fuerza-bruta-con-medusa.html](https://www.lapsusmentis.com/2015/08/ataque-por-fuerza-bruta-con-medusa.html)
