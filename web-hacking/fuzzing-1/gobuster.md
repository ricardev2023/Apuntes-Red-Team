---
description: >-
  Otro Fuzzer que tiene como característica principal que nos permite fuzzear
  Subdominos DNS y Hosts virtuales además de directorios.
---

# GOBUSTER

{% hint style="info" %}
Éste artículo está traducido del artículo [**Gobuster for directory, DNS and virtual hosts bruteforcing**](https://erev0s.com/blog/gobuster-directory-dns-and-virtual-hosts-bruteforcing/) de la página web erev0s.
{% endhint %}

En este artículo vamos a explorar el fuzzer llamado GoBuster. El proyecto está disponible en Github.

{% embed url="https://github.com/OJ/gobuster" %}

Vamos a ver algunas de las ventajas que posicionan Gobuster por encima de otras herramientas similares como dirb. Vamos a desarrollar su funcionamiento y por último, dejaré una cheatsheet para los casos de uso más habituales.

## CARACTERÍSTICAS

Hay tres características que ponen Gobuster en el primer lugar de la lista de fuzzers.

* Se ejecuta desde la linea de comandos.
* Permite hacer fuerza bruta a ficheros y a varios tipos de extensiones al mismo tiempo.
* Velocidad.

GoBuster está escrito en Go, esto lo hace muy bueno a la hora de realizar trabajos concurrentes (utilizar hilos para ejecutar acciones simultaneas). Esto desemboca en más velocidad a la hora de realizar fuerza bruta. Además, el hecho de no necesitar especificar si realizamos el ataque sobre directorios o sobre archivos simplifica mucho las tareas de fuzzing.

Tiene tres modos principales de funcionamiento:

* **dir** - El clasico fuzzer de directorios.
* **dns** - Fuzzea subdominios DNS.
* **vhost** - Fuzzea Hosts virtuales, que no es lo mismo que DNS.

La ayuda de GoBuster muestra lo siguiente:

```
[email protected]:~$ gobuster help
Usage:
  gobuster [command]

Available Commands:
  dir         Uses directory/file brutceforcing mode
  dns         Uses DNS subdomain bruteforcing mode
  help        Help about any command
  vhost       Uses VHOST bruteforcing mode

Flags:
  -h, --help              help for gobuster
  -z, --noprogress        Don't display progress
  -o, --output string     Output file to write results to (defaults to stdout)
  -q, --quiet             Don't print the banner and other noise
  -t, --threads int       Number of concurrent threads (default 10)
  -v, --verbose           Verbose output (errors)
  -w, --wordlist string   Path to the wordlist

Use "gobuster [command] --help" for more information about a command.
```

## FUNCIONAMIENTO

Primero hablaremos de los tres argumentos globales más útiles del programa:

1. `-q` permite fozalizar la salida unicamente en lo que interesa.
2. `-t` se utiliza para definir cuantos hilos concurrentes queremos utilizar. El número a utilizar dependerá principalmente de dos factores: Ancho de banda de nuestra conexión a internet y los recursos del servidor al que estamos atacando. Normalmente entre 50 y 100 es un buen número para empezar.
3. `-w` define el diccionario a utilizar.

### DIR mode

```
[email protected]:~$ gobuster dir --help
Uses directory/file brutceforcing mode

Usage:
  gobuster dir [flags]

Flags:
  -f, --addslash                      Apped / to each request
  -c, --cookies string                Cookies to use for the requests
  -e, --expanded                      Expanded mode, print full URLs
  -x, --extensions string             File extension(s) to search for
  -r, --followredirect                Follow redirects
  -H, --headers stringArray           Specify HTTP headers, -H 'Header1: val1' -H 'Header2: val2'
  -h, --help                          help for dir
  -l, --includelength                 Include the length of the body in the output
  -k, --insecuressl                   Skip SSL certificate verification
  -n, --nostatus                      Don't print status codes
  -P, --password string               Password for Basic Auth
  -p, --proxy string                  Proxy to use for requests [http(s)://host:port]
  -s, --statuscodes string            Positive status codes (will be overwritten with statuscodesblacklist if set) (default "200,204,301,302,307,401,403")
  -b, --statuscodesblacklist string   Negative status codes (will override statuscodes if set)
      --timeout duration              HTTP Timeout (default 10s)
  -u, --url string                    The target URL
  -a, --useragent string              Set the User-Agent string (default "gobuster/3.0.1")
  -U, --username string               Username for Basic Auth
      --wildcard                      Force continued operation when wildcard found
```

Ahora para los argumentos concretos del **modo dir**, la ayuda se explica sola, sin embargo, vamos a explicar algunos de los argumentos que más vamos a utilizar.

En muchos CTFs la certificación SSL está autofirmada y por lo tanto, no verificada. Utilizando `-k` podemos saltarnos la verificación SSL y continuar con el fuzz.&#x20;

Otra cosa muy útil es que nos permite decidir qué codigos de respuesta son válidos y cuales no. Con los argumentos `-s` (para la lista blanca) y `-b` (para la lista negra)&#x20;

Tambien le podemos decir a GoBuster qué tipo de archivos queremos que busque utilizando el argumento `-x` donde podemos definir las extensiones que estamos buscando. Por ejemplo, si busco imágenes puedo utilizar `-x jpg,png,gif`

Un ejemplo básico quedaría como sigue:

```
[email protected]:~$ gobuster dir -u https://erev0s.com -w awesome_wordlist.txt
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            https://erev0s.com
[+] Threads:        10
[+] Wordlist:       awesome_wordlist.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/02/01 18:46:25 Starting gobuster
===============================================================
/blog (Status: 301)
===============================================================
2020/02/01 18:46:36 Finished
===============================================================
```

### DNS mode

En el modo DNS estamos buscando subdominios de un dominio específico. Es muy importante en pentesting ya que puede revelar areas "escondidas" que no están tan protegidas como otras.

Como su nombre indica, en este modo GoBuster trata de resolver el nombre de los subdominios mediante DNS. Así puede verificar su existencia o no.

```
[email protected]:~$ gobuster help dns
Uses DNS subdomain bruteforcing mode

Usage:
  gobuster dns [flags]

Flags:
  -d, --domain string      The target domain
  -h, --help               help for dns
  -r, --resolver string    Use custom DNS server (format server.com or server.com:port)
  -c, --showcname          Show CNAME records (cannot be used with '-i' option)
  -i, --showips            Show IP addresses
      --timeout duration   DNS resolver timeout (default 1s)
      --wildcard           Force continued operation when wildcard found
```

El argumento `-d` es el encargado de definir el nombre de dominio víctima.

Hay casos en los que es necesario definir el servidor DNS a utilizar. Esto se hace con `-r` .

Con `-i` podemos obtener la IP asociada al subdominio.&#x20;

Un ejemplo sencillo quedaría como sigue:

```
[email protected]:~$ gobuster dns -d erev0s.com -w awesome_wordlist.txt -i
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Domain:     erev0s.com
[+] Threads:    10
[+] Show IPs:   true
[+] Timeout:    1s
[+] Wordlist:   awesome_wordlist.txt
===============================================================
2020/02/01 23:21:12 Starting gobuster
===============================================================
Found: dok.erev0s.com [185.165.40.5]
===============================================================
2020/02/01 23:21:12 Finished
===============================================================
```

### VHOST mode

Este modo no debe ser confundico con ser lo mismo que el modo DNS. Mientras que el modo DNS trata de resolver los nombres de los subdominios mediante DNS, el modo VHOST chequea si el subdominio existe visitando la url y verificando la IP.&#x20;

```
[email protected]:~$ gobuster vhost --help
Uses VHOST bruteforcing mode

Usage:
  gobuster vhost [flags]

Flags:
  -c, --cookies string        Cookies to use for the requests
  -r, --followredirect        Follow redirects
  -H, --headers stringArray   Specify HTTP headers, -H 'Header1: val1' -H 'Header2: val2'
  -h, --help                  help for vhost
  -k, --insecuressl           Skip SSL certificate verification
  -P, --password string       Password for Basic Auth
  -p, --proxy string          Proxy to use for requests [http(s)://host:port]
      --timeout duration      HTTP Timeout (default 10s)
  -u, --url string            The target URL
  -a, --useragent string      Set the User-Agent string (default "gobuster/3.0.1")
  -U, --username string       Username for Basic Auth
```

Los argumentos que se pueden utilizar ya estaban incluidos en el modo DIR.&#x20;

Veamos un ejemplo sencillo:

```
[email protected]:~$ gobuster vhost -u erev0s.com -w awesome_wordlist.txt
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:          http://erev0s.com
[+] Threads:      10
[+] Wordlist:     awesome_wordlist.txt
[+] User Agent:   gobuster/3.0.1
[+] Verbose:      true
[+] Timeout:      10s
===============================================================
2020/02/02 21:58:08 Starting gobuster
===============================================================
Found: www.erev0s.com (Status: 301) [Size: 0]
===============================================================
2020/02/02 21:58:09 Finished
===============================================================
```

{% hint style="danger" %}
Una página web que se esconda tras Cloudflare, puede estropearnos este tipo de escaneres.
{% endhint %}

Por ejemplo, si el servidor ever0s.com estuviera detrás de CLoudFlare, esto es lo que obtendríamos:

```
[email protected]:~$ gobuster vhost -u erev0s.com -w awesome_wordlist.txt -v
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:          http://erev0s.com
[+] Threads:      10
[+] Wordlist:     awesome_wordlist.txt
[+] User Agent:   gobuster/3.0.1
[+] Verbose:      true
[+] Timeout:      10s
===============================================================
2020/02/02 21:57:42 Starting gobuster
===============================================================
Found: sgdfsd.erev0s.com (Status: 530) [Size: 3671]
Found: agsfdg.erev0s.com (Status: 530) [Size: 3671]
Found: hi.erev0s.com (Status: 530) [Size: 3659]
Found: fgsdfg.erev0s.com (Status: 530) [Size: 3671]
Found: www.erev0s.com (Status: 301) [Size: 338]
===============================================================
2020/02/02 21:57:42 Finished
===============================================================
```

Podemos ver que GoBuster muestra como encontradas todas las entradas de nuestro diccionario aunque lo hace con un código de respuesta 530 que equivale a "origin DNS error". Esto se debe a que no es el error que la aplicación espera y le parece relevante. Para evitar esto podemos utilizar el argumento `-s` y `-b` .

## CHEATSEET

| **Description**                | **Command**                                                                                       |
| ------------------------------ | ------------------------------------------------------------------------------------------------- |
| archivos interesantes web      | gobuster dir -u mytarget.com -w path/to/my/awesome/wordlist.txt -e -t 100 -x php,txt,html,htm     |
| Encontrar imágenes             | gobuster dir -u mytarget.com -w path/to/my/awesome/wordlist.txt -e -t 100 -x jpg,jpeg,png,gif,ico |
| Evitar verificación SSL        | gobuster dir -u mytarget.com -w path/to/my/awesome/wordlist.txt -k                                |
| Bypasear Autenticación simple. | gobuster dir -u mytarget.com -w path/to/my/awesome/wordlist.txt -U BasicAuthUser -P BasicAuthPass |
| Personalizar Servidor DNS..    | gobuster dns -d mytarget.com -w path/to/my/awesome/wordlist.txt -r 10.10.10.10 -i -v              |
