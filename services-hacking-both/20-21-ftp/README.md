---
description: explotar servicio de transmisión de archivos FTP
---

# 20,21 - FTP

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ftp" %}

## INFORMACIÓN BÁSICA

El **Protocolo de transferencia de archivos** (en inglés **File Transfer Protocol** o **FTP**) es un protocolo de red para la transferencia de archivos entre sistemas conectados a una red TCP , basado en la arquitectura cliente-servidor.&#x20;

Desde un equipo cliente se puede conectar a un servidor para descargar archivos desde él o para enviarle archivos, independientemente del sistema operativo utilizado en cada equipo.

Funciona utilizando dos canales de datos:

* **Puerto 20** -> canal de datos.
* **Puerto 21** -> canal de control (tambien llamado comando).

Gracias a estos dos canales se pueden seguir mandando comandos al servidor sin tener que esperar a la carga o descarga de datos.

### Problemas de seguridad

El mayor problema de este servicio es que toda la información se transmite en texto claro. Para ello se han desarrollado dos nuevos sistemas:

* **SFTP** -> Utiliza el servicio SSH que es más seguro para la transferencia de datos. Solo utiliza el **puerto 22** (SSH).
* **FTPS** -> También conocido como FTP/SSL. Utiliza encriptación sobre el servicio FTP tradicional.

### Conexiones activas y pasivas

#### Conexión Activa

En una **conexión activa** el cliente FTP inicia la conexión de control desde el puerto **N** al puerto de comando del servidor FTP (puerto 21). El cliente entonces escucha en el puerto **N+1** y le envía el puerto **N+1** al servidor FTP. **El servidor FTP entonces inicia la conexión de datos**, desde su puerto **M** al puerto **N+1** del cliente.

#### Conexión Pasiva

Si el cliente FTP tiene un firewall que controla las conexiones entrantes desde el exterior, la conexión activa puede ser un problema. Por eso se utiliza la conexión pasiva.

En una conexión pasiva el cliente inicia la conexión de control desde el puerto **N** al puerto 21 del servidor FTP. Después, el cliente envía un **comando passv**. El servidor entonces envía al cliente uno de sus números  de puerto **M**. Entonces el cliente inicia la conexión de datos desde su puerto **P** a al puerto **M** del servidor FTP.

