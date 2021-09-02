---
description: Articulo sobre la herramienta wfuzz y su utilización.
---

# WFUZZ

{% hint style="info" %}
Este artículo es una traducción de la wiki oficial de wfuzz.
{% endhint %}

{% embed url="https://wfuzz.readthedocs.io/en/latest/user/basicusage.html" %}

## **FUZZING DE RUTAS Y ARCHIVOS**

**Wfuzz** puede ser utilizado para buscar contenido oculto en servidores web, como por ejemplo archivos y directorios, permitiendo encontrar vectores de ataque escondidos. Es importante tener en cuenta que gran parte del exito de esta tarea se debe a la elección de un buen diccionario.

Sin embargo, debido a la limitada cantidad de plataformas que ofrecen servicio de servidor web, las instalaciones predeterminadas y los recursos conocidos como por ejemplo logs o directorios de administración, un número considerable de recursos pueden ser localizados en lugares predecibles de la red. Por este motivo, realizar un ataque de fuerza bruta sobre estos contenidos se convierte en una tarea más asequible.

Aquí tenemos un ejemplo de **Wfuzz** buscando directorios comunes:

`wfuzz -w wordlist/general/common.txt http://testphp.vulnweb.com/FUZZ`

### **FUZZING DE PARAMETROS DE LA URL**

Muchas veces queremos fuzzear algún tipo de dato que se encuentra en las busquedas de la URL \(normalmente vienen en esta forma **url?dato=valor**\). Esto lo podemos conseguir facilmente utilizando la palabra clave **FUZZ** en donde debería ir el valor.

`wfuzz -z range,0-10 --hl 97 http://testphp.vulnweb.com/listproducts.php?cat=FUZZ`

### FUZZING DE POST REQUESTS

Si queremos fuzzear algún tipo de dato que se encuentra encriptado como lo haría un **formulario HTML**, solo hace falta pasarle a **WFUZZ** el parámetro **-d**:

`wfuzz -z file,wordlist/others/common_pass.txt -d "uname=FUZZ&pass=FUZZ" --hc 302 http://testphp.vulnweb.com/userinfo.php`

### **FUZZING DE COOKIES**

Para **enviar sus propias cookies** al servidor, por ejemplo, para asociar una solicitud a una session HTTP, se puede utilizar el parámetro **-b** \(si hay varias cookies tendremos que repetirlo, como en el ejemplo\):

`wfuzz -z file,wordlist/general/common.txt -b cookie=value1 -b cookie2=value2 http://testphp.vulnweb.com/FUZZ`

El comando de arriba generaría la siguiente solicitud HTTP:

```http
GET /attach HTTP/1.1  
Host: testphp.vulnweb.com  
Accept: */*  
Content-Type: application/x-www-form-urlencoded  
Cookie: cookie=value1; cookie2=value2  
User-Agent: Wfuzz/2.2  
Connection: close
```

Las **cookies pueden tambien ser fuzzeadas**, no solo alteradas:

`wfuzz -z file,wordlist/general/common.txt -b cookie=FUZZ http://testphp.vulnweb.com/`

### FUZZING ENCABEZADOS PERSONALIZADOS

Si quiere **añadir encabezados** a una solicitud HTTP solo tiene que añadir el parámetro **-H** \(si queremos añadir varios encabezados habrá que repetir en parámetro\):

`wfuzz -z file,wordlist/general/common.txt -H "myheader: headervalue" -H "myheader2: headervalue2" http://testphp.vulnweb.com/FUZZ`

El comando de arriba generaría la siguiente **solicitud HTTP**:

```http
GET /agent HTTP/1.1  
Host: testphp.vulnweb.com  
Accept: */*  
Myheader2: headervalue2  
Myheader: headervalue  
Content-Type: application/x-www-form-urlencoded  
User-Agent: Wfuzz/2.2  
Connection: close
```

Tambien puede **modificar encabezados existentes**, por ejemplo, personalizando el user agent:

`wfuzz -z file,wordlist/general/common.txt -H "myheader: headervalue" -H "User-Agent: Googlebot-News" http://testphp.vulnweb.com/FUZZ`

El comando de arriba generaría la siguiente **solicitud HTTP**:

```http
GET /asp HTTP/1.1  
Host: testphp.vulnweb.com  
Accept: */*  
Myheader: headervalue  
Content-Type: application/x-www-form-urlencoded  
User-Agent: Googlebot-News  
Connection: close
```

Por último, tambien pueden ser **fuzzeados**:

`wfuzz -z file,wordlist/general/common.txt -H "User-Agent: FUZZ" http://testphp.vulnweb.com/`

### **FUZZEANDO METODOS HTTP**

Los **métodos HTTP** \(GET, HEAD, POST, TRACE, OPTIONS\) tambien se pueden fuzzear utilizando el parámetro **-X**

`wfuzz -z list,GET-HEAD-POST-TRACE-OPTIONS -X FUZZ http://testphp.vulnweb.com/`

Tambien se puede especificar el método, por ejemplo `-X HEAD`.

If you want to perform the requests using a specific verb you can also use “-X HEAD”.

### **ESCRIBIENDO EL RESULTADO EN UN ARCHIVO**

Wfuzz soporta la opción de escribir el resultado en un archivo en diferentes formatos. Esto lo ejecuta con unos plugins que se denominan "printers". Los printers disponibles se pueden averiguar ejecutando:

`wfuzz -e printers`

Por ejemplo, para escribir los resultados en un archivo en formato JSON podemos utilizar el siguiente comando:

`wfuzz -f /tmp/outfile,json -w wordlist/general/common.txt http://testphp.vulnweb.com/FUZZ`

#### **Personalizar el Output**

Utilizando el output por defecto \(raw\) tambien podemos seleccionar campos adicionales para que se muestren con el parametro -efield y el payload 

When using the default or raw output you can also select additional FuzzResult’s fields to show, using –efield, together with the payload description:

`$ wfuzz -z range --zD 0-1 -u http://testphp.vulnweb.com/artists.php?artist=FUZZ --efield r`

El comando de arriba guardaría un output así, añadiendo la solicitud HTTP que se ha realizado.

```http
...
000000001:   200        99 L     272 W    3868 Ch   0 | GET /artists.php?artist=0 HTTP/1.1
                                                    Content-Type: application/x-www-form-urlencoded
                                                    User-Agent: Wfuzz/2.4
                                                    Host: testphp.vulnweb.com
...
```

{% hint style="info" %}
La wiki tiene más contenido que no me parece de gran utilidad en este momento. Permite:

* Fuzzear autenticaciones \(con --basic/ntlm/digest\)
* Activar el modo recursivo \(con -R\)
* Fuzzear proxies \(con -p\)
{% endhint %}

