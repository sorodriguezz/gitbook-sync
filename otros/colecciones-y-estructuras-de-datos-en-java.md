---
description: >-
  colecciones más comunes, organizadas por su implementación, tipo de colección,
  orden, permitir o no duplicados, complejidad de operaciones y otros rasgos
  clave.
---

# Colecciones y estructuras de datos en Java

## Colecciones de tipo **List**

### `ArrayList`

* **Orden:** Mantiene el orden de inserción.
* **Duplicados:** Permitidos.
* **Implementación Interna:** Basada en un **array dinámico** que se redimensiona al crecer la colección.
* **Complejidad Principal:**
  * `get(index)`: O(1) (acceso aleatorio rápido)
  * `add`/`remove` en el **medio**: O(n) (desplaza elementos)
* **Propiedades:**
  * Muy buena para **lecturas** y acceso por índice.
  * **Inserciones y eliminaciones** en posiciones intermedias son más costosas al requerir desplazamientos de elementos.

### `LinkedList`

* **Orden:** Mantiene el orden de inserción.
* **Duplicados:** Permitidos.
* **Implementación Interna:** **Lista doblemente enlazada**.
* **Complejidad Principal:**
  * `get(index)`: O(n) (requiere recorrer los nodos)
  * `add`/`remove` en extremos: O(1)
* **Propiedades:**
  * Muy eficiente para **inserción/borrado** en cabeza o cola.
  * Acceso aleatorio lento si se requiere un índice específico.

### `Vector`

* **Orden:** Mantiene el orden de inserción.
* **Duplicados:** Permitidos.
* **Implementación Interna:** Array dinámico **sincronizado**.
* **Complejidad Principal:**
  * `get(index)`: O(1)
  * `add`/`remove` en el medio: O(n)
* **Propiedades:**
  * **Thread-safe** (sincronizado), pero más lento que `ArrayList` en entornos de un solo hilo.
  * Considerado “legado”, hoy en día se prefiere usar colecciones de la librería `java.util.concurrent` o `ArrayList` con bloqueos externos cuando se requiere concurrencia.

### `Stack`

* **Tipo:** Aunque es una subclase de `Vector`, se considera una estructura LIFO (Last-In-First-Out).
* **Orden:** LIFO.
* **Duplicados:** Permitidos.
* **Implementación Interna:** Extiende `Vector` (por tanto, sincronizado).
* **Complejidad Principal:**
  * `push`/`pop`: O(1)
* **Propiedades:**
  * Uso clásico como **pila**.
  * API está algo deprecada en favor de `Deque` (por ejemplo, `ArrayDeque`) para implementar pilas.

***

## Colecciones de tipo **Queue** (Colas)

### `PriorityQueue`

* **Orden:** Orden natural de los elementos (usando `Comparable`) o un **Comparator** personalizado.
* **Duplicados:** Permitidos.
* **Implementación Interna:** Basada en un **montículo binario (binary heap)**.
* **Complejidad Principal:**
  * `insert`/`remove`/`poll`: O(log n)
* **Propiedades:**
  * No mantiene estrictamente el orden de inserción, sino que **prioriza** según el criterio definido.
  * Ideal para gestionar colas con prioridad (ej. algoritmos de Dijkstra, programación de tareas).

### `ArrayDeque`

* **Orden:** Mantiene el orden de inserción, pero permite inserción/extracción en ambos extremos (doble cola).
* **Duplicados:** Permitidos.
* **Implementación Interna:** **Array redimensionable**.
* **Complejidad Principal:**
  * `add/remove` en ambos extremos: O(1)
* **Propiedades:**
  * Más rápida que `LinkedList` para operaciones de cola (head/tail).
  * Se recomienda para **pilas y colas** en vez de `Stack` y `LinkedList`.

### `LinkedList` (como Queue)

* Aunque se trate como lista, `LinkedList` implementa `Deque` y puede comportarse como **doble cola**.
* **Orden:** Inserción.
* **Duplicados:** Permitidos.
* **Implementación Interna:** Lista doblemente enlazada.
* **Complejidad Principal:**
  * `offer`/`poll` en extremos: O(1)
* **Propiedades:**
  * También se puede utilizar como **pila** (LIFO) o **cola** (FIFO).

***

## Colecciones de tipo **Set**

### `HashSet`

* **Orden:** **Sin** orden definido (no garantiza el orden de los elementos).
* **Duplicados:** **No** (por definición de Set).
* **Implementación Interna:** **Tabla hash**.
* **Complejidad Principal:**
  * `add`/`remove`/`contains`: O(1) promedio
