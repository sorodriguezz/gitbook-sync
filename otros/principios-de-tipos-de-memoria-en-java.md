---
description: >-
  En la JVM (Java Virtual Machine) existen diferentes regiones de memoria con
  propósitos y características específicas.
---

# Principios de tipos de memoria en Java

## Stack Memory

* **Uso principal:**
  * Almacena llamadas a métodos, variables locales y detalles específicos de las llamadas de función.
  * Cada hilo de ejecución (thread) tiene su propia pila (stack).
* **Velocidad de acceso:**
  * **Rápida**, pues se gestiona con un puntero que crece y decrece al entrar y salir de métodos.
  * No requiere recolección de basura.
* **¿Es gestionada por el Garbage Collector?**
  * **No.** Cuando un método termina, la memoria para sus variables locales se libera automáticamente al “retroceder” el puntero de la pila.
* **Mejor para:**
  * Datos **de vida muy corta**, variables temporales o parámetros de métodos.
  * Escenarios en los que la asignación y liberación sean rápidas y previsibles.

**Observación importante:** Si se produce un uso excesivo de pila (por ejemplo, en recursiones profundas), puede desencadenarse un `StackOverflowError`.

## Heap Memory

* **Uso principal:**
  * Almacena objetos **dinámicamente asignados** y todas las instancias de clase que persisten más allá del alcance de un único método.
  * Aquí residen la mayoría de los objetos creados con `new`.
* **Velocidad de acceso:**
  * **Moderada** en comparación con la pila, ya que la JVM necesita administrar la memoria libre y el Garbage Collector debe identificar y limpiar objetos que ya no se usan.
* **¿Es gestionada por el Garbage Collector?**
  * **Sí.** El GC se encarga de liberar los objetos que ya no tienen referencias activas.
*   **Mejor para:**

    * Objetos de vida **más larga** o cuya duración no está ligada a una sola llamada de función.
    * Escenarios donde se necesitan grandes estructuras de datos (colecciones, buffers, etc.).

    **Notas:**

    * Se subdivide en generaciones (Young Generation, Old Generation) según el algoritmo de GC que se utilice (ej.: G1, Parallel, ZGC, etc.).
    * Un uso excesivo de heap puede llevar a problemas de rendimiento o a `OutOfMemoryError` si la JVM se queda sin espacio.

## Metaspace

* **Uso principal:**
  * Almacena la **metadata** de clases, definiciones de métodos y variables estáticas.
  * En versiones anteriores de Java (pre-Java 8) existía la PermGen (Permanent Generation) con un propósito similar, pero a partir de Java 8 se sustituyó por Metaspace.
* **Velocidad de acceso:**
  * **Moderada**, normalmente gestionada con estructuras internas de la JVM para permitir la carga y descarga de clases.
* **¿Es gestionada por el Garbage Collector?**
  * **Sí**, aunque con distintas particularidades frente al heap tradicional. El GC puede limpiar información de clases no utilizadas si la clase se descargó (class unloading).
* **Mejor para:**
  * Aplicaciones con **mucho uso de reflexión** o con numerosos **componentes** y **clases cargadas dinámicamente** (ej. servidores de aplicaciones que cargan y descargan módulos).
  * Escenarios que requieren un control más flexible sobre la memoria de la metadata de las clases.

**Notas:**

* Es posible establecer límites máximos para Metaspace con banderas como `-XX:MaxMetaspaceSize` para evitar un crecimiento descontrolado.
* Si se llega a esos límites sin una configuración adecuada, puede producirse un `OutOfMemoryError: Metaspace`.

## Off-Heap Memory

* **Uso principal:**
  * Memoria **externa** al heap de la JVM, comúnmente utilizada por **DirectByteBuffer** en NIO (New I/O) para operaciones con archivos grandes o sockets.
  * Permite evitar la sobrecarga que puede generar el GC sobre objetos muy grandes.
* **Velocidad de acceso:**
  * **Más lenta** que la memoria en la pila o en el heap, dado que a menudo implica llamadas nativas (JNI) o acceso directo al sistema operativo.
  * Útil para reducir la presión sobre el GC, pero con cierto coste de complejidad y velocidad.
* **¿Es gestionada por el Garbage Collector?**
  * **No** de la forma tradicional. Si bien las referencias a los buffers pueden ser liberadas por el GC, el espacio asignado fuera del heap no se limpia automáticamente de la misma manera. Se suele liberar al cerrar el buffer o esperar a que el GC finalice la referencia nativa.
* **Mejor para:**
  * Procesamiento de **archivos muy grandes**, transmisión de datos o buffering de red donde se busca minimizar la interferencia con la recolección de basura.
  * Situaciones en las que se requiera más control granular sobre la asignación de memoria.

**Notas:**

* Debe utilizarse con cuidado, ya que un mal manejo puede ocasionar fugas de memoria a nivel nativo.
* Herramientas de profiling tradicionales (centradas en heap) pueden no informar correctamente el consumo de off-heap.

## Resumen de cada región de memoria en la JVM

| **Tipo de Memoria** | **Uso Principal**                                        | **Velocidad de Acceso** | **Recolección de Basura**  | **Mejor Para**                                                     |
| ------------------- | -------------------------------------------------------- | ----------------------- | -------------------------- | ------------------------------------------------------------------ |
| **Stack Memory**    | Variables locales, llamadas a métodos y detalles de pila | **Rápida**              | No (liberación automática) | Datos de corta duración, aplicaciones con pocas llamadas profundas |
| **Heap Memory**     | Objetos dinámicos, instancias de clase                   | **Moderada**            | Sí (managed by GC)         | Objetos de vida larga, grandes estructuras, etc.                   |
| **Metaspace**       | Metadata de clases, métodos, variables estáticas         | **Moderada**            | Sí, pero diferente al heap | Carga dinámica de clases, entornos que usan mucha reflexión        |
| **Off-Heap Memory** | Buffer nativo (ej. DirectByteBuffer), datos externos     | **Lenta**               | No (control manual)        | Procesamiento de datos grandes, minimizando impacto en el GC       |

* **Stack Memory** es ideal para datos temporales y ligados a la ejecución de métodos.
* **Heap Memory** es el área principal para almacenar objetos a largo plazo y está sujeta al Garbage Collector.
* **Metaspace** gestiona la metadata de las clases y reemplazó a PermGen en Java 8+, ofreciendo mayor flexibilidad.
* **Off-Heap Memory** se utiliza para optimizaciones específicas (por ejemplo, en IO masivo) y requiere un manejo cuidadoso ya que no es gestionada de la misma manera por el GC.
