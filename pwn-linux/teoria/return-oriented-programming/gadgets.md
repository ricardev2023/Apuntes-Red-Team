---
description: Controlar la ejecución con fragmentos de código
---

# GADGETS

Los Gadgets son pequeños fragmentos de código seguidos de una instrucción `ret`. Por ejemplo: `pop rdi; ret`.  Podemos manipular el `ret` de esto gadgets de tal manera que podemos encadenar muchos de ellos para hacer lo que queremos.

### Ejemplo

Vamos a suponer que el stack está así durante la ejecución de un gadget `pop rdi; ret`.

![](<../../../.gitbook/assets/1 (2).png>)

Lo que ocurre es bastante obvio. `0x10` pasa a `rdi` al estar en lo alto del stack durante la instrucción `pop rdi`. Una vez que ocurre el pop, el `rsp` se mueve:

![](<../../../.gitbook/assets/2 (2).png>)

Y puesto que `ret` es equivalente a `pop rip`, `0x5655576724` es movido al `rip`.

### Utilizando gadgets

Cuando sobreescribimos el **puntero de retorno**, sobreescribimos el valor al que apunta el `rsp`. Una vez el valor ha sido sacado del stack, el `rsp` apunta al **siguiente valor en el stack**. Pero nosotros tambien podemos sobreescribir el siguiente valor en el stack.

Digamos que para explotar un binario debemos utilizar un gadget pop rdi; ret para hacer introducir 0x100 en el rdi y luego saltar a flag(). Vamos a ejecutar paso por paso.&#x20;

![](<../../../.gitbook/assets/3 (2).png>)

Cuando el `rip` apunta a la instrucción `ret` original, el `rsp` está apuntado a la `dirección del gadget` que realiza un `pop`. Ahora el `rip` se mueve para apuntar al  `gadget` y el `rsp` se mueve a la `siguiente dirección de memoria`.

![](<../../../.gitbook/assets/4 (2).png>)

El `rsp` se mueve a `0x100`; el `rip` se mueve al `pop rdi`. Por tanto hacemos un pop y metemos `0x100` al `rdi`.

![](<../../../.gitbook/assets/5 (2).png>)

el `rsp` se mueve a la dirección de `flag()` y el `rip` se mueve al `ret del gadget`. El `ret` es ejecutado y **llamamos a la función flag().**

### En resumen

Esencialmente, si el gadget saca valores del stack (pop), simplemente coloque esos valores después. Si queremos hacer pop 0x10 y despues saltar a 0x16, debemos utilizar:

![](<../../../.gitbook/assets/6 (2).png>)

Tenga en cuenta que si tenemos varias instrucciones pop, podemos añadir varios valores:

![](<../../../.gitbook/assets/7 (1).png>)

{% hint style="info" %}
Utilizamos rdi en los ejemplos por que es el registro que almacena el primer argumento en la arquitectura de 64 bits.&#x20;

Por este motivo, el control de este registro por medio de gadgets es muy importante.
{% endhint %}

### Encontrando gadgets

Podemos utilizar la herramienta llamada ROPgadget para encontrar posibles gadgets:

```nasm
$ ROPgadget --binary vuln-64 | grep rdi

0x0000000000401096 : or dword ptr [rdi + 0x404030], edi ; jmp rax
0x00000000004011db : pop rdi ; ret
```
