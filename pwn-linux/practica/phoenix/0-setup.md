---
description: A continuación se explicará como instalar la máquina virtual y ejecutarla.
---

# 0-SETUP

## INSTALAR QEMU

Phoenix se proporciona como una imagen para el software de virtualización qemu. Es básicamente una máquina virtual. Para instalarlo solo tenemos que seguir las instrucciones de la página web oficial:

#### Linux

{% embed url="https://www.qemu.org/download" %}

#### Windows

{% embed url="https://qemu.weilnetz.de/w64" %}

Solo hay que ejecutar el ejecutable y dejarlo todo por defecto.

## EJECUTAR LA IMAGEN

Lo primero que debemos hacer es descargar la imagen de la página de Exploit education:

{% embed url="https://exploit.education/downloads" %}

En este caso utilizaré la imagen **Qcow2 AMD64**.

Para ejecutarla seguimos el siguiente procedimiento:

#### Linux

Extraemos el archivo descargado:

```
tar xJf exploit-education-phoenix-amd64-v1.0.0-alpha-3.tar.xz
```

Ejecutamos el script de inicio:

```
cd exploit-education-phoenix-amd64/
./boot-exploit-education-phoenix-amd64.sh
```

Ahora que la imagen se está ejecutando podemos conectarnos por SSH con **user:user**.

```
ssh -p2222 user@localhost
```

#### Windows

Extraemos el archivo descargado con una aplicación tipo WinRAR.&#x20;

El script de inicio no servirá aquí ya que es para Linux. Por lo tanto, lo vamos a modificar para Powershell (creamos un script de powershell como el siguiente):

```
boot-exploit-education-phoenix-amd64.ps1

\Program` Files\qemu\qemu-system-x86_64.exe `
    -kernel vmlinuz-4.9.0-8-amd64 `
    -initrd initrd.img-4.9.0-8-amd64 `
    -append "root=/dev/vda1" `
    -m 1024M `
    -netdev user,id=unet,hostfwd=tcp:127.0.0.1:2222-:22 `
    -device virtio-net,netdev=unet `
    -drive file=exploit-education-phoenix-amd64.qcow2,if=virtio,format=qcow2,index=0
```

Ahora con clicar con el botón derecho sobre el nuevo script .ps1 seleccionamos "Ejecutar con PowerShell".

Este procedimiento asume que lo hemos instalado con las opciones por defecto, si no, habrá que cambiar la carpeta.

Con la imagen corriendo podemos conectarnos a la máquina con el cliente SSH que más nos guste. (En mi caso PUTTY).
