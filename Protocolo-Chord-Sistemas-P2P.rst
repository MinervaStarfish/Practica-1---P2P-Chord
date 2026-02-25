https://rsted.info.ucl.ac.be/?theme=nature&n=9df020325d31fc0ea43430523bb274fd

=============================================================================
Protocolo Chord en Sistemas P2P: 
=============================================================================

:Asignatura: Arquitectura de Nube y Sistemas Distribuidos
:Carrera: Ingenieria de Sistemas e Informatica
:Facultad: Ingeniería en TIC
:Institución: Universidad Pontificia Bolivariana
:Profesor: Álvaro Enrique Ospina
:Estudiante: Mariana Osorio Rojas

Objetivo
========

En el presente taller nos proponemos:

* Explicar el problema de localización en redes P2P y cómo Chord lo resuelve.
* Construir y usar la finger table de un nodo.
* Determinar la ruta de enrutamiento (lookup) entre nodos.
* Describir el proceso de unión de nodos y actualización.
* Argumentar los trade-offs arquitectónicos y comunicar el análisis en un video breve.

Escenario Base
==============

Se define:

* :math:`2^m` donde :math:`m = 5`.
* **Espacio de identificadores:** 0 - 31.
* **Nodos activos:** 1, 4, 8, 14, 21, 28.
* **Regla de sucesor:** :math:`successor(k) = \text{primer nodo con ID} \ge k` (con *wrap-around* en el anillo).

Parte A — Finger Table (30%)
============================

A1. Construcción de la finger table para el nodo 8
---------------------------------------------------

Fórmulas utilizadas:

* :math:`start_i = (n + 2^{(i-1)}) \ mod{2^m}`
* :math:`finger[i] = successor(start_i)`
* :math:`n + 2^{(i-1)}, \quad i \ge 1`

Finger table para :math:`n= 8`:

+---+---------+--------------------+
| i | start-i | successor(start-i) |
+===+=========+====================+
| 1 | 9       | 14                 |
+---+---------+--------------------+
| 2 | 10      | 14                 |
+---+---------+--------------------+
| 3 | 12      | 14                 |
+---+---------+--------------------+
| 4 | 16      | 21                 |
+---+---------+--------------------+
| 5 | 24      | 28                 |
+---+---------+--------------------+

A2. Interpretación de los intervalos
------------------------------------

Los intervalos van desde el nodo de referencia y avanzan en saltos de potencias de 2: (:math:`2^{(i-1)}`). Esto asegura:

1. Cobertura primero en intervalos cercanos.
2. Luego cobertura en intervalos lejanos.
3. Crecimiento exponencial del alcance.

¿Por qué la búsqueda es :math:`O(\log N)`?
------------------------------------------

**Explicación verbal:**
Suponiendo que partimos desde un nodo hasta un nodo, existe una distancia. Usando *closest preceding finger*, por cada salto la distancia se reduce a la mitad o más.

**Demostración matemática completa:**
Cualquier número entero cumple que está entre dos potencias consecutivas de 2:

.. math::

   2^{(i-1)} \le d < 2^i

Donde :math:`d` es la distancia entre nodos. Luego de un paso, la nueva distancia :math:`d_n` es:

.. math::

   d_n = d - s

Donde :math:`s` es el salto realizado. Usando *closest preceding finger*: :math:`s \ge 2^{(i-1)}`. 

Entonces:

.. math::

   d_n \le d - 2^{(i-1)}

Como :math:`d < 2^i \implies \frac{d}{2} < 2^{(i-1)}`, sustituyendo tenemos:

.. math::

   d_n < d - \frac{d}{2} \implies d_n < \frac{d}{2}

Por tanto, cada salto reduce la distancia a menos de la mitad. 

Aplicando esto a :math:`t` saltos:

.. math::
 d_t \le \frac{d}{2^t}

Cuando:

.. math::

 \frac{d}{2^t} = 1

entonces: 

.. math::

 2^t = d

Lo que implica:

.. math::

 t = \log(d)

En el peor caso :math:`d \le N`, por tanto:

