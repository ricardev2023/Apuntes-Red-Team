# SPN Y KERBEROS

En este artículo desarrollaré dos conceptos que son relevantes para entender los próximos ataques. Si ya tiene conocimiento sobre ellos puede continuar.

Estos conceptos son:

* Service Principal Name (SPN)
* Kerberos

## SERVICE PRINCIPAL NAME

Esta sección se va a centrar en entender como funciona y que es SPN.

{% hint style="info" %}
Es una traducción y resumen de la siguiente [web](https://en.hackndo.com/service-principal-name-spn/).
{% endhint %}

Para entender que es el **SPN** primero hay que entender el **concepto de servicio** en entornos de Active Directory.

Un **Servicio** es una capacidad, un software o algo que puede ser utilizado por otros miembros del **AD**. Por ejemplo un servidor web, un recurso compartido a nivel de red o un servicio de impresión. Para identificar un servicio necesitamos dos cosas:

* Un mismo servicio puede ser ejecutado por varios equipos. Por lo tanto necesitamos el **Host**
* Obviamente necesitamos el nombre del Servicio.

En resumen, la combinación de estas dos cosas de manera oficial es lo que se denomina **Service Principal Name** y luce asÍ:

`service_class/hostname_or_FQDN:PORT/Nombre_dado_por_mi`

Service_class no es más que una denominación genérica para el servicio (por ejemplo, los servidores web tienen la_ **service\_class www** y los servicios SQL tienen la **SqlServer**.

Un SPN se ve de la siguiente manera:

![SPN de un servicio en un Ticket de Kerberos. ](<../../.gitbook/assets/imagen (38).png>)

## KERBEROS

### Introducción

{% hint style="info" %}
Es una traducción y resumen de la siguiente [web](https://en.hackndo.com/kerberos/).
{% endhint %}

Kerberos es un protocolo que permite a los usuarios autenticarse en la red y acceder a servicios una vez autenticados.

Kerberos es utilizado cada vez que un usuario quiere acceder a un servicio en la red. Gracias a Kerberos el usuario no necesita escribir su contraseña cada vez que quiera autenticarse y el servidor tampoco necesita conocer todas las contraseñas. Es un protocolo de autenticación centralizada.

Para conseguir lo anterior, hacen falta 3 entidades:

* Un **cliente**.
* Un **servicio**.
* Un **Key Distribution Center** (KDC) que es un **Domain Controler** (DC).

![Las tres entidades.](<../../.gitbook/assets/imagen (2).png>)

La idea es que cuando un usuario quiera acceder a un servicio, la contreseña no viaje por la red para evitar comprometer la misma.

Para esto, el proceso es un poco engorroso pero se puede dividir en tres pasos:

1. **Authentication Service (AS)**: El usuario se autentica ante el **KDC**.
2. **Ticket-Granting Ticket (TGT)**: El usuario debe pedir un ticket para acceder al servicio elegido.
3. **Application Request (AP)**: Finalmente puede utilizar el servicio entregando un **Ticket-Granting Service (TGS).**

Un buen ejemplo es una fiesta con barra libre en la que tu entras con tu DNI que prueba que tu eres quien dice ser (**TGT**). Si quieres beber algo, vas al cajero con tu DNI para demostrar que eres tu y éste te suministra un ticket de bebida (**TGS**). Este ticket de bebida está sellado y es personalizado, incluye hasta tu edad. Por último, vas a la barra a que te sirvan una cerveza y entregas tu ticket de bebida (TGS). En la barra comprueban que el ticket es válido (está sellado) y que eres mayor de edad (tiene privilegios de acceso) y si es así te dan acceso a la cerveza.

{% hint style="info" %}
Es importante en el ejemplo la parte que dice que el ticket de bebida tiene la edad escrita y que es en la barra donde se comprueba que eres mayor de edad. No está puesto al azar.

**KERBEROS ES UN PROTOCOLO DE AUTENTICACIÓN NO DE AUTORIZACIÓN**.

Esto significa que el TGS lo puedo obtener aunque yo no tenga autorización para utilizar el servicio, eso lo decide el servicio en el último paso.
{% endhint %}

Sin especificar, el proceso es el siguiente:

![Proceso resumido sin especificar conceptos.](<../../.gitbook/assets/imagen (14).png>)

### Como funciona

En el contexto de un **Directorio Activo**, el **KDC** siempre será un **Domain Controler**. El **KDC** contiene, por tanto, toda la información del dominio, incluyendo los secretos de cada servicio, equipo y usuario.

![Representación gráfica de que el KDC tiene todos los secretos del domino.](<../../.gitbook/assets/imagen (82).png>)

Tomamos el usuario `strelock` como ejemplo. El usuario `strelock` quiere utilizar un servicio. Para ello, debe autenticarse frente al KDC y enviar una solicitud para utilizar el servicio. Esta es la fase de **Authentication Service (AS).**

#### **Authentication Service (AS)**

* **KRB\_AS\_REQ**

`strelock` enviará en primer lugar una solicitud de **Ticket Granting Ticket (TGT)** al **Domain Controler (DC)**. Esta solicitud se llama **KRB\_AS\_REQ (Kerberos Authentication Service Request)**. El **TGT** es una pequeña cantidad de información encriptada que incluye entre otras cosas una **Session Key** e **información de usuario** (SID, nombre, grupos, ...).

Para solicitar el TGT el usuario envia al KDC: su nombre en texto plano y la hora exacta (timestamp) de la solicitud encriptado con una versión hasheada de las contraseña del usuario `strelock` (Recordemos que el KDC posee este hash con el que hemos encriptado la información de antemano).

![Contenido del KRB\_AS\_REQ](<../../.gitbook/assets/imagen (13).png>)

El **KDC** recibe el nombre de usuario y lo comprueba con la base de datos. Como en esta base de datos tiene el hash de la contraseña del usuario `strelock` procede a desencriptar el **timestamp** con este **hash**. Pueden darse dos escenarios:

El **timestamp** no se desencripta correctamente (por lo tanto la contraseña suministrada por el usuario es incorrecta).

El timestamp se desencripta lo que asegura que el usuario es quien dice ser. El **KDC** genera una session key unica para este usuario y con una duración limitada.

![Sessión Key](<../../.gitbook/assets/imagen (43).png>)

* **KRB\_AS\_REP**

La respuesta del **KDC** incluye lo siguiente:

* **Session key** encriptada con el Hash del usuario
* **TGT** encriptado con el secreto del **KDC** (solo el **KDC** puede desencriptarlo):
  * **Nombre de usuario** (strelock).
  * **Periodo de validez.**
  * La **Session Key** generada.
  * El **Privilege Attribute Certificate (PAC)** que contiene mucha información relevante del usuario como su SID y los grupos a los que pertenece.

![Contenido del KRB\_AS\_REP](<../../.gitbook/assets/imagen (31).png>)

{% hint style="info" %}
El **TGT** es información pública por lo que es facilmente interceptable en la fase de autenticación. Por eso es muy importante la **Session Key** que lo acompaña.
{% endhint %}

El cliente recibe el **TGT** y desencripta la **Session Key** que necesitará pronto.

#### Ticket-Granting Service (TGS)

Ahora que el usuario está autenticado nos encontramos en el escenario en el que el cliente posee su propio **secreto**, la **Session Key** desencriptada y el **TGT** encriptado por el **KDC** que contiene entre otras cosas la misma **Session Key**. Esto se ilustra en la siguiente imagen.

![Situación al inicio de esta fase.](<../../.gitbook/assets/imagen (74).png>)

* **KRB\_TGS\_REQ**

Si el usuario quiere utilizar un servicio, como por ejemplo `CIFS` en el `SERVER01`, Tendrá que enviar varias cosas al **KDC** para solicitar el **TGS**.

* El TGT.
* El servicio al que se quiere conectar, con el **formato SPN** `CIFS/SERVER01`.
* Un **autenticador** parecido al que mandó en el **KRB\_AS\_REQ**. Es decir su **nombre de usuario** y **timestamp** solo que esta vez **encriptados con la Session Key**.

![Contenido del KRB\_TGS\_REQ](<../../.gitbook/assets/imagen (22).png>)

Una vez recibida la solicitud, el KDC desencripta el TGT y con la información que contiene (nombre de usuario y Session Key) desencripta el Autenticador y comprueba los datos que contiene. Si los datos son correctos, el usuario es quien dice ser. En el siguiente diagrama se ve claro:

![Diagrama de autenticación en la fase de TGS.](<../../.gitbook/assets/imagen (42).png>)

* **KRB\_TGS\_REP**

Ahora que está verificado que el usuario strelock es quien dice ser, el KDC envía la información al usuario que le permitirá realizar la solicitud al servicio, esta información es la siguiente:

* Un **TGS** (encriptado con el secreto del servicio solicitado) que contiene:
  * El nombre de usuario.
  * El servicio solicitado.
  * El PAC.
  * Una nueva Session Key solo valida para comunicarse entre el usuario y el servicio solicitado.
* Una nueva Session Key.

Todo el paquete viene encriptado a su vez con la primera **Session Key**.

![Contenido del KRB\_TGS\_REP](<../../.gitbook/assets/imagen (30).png>)

El usuario en este momento desencripta la primera capa de cifrado con la primera **Session Key** obteniendo acceso así a la segunda **Session Key** y al **TGS encriptado por el servicio**.

![Desencriptado de la primera capa y acceso al 2º Session Key y al TGS encriptado.](<../../.gitbook/assets/imagen (63).png>)

#### Application Request (AP)

* **KRB\_AP\_REQ**

En este momento, el usuario `strelock` genera un nuevo **autenticador** que encripta con la nueva Session Key.

![Encriptado del nuevo autenticador con la 2ª Session Key.](<../../.gitbook/assets/imagen (54).png>)

El servicio **CIFS** recibe el **TGS** y lo **desencripta con su propio secreto**. Este **TGS** contiene la **Session Key** que utiliza para **desencriptar el autenticador**. Así comprobamos la autenticidad del usuario.

![Proceso de autenticación frente al Servicio.](<../../.gitbook/assets/imagen (61).png>)

Si todo sale bien, el Servicio procede a dar acceso al usuario.

#### Resumen

En esta última imagen vemos todo el proceso de manera esquematizada:

![Esquema de resumen.](<../../.gitbook/assets/imagen (9).png>)

## REFERENCIAS

[https://en.hackndo.com/service-principal-name-spn/](https://en.hackndo.com/service-principal-name-spn/)\
[https://en.hackndo.com/kerberos/](https://en.hackndo.com/kerberos/)