* **Propiedades:**
  * Muy eficiente en búsquedas e inserciones.
  * El orden de iteración es impredecible.

### `LinkedHashSet`

* **Orden:** Mantiene el **orden de inserción**.
* **Duplicados:** No.
* **Implementación Interna:** **Tabla hash** + **lista enlazada** para el orden.
* **Complejidad Principal:**
  * `add`/`remove`/`contains`: O(1) promedio
* **Propiedades:**
  * Combina la velocidad de `HashSet` con la predictabilidad del orden de inserción.
  * Ligero overhead adicional por la lista.

### `TreeSet`

* **Orden:** Ordenado según **orden natural** (`Comparable`) o `Comparator` proporcionado.
* **Duplicados:** No.
* **Implementación Interna:** **Árbol Rojo-Negro (Red-Black Tree)**.
* **Complejidad Principal:**
  * `add`/`remove`/`contains`: O(log n)
* **Propiedades:**
  * Permite métodos como `headSet`, `tailSet`, `subSet` para rangos ordenados.
  * Ideal cuando se necesita un **conjunto ordenado**.

***

## Colecciones de tipo **Map**

### `HashMap`

* **Orden:** Sin orden de keys específico.
* **Claves duplicadas?** No (las keys deben ser únicas), pero **valores** sí (pueden repetirse).
* **Implementación Interna:** **Tabla hash**.
* **Complejidad Principal:**
  * `get`/`put`/`remove`: O(1) promedio
* **Propiedades:**
  * Eficiente para búsquedas y almacenamiento.
  * Iterar sobre él no respeta el orden de inserción ni ningún orden natural.

### `LinkedHashMap`

* **Orden:** Mantiene el **orden de inserción** o, opcionalmente, el orden de acceso (si se habilita un modo LRU).
* **Claves duplicadas?** No, valores sí.
* **Implementación Interna:** **Tabla hash** + **lista doblemente enlazada**.
* **Complejidad Principal:**
  * `get`/`put`/`remove`: O(1) promedio
* **Propiedades:**
  * Útil cuando se desea un **orden predecible** de iteración.
  * Suelen usarse para caches simples basadas en LRU.

### `TreeMap`

* **Orden:** **Ordenado** por la clave (natural o `Comparator`).
* **Claves duplicadas?** No, valores sí.
* **Implementación Interna:** **Árbol Rojo-Negro**.
* **Complejidad Principal:**
  * `get`/`put`/`remove`: O(log n)
* **Propiedades:**
  * Soporta operaciones de rango como `subMap`, `headMap`, `tailMap`.
  * Perfecto cuando se necesitan **maps ordenados** (ej. para rangos de claves).

### `Hashtable`

* **Orden:** Sin orden definido.
* **Claves duplicadas?** No, valores sí.
* **Implementación Interna:** **Hash table** **sincronizada**.
* **Complejidad Principal:**
  * O(1) promedio para `get/put/remove`.
* **Propiedades:**
  * Es **thread-safe**, pero más **lento** que `HashMap`.
  * Considerado “legado”, se recomienda usar `ConcurrentHashMap` en entornos concurrentes.

***

## Colecciones Concurrentes (Thread-Safe)

### `ConcurrentHashMap`

* **Orden:** No definido.
* **Claves duplicadas?** No, valores sí.
* **Implementación Interna:** **Tabla hash segmentada** que reduce la contención.
* **Complejidad Principal:**
  * O(1) para operaciones principales en condiciones promedio.
* **Propiedades:**
  * Diseñado para alto rendimiento en **multithreading**.
  * Permite iteraciones más seguras sin necesidad de bloquear toda la estructura.

### `CopyOnWriteArrayList`

* **Orden:** Mantiene orden de inserción.
* **Duplicados:** Permitidos.
* **Implementación Interna:** Basada en un **array** que se **copia** cada vez que se modifican los datos.
* **Complejidad Principal:**
  * Lecturas: O(1) (acceso por índice).
  * Escritas (`add`, `remove`): O(n), ya que crea una copia.
* **Propiedades:**
  * **Thread-safe** y altamente eficiente en escenarios de **muchas lecturas** y **pocas escrituras**.
  * Usado frecuentemente en suscripciones, listeners, etc.

### `ConcurrentLinkedQueue`

