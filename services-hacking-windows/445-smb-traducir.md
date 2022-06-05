---
description: Protocolo SMB y como enumerarlo
---

# 445 - SMB

{% hint style="info" %}
Este artículo es una traducción de un artículo de Hacking Articles sobre SMB Pentesting
{% endhint %}

{% embed url="https://www.hackingarticles.in/smb-penetration-testing-port-445/" %}

## PROTOCOLO SMB

### **Introducción al protocolo SMB**

**Server Message Block (SMB)** es el nuevo "dialecto" de lo que antes era conocido como **Common Internet File System**. Opera en la capa de aplicación como protocolo de red para compartir archivos que permite a las aplicaciones en un equipo leer y escribir archivos así como solicitar servicios de un servidor en la red. El protocolo SMB puede ser utilizado sobre  protocolo TCP/IP u otros protocolos de red.&#x20;

Utilizando SMB, una aplicación (o el usuario de la aplicación) puede acceder recursos en el servidor remoto. Esto permite a las aplicaciones leer, crear y actualizar archivos en el servidor remoto. Tambien puede comunicarse con todas las aplicaciones del lado del servidor que esten configuradas para recibir solicitudes SMB.&#x20;

### **Funcionamiento de SMB**

SMB funciona como protocolo de solicitud-respuest o cliente servidor. El único momento en que no actua de esta manera es cuando un cliente solicita un "opportunistic lock" (oplock) y el servidor tiene que romper un oplock existente.

Los clientes que se conectan utilizando el protocolo SMB lo hacen a un servidor a través de NetBIOS sobre TCP/IP, IPX/SPX o NetBUI. Una vez la conexión es establecida, El cliente puede acceder a los recursos compartidos de la misma manera que si se encontraran en su sistema de archivos local.

### **Versions of Windows SMB**

* **CIFS**: La versión antigua de SMB que fue incluida con Win NT 4.0 en 1996.
* **SMB 1.0 / SMB1**: Version utilizada en Windows 2000, Windows XP, Windows Server 2003 y Windows Server 2003 R2.
* **SMB 2.0 / SMB2**: Versión utilizada en Windows Vista y Windows Server 2008.
* **SMB 2.1 / SMB2.1**: Versión utilizade en Windows 7 y Windows Server 2008 R2.
* **SMB 3.0 / SMB3**: Versión utilizada en Windows 8 y Windows Server 2012.
* **SMB 3.02 / SMB3**: Versión utilizada en Windows 8.1 y Windows Server 2012 R2.
* **SMB 3.1**: Versión utilizada en Windows Server 2016 y Windows 10.

Actualmente la última versión de SMB es la 3.1.1 que fue introducida en Windows 10 y Windows Server 2016. Esta versión soporta encriptado AES 128 GCM además de AES 128 CCM que fue incluido en SMB 3. Además implementa control de integridad previo a la autenticación utilizando hash SHA-512.&#x20;

SMB 3.1.1 obliga a utilizar conexión segura cuando se conecta con clientes SMB 2.x y posteriores.

### **Seguridad del protocolo SMB**

El protocolo SMB soporta dos niveles de seguridad:

El primero es el **nivel del recurso compartido**.  El servidor está protegido a este nivel y cada recurso compartido tiene una contraseña. El cliente tiene que introducir la contraseña para acceder a los datos guardados en determinados recursos compartidos.

La protección a niver de usuario o cliente se añadió más tarde al protocolo. Esta se aplica a archivos concretos y cada recurso compartido está atado a unos privilegios de acceso diferentes. Una vez el servidor autentica al usuario, éste recibe una identificación única (UID) que se debe presentar al acceder al servidor. Esta seguridad individual existe desde que se implementó LAN Manager 1.0.

## **ENUMERACIÓN SMB**

Para identificar esta información en sistemas Windows o Linux (Samba), el hacker busca enumerar el servicio SMB. Podemos obtener la siguiente información:

* &#x20;**Banner Grabbing**
* **RID cycling**
* **Listado de Usuarios.**
* **Listado de información acerca de miembros de grupos.**
* **Enumeración de recursos compartidos.**
* **Detectar si el equipo pertenece a un Workgroup o a un Dominio.**
* **Identificación de sistema operativo remota.**
* **Recuperación de la política de contraseñas.**

Aquí puede observar como podemos utilizar **nmap** para enumerar SMB\
`nmap -p 445 -A 192.168.1.101`

![](<../.gitbook/assets/imagen (11).png>)

As a result, we enumerated the following information about the target machine:

```
Operating System: Windows 7 ultimate  
Computer Name & NetBIOS Name: Raj  
SMB security mode: SMB 2.02
```

Hay muchas herramientas y scripts que automatizan la enumeración de SMB. En este artículo presentamos las más importantes.

### Hostname

Empezamos la enumeración de SMB encontrando el **Hostname** de la máquina objetivo. Para eso podemos utilizar varias herramientas:

#### **nmblookup**

La primera herramienta es nmblookup. Esta herramienta está desarrollada para hacer uso de busquedas de nombres de NetBIOS y luego mapearlas con las direcciones IP de la red. Las opciones permiten que estas busquedas sean dirigidas sobre una única máquina  o sobre una red. Todas las busquedas se realizan por UDP.

Para nombres únicos:

* 00: Workstation Service (workstation name)
* 03: Windows Messenger service
* 06: Remote Access Service
* 20: File Service (also called Host Record)
* 21: Remote Access Service client
* 1B: Domain Master Browser – Primary Domain Controller for a domain
* 1D: Master Browser

Para nombres de grupos:

* 00: Workstation Service (workgroup/domain name)
* 1C: Domain Controllers for a domain
* 1E: Browser Service Elections

`nmblookup -A 192.168.1.17`

#### **nbtscan**

**NBTscan** es otro programa que escanea las IPs de la red en busca de nombres NetBIOS. Envia una "NetBIOS status query" a cada una de las direcciones en el rango y lista la información recibida en formato "human-readable" es decir, entendible por humanos.&#x20;

Para cada respuesta lista:

* dirección IP
* Nombre NetBIOS
* nombre del usuario logueado
* dirección MAC

**nbstat NSE Script**

Este script de **Nmap** permite recopilar información del nombre NetBIOS del objetivo y su dirección MAC. Por defecto, el script muestra el nombre del equipo y el usuario logueado. Si activamos la verbosidad, muestra tambien todos los nombres que cree que posee el equipo. Tambien muestra las flags de nmblookup.

`nmap --script nbstat.nse 192.168.1.17`

#### **nbtstat** (windows)

Este comando para Windows muestralas estadísticas del protocolo NetBIOS sobre TCP/IP (NetBT). Puede leer la tabla de nombres NetBIOS de equipos locales y remotos. Tambien puede leer la cache de nombres NetBIOS.

Este comando permite que se actualice la caché de nombres de NetBIOS y de los nombres registrados con "Windows Internet Name Service" (WINS).

Si lo utilizamos sin parametros, muestra la información de ayuda. Solo funciona si está instalado el protocolo TCP/IP como componente.

#### **Ping**

Tambien podemos utilizar el comando ping para detectar el hostname de una maquina o servidor SMB. el parámetro **-a** especifica la resolución reversa de nombre en la IP de destino. Si funciona, ping muestra el hostname.

#### **smb-os-discovery NSE Script**

Este script NSE para Nmap trata de determinar el sistema operativo, nombre del equipo, dominio, workgroup y hora actual a través del protocolo SMB (puertos 445 o 139). Lo consigue conectandose con una sesión anónima (si lo permite el servidor) o con una cuenta de usuario si se la suministramos.

En respuesta a un inicio de sesión el servidor enviará toda esta información.

Los siguientes campos son los que se incluyen en el output:

* Sistema Operativo.
* Nombre del equipo.
* Nombre del Dominio.
* Nombre del Bosque.
* FQDN
* NetBIOS Hostname
* NetBIOS Domain name
* Workgroup
* Hora del sistema.

`nmap --script smb-os-discovery 192.168.1.17`

### Recursos compartidos y sesion nula

Como ya vimos antes, SMB se encarga de compartir archivos y recursos. Con el objetivo de transferir estos archivos o recursos, existen los recursos compartidos. Existen recurso compartidos públicos que son accesibles por todos los miembros de una red y los recursos compartidos específicos que solo pueden acceder algunos usuarios.&#x20;

Vamos a enumerar estos recursos compartidos.

**SMBMap**

Permite enumerar recursos compartidos por SMB o Samba a lo largo de todo el dominio. Lista los recursos compartidos, los permisos de acceso, las funcionalidades de subida y descarga de archivos e incluso puede ejecutar comandos remotos.

Esta herramienta se centra en el pentesting y trata de simplificar la busqueda de datos potencialmente sensibles a lo largo de grandes redes.

Vamos a enumerar los recursos compartidos específicos de un usuario (raj) utilizando sus credenciales:

`smbmap -H 192.168.1.17 -u raj -p 123`

**smbclient**

Es un cliente de Samba con una interfaz parecida a la de una FTP. Es una herramienta muy util para comprobar la conectividad con un recurso compartidod de Windows. Puede utilizarse para transferir archivos y para ver los nombres de los recursos compartidos. Además, tiene la capacidad de generar un backup (con tar) de todos los datos y restaurarlos de un servidor a un cliente y viceversa.

En este ejemplo enumeramos la máquina objetivo y encontramos los recursos compartidos directamente con smbclient. Despues nos conectamos al equipo y descargamos el archivo que hemos encontrado.

`smbclient -L 192.168.1.40`  \
`smbclient //192.168.1.40/guest`  \
`get file.txt`

![](<../.gitbook/assets/imagen (18).png>)

Ahora enumeramos un recurso compartido especifico del usuario raj (como hemos hecho antes). Nos conectamos a SMB como el usuario raj y encontramos un recurso compartido con el nombre de "share". Reconfiguramos nuestro comando para poder acceder a share y ahora podemos descargar el archivo que hemos encontrado.

`smbclient -L 192.168.1.17 -U raj%123`  \
`smbclient //192.168.1.17/share -U raj%123`  \
`get raj.txt`

![](<../.gitbook/assets/imagen (50).png>)

**smb-enum-shares NSE Script**

Este Script NSE de Nmap permite listar los recursos compartidos utilizando la función de MSRPC **srvsvc.NetShareEnumAll** y obtiene más información sobre ellos utilizando **srvsvc.NetShareGetInfo.** Si el acceso a estas funciones es denegado, se comprueba un listado de nombres habituales de recursos compartidos. NetShareGetInfo requiere de permisos de administrador si UAC esta apagado.&#x20;

Incluso si no tenemos permisos para utilizar ninguna de estas funciones, intentar conectarse a un recurso compartido revelará siempre su existencia. Una vez comprobada la lista de nombres habituales, el script coge la lista de recursos compartidos que existen y trata de autenticarse como anónimo. Finalmente las divide entre restringidas y anonimas.

`nmap --script smb-enum-shares -p139,445 192.168.1.17`

**Net view** (windows)

Muestra una lista de dominios, equipos y recursos que estan siendo compartidos por el equipo especificado. Sin parámetros muestra la lista de equipos del dominio.

En este caso, en una máquina Windows, utilizamos el parámetro /All para listar todos los recursos compartidos a los que tiene acceso la víctima.

`net view \\192.168.1.17 /All`

**CrackMapExec** (postexploitation)

CrackMapExec (tambien conocido como CME)  es una herramienta de postexplotación que permite automatizar el analisis de grandes redes de Active Directory.

Desarrollado con la intención de ser sigiloso, CME sigue el concepto de "vivir de la tierra" (Living off the Land en inglés) aprovechandose de características y protocolos que forman parte de Active Directory para conseguir funcionar y así evitando la mayor parte de protecciones.

CrackMapExec puede:

* Mapear los equipos de una red.
* Generar una lista de repetidores.&#x20;
* Enumerar recursos compartidos.&#x20;
* Acceder a ellos.
* Enumerar sesiones activas.
* Enumerar discos.
* Enumerar usuarios logueados.
* Enumerar usuarios del dominio.
* Enumerar usuarios por fuerza bruta del RID.
* Enumerar grupos del dominio.
* Enumerar grupos locales.
* Etc...

`crackmapexec smb 192.168.1.17 -u 'raj' -p '123' --shares`

### Usuarios



#### **Impacket: Lookupsid**

Un identificador de seguridad (SID) es un valor único de longitud variable que es utilizado para identificar cuentas de usuario.

A través de la enumeración SID podemos extraer información acerca de qué usuarios existen y sus datos.

**impacket-lookupsid** puede enumerar usuarios a nivel local y a nivel de dominio. Tambien existe un módulo de metasploit para esto.

Si estás pensando en injectar un **silver** o **golden** **ticket** en un servidor, entonces una de las cosas que necesitas es el SID del usuario 500. En este escenario podemos utilizar la herramienta.

**Lookupsid** requiere la siguiente información:

* Dominio.
* Nombre de usuario.
* Contraseña o Hash NTLM.
* Dirección IP objetivo.

`python3 lookupsid.py DESKTOP-ATNONJ9/raj:123@192.168.1.17`

### Vulnerabilidades

#### **smb-vuln NSE Script**

Nmap solía tener un script NSE que se llamaba **smb-check-vulns** que comprobaba varias vulnerabilidades en el servidor como son:

* conficker
* cve2009-3103
* ms06-025
* ms07-029
* regsvc-dos
* ms08-067

Después se dividió este script en varios comprobadores de vulnerabilidades que funcionan individualmente, los **smb-vuln-\***.

`nmap --script smb-vuln* 192.168.1.16`

![](<../.gitbook/assets/imagen (59).png>)

{% hint style="danger" %}
El artículo original incluye un apartado de explotación y otro de post-explotación en el que utiliza metasploit para explotar el servicio SMB.
{% endhint %}

## ENUMERACIÓN COMPLETA

### Enum4linux

Es una herramienta diseñada para detectar, extraer y enumerar datos de sistemas Windows y Linux, incluyendo la información obtenida a través de SMB. Enum4Linux puede descubrir  lo siguiente:

* Dominios y Miembros de grupos.
* Listado de usuarios.
* Recursos compartidos en un equipo.
* Politicas de contraseñas en el objetivo.
* Sistema operativo del objetivo.

Iniciamos un escaneo normal utilizando Enum4Linux y extraemos el rango RID, nombres de usuarios, workgroup, información de NetBIOS (con nbtstat), sesiones, SIDs e información del Sistema Operativo.

is a tool that is designed to detecting and extracting data or enumerate from Windows and Linux operating systems, including SMB hosts those are on a network. Enum4linux is can discover the following:\
• Domain and group membership\
• User listings\
• Shares on a device (drives and folders)\
• Password policies on a target\
• The operating system of a remote target

We start to normal scan using enum4linux. It extracts the RID Range, Usernames, Workgroup, Nbtstat Information, Sessions, SID Information, OS Information.

## REFERENCIAS

[https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/](https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/)
