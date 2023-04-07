# Table of contents

* [Presentación](README.md)
* [Apuntes Linux](https://ricardev.gitbook.io/apuntes-linux/)
* [Apuntes Blue Team](https://ricardev.gitbook.io/blue-team/)
* [Apuntes Python](https://ricardev.gitbook.io/apuntes-python/)
* [Ricardev github](https://github.com/ricardev2023)

## RECON <a href="#reconocimiento" id="reconocimiento"></a>

* [OSINT](reconocimiento/osint.md)
* [DESCUBRIENDO LA RED](reconocimiento/descubriendo-la-red/README.md)
  * [NMAP NSE](reconocimiento/descubriendo-la-red/nse-scripts.md)
* [SNIFFING](reconocimiento/sniffing/README.md)
  * [TCPDUMP](reconocimiento/sniffing/tcpdump.md)

## TECHNIQUES

* [FUERZA BRUTA](techniques/fuerza-bruta/README.md)
  * [HYDRA](techniques/fuerza-bruta/untitled.md)
  * [MEDUSA](techniques/fuerza-bruta/untitled-1.md)
  * [JOHN THE RIPPER](techniques/fuerza-bruta/untitled-2.md)
  * [NCRACK](techniques/fuerza-bruta/untitled-3.md)
  * [RAINBOW TABLES](techniques/fuerza-bruta/untitled-4.md)
  * [CHEATSHEET](techniques/fuerza-bruta/cheatsheet.md)
* [CLAVES RSA DÉBILES](techniques/claves-rsa-debiles.md)

## WEB HACKING

* [FUZZING](web-hacking/fuzzing-1/README.md)
  * [GOBUSTER](web-hacking/fuzzing-1/gobuster.md)
  * [WFUZZ](web-hacking/fuzzing-1/fuzzing.md)
  * [OTRAS HERRAMIENTAS DE RECONOCIMIENTO WEB](web-hacking/fuzzing-1/otras-herramientas-de-reconocimiento-web.md)
* [OWASP TOP 10](web-hacking/untitled/README.md)
  * [A1-2017. SQL INJECTION](web-hacking/untitled/sql-injection/README.md)
    * [LOGIN FORM BYPASS](web-hacking/untitled/sql-injection/untitled.md)
    * [EXTRACCIÓN DE INFORMACIÓN.](web-hacking/untitled/sql-injection/extraccion-de-informacion..md)
    * [SQLI MODIFIED HEADERS](web-hacking/untitled/sql-injection/untitled-1.md)
    * [BOOLEAN BLIND SQLI](web-hacking/untitled/sql-injection/untitled-2.md)
    * [TIME-BASED BLIND SQLI](web-hacking/untitled/sql-injection/untitled-3.md)
    * [AUTOMATIC INJECTION](web-hacking/untitled/sql-injection/untitled-4.md)
  * [A2-2017. ATAQUES A SISTEMAS DE AUTENTICACIÓN](web-hacking/untitled/a2-2017.-ataques-a-sistemas-de-autenticacion.md)
  * [A3-2017 - EXPOSICIÓN DE DATOS SENSIBLES](web-hacking/untitled/a3-2017-exposicion-de-datos-sensibles.md)
  * [A4-2017. XXE](web-hacking/untitled/a4-2017.-xxe.md)
  * [A5-2017. CONTROL DE ACCESO VULNERABLE](web-hacking/untitled/untitled.md)
  * [A6-2017. SEGURIDAD MAL CONFIGURADA](web-hacking/untitled/a6-2017.-seguridad-mal-configurada.md)
  * [A7-2017. XSS](web-hacking/untitled/untitled-1.md)
  * [A8-2017. DESERIALIZACIÓN INSEGURA](web-hacking/untitled/a8-2017.-deserializacion-insegura.md)
  * [A9-2017. USO DE COMPONENTES CON VULNERABILIDADES CONOCIDAS](web-hacking/untitled/untitled-2.md)
  * [A10-2017. REGISTRO Y MONITOREO INSUFICIENTE](web-hacking/untitled/untitled-3.md)
* [SERVICIOS WEB](web-hacking/servicios-web/README.md)
  * [APACHE TOMCAT (RCE)](web-hacking/servicios-web/apache-tomcat-rce.md)
  * [PRTG NETWORK MONITOR (RCE)](web-hacking/servicios-web/prtg-network-monitor-rce.md)

## SERVICES HACKING (BOTH)

* [20,21 - FTP](services-hacking-both/20-21-ftp/README.md)
  * [FTP BOUNCE ATTACK - ESCANEO](services-hacking-both/20-21-ftp/ftp-bounce-attack-escaneo.md)
  * [FTP BOUNCE ATTACK- DESCARGA DE OTRA FTP](services-hacking-both/20-21-ftp/ftp-bounce-attack-descarga-de-otra-ftp.md)
* [23 - TELNET](services-hacking-both/23-telnet.md)
* [25, 465 587 - SMTP](services-hacking-linux/25-465-587-smtp.md)
* [111, 2049 - RPCBIND Y NFS](services-hacking-linux/111-2049-rpcbind-y-nfs.md)
* [161,162,10161,10162/udp - SNMP](services-hacking-both/161-162-10161-10162-udp-snmp/README.md)
  * [SNMP (RCE Linux)](services-hacking-both/161-162-10161-10162-udp-snmp/snmp-rce-linux.md)
* [445 - SMB](services-hacking-both/445-smb-traducir/README.md)
  * [ETERNALBLUE](services-hacking-both/445-smb-traducir/eternalblue.md)
* [3306 - MYSQL](services-hacking-both/3306-mysql.md)

## SERVICES HACKING (LINUX)

* [3632 - DISTCCD](services-hacking-linux/untitled.md)

## SERVICES HACKING (WINDOWS)

* [135, 539 - MSRPC](services-hacking-windows/135-539-msrpc.md)
* [389, 636 - LDAP / LDAPS](services-hacking-windows/389-636-ldap-ldaps.md)
* [1443 - MSSQL](services-hacking-windows/1443-mssql.md)

## ACTIVE DIRECTORY HACKING

* [CREANDO UN LABORATORIO DE AD](active-directory-hacking/creando-un-laboratorio-de-ad/README.md)
  * [1. Instalación de Windows Server 2016](active-directory-hacking/creando-un-laboratorio-de-ad/1.-instalacion-de-windows-server-2016.md)
  * [2. ROL DE ACTIVE DIRECTORY](active-directory-hacking/creando-un-laboratorio-de-ad/2.-rol-de-active-directory.md)
  * [3. MALAS PRÁCTICAS NECESARIAS](active-directory-hacking/creando-un-laboratorio-de-ad/3.-misconfiguraciones-importantes.md)
* [CONCEPTOS](active-directory-hacking/conceptos/README.md)
  * [SPN Y KERBEROS](active-directory-hacking/conceptos/conceptos-importantes-antes-de-continuar.md)
* [ENUMERACIÓN](active-directory-hacking/enumeracion/README.md)
  * [BLOODHOUND](active-directory-hacking/enumeracion/bloodhound.md)
* [ATAQUES](active-directory-hacking/ataques/README.md)
  * [SMB RELAY](active-directory-hacking/ataques/smb-relay.md)
  * [NTLM RELAY](active-directory-hacking/ataques/ntlm-relay.md)
  * [KERBEROASTING](active-directory-hacking/ataques/kerberoasting.md)
  * [AS\_REP ROASTING](active-directory-hacking/ataques/as\_rep-roasting.md)

## PRIVESC

* [WINDOWS](privesc/untitled.md)
* [LINUX](privesc/linux/README.md)
  * [LXD/LXC GROUP](privesc/linux/lxd-lxc-group.md)

## EXFILTRACIÓN

* [EXFILTRANDO INFORMACIÓN](exfiltracion/exfiltrando-informacion.md)

## SHELL AND POWERSHELL TRICKS

* [Transfiriendo datos (traducir)](shell-and-powershell-tricks/untitled.md)
* [MEJORANDO SHELL A TTY INTERACTIVA (Traducir)](shell-and-powershell-tricks/mejorando-shell-a-tty-interactiva.md)

## PWN LINUX

* [CREANDO UN LABORATORIO SIN MITIGACIONES](pwn-linux/creando-un-laboratorio-sin-mitigaciones.md)
* [TEORÍA](pwn-linux/teoria/README.md)
  * [ESTRUCTURA DE UN BINARIO DE LINUX](pwn-linux/teoria/estructura-de-un-binario-de-linux/README.md)
    * [HERRAMIENTAS](pwn-linux/teoria/estructura-de-un-binario-de-linux/herramientas.md)
  * [ENSAMBLADOR](pwn-linux/teoria/ensamblador.md)
  * [CONVENCIÓN DE LLAMADAS](pwn-linux/teoria/32-bits-vs-64-bits.md)
  * [MITIGACIONES](pwn-linux/teoria/mitigaciones.md)
  * [SYSCALL Y SHELLCODE](pwn-linux/teoria/syscall-y-shellcode.md)
  * [FORMAT STRING](pwn-linux/teoria/format-string.md)
  * [RETURN-ORIENTED PROGRAMMING](pwn-linux/teoria/return-oriented-programming/README.md)
    * [GADGETS](pwn-linux/teoria/return-oriented-programming/gadgets.md)
* [ESTRATEGIAS DE EXPLOIT](pwn-linux/estrategias-de-exploit/README.md)
  * [STACK EXPLOITS](pwn-linux/estrategias-de-exploit/stack-exploits/README.md)
    * [ATAQUE “SMASH THE STACK” SENCILLO](pwn-linux/estrategias-de-exploit/ataque-smash-the-stack-sencillo.md)
    * [ATAQUE RET2WIN](pwn-linux/estrategias-de-exploit/ataque-smash-the-stack-ii.md)
    * [ATAQUE RET2SHELLCODE](pwn-linux/estrategias-de-exploit/stack-exploits/ataque-smash-the-stack-ii-1.md)
    * [ATAQUE FORMAT STRING RET2SHELLCODE 2 BYTES](pwn-linux/estrategias-de-exploit/stack-exploits/ataque-format-string-ret2shellcode-2-bytes.md)
    * [ATAQUE FORMAT STRING RET2SHELLCODE 4 BYTES](pwn-linux/estrategias-de-exploit/stack-exploits/ataque-format-string-ret2shellcode-4-bytes.md)
    * [CANARY BYPASS](pwn-linux/estrategias-de-exploit/stack-exploits/canary-bypass.md)
    * [ATAQUE RET2LIBC](pwn-linux/estrategias-de-exploit/stack-exploits/ataque-ret2libc.md)
* [PRÁCTICA](pwn-linux/practica/README.md)
  * [PHOENIX](pwn-linux/practica/phoenix/README.md)
    * [SETUP](pwn-linux/practica/phoenix/setup.md)
    * [STACK-ZERO amd64](pwn-linux/practica/phoenix/stack-zero-amd64.md)
    * [STACK-ONE amd64](pwn-linux/practica/phoenix/stack-one-amd64.md)
    * [STACK-TWO amd64](pwn-linux/practica/phoenix/stack-two-amd64.md)
    * [STACK-THREE amd64](pwn-linux/practica/phoenix/stack-three-amd64.md)
    * [STACK-FOUR amd64](pwn-linux/practica/phoenix/stack-four-amd64.md)
    * [STACK-FIVE amd64](pwn-linux/practica/phoenix/stack-five-amd64.md)
    * [STACK-SIX amd64](pwn-linux/practica/phoenix/stack-six-amd64.md)
    * [FORMAT-ZERO amd64](pwn-linux/practica/phoenix/format-zero-amd64.md)