* **Orden:** FIFO (cola).
* **Duplicados:** Permitidos.
* **Implementación Interna:** Basada en **lista enlazada** sin bloqueos (lock-free).
* **Complejidad Principal:**
  * `enqueue`/`dequeue`: O(1) en condiciones normales.
* **Propiedades:**
  * Orientada a **alto rendimiento** en colas multi-productor/multi-consumidor.
  * Evita bloqueos explícitos, usando CAS (compare-and-swap).

***

## Comparativ

| **Colección**             | **Tipo**    | **Orden**                       | **Complejidad (básica)**               | **Propósito Principal**                      |
| ------------------------- | ----------- | ------------------------------- | -------------------------------------- | -------------------------------------------- |
| **ArrayList**             | List        | Inserción                       | get: O(1), add/remove en medio: O(n)   | Uso general, acceso aleatorio rápido         |
| **LinkedList**            | List/Deque  | Inserción                       | add/remove en extremos: O(1)           | Óptima para inserciones/borrados en extremos |
| **Vector**                | List        | Inserción                       | Similar a ArrayList, pero sincronizado | Legado, thread-safe                          |
| **Stack**                 | List (LIFO) | LIFO                            | push/pop: O(1)                         | Estructura de pila, basada en Vector         |
| **PriorityQueue**         | Queue       | Orden natural/custom comparator | insert/remove: O(log n)                | Cola con prioridad (heap)                    |
| **ArrayDeque**            | Queue/Deque | Inserción                       | add/remove en extremos: O(1)           | Reemplazo recomendado para Stack o Queue     |
| **HashSet**               | Set         | Sin orden                       | O(1) promedio                          | Conjunto rápido, sin orden                   |
| **LinkedHashSet**         | Set         | Inserción                       | O(1) promedio                          | Set con orden predecible                     |
| **TreeSet**               | Set         | Ordenado                        | O(log n)                               | Conjunto ordenado por comparador             |
| **HashMap**               | Map         | Sin orden                       | O(1) promedio                          | Clave-valor rápido, sin orden                |
| **LinkedHashMap**         | Map         | Inserción (o acceso con LRU)    | O(1) promedio                          | Iteración ordenada; LRU caching              |
| **TreeMap**               | Map         | Ordenado por clave              | O(log n)                               | Mapa con acceso ordenado por clave           |
| **Hashtable**             | Map         | Sin orden, sincronizado         | O(1) promedio                          | Legado, thread-safe pero lento               |
| **ConcurrentHashMap**     | Concurrent  | Sin orden                       | O(1) promedio                          | Map concurrente de alto rendimiento          |
| **CopyOnWriteArrayList**  | Concurrent  | Inserción                       | Lectura: O(1), escritura: O(n)         | Ideal para más lecturas que escrituras       |
| **ConcurrentLinkedQueue** | Concurrent  | FIFO                            | enqueue/dequeue: O(1)                  | Cola lock-free para entornos multihilo       |

* **Listas**:
  * Usa `ArrayList` para accesos aleatorios rápidos y **pocas inserciones en el medio**.
  * Usa `LinkedList`/`ArrayDeque` para inserciones/borrados eficientes en extremos o cuando necesites una estructura de cola/pila.
* **Conjuntos** (`Set`):
  * `HashSet` para una **búsqueda rápida** y sin orden.
  * `LinkedHashSet` si requieres mantener el orden de inserción.
  * `TreeSet` si necesitas un conjunto **ordenado** (rangos, mayor/menor, etc.).
* **Mapas** (`Map`):
  * `HashMap` es la opción más común por su **velocidad**.
  * `LinkedHashMap` si deseas preservar el orden de inserción o implementar un cache LRU.
  * `TreeMap` cuando necesites que las claves estén **ordenadas**.
* **Colas** (`Queue`):
  * `PriorityQueue` para colas con **prioridad**.
  * `ArrayDeque` o `LinkedList` como cola FIFO simple o pila LIFO.
  * `ConcurrentLinkedQueue` en entornos multihilo con alto rendimiento.
* **Concurrencia**:
  * `ConcurrentHashMap` para mapas en escenarios multithreading sin bloqueos centrales.
  * `CopyOnWriteArrayList` en caso de **muchas lecturas** y muy **pocas escrituras**.
  * Evitar `Vector`, `Stack`, `Hashtable` en nuevos desarrollos (son clases legado); preferir alternativas concurrentes modernas (`ConcurrentHashMap`, `ArrayDeque`, etc.).
