---
description: >-
  SQL Injection está reconocido como la 1ª vulnerabilidad Web más importante
  según OWASP. Aquí aprenderemos qué es, como funciona y como explotarlo.
---

# A1-2017. SQL INJECTION

## **QUE ES SQL**

 **Structured Query Language**. Lenguaje de consultas estructuradas

 Es un lenguaje que permite especificar mediante consultas diversos tipos de operaciones en un gestor de base de datos.  
 Estas **operaciones** pueden ser:

* Crear
* Modificar
* Eliminar
* Incluir
* etc...

 Los **Gestores de Bases de datos** mas conocidos son:

* MariaDB
* SQL Server
* MySQL
* Oracle
* PostgreSQL
* etc...

##  **SQL INJECTION**

Es una **vulnerabilidad** que permite la inyeccion de codigo SQL mediante la **entrada de datos provenientes del lado del cliente hacia el servidor**.

Esto **permite alterar el funcionamiento normal del programa y lograr que se ejecute el codigo inyectado**.

Se puede hacer todo lo que permite hacer el lenguaje SQL : **Consultar, Eliminar, Modificar e incluir informacion en la base de datos.**

Existen varios **tipos:**

* **INYECCION SQL IN-BAND** → injeccion SQL Clasico.
  * Inyeccion SQL Basada en Error
  * Inyeccion SQL Basada en Uniones
* **INYECCION SQL INFERENCIAL** → Blind SQL Injection \(SQLi ciega\).
  * Inyeccion SQL Basada en Booleanos
  * Inyeccion SQL Basada en Tiempo
* **INYECCION SQL OUT-OF-BAND** → Envio de peticiones a un servidor controlado por el atacante.

###  **Metodos de inyección**

* Entrada de valores enteros en URL
* Entrada de valores de cadena en URL
* Entrada de datos en formularios
  * Entrada de dato libre
  * Dropdown Box
* Entrada de datos mediante cabeceras modificadas
  * Cookies
  * User-Agent

###  **Como identificar vulnerabilidades SQLi**

Para empezar, debo encontrar un elemento de la pagina web que obtenga informacion de una base de datos, ya sea un formulario o un parámetro de una URL ****\(normalmente los parámetros tienen la forma **url?dato=valor**\).

Una vez lo he encontrado, la forma mas sencilla de comprobar si es vulnerable o no es modificando el **valor de busqueda por una comilla simple** \('\). De esta manera, obtendremos un **error de sintaxis de SQL** que no solo confirmara que nos encontramos ante una vulnerabilidad sino que ademas **nos indicara el Gestor de Base de Datos que se esta utilizando.**

```text
You have an error in your SQL syntax; check the manual that corresponds to 
your MySQL server version for the right syntax to use near ''''' at line 1
```

