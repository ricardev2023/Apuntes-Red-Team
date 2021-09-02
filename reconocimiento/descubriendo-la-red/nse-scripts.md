---
description: Explicación y uso de Nmap Scripting Engine
---

# NMAP NSE

## NMAP SCRIPTING ENGINE

La verdadera potencia de Nmap viene con el **NSE** , ya que se permite a los usuarios escribir \(y compartir\) scripts simples para automatizar una amplia variedad de tareas de red. Los scripts estan escritos en lenguaje **LUA**.

Podemos encontrar los scripts en:

 **`/usr/share/nmap/scripts`**

 Podemos encontrar los diccionarios en:

 **`/usr/share/nmap/nselib/data`**

###  **Categorias NSE**

* **Auth**: autenticacion de usuarios.
* **Broadcast**: Broadcast para obtener informacion.
* **Brute**: Auditar passwords con fuerza bruta.
* **Default**: Script por defecto de escaner \(-sC los ejecuta\).
* **Discovery**: Descubrir hosts y servicios.
* **Dos**: ataque DoS.
* **Exploit**: explotar vulnerabilidades.
* **External**: Scripts de terceros.
* **Fuzzer**: ejecutar fuzzing \(parecido a dirb\).
* **Intrusive**: Generar ruido en la red para generar fallo o error.
* **Malware**: Deteccion de malware.
* **Safe**: Scripts seguros.
* **Version**: Deteccion avanzada de versiones.
* **Vuln**: Detectar y explotar vulnerabilidades.

###  **Comandos NSE**

**`nmap -sC`** → aplica los scripts de la categoria Default.

**`nmap --script filename|category|directory|expression,...`** → Es decir, aplica algunos de los scripts. como por ejemplo:

**`nmap --script discovery,vuln`** → Aplica los scripts de las categorias marcadas.

**`nmap --script “not (exploit or dos or intrusive)”`** → Aplica todos los scripts menos los marcados.

**`nmap --script “auth and Broadcast”`** → Aplica solo los scripts marcados, como la coma

**`nmap --script snmp-*`** → Aplica todos los scripts del protocolo SNMP \(hay que tener en cuenta que hay que incluir argumentos\)

**`nmap --script "snmp-* and not (snmp-enum or snmp-login)`** → Igual que el anterior pero sin esos dos scripts.

**`nmap --script http-tittle --script-args http.useragent="Mozilla 66"`** → Incluye argumentos al script, los arg necesarios se ven en el codigo fuente del script\).

**`nmap --script http-tittle --script-args-file argumetentos.txt`** → Se pueden incluir argumentos en un archivo de texto, uno por linea

**`nmap --script nbstat --script-trace`** → muestra el trafico que se genera con un determinado script para depurar los argumentos.

**`nmap --script nbstat -d[1-9]`** → muestra el trafico tambien \(cuanto mas alto el numero 1-9 mas detalle en el resultado.

###  **Output de NMAP**

 **-oG** → formato grepeable.

 **-oS** → formato script kiddie \(formato de broma cambiando letras como escriben los Hackers de película\)

 **-oN** → formato normal \(igual que la salida en terminal\).

 **-oX** → formato .xml para navegador web \(es muy practico por que es importable a muchas otras aplicaciones\).

Con **ndiff** Podemos comparar dos outputs de nmap y no dice la diferencia en estado de puertos, servicios, hosts, etc...

###  **Descubrimiento de Hosts**

 **`nmap -sL --script=targets-sniffer -e wlan0`**

Esto permite descubrir hosts en una red o incluso en las redes con las que tiene relacion. Es muy util a la hora de pivotar entre redes.

###  **Fuerza Bruta**

 **`nmap -p3306 --script mysql-brute`**

 Asi podemos hacer fuerza bruta al servicio mysql \(es util pero existen aplicaciones específicas dedicadas a esto\)

 **`nmap -p445 --script smb-brute --datadir=/root/diccionario.txt`** → Selecciona el diccionario que quieras.

## REFERENCIAS

{% embed url="https://nmap.org/book/nse.html" %}



