---
description: >-
  Control de acceso vulnerable está reconocido como la 4ª vulnerabilidad Web más
  importante según OWASP. Aquí aprenderemos qué es, como funciona y como
  explotarlo.
---

# A5-2017. CONTROL DE ACCESO VULNERABLE

{% hint style="info" %}
Este artículo está copiado de Seguridad Ofensiva, en su artículo: [**OWASP Top 5 – Control de acceso vulnerable**](https://seguridad-ofensiva.com/blog/owasp-top-10/owasp-top-5/)\*\*\*\*
{% endhint %}

## CONTROL DE ACCESO VULNERABLE

![OWASP Top 5](https://seguridad-ofensiva.com/blog/ptukregr/2019/10/3.jpg)

El **control de acceso vulnerable** se clasifica como la vulnerabilidad numero cinco en la lista de OWASP-Top 10. El **control de acceso** o **sistema de autorización** se refiere a cómo una aplicación Web otorga acceso a contenido y funciones a algunos usuarios y no a otros.

Muchos de estos esquemas de acceso no fueron diseñados deliberadamente, sino que simplemente evolucionaron junto con el sitio web. En estos casos, las reglas de control de acceso se insertan en varias ubicaciones en todo el código y en las carpetas de archivos. A medida que el sitio Web se acerca a la implementación, el conjunto de reglas se vuelve tan difícil de manejar que es casi imposible de entender.

Muchos de estos esquemas de control de acceso defectuosos no son difíciles de descubrir y explotar. Una vez que se descubre una falla, las consecuencias de un esquema de control de acceso defectuoso pueden ser devastadoras. Además de ver contenido no autorizado, un atacante podría cambiar o eliminar contenido, realizar funciones no autorizadas o incluso hacerse cargo de la administración del sitio con todo lo implica la perdida de la reputación de la compañía afectada.

Veamos un ejemplo de control de acceso vulnerado. Para entender esta demostración debemos saber que los sitios Web residen como archivos en carpetas con ciertos permisos asignados dentro de un sistema operativo. Cuando esas carpetas son configuradas con un conjunto de permisos erróneos, pueden ser vulneradas por atacantes accediendo a los archivos de configuración de la página Web, los cuales pueden tener contraseñas o información confidencial.

## ANATOMIA DE UN ATAQUE AL CONTROL DE ACCESO

Es posible para un atacante experimentado hacer un análisis detallado de las posibles vulnerabilidades de una página Web, mucho antes siquiera de pensar en atacar el objetivo. En este caso, el atacante, en su proceso de enumeración, puede encontrar distintas vulnerabilidades, entre ellas una vulnerabilidad llamada “**Ataque transversal de ruta**” \(**directory traversal** en inglés\).

Este pequeño reporte nos muestra el nombre de la vulnerabilidad, el enlace de la página Web que es vulnerable, el riesgo de ser atacado, que en este caso es alto y la evidencia de que la herramienta de análisis ya fue capaz de confirmar la vulnerabilidad como explotable mostrándonos parte del documento al cual accedió, en este caso “root:x:0:0”:

![OWASP Top 5](https://seguridad-ofensiva.com/blog/ptukregr/2019/10/word-image-16.png)

Esta vulnerabilidad es definida por OWASP-Top 10 de la siguiente manera:

Un **ataque transversal de ruta** \(también conocido como transversal de directorio\) tiene como objetivo **acceder a archivos y directorios que están almacenados fuera de la carpeta raíz web**. Al manipular variables que hacen referencia a archivos con secuencias de “punto-punto-barra diagonal \(../\)” y sus variaciones o al usar rutas de archivos absolutas, es posible acceder a archivos arbitrarios y directorios almacenados en el sistema de archivos, incluido el código fuente o la configuración de la aplicación y archivos críticos del sistema. **Cabe señalar que el acceso a los archivos está limitado por el control de acceso operativo del sistema, o sea, por los permisos que se asignan a las carpetas y archivos.**

Si seguimos la misma ruta que la herramienta de análisis nos muestra usando cualquier buscador Web, tenemos acceso al archivo “/etc/passwd” que reside en el servidor donde la aplicación vulnerable reside.

![](https://seguridad-ofensiva.com/blog/ptukregr/2019/10/word-image-17.png)

![](https://seguridad-ofensiva.com/blog/ptukregr/2019/10/word-image-18.png)

![](https://seguridad-ofensiva.com/blog/ptukregr/2019/10/word-image-19.png)

Otras herramientas de análisis más especializadas toman ventaja de esta vulnerabilidad para incluso abusar mas de ella y proveernos una **conexión remota directa** a este servidor vulnerable.

{% hint style="warning" %}
Es importante mencionar que los resultados de los análisis y accesos no autorizados a estos archivos y carpetas son posibles porque alguien no configuro los permisos de los archivos de manera apropiada.
{% endhint %}

#### **¿Cómo puedo determinar si soy vulnerable a este tipo de ataques?**

Para responder esta pregunta es necesario hacer un análisis de la política de control de acceso que su departamento de tecnología debiese tener documentada. En primer lugar, ¿Hay una política en su empresa que sirva como guía a los administradores de sus sitios Web para crear nuevos usuarios y para asignar permisos a los archivos del sistema? Si esta política de asignación de permisos no existe, es muy probable que sus aplicaciones Web estén afectadas, lo cual permitiría extraer información de sus servidores o tener acceso a información a la cual el usuario no debiese acceder.

Si una política de asignación de permisos definida no existe en relación a la creación de nuevos usuarios o de asignación de permisos a archivos y carpetas, es muy probable que su sitio Web sea vulnerable.

#### **Solución y prevención**

[https://seguridad-ofensiva.com/evaluaciones-de-vulnerabilidades](https://seguridad-ofensiva.com/evaluaciones-de-vulnerabilidades)

[https://seguridad-ofensiva.com/pruebas-de-penetracion-externas](https://seguridad-ofensiva.com/pruebas-de-penetracion-externas)

[https://seguridad-ofensiva.com/pruebas-de-penetracion-en-sitios-web](https://seguridad-ofensiva.com/pruebas-de-penetracion-en-sitios-web)

#### **Recursos adicionales con relación a este tipo de ataque**:

[https://owasp.org/www-project-top-ten/OWASP\_Top\_Ten\_2017/Top\_10-2017\_A5-Broken\_Access\_Control.html](https://owasp.org/www-project-top-ten/OWASP_Top_Ten_2017/Top_10-2017_A5-Broken_Access_Control.html)

[https://cheatsheetseries.owasp.org/cheatsheets/Access\_Control\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Access_Control_Cheat_Sheet.html)

[https://cwe.mitre.org/data/definitions/22.html](https://cwe.mitre.org/data/definitions/22.html)

[https://cwe.mitre.org/data/definitions/284.html](https://cwe.mitre.org/data/definitions/284.html)

[https://cwe.mitre.org/data/definitions/285.html](https://cwe.mitre.org/data/definitions/285.html)

[https://cwe.mitre.org/data/definitions/639.html](https://cwe.mitre.org/data/definitions/639.html)

