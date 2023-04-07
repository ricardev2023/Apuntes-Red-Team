---
description: >-
  La exposición de datos sensibles está reconocido como la 3ª vulnerabilidad Web
  más importante según OWASP. Aquí aprenderemos qué es, como funciona y como
  explotarlo.
---

# A3-2017 - EXPOSICIÓN DE DATOS SENSIBLES

{% hint style="info" %}
Este artículo está copiado de Seguridad Ofensiva, en su artículo: [**OWASP Top 3 – Exposición de datos confidenciales**](https://seguridad-ofensiva.com/blog/owasp-top-10/owasp-top-3/)**.**
{% endhint %}

## &#x20;EXPOSICIÓN DE DATOS SENSIBLES

![OWASP Top 3](https://seguridad-ofensiva.com/blog/ptukregr/2019/10/123.jpg)

**La exposición de datos confidenciales** se clasifica como la vulnerabilidad número tres en la lista de OWASP-Top 10. Esta vulnerabilidad cubre un amplio espectro y los piratas informáticos tienen un arsenal de herramientas y procedimientos que pueden utilizar, por ejemplo:

#### **Ataques MIMT**&#x20;

Ataque donde el atacante retransmite en secreto y posiblemente altera las comunicaciones entre dos partes para acceder a datos confidenciales

#### **Rastreo manual del sitio web**&#x20;

Permite encontrar datos sensibles expuestos en el código fuente del sitio web.

#### **Uso de “Google Dorks”**&#x20;

Los Google Dorks permiten escanear todas las páginas web indexadas por Google para encontrar información que les de acceso no autorizado a diferentes redes.   &#x20;

Esta es una vulnerabilidad que afecta a sitios web, redes Wi-Fi y redes internas, por igual. Es por eso la importancia de ejecutar evaluaciones constantes de vulnerabilidad y pruebas de penetración centradas en toda la red. Vemos empresas que solo solicitan pruebas de penetración de sitios web y no se centran en otros servicios expuestos a Internet como FTP, SSH, SMTP, TELNET o IMAP, por nombrar algunos. Es común que el sitio web pudiese estar lo suficientemente protegido, pero la vulnerabilidad puede residir en un servicio FTP no seguro configurado con una cuenta anónima sin contraseña.

## ANATOMÍA DE UN ATAQUE

Hay una manera fácil de verificar la prevalencia de esta vulnerabilidad utilizando un ejemplo muy simple. **Google Dorks** es una técnica de piratería informática que utiliza el buscador de Google para encontrar agujeros de seguridad en la configuración de sitios web; recuerde, estamos buscando datos confidenciales expuestos. Ejecutamos el siguiente comando en el buscador de Google:

![](https://seguridad-ofensiva.com/blog/ptukregr/2019/10/1234-1.png)

Unos pocos clics en los resultados le mostrarán la prevalencia de esta vulnerabilidad y la cantidad de nombres de usuarios con sus respectivas contraseñas. Esta misma técnica puede ser usada para referenciar todo tipo de plataformas, bases de datos, servicios, protocolos y configuraciones con el fin de encontrar cualquier tipo de información que permita el acceso no autorizado a un sistema determinado.

{% embed url="https://gbhackers.com/latest-google-dorks-list/" %}
Lista de Google Dorks
{% endembed %}

#### **Solución y prevención**

[https://seguridad-ofensiva.com/evaluaciones-de-vulnerabilidades](https://seguridad-ofensiva.com/evaluaciones-de-vulnerabilidades)

[https://seguridad-ofensiva.com/pruebas-de-penetracion-externas](https://seguridad-ofensiva.com/pruebas-de-penetracion-externas)

[https://seguridad-ofensiva.com/pruebas-de-penetracion-en-sitios-web](https://seguridad-ofensiva.com/pruebas-de-penetracion-en-sitios-web)

#### **Recursos adicionales con relación a este tipo de ataque**:

[https://owasp.org/www-project-top-ten/OWASP\_Top\_Ten\_2017/Top\_10-2017\_A3-Sensitive\_Data\_Exposure.html](https://owasp.org/www-project-top-ten/OWASP\_Top\_Ten\_2017/Top\_10-2017\_A3-Sensitive\_Data\_Exposure.html)

[https://cheatsheetseries.owasp.org/cheatsheets/User\_Privacy\_Protection\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/User\_Privacy\_Protection\_Cheat\_Sheet.html)

[https://cheatsheetseries.owasp.org/cheatsheets/Password\_Storage\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Password\_Storage\_Cheat\_Sheet.html)

[https://cheatsheetseries.owasp.org/cheatsheets/HTTP\_Strict\_Transport\_Security\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/HTTP\_Strict\_Transport\_Security\_Cheat\_Sheet.html)
