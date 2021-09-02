---
description: Comandos útiles a la hora de descubrir hosts y puertos en una red.
---

# DESCUBRIENDO LA RED

## OBTENER IP

Para obtener la direccion IP del objetivo utilizamos **netdiscover** o **nbtscan**:

`netdiscover -r 192.168.1.1/24 #Para analizar una red completa`  
`netdiscover -i wlan0 → Para analizar en base a una interfaz de red`

`nbtscan -r 192.168.1.0/24`

## ESCANEO DE PUERTOS

### NMAP

Procedimiento que utilizo para enumeración de puertos abiertos.

**1. Analizo todos los puertos TCP sin comprobar si el host está arriba ni resolucion DNS solo sS \(SYN scan\). Ademas verbose para ir viendo los avances. por utlimo el output lo hago en formato grepeable.**  
 `nmap -p- -sS --open -min-rate 5000 -Pn -n -vvv [IP] -oG allPorts`

{% hint style="info" %}
El formato grepeable arriba nos permite extraer los puertos con script parecido a este:

\(escrito por S4vitar\)
{% endhint %}

```bash
extractPorts () {
	ports="$(cat $1 | grep -oP '\d{1,5}/open' | awk '{print $1}' FS='/' | 
	xargs | tr ' ' ',')" 
	ip_address="$(cat $1 | grep -oP '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}' | 
	sort -u | head -n 1)" 
	echo -e "\n[*] Extracting information...\n" > extractPorts.tmp
	echo -e "\t[*] IP Address: $ip_address" >> extractPorts.tmp
	echo -e "\t[*] Open ports: $ports\n" >> extractPorts.tmp
	echo $ports | tr -d '\n' | xclip -sel clip
	echo -e "[*] Ports copied to clipboard\n" >> extractPorts.tmp
	cat extractPorts.tmp
	rm extractPorts.tmp
```

**2. Analizo los puertos abiertos de la anterior con -sC y -sV para sacar servicios y versiones.**

`nmap -p[puertos abiertos] -sC -sV [IP] -n -Pn -oN targeted`

**3. Analizo por UDP si no encuentro nada relevante en TCP.**

`nmap -p- -sU --open -min-rate 10000 -Pn -n -vvv [IP] -oG UDP_allPorts`

#### **OTRAS OPCIONES**

**Services scan \(-sV\) using default safe scripts \(-sC\) with O.S. detection \(-A\) on all 65535 ports with default output file \(-oN\)**

`nmap -p- -sC -sV -A -oN /ruta/nmap.txt 192.168.0.29`

**TCP scan \(-sT\) on all ports \(-p\) with default output file \(-oN\)**

`nmap -p- -sT -oN /home/ricardo/Documentos/maquinas_virtuales/Android4/tcp.txt 192.168.0.29`

**UDP scan \(-sU\) with default output file \(-oN\)**

`sudo nmap -sU -oN /home/ricardo/Documentos/maquinas_virtuales/Android4/udp.txt 192.168.0.29`

**Syn scan \(-sS\) no DNS resolution \(-n\) skip host discovery \(-Pn\) source port 53 \(-g\) port 80,8080,443,139,445 \(-p\) show only open ports** **\(--open\)** **Services scan \(-sV\) using default safe scripts \(-sC\)**

`nmap -sS -n -Pn -g 53 -T4 -p80,8080,443,139,445 --open -sV -sC 172.18.1-20.1-254`

**Syn scan \(-sS\) ‘A' mode \(OS, version, -sC, traceroute\) skip host discovery \(-Pn\) all ports \(-p-\) custom script scan \(--script\) maximum connection retries 0**

`nmap -sS -A -Pn -p- --script=http-title [target].com --max-retries 0`

**Script scan, service version, verbose x2, output all, custom report stylesheet, quick tcp scan**

`nmap -sC -sV -vv -oA /ruta/custom --stylesheet https://raw.githubusercontent.com/honze-net/nmap-bootstrap-xsl/master/nmap-bootstrap.xsl [10.10.10.10]`

**Udp scan, service version, verbose x2, output all, quick udp scan**

`nmap -sU -sV -vv -oA /ruta/output [10.10.10.10]`

**Find nmap scripts**

`find /usr/share/nmap/scripts -type f | grep smb`

### **MASSCAN**

Es un escaner que sirve solo para comprobar si los puertos estan abiertos o cerrados. No sirve para ver detalles de los servicios.

`masscan --nmap [IP]`

`--nmap` → compatibilidad con nmap  
`-p` → seleccion de puertos. Si quiero alguno de UDP utilizo U:52.  
`--rate` → 150000 normalmente \(es la velocidad\)  
`--open-only` → solo abiertos

