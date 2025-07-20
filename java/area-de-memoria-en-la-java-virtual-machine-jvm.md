---
description: >-
  La JVM administra diversas áreas de memoria para almacenar objetos, datos de
  clases, información de métodos, y coordinar la ejecución de hilos.
---

# Área de memoria en la Java Virtual Machine (JVM)

## Heap

### **Ámbito (Scope)**

Compartido entre **todos los hilos**.

### **Propósito**

* Almacena los **objetos** creados por la aplicación (asignados con `new`).
* Gestionado por el **Garbage Collector**: cuando los objetos dejan de ser referenciados, el recolector de basura recupera la memoria.

### **Subdivisiones internas (en la mayoría de implementaciones)**

* **Eden Space:** Donde se asignan inicialmente los objetos recién creados.
* **Survivor Spaces:** Pequeñas áreas (generalmente dos) para objetos que “sobreviven” al primer ciclo de recolección.
* **Old Generation (Tenured):** Almacena objetos de **vida larga**, que se promueven desde la Young Generation (Eden + Survivor).

### **Importancia**

* **Principal área de memoria** para los datos dinámicos en tiempo de ejecución.
* El dimensionamiento del Heap y la configuración del recolector de basura influyen directamente en el rendimiento de la aplicación.

***

## Method Area

### **Ámbito (Scope)**

Compartido entre **todos los hilos**.

### **Propósito**

* Almacenar **metadata de clases**, incluyendo métodos estáticos, variables estáticas y el **bytecode** del propio método.
* A partir de Java 8, parte de su contenido se administra en el **Metaspace** (reemplazando la antigua PermGen).

### **Detalles clave**

* Contiene la información necesaria para la reflexión (Reflection) y para cargar clases en tiempo de ejecución.
* Incluye estructuras esenciales, como la definición de cada clase (nombre, superclase, interfaces implementadas).

***

## Runtime Constant Pool

### **Ámbito (Scope)**

Asociado a cada **Clase**, pero compartido en el sentido de que otros hilos pueden acceder a la misma clase cargada.

### **Propósito**

* Almacenar **constantes** y **literales** (por ejemplo, strings) usados en el código, así como referencias a métodos y campos.
* Permite acceder a estos datos de manera global sin repetir definiciones en cada objeto.

### **Detalles clave**

* Es parte de la Method Area o Metaspace en implementaciones modernas de la JVM.
* Cada clase tiene su propio Pool de Constantes, pero los literales de cadena suelen residir en un “String Pool” compartido (especialmente desde Java 7 y 8, donde pasaron parte a Heap y parte a Metaspace).

***

## JVM Stack

### **Ámbito (Scope)**

**Por hilo**: cada hilo de ejecución tiene su **propia** pila.

### **Propósito:**

* Almacena los marcos de pila (stack frames) asociados a cada **llamada de método**:
  * Variables locales, parámetros de método, valores intermedios
  * Dirección de retorno (cuándo el método finaliza)
* Crece y decrece a medida que los métodos se invocan y retornan.

### **Importancia**

* La velocidad de acceso es **muy alta** porque solo se mueve el puntero de pila.
* Los errores de recursión infinita suelen disparar un `StackOverflowError`.

***

## Native Stack

### **Ámbito (Scope)**

**Por hilo**, pero se utiliza para ejecutar código nativo (C/C++) a través de JNI (Java Native Interface).

### **Propósito**

* Ejecutar funciones escritas en código **nativo**, que no forma parte directa del bytecode Java.
* Separa la lógica de Java (JVM Stack) de la lógica específica de la plataforma nativa.

### **Detalles clave**

* Un uso inadecuado (por ejemplo, fugas de memoria en código C) puede producir errores difíciles de rastrear en la aplicación Java.

***

## Program Counter (PC) Register

### **Ámbito (Scope)**

**Por hilo**: cada hilo dispone de su propio contador de programa.

### **Propósito**

* Llevar el **registro de la instrucción** de bytecode que se está ejecutando en un momento dado.
* Facilita el cambio de contexto entre hilos (cada hilo reanuda la ejecución donde lo dejó).

### **Detalles clave**

* Si el hilo está ejecutando código nativo, el PC register puede ser indefinido (depende de la implementación).

***

## Code Cache

### **Ámbito (Scope)**

**Compartido** entre los hilos.

### **Propósito**

* Almacenar **código máquina** compilado por el **JIT (Just-In-Time)** Compiler.
* Permite optimizar el rendimiento al recompilar partes críticas de bytecode a instrucciones nativas más rápidas.

### **Detalles clave**

* La JVM utiliza la información de ejecución (profiling) para decidir qué métodos “just in time” compilar y así mejorar la velocidad.
* Si la Code Cache se llena, se puede degradar el rendimiento (la JVM deja de compilar de forma agresiva).

