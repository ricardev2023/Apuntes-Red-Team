---
description: >-
  El registro y monitoreo insuficiente está reconocido como la 10ª
  vulnerabilidad Web más importante según OWASP. Aquí aprenderemos qué es, como
  funciona y como explotarlo.
---

# A10-2017. REGISTRO Y MONITOREO INSUFICIENTE

{% hint style="info" %}
Este artículo está copiado de Seguridad Ofensiva, en su artículo: [**OWASP Top 10 – Registro y monitoreo insuficiente**](https://seguridad-ofensiva.com/blog/owasp-top-10/owasp-top-10/)**.**
{% endhint %}

## **REGISTRO Y MONITOREO INSUFICIENTE**

**Registro y monitoreo insuficiente** se clasifica como la vulnerabilidad número diez en la lista de OWASP-Top 10. Según OWASP, “El registro y monitoreo insuficiente es la base de casi todos los incidentes importantes. Los atacantes confían en la falta de monitoreo y respuesta oportuna para lograr sus objetivos sin ser detectados “.

Deseamos mencionar un par de ejemplos para que pueda visualizar esta situación:

### **Ejemplo 1:**

Un software de código abierto creado por un pequeño equipo de trabajo fue pirateado usando una falla en su mismo software. Los atacantes lograron borrar el repositorio de código fuente interno que contenía la próxima versión y todos los contenidos. Aunque se pudo recuperar la fuente, la falta de monitoreo, registro o alerta condujo a una violación mucho peor relacionada con los datos personales de sus clientes. El proyecto de software ya no está activo como resultado de este problema.

### **Ejemplo 2:**

Una importante empresa minorista en Santiago de Chile tenía un entorno limitado de análisis de malware interno que analizaba los archivos adjuntos. El software había detectado código potencialmente malicioso, pero nadie respondió a esta detección. El sistema de detección había estado generando advertencias durante algún tiempo antes de que se detectara una serie de transacciones fraudulentas relacionadas a las tarjetas de crédito de la compañía.

## **¿CÓMO PREVENIR?**

1. ¿Ha chequeado los logs pertenecientes a los servidores Apache o IIS?
2. ¿Están todos los sistemas de inicio de sesión configurados con un límite de intentos?
3. ¿Puede su sistema de logs detectar intentos de ataque usando fuerza bruta?
4. ¿Puede su sistema de logs registrar fallas de inicio de sesión, control de acceso y validación de entrada del lado del servidor con el fin de identificar actividades sospechosas?

Asegurarse de que estas prácticas son parte de una política de seguridad activa en su entorno tecnológico es de gran ayuda para mantener sus sistemas seguros.

#### **Solución y prevención**

[https://seguridad-ofensiva.com/evaluaciones-de-vulnerabilidades](https://seguridad-ofensiva.com/evaluaciones-de-vulnerabilidades)

[https://seguridad-ofensiva.com/pruebas-de-penetracion-externas](https://seguridad-ofensiva.com/pruebas-de-penetracion-externas)

[https://seguridad-ofensiva.com/pruebas-de-penetracion-en-sitios-web](https://seguridad-ofensiva.com/pruebas-de-penetracion-en-sitios-web)

#### **Recursos adicionales con relación a este tipo de ataque**:

[https://owasp.org/www-project-top-ten/OWASP\_Top\_Ten\_2017/Top\_10-2017\_A10-Insufficient\_Logging%252526Monitoring](https://owasp.org/www-project-top-ten/OWASP\_Top\_Ten\_2017/Top\_10-2017\_A10-Insufficient\_Logging%2526Monitoring)

[https://cwe.mitre.org/data/definitions/359.html](https://cwe.mitre.org/data/definitions/359.html)

[https://cwe.mitre.org/data/definitions/778.html](https://cwe.mitre.org/data/definitions/778.html)

[https://cwe.mitre.org/data/definitions/223.html](https://cwe.mitre.org/data/definitions/223.html)
