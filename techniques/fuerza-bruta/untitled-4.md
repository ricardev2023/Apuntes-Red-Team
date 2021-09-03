---
description: Explicación detallada de las Rainbow Tables.
---

# RAINBOW TABLES

## **RAINBOW TABLES**

Las **Rainbow** **Tables** son tablas de consulta que ofrecen un compromiso espacio-tiempo para obtener claves en texto simple a partir del resultado de una funcion de hash.

###  **OPHCRACK**

Ophcrack es una herramienta para crackear las contraseñas de Windows, Linux o Mac OS X basada en **tablas Rainbow.**

 Es una herramienta de entorno grafico bastante obsoleta debido a que se ha quedado **desactualizada desde Windows Vista**.

###  **HASHCAT**

**Hashcat** es una aplicación que permite la **recuperación de las contraseñas**, a partir del valor del **hash** para cada una de ellas. Si se tiene en cuenta el hecho de que éstas no suelen almacenarse como **texto plano** en la base de datos, sino como su hash, esta herramienta provee diversos métodos para la recuperación. Al ser esta aplicación una de las **más rápidas** para estas tareas.

**Hashcat** realiza justamente este procedimiento: toma como entrada un conjunto de palabras en texto plano y **calcula el hash** para ellas, comparando contra otro archivo que almacena los hashes de las contraseñas originales; todas las **coincidencias** serán las contraseñas recuperadas. Sin embargo, aquello que hace a hashcat la herramienta predilecta para este tipo de tareas es su capacidad de acelerar notablemente los cálculos mediante la utilización de **varios hilos ejecutándose en paralelo**. Además, va un paso más adelante al proveer **no sólo la opción de realizar los cálculos en CPU**, sino también utilizando el **mayor poder de procesamiento de las GPU** actuales, pasando del orden de los millones de hashes a miles de millones por segundo.

 **`hashcat -a 0 -m 1000 hash.txt`**

 **-a** → Es el modo de ataque, hay que mirar **hashcat -h** para comprobar la referencia del tipo de ataque que necesito.

 **-m** → Tipo de Hash, hay que mirar la referencia tambien

 **hash.txt** → Archivo con uno o varios hashes almacenados para descifrar.

## REFERENCIAS

[https://github.com/hashcat/hashcat](https://github.com/hashcat/hashcat)  
[https://github.com/luisgg/ophcrack](https://github.com/luisgg/ophcrack)