**fuente**: [https://www.thesecuritybuddy.com/vulnerabilities/what-is-ftp-bounce-attack/](https://www.thesecuritybuddy.com/vulnerabilities/what-is-ftp-bounce-attack/)

## Enumeración

### Banner Grabbing

```bash
nc -vn <IP> 21
openssl s_client -connect crossfit.htb:21 -starttls ftp #Get certificate if any
```

### Conectar a un servicio FTPS

Recordemos que el servicio FTPS utiliza SSL-TLS

```
lftp
lftp :~> set ftp:ssl-force true
lftp :~> set ssl:verify-certificate no
lftp :~> connect 10.10.10.208
lftp 10.10.10.208:~> login                       
Usage: login <user|URL> [<pass>]
lftp 10.10.10.208:~> login username Password
```

### Enumeración sin autorización

Para esta enumeración se puede utilizar nmap con sus scripts por defecto:

```
nmap -sC -sV -p21 -A <IP>
o
nmap --script="*ftp*" -p21 <IP>
```

Se pueden utilizar los comandos HELP, FEAT y STAT para obtener información sobre el servidor FTP.

```
HELP
-- Muestra los comandos que se pueden utilizar --

214-The following commands are recognized (* =>'s unimplemented):
214-CWD     XCWD    CDUP    XCUP    SMNT*   QUIT    PORT    PASV    
214-EPRT    EPSV    ALLO*   RNFR    RNTO    DELE    MDTM    RMD     
214-XRMD    MKD     XMKD    PWD     XPWD    SIZE    SYST    HELP    
214-NOOP    FEAT    OPTS    AUTH    CCC*    CONF*   ENC*    MIC*    
214-PBSZ    PROT    TYPE    STRU    MODE    RETR    STOR    STOU    
214-APPE    REST    ABOR    USER    PASS    ACCT*   REIN*   LIST    
214-NLST    STAT    SITE    MLSD    MLST    
214 Direct comments to root@drei.work

FEAT
-- Muestra las características disponibles --

211-Features:
 PROT
 CCC
 PBSZ
 AUTH TLS
 MFF modify;UNIX.group;UNIX.mode;
 REST STREAM
 MLST modify*;perm*;size*;type*;unique*;UNIX.group*;UNIX.mode*;UNIX.owner*;
 UTF8
 EPRT
 EPSV
 LANG en-US
 MDTM
 SSCN
 TVFS
 MFMT
 SIZE
211 End

STAT
-- Información concreta acerca del servidor FTP (versión, configuración, status) --
```

### Acceso anónimo

Los credenciales para acceso anónimo pueden ser:

* anonymous:anonymous
* anonymous:
* ftp:ftp

### Fuerza Bruta

Podemos encontrar una lista con credenciales por defecto aquí:

{% embed url="https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt" %}

## CONEXIÓN A TRAVÉS DE NAVEGADOR

Puedes conectarte a un servicio ftp a través del navegador con la siguiente URL:

```http
ftp://anonymous:anonymous@<IP>
```

Si la aplicación web está mandando **datos controlados por el usuario directamente al servidor FTP** (datos sin comprobar y sin depurar), puedes enviar en doble codificaciones URL los bytes `%0d%0a` (en doble codificación esto es `%250d%250a`) y conseguir que el **servidor FTP se comporte de manera arbitraria**.

Una de estas acciones arbitrarias es por ejemplo **descargar contenido del servidor**, realizar un **escaneo de puertos** o **intentar hablar con otros servicios** que se comunican en texto plano como por ejemplo http.

### Descargar todos los archivos de una FTP

```
wget -m ftp://anonymous:anonymous@<IP>
wget -m --no-passive ftp://anonymous:anonymous@<IP>
```

## ALGUNOS COMANDOS FTP

* **USER** username
* **PASS** password
* **HELP** El servidor indica qué comandos soporta.
* **PORT 127,0,0,1,0,80** Esto indica al servidor FTP que establezca una conexión con la IP 127.0.0.1 por el puerto 80. Se debe poner el 5 puesto como 0 para poner en el 6 el puerto en decimal o utilizar los dos para inidicar el número de puerto en hexadecimal.
* **EPRT |2|127.0.0.1|80|** Este comando indica al servidor FTP que establezca una conexión TCP (2) con la IP 127.0.0.1 por el puerto 80. Este comando soporta IPv6.
* **LIST** Lista los archivos en la carpeta actual.
  * **LIST -R**  Lista de manera recursiva (si lo permite el servidor)&#x20;
* **APPE /path/something.txt** Esto indica al servidor que almacene los datos recibidos de una conexón pasiva o de una PORT-EPRT en un archivo. Si el archivo ya existe, lo anexa al mismo.&#x20;
* **STOR /path/something.txt** Como APPE pero sobreescribe el archivo.
* **STOU /path/something.txt** Como APPE pero si existe no hace nada.
* **RETR /path/to/file** Si se realiza una conexión pasiva o por puerto, el servidor FTP mandará dicho archivo por la conexión.
* **REST 6** Le dice al servidor que la próxima vez que envíe algo con RETR, debe empezar por el 6 byte.
* **TYPE i** Fija la transferencia en modo binario.&#x20;
* **PASV**  Esto abre una conexioón pasiva e indica al usuario donde debe conectarse.
* **PUT /tmp/file.txt** Sube un archivo al servidor FTP.

## EXPLOTACIÓN

### Análisis de puertos a través de FTP

{% content-ref url="ftp-bounce-attack-escaneo.md" %}
[ftp-bounce-attack-escaneo.md](ftp-bounce-attack-escaneo.md)
{% endcontent-ref %}

### Descargar una archivo de una segunda FTP

{% content-ref url="ftp-bounce-attack-descarga-de-otra-ftp.md" %}
[ftp-bounce-attack-descarga-de-otra-ftp.md](ftp-bounce-attack-descarga-de-otra-ftp.md)
{% endcontent-ref %}

### Vulnerabilidad del servidor FileZilla&#x20;

El servidor FTP FileZilla suele abrir el **puerto 14147** para un **servicio de administración** de FileZilla-Server. Si puedes crear un túnel de tu máquina para acceder a este puerto, te puedes conectar con una **contraseña en blanco** y **crear un nuevo usuario para el servicio FTP**.

## POST-EXPLOTACIÓN

Los archivos de configuración de FTP son:

```
ftpusers
ftp.conf
proftpd.conf
vsftpd.conf
```

En el archivo vsftpd.conf se pueden activar algunas configuraciones muy peligrosas:

* **anonymous\_enable=YES**
* **anon\_upload\_enable=YES**
* **anon\_mkdir\_write\_enable=YES**
* **anon\_root=/home/username/ftp -** Directory for anonymous**.**
* **chown\_uploads=YES -** Change ownership of anonymously uploaded files
* **chown\_username=username -** User who is given ownership of anonymously uploaded files
* **local\_enable=YES -** Enable local users to login
* **no\_anon\_password=YES -** Do not ask anonymous for password
* **write\_enable=YES -** Allow commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE
