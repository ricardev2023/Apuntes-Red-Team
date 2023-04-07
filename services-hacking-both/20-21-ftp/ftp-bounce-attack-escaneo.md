---
description: >-
  Explotar el FTP Bounce Attack para escanear puertos a través de una conexión
  FTP
---

# FTP BOUNCE ATTACK - ESCANEO

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ftp/ftp-bounce-attack" %}

## MANUAL

1. Conectar con la FTP vulnerable.
2. Utilizar **PORT** o **EPRT** (solo unos de ellos) para hacer al servidor establecer una conexión con la `<IP:PUERTO>` que se quiere escanear:\
   `PORT 172,32,80,80,0,8080`\
   `EPRT |2|172.32.80.80|8080|`
3. Utilizar **LIST**. Esto mandará a la conexión `<IP:PUERTO>` la lista de archivos en la carpeta actual del FTP y espera respuesta: \
   `150 File status okay` Esto quiere decir que el puerto está **abierto**.\
   `425 No connection established` Esto quiere decir que el puerto está **cerrado**.
4. En lugar de **LIST** se puede utilizar **RETR** /file/in/ftp y esperar una **respuesta similar** a la de LIST.

Ejemplo utilizando **PORT** (Puerto 8080 abierto / Puerto 7777 cerrado):

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Ejemplo con **EPRT** (Puerto 8080 abierto / Puerto 7777 cerrado):

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Ejemplo con **RETR** en vez de LIST:

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

## NMAP

```
nmap -b <name>:<pass>@<ftp_server> <victim>
nmap -Pn -v -p 21,80 -b ftp:ftp@10.2.1.5 127.0.0.1 #Scan ports 21,80 of the FTP
nmap -v -p 21,22,445,80,443 -b ftp:ftp@10.2.1.5 192.168.0.1/24 #Scan the internal network (of the FTP) ports 21,22,445,80,443
```
