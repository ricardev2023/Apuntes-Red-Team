---
description: Ejemplos de otros fuzzers existentes en el mercado.
---

# OTRAS HERRAMIENTAS DE RECONOCIMIENTO WEB

## FUZZERS

### DIRB

Un fuzzer eficaz y muy sencillo de utilizar. Por defecto es recursivo y utiliza un diccionario bastante completo de la misma aplicación.

`dirb http://192.168.0.30`

### DIRBUSTER

Es el único que cuenta con interfaz gráfica lo que lo hace más amigable a la hora de representar la información obtenida en forma de arbol. Desarrollado por el proyecto [OWASP](https://owasp.org/).

### GOBUSTER

Es otro fuzzer de linea de comandos desarrollado en lenguaje GO. Es más versatil que DirB.

### SKIPFISH

Es otra herramienta activa de reconocimiento web que realiza un fuzzing recursivo de rutas en base a diccionario para despues pasar a través de las rutas disponibles una serie de test de seguridad.

El resultado es mostrado en un mapa web interactivo. El output está pensado como punto de inicio para una auditoría profesional de aplicaciones web.

`skipfish -m 10 -LY -S /usr/share/skipfish/dictionaries/complete.wl -o ./salida.txt -u [URL]`

## **ANÁLISIS DE SERVIDORES**

### **NIKTO**

Nikto no es un fuzzer en si mismo pero si que analiza el servidor web de la victima para encontrar fallas de seguridad comunes en los sistemas actuales.

`nikto -h http://target.net/`

### **WORDPRESS SCAN**

Es una herramienta automatizada y especializada en el analisis de vulnerabilidades en servidores que están corriendo un sistema de gestión de contenido **Wordpress**.

`wpscan -u 10.10.10.10/wp/`

