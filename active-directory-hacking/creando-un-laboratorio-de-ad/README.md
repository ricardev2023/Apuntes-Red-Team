---
description: Creación de un laboratorio de Active Directory desde cero.
---

# CREANDO UN LABORATORIO DE AD

## EXPLICACIÓN

Los entornos empresariales reales están en una gran proporción creados en base a diferentes versiones de **Windows Server**. 

**Active Directory**  \(o Directorio Activo en castellano\) es un servicio de directorio implementado por Microsoft en sus distribuciones de Windows Server y que permiten administrar una red distribuida.

De forma sencilla se puede decir que es un servicio establecido en uno o varios servidores en donde se crean **objetos** tales como _usuarios, equipos o grupos_, con el objetivo de administrar los inicios de sesión en los equipos conectados a la red, así como también la administración de políticas en toda la red.

Su **estructura jerárquica** permite mantener una serie de objetos relacionados con componentes de una red, como usuarios, grupos de usuarios, permisos y asignación de recursos y políticas de acceso.​

Active Directory permite a los **administradores** establecer **políticas a nivel de empresa**, desplegar programas en muchos ordenadores y aplicar actualizaciones críticas a una organización entera. Un Active Directory almacena información de una organización en una **base de datos central**, organizada y accesible. Pueden encontrarse desde directorios con cientos de objetos para una red pequeña hasta directorios con millones de objetos.

Active Directory trabaja principalmente con los protocolos: **LDAP, DNS, DHCP, RDP y KERBEROS.**

## ACCIONES PREVIAS

El despliegue de nuestra red empresarial va a realizarse utilizando máquinas virtuales, aunque si lo prefiere puede utilizar equipos físicos para cada una de las máquinas. 

{% hint style="warning" %}
Tenga en cuenta que el objetivo de esta sección es desarrollar un **ENTORNO VULNERABLE Y MUY SIMPLE**. Por lo tanto, es recomendable que éstas máquinas no tengan acceso a internet.

Para ello, en el caso de las máquinas virtuales, **debemos configurar todas en modo de red NAT**.
{% endhint %}

Para ésta sección vamos a necesitar:

* Un ordenador con espacio de almacenamiento suficiente, al menos 16 GB de RAM y 4 nucleos \(Puede funcionar con menos pero es recomendable\). 
* VMWare o VirtualBox \(yo estaré utilizando VMWare\) 
* Una ISO de Windows Server 2016: En la página oficial de Microsoft puede encontrar una [versión de evaluación](https://www.microsoft.com/es-es/evalcenter/evaluate-windows-server-2016). 
* Una ISO de Windows 10: Enterprise Edition : En la página oficial de Microsoft puede encontrar una [versión de evaluación](https://www.microsoft.com/es-es/evalcenter/evaluate-windows-10-enterprise).

## REFERENCIAS

[https://es.wikipedia.org/wiki/Active\_Directory](https://es.wikipedia.org/wiki/Active_Directory)  
[https://www.youtube.com/watch?v=LLevcaB4qew&list=PLlb2ZjHtNkpg2Mc3mbkdYAhEoqnMGdl2Z](https://www.youtube.com/watch?v=LLevcaB4qew&list=PLlb2ZjHtNkpg2Mc3mbkdYAhEoqnMGdl2Z)  
[https://en.hackndo.com/service-principal-name-spn/](https://en.hackndo.com/service-principal-name-spn/)  


