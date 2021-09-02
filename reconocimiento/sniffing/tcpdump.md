---
description: Sniffer de red para Linux de linea de comandos.
---

# TCPDUMP

**TCPDump** Es un sniffer por linea de comandos que se encuentra preinstalado en la mayoria de los LINUX.

**`tcpdump -n -s 1500 -i wlan0 -vvv -X -w archivo.pcap`**

 **-n** → no resuelve los DNS. es mucho mas rapido

 **-i** → marca la interfaz de red.

 **-s** → marca los bytes capturados por paquete. Por defecto 64 bytes, que equivale a las cabeceras \(La mtu es la cantidad maxima de bytes por paquete\).

 En Linux obtengo la mtu con **`ifconfig`**.  
 En Windows obtengo la mtu con **`interface ipv4 show subinterfaces`**.

 **-v** → Verbose, como siempre.

 **-w** → Para escribir el dump en un archivo. Lo normal es formato .pcap

 **-X** → Para modo ASCII

 **-r** → Para leer un archivo .pcap Se pueden utilizar una serie de filtros.

 **tcp/udp/ip/arp/ether/...** → filtra por protocolo.

 **port 445** → solo me lee por el puerto marcado.

 **host 10.10.10.10** → solo lee los paquetes que entran o salen del host.

 **net 192.168.10.0/24** → solo lee los paquetes que entran o salen de esa red.

 **src 8.8.8.8** → solo lee los paquetes que salen de ese host \(source\).

 **dst 8.8.8.8** → solo lee los paquetes que entran en ese host \(destination\).

 **and / or** → para concatenar diferentes filtros.