.. math::

 t = O(\log N)`.

Parte B — Lookup (25%)
======================

.. admonition:: Condición importante
   :class: warning

   Para usar un valor de la finger table como *closest preceding finger*, debe pertenecer estrictamente al intervalo :math:`(n, K)`.

B1. Búsqueda de K = 26 desde el nodo 8
--------------------------------------

* **Nodos activos:** 1, 4, 8, 14, 21, 28.
* **Nodo inicial:** 8.

Finger table para :math:`n= 8`:

+---+---------+--------------------+
| i | start-i | successor(start-i) |
+===+=========+====================+
| 1 | 9       | 14                 |
+---+---------+--------------------+
| 2 | 10      | 14                 |
+---+---------+--------------------+
| 3 | 12      | 14                 |
+---+---------+--------------------+
| 4 | 16      | 21                 |
+---+---------+--------------------+
| 5 | 24      | 28                 |
+---+---------+--------------------+

* **Intervalo válido:** (8, 26).
* **El mayor nodo dentro del intervalo es:** 21.
* **Entonces:** 8 → 21.

Finger table para :math:`n= 21`:

+---+---------+--------------------+
| i | start-i | successor(start-i) |
+===+=========+====================+
| 1 | 22      | 28                 |
+---+---------+--------------------+
| 2 | 23      | 28                 |
+---+---------+--------------------+
| 3 | 25      | 28                 |
+---+---------+--------------------+
| 4 | 29      | 1                  |
+---+---------+--------------------+
| 5 | 5       | 8                  |
+---+---------+--------------------+

* Su finger table no tiene nodos en (21, 26). Se pasa al sucesor inmediato.
* **Entonces:** 21 → 28.

Finger table para :math:`n= 28`:

+---+---------+--------------------+
| i | start-i | successor(start-i) |
+===+=========+====================+
| 1 | 29      | 1                  |
+---+---------+--------------------+
| 2 | 30      | 1                  |
+---+---------+--------------------+
| 3 | 0       | 1                  |
+---+---------+--------------------+
| 4 | 4       | 4                  |
+---+---------+--------------------+
| 5 | 12      | 14                 |
+---+---------+--------------------+

* **Resultado:** :math:`successor(26) = 28`. **Ruta:** 8 → 21 → 28.

B2. Búsqueda de K = 2 desde el nodo 4 (wrap-around)
---------------------------------------------------


* **Nodos activos:** 1, 4, 8, 14, 21, 28.
* **Sabemos:** :math:`successor(2) = 4`

**Usando estrictamente closest preceding finger:**

Finger table para :math:`n= 4`:

+---+---------+--------------------+
| i | start-i | successor(start-i) |
+===+=========+====================+
| 1 | 5       | 8                  |
+---+---------+--------------------+
| 2 | 6       | 8                  |
+---+---------+--------------------+
| 3 | 8       | 8                  |
+---+---------+--------------------+
| 4 | 12      | 14                 |
+---+---------+--------------------+
| 5 | 20      | 21                 |
+---+---------+--------------------+

* **Todos son mayores que 2**, sin embargo el intervalo (4,2) implica *wrap-around*: :math:`(4 \to 31) \cup (0 \to 2)`.

* El nodo 21 pertenece a ese intervalo: 4 → 21.

Finger table para :math:`n= 21`:

+---+---------+--------------------+
| i | start-i | successor(start-i) |
+===+=========+====================+
| 1 | 22      | 28                 |
+---+---------+--------------------+
| 2 | 23      | 28                 |
+---+---------+--------------------+
| 3 | 25      | 28                 |
+---+---------+--------------------+
| 4 | 29      | 1                  |
+---+---------+--------------------+
| 5 | 5       | 8                  |
+---+---------+--------------------+

* El nodo más cercano antes de 2 es 1. 
* **Entonces:** 21 → 1.

Finger table para :math:`n= 1`:

+---+---------+--------------------+
| i | start-i | successor(start-i) |
+===+=========+====================+
| 1 | 2       | 4                  |
+---+---------+--------------------+
| 2 | 3       | 4                  |
+---+---------+--------------------+
| 3 | 5       | 8                  |
+---+---------+--------------------+
| 4 | 9       | 14                 |
+---+---------+--------------------+
| 5 | 17      | 21                 |
+---+---------+--------------------+

* Ninguno cumple (1, 2) 
* **Se pasa al sucesor inmediato:** 1 → 4.
* **Resultado:** :math:`successor(2) = 4`. **Ruta:** 4 → 21 → 1 → 4.

Parte C — Join y Actualización (20%)
====================================

Estado inicial: 1 - 4 - 8 - 14 - 21 - 28. Se agrega el **nodo 18**.

C1. Proceso de unión
--------------------

1. El nodo 18 busca su sucesor: :math:`successor(18) = 21`.
2. Se identifica el predecesor: :math:`predecessor(21) = 14 \implies predecessor(18) = 14`.
3. El nodo 21 actualiza su predecesor a 18 mediante ``notify(18)``.
4. ``stabilize(14)`` detecta el cambio y actualiza :math:`successor(14) = 18`.

Finger table para :math:`n= 18`:

+---+---------+--------------------+
| i | start-i | successor(start-i) |
+===+=========+====================+
| 1 | 19      | 21                 |
+---+---------+--------------------+
| 2 | 20      | 21                 |
+---+---------+--------------------+
| 3 | 22      | 28                 |
+---+---------+--------------------+
| 4 | 26      | 28                 |
+---+---------+--------------------+
| 5 | 2       | 4                  |
+---+---------+--------------------+

Inicialmente ningún finger externo apunta a 18. Esto se corrige con fix_fingers().

C2. Algoritmos clave
--------------------

*   **stabilize(n):** Pregunta al sucesor por su predecesor para verificar consistencia.
*   **notify(n):** Sugiere a un nodo que :math:`n` podría ser su nuevo predecesor.
*   **fix_fingers():** Recalcula periódicamente las entradas de la finger table.

Parte D — Análisis (15%)
========================

¿Cuándo Chord es preferible a Cliente/Servidor?
-----------------------------------------------

Es preferible cuando se requiere **alta escalabilidad horizontal**, no se desea un **punto único de falla** y los nodos son dinámicos.

*   **C/S:** Sufre cuellos de botella, escalar es costoso y hay dependencia central.
*   **Chord:** El lookup es :math:`O(\log N)`, la carga se distribuye automáticamente y es resiliente.
*   **Sacrificios:** Simplicidad, consistencia fuerte inmediata y latencia determinística.

Impacto del churn
-----------------

La alta rotación (*churn*) provoca tablas desactualizadas, rutas más largas, mayor tráfico de mantenimiento y latencia. Chord funciona mejor con churn moderado.

Conclusión
==========

En el presente taller demostramos formalmente cómo Chord resuelve el problema de localización mediante hashing consistente. Verificamos la complejidad :math:`O(\log N)`, analizamos rutas con *wrap-around* y evaluamos la superioridad de su descentralización frente al modelo C/S en escenarios de gran escala.

Referencias
===========

*   **Stoica, I., et al. (2001).** `Chord: A scalable peer-to-peer lookup protocol <https://doi.org/10.1145/964723.383071>`_. Proceedings of ACM SIGCOMM.

→ Usada para entender el funcionamiento de la arquitectura.

*   **OpenAI. (2024).** ChatGPT (modelo GPT-5) [Modelo de lenguaje].

→ Usada para darle formato y una mejor sintaxis al documento.
