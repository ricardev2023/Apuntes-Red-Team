---
description: Explicación detallada de la herramienta Hydra.
---

# HYDRA

## **HYDRA**

En este ejemplo vamos a utilizar Hydra para obtener desde cero unos credenciales válidos para acceder a una página wordpress con poca seguridad.

### Obtener nombre de Usuario válido

Para obtener un nombre de usuario valido:

```text
hydra -L [wordlist] -p [cualquiera] -vV 192.168.0.30 http-post-form 
'/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid Username'
```

 **-L** → utilizamos una wordlist para obtener un usuario valido.  
 **-p** → la password puede ser cualquiera por que no nos interesa descubrirla.  
 **-vV** → Modo muy verbose.  
 la IP del servidor sobre el que vamos a actuar.  
 El tipo de formulario y solicitud ante el que nos encontramos. Esto puede variar la informacion a introducir. → Lo obtenemos con la consola del explorador o con burpsuit.  
 Por ultimo encontramos 3 datos separados por **“:”**. Estos son:  
 **/wp-login.php** → A quien se le realiza la peticion de autenticacion \(normalmente el referer del header\)  
 **log=^USER^&pwd=^PASS^&wp-submit=Log+In** → La estructura de la peticion \(el payload del request\)  
 **F=Invalid Username** → Mensaje de error cuando se introduce de manera incorrecta.

###  Obtener Contraseña del Usuario

Tras obtener un nombre de usuario valido puedo iniciar brute force con las pass.

```text
hydra -l [usuario_valido] -P [wordlist] -vV 192.168.0.30 http-post-form 
'/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=is incorrect'
```

 **-l** → para un usuario concreto  
 **-P** → para listado de pass  
 Como se puede ver, el mensaje de error ha cambiado debido a que el usuario es correcto.

{% hint style="danger" %}
Si el mensaje de error no cambiara en caso de usuario incorrecto o pass incorrecta y fuera invariable, no podria sacar un usuario valido.
{% endhint %}

###  **Otros argumentos importantes**

 **-S** → Activa SSL para trabajar en https

 **-s** → Cambia el puerto por defecto

 **-e** → Prueba passwords especiales \(n= vacia s=usuario r= login inverso\)

{% hint style="info" %}
La aplicacion permite tambien hacer ataques de fuerza buta sobre direcciones de correo
{% endhint %}

{% hint style="success" %}
**XHIDRA** tiene toda la potencia de hydra pero en entorno grafico
{% endhint %}

## REFERENCIAS

[https://github.com/vanhauser-thc/thc-hydra](https://github.com/vanhauser-thc/thc-hydra)

