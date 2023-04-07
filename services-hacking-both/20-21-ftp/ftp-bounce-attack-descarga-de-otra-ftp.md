# FTP BOUNCE ATTACK- DESCARGA DE OTRA FTP

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ftp/ftp-bounce-download-2oftp-file" %}

## RESUMEN

Si tienes acceso a un servidor FTP del que se pueda explotar el Bounce Attack, puedes hacerle solicitar archivos en otro servidor FTP (del que conoces alguna credencial) y descargarlo hasta tu sistema.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Esto puede ser muy útil en CTFs donde hemos conseguido unos credenciales para una FTP donde se guardan datos críticos pero que solo está conectada a una red local. Utilizando éste método (parecido al port mirroring) podemos obtener esos datos sin tener acceso a la red interna.&#x20;
{% endhint %}

### Requisitos Previos

* Credenciales válidos en el servidor FTP intermedio.
* Credenciales válidos en el servidor FTP victima.
* Ambos servidores aceptan el comando PORT (para poder hacer el Bounce)
* Tienes permiso de escritura en al menos uno de los directorios del servidor intermedio.
* El servidor intermedio tiene más permisos que tú en el servidor victima (o simplemente tiene acceso y tu no).&#x20;

### Pasos

1. Conéctate a tu propio servidor FTP de manera pasiva (comando PASV) para hacerlo escuchar en un directorio donde la victima mandará el archivo.
2. Escribe el archivo que la FTP intermedia le mandará a la victima (el exploit). Este archivo será un texto claro con los comandos necesarios para autenticarse contra la víctima, cambiar de directorio y descargar el archivo a nuestro propio servidor.
3. Conectate al servidor FTP intermedio y sube el archivo anteriormente desarrollado.
4. Haz que el servidor FTP intermedio establezca una conexión con la victima y envía el exploit.
5. Captura el archivo de vuelta con tu propio servidor FTP.
6. Elimina el archivo de exploit del servidor intermedio.

{% embed url="http://www.ouah.org/ftpbounce.html" %}

{% embed url="https://www.thesecuritybuddy.com/vulnerabilities/what-is-ftp-bounce-attack/" %}

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ftp/ftp-bounce-download-2oftp-file#steps" %}