***

## Direct Memory (Off-Heap)

### **Ámbito (Scope)**

**Compartida** entre hilos (aunque la lógica de acceso debe sincronizarse externamente).

### **Propósito**

* Memoria asignada fuera del Heap tradicional, **usada para I/O de alto rendimiento** (ej. `ByteBuffer.allocateDirect()`).
* Evita parte del overhead de la recolección de basura y las copias de datos entre la memoria de la JVM y la nativa.

### **Detalles clave**

* No está controlada por el GC de forma directa; la liberación depende de referencias nativas o invocaciones explícitas.
* Puede reducir la latencia en operaciones de red o archivos grandes.

***

## Eden Space (Heap Subdivision)

### **Ámbito (Scope)**

**Compartido** dentro del Heap en la región joven (Young Generation).

### **Propósito**

* **Crear nuevos objetos**. Es la primera parada para casi todos los objetos que se instancian.
* Cuando se llena, ocurre un “Minor GC” (recolección menor) que limpia los objetos no referenciados y promociona los sobrevivientes a la zona Survivor.

### **Detalles clave**

* Mantener un **ratio** y tamaño adecuados de la Eden Space puede mejorar el rendimiento, evitando GC frecuentes o muy costosos.

***

## Survivor Space (Heap Subdivision)

### **Ámbito (Scope)**

**Compartido** dentro de la Young Generation.

### **Propósito**

* Almacenar los **objetos que sobrevivieron** a un Minor GC en Eden.
* Existen habitualmente **dos** Survivor Spaces (S0 y S1), entre las cuales se van intercambiando los objetos vivos tras cada recolección.

### **Detalles clave**

* Después de cierto número de sobrevivencias (edad del objeto), las instancias se **promueven a la Old Generation** para evitar un procesamiento repetido en la Young Gen.

***

## Old Generation (Heap Subdivision)

### **Ámbito (Scope)**

**Compartido** (Heap) en la región para objetos de larga vida.

### **Propósito**

* Almacena los **objetos promocionados** desde Young Gen que han demostrado tener una vida más prolongada.
* Los recolectores de basura (Major GC o Full GC) operan en esta región con menor frecuencia que en la Young Generation.

### **Detalles clave**

* Una **recolección en Old Gen** suele ser más costosa en términos de tiempo (dependiendo del GC elegido: Parallel, G1, ZGC, etc.).
* Configuraciones inadecuadas pueden producir **pausas largas** y `OutOfMemoryError` si no hay espacio suficiente para objetos promovidos.

***

### Resumen de las Áreas de Memoria en la JVM

| **Área**                     | **Ámbito**            | **Propósito**                                                                    |
| ---------------------------- | --------------------- | -------------------------------------------------------------------------------- |
| **Heap**                     | Compartida (hilos)    | Objetos e instancias (manejado por GC).                                          |
| **Method Area**              | Compartida (hilos)    | Metadata de clases, métodos estáticos, bytecode (parte de Metaspace en Java 8+). |
| **Runtime Constant Pool**    | Por clase, compartido | Constantes, literales y referencias a métodos/campos.                            |
| **JVM Stack**                | Por hilo              | Variables locales, marcos de ejecución de métodos (stack frames).                |
| **Native Stack**             | Por hilo              | Ejecución de código nativo (C/C++) vía JNI.                                      |
| **Program Counter (PC)**     | Por hilo              | Rastrea la instrucción actual ejecutada por el hilo.                             |
| **Code Cache**               | Compartida (hilos)    | Almacena el código nativo compilado por el JIT.                                  |
| **Direct Memory (Off-Heap)** | Compartida (hilos)    | Memoria externa al Heap para I/O de alto rendimiento.                            |
| **Eden Space**               | Subdivisión del Heap  | Asignación de nuevos objetos (Young Gen).                                        |
| **Survivor Space**           | Subdivisión del Heap  | Almacena objetos sobrevivientes tras el Minor GC.                                |
| **Old Generation**           | Subdivisión del Heap  | Objetos de larga vida, promocionados desde Young Gen.                            |

La JVM segmenta y organiza la memoria para **optimizar**:

1. **La creación y el uso de objetos** (Heap subdividido).
2. **La ejecución de métodos** (JVM Stack, PC Register).
3. **La carga de clases y compilación JIT** (Method Area, Code Cache).
4. **La interacción con código nativo** (Native Stack, Direct Memory).

Entender cada área permite:

* Ajustar parámetros de la JVM (tamaños de Heap, Metaspace, etc.) para mejorar el **rendimiento**.
* Detectar problemas de **fugas de memoria**, excesivo uso de CPU por GC, o **StackOverflowError**.
* Diseñar arquitecturas con una gestión de memoria **más eficiente**, especialmente en entornos de alta concurrencia y grandes volúmenes de datos.
