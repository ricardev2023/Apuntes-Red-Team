---
description: Técnica más básica de SQLi
---

# LOGIN FORM BYPASS

## **BYPASS DE FORMULARIO DE INICIO DE SESION**

Los formularios de inicio de sesion son un claro ejemplo de busqueda de datos en una base de datos.

###  **1. Formulario de inicio de sesión básico**

 El comando SQL que se ejecuta por debajo del formulario es el siguiente:

 **`SELECT * FROM users WHERE name='aaaaa' and password='bbbb'`**

 Esto quiere decir: **“SELECCIONA TODO DE LA TABLA users DONDE LA COLUMNA name EQUIVALE A ‘aaaaa’ Y LA COLUMNA password EQUIVALE A ‘bbbb’”**

 Esto quiere decir que se deben cumplir ambos datos.

 Por tanto, sabiendo que este formulario es vulnerable a SQL INJECTION, debemos generar un comando para el usuario que siempre sea cierto y un comando para la password que siempre sea cierto tambien, de tal manera que podemos hacer:

 **USERNAME:** `'Daniel ' or 1=1 -- -`  
 **PASSWORD:** `'Daniel ' or 1=1 -- -`

 Como vemos, aunque Daniel no se encuentre en la **COLUMNA name** de la **TABLA users,** se va a cumplir que 1=1 y lo mismo ocurre en la **COLUMNA password**.

{% hint style="info" %}
Cuando pone **“-- -”** es que se comenta el resto del comando. Es importante para que funcionen este tipo de comandos ya que puede haber elementos que molesten.  
 En el siguiente ejemplo se ve más claro.
{% endhint %}

 Asi conseguimos acceso.

###  **2. Formulario de inicio de sesión con paréntesis y comillas simples.**

 En este caso, la consulta SQL es un poco diferente:

 **`SELECT * FROM users WHERE name=('aaaaa') and password=('bbbb')`**

 Vemos que se ponen parentesis a la hora de llamar al contenido de las columnas. Si utilizamos el mismo truco de antes, obtendremos:

 **`SELECT * FROM users WHERE name=(''Daniel' or 1=1 -- -') and password=(''Daniel' or 1=1 -- -')`**

 Esto da un error como resultado, por tanto debemos conseguir cerrar el parentesis para que no moleste, teniendo en cuenta que despues cuando se cierre de manera “natural” dara igual por que estara comentado. Por tanto:

 **USERNAME:** `') or 1=1 -- -`  
 **PASSWORD:** `') or 1=1 -- -`

 **`SELECT * FROM users WHERE name=('') or 1=1 -- -') and password=('') or 1=1 -- -')`**

Ya tenemos acceso.

###  **3.** Formulario de inicio de sesión básico con paréntesis y comillas dobles

 Exactamente lo mismo que en el anterior pero con dobles:

 **`SELECT * FROM users WHERE name=("aaaaa") and password=("bbbb")`**

 **USERNAME:** `") or 1=1 -- -`  
 **PASSWORD:** `") or 1=1 -- -`

 **`SELECT * FROM users WHERE name=("") or 1=1 -- -") and password=("") or 1=1 -- -")`**

 Y ya tenemos acceso

###  **4. Formulario de inicio de sesión con validación de Javascript**

 En este caso, el problema es que no podemos poner caracteres especiales \('\) en el input del formulario. Por tanto no podemos introducir nuestro PAYLOAD en el formulario.

 **SOLUCION**: Utilizar BURP SUITE.

 **1.** Para ello, iniciamos **BURP SUITE**, activamos el Proxy en el navegador y enviamos unas credenciales cualquiera para que las intercepte BURP SUITE.

 **2.** Mandamos la peticion POST al REPEATER y modificamos los parametros con los que sabemos que vamos a acceder:

 **USERNAME:** `' or 1=1 -- -`  
 **PASSWORD:** `' or 1=1 -- -`

 **3.** En este caso ya no nos enfrentamos a la validacion JAVASCRIPT ya que estamos modificando directamente la peticion.

 ya tenemos acceso.

###  **5. Formulario de inicio de sesión con password en MD5**

 Muestra la PASS en MD5 pero aun asi, si utilizamos los parametros habituales, no tenemos problema en acceder.

 **USERNAME:** `' or 1=1 -- -`  
 **PASSWORD:** `' or 1=1 -- -`

 y ya tenemos acceso

