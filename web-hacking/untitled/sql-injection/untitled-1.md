---
description: Otras técnicas de SQLi que podrían funcionar.
---

# SQLI MODIFIED HEADERS

## **INYECCION SQL MEDIANTE USER-AGENT**

Hay algunas paginas que comprueban el user agent y lo comparan con una Base de Datos de la Web.

Si modifico mi User-Agent en **BURP SUIT** y lo convierto en una consulta SQL puedo explotar una vulnerabilidad de SQL Injection:

**`User-Agent =' UNION SELECT user,password FROM users -- -`**

&#x20;Asi consigo acceso a las tablas.

## **INYECCION SQL MEDIANTE COOKIES**

&#x20;Hay algunas paginas que comprueban el valor de una cookie y lo compara con una Base de Datos de la Web.

&#x20;Si modifico la Cookie en **BURP SUITE** y la convierto en una consulta SQL, puedo explotar una vulnerabilidad SQL Injection:

&#x20;**`Cookie =' UNION SELECT user,password FROM users -- -`**

## &#x20;**INYECCION SQL MEDIANTE BASE 64**

&#x20;En ocasiones la web codificara la URL en base 64 para complicar la Inyeccion SQL. Lo unico que debemos hacer es convertir nuestra Query a Base 64 y la copiamos en el mismo sitio de la URL donde lo pondriamos normalmente.

&#x20;**`' UNION SELECT user,password FROM users -- -`**

&#x20;**`JyBVTklPTiBTRUxFQ1QgdXNlcixwYXNzd29yZCBGUk9NIHVzZXJzIC0tIC0`**
