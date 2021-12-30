---
description: Explicación detallada de la herramienta Hydra.
---

# HYDRA

## **HYDRA**

Hydra es una poderosa herramienta la cual permite realizar ataques de fuerza bruta a servicios online como ejemplo, FTP, SSH, Mysql, POP3, Telnet, etc. Es importante mencionar que ataques como por ejemplo al servicio Gmail se encuentran obsoletos, por lo tanto es recomendable no realizar ataques a este servicio con Hydra.

En este ejemplo vamos a utilizar Hydra para obtener desde cero unos credenciales válidos para acceder a una página wordpress con poca seguridad.

### Obtener nombre de Usuario válido

Para obtener un nombre de usuario valido:

```
hydra -L [wordlist] -p [cualquiera] -vV 192.168.0.30 http-post-form 
'/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid Username'
```

&#x20;**-L** → utilizamos una wordlist para obtener un usuario valido.\
&#x20;**-p** → la password puede ser cualquiera por que no nos interesa descubrirla.\
&#x20;**-vV** → Modo muy verbose.\
&#x20;la IP del servidor sobre el que vamos a actuar.\
&#x20;El tipo de formulario y solicitud ante el que nos encontramos. Esto puede variar la informacion a introducir. → Lo obtenemos con la consola del explorador o con burpsuit.\
&#x20;Por ultimo encontramos 3 datos separados por **“:”**. Estos son:\
&#x20;**/wp-login.php** → A quien se le realiza la peticion de autenticacion (normalmente el referer del header)\
&#x20;**log=^USER^\&pwd=^PASS^\&wp-submit=Log+In** → La estructura de la peticion (el payload del request)\
&#x20;**F=Invalid Username** → Mensaje de error cuando se introduce de manera incorrecta.

### &#x20;Obtener Contraseña del Usuario

Tras obtener un nombre de usuario valido puedo iniciar brute force con las pass.

```
hydra -l [usuario_valido] -P [wordlist] -vV 192.168.0.30 http-post-form 
'/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=is incorrect'
```

&#x20;**-l** → para un usuario concreto\
&#x20;**-P** → para listado de pass\
&#x20;Como se puede ver, el mensaje de error ha cambiado debido a que el usuario es correcto.

{% hint style="danger" %}
Si el mensaje de error no cambiara en caso de usuario incorrecto o pass incorrecta y fuera invariable, no podria sacar un usuario valido.
{% endhint %}

### &#x20;**Otros argumentos importantes**

&#x20;**-S** → Activa SSL para trabajar en https

&#x20;**-s** → Cambia el puerto por defecto

&#x20;**-e** → Prueba passwords especiales (n= vacia s=usuario r= login inverso)

{% hint style="info" %}
La aplicacion permite tambien hacer ataques de fuerza buta sobre direcciones de correo
{% endhint %}

{% hint style="success" %}
**XHIDRA** tiene toda la potencia de hydra pero en entorno grafico
{% endhint %}

## REFERENCIAS

[https://github.com/vanhauser-thc/thc-hydra](https://github.com/vanhauser-thc/thc-hydra)****\
****[https://backtrackacademy.com/articulo/hydra-ataques-de-fuerza-bruta-online](https://backtrackacademy.com/articulo/hydra-ataques-de-fuerza-bruta-online)
