---
description: >-
  Es fundamental controlar el acceso a recursos compartidos y coordinar la
  ejecución de múltiples hilos para evitar condiciones de carrera,
  inconsistencias de datos y bloqueos (deadlocks).
---

# Mecanismos de bloqueo de Java

## `synchronized` Blocks (Bloques sincronizados)

### **Uso**

* Se utiliza la palabra clave `synchronized` para bloquear (mutex) objetos o métodos y asegurar la **exclusión mutua**.
*   Ejemplo típico:

    ```java
    javaCopiarEditarpublic synchronized void increment() {
        counter++;
    }
    ```

    O bien:

    ```java
    javaCopiarEditarsynchronized(someObject) {
        // lógica crítica
    }
    ```

### **Rendimiento**

* **Moderado:** Adecuado para casos de baja contención.
* Cuando muchos hilos compiten por el mismo lock, puede haber esperas prolongadas.

### **Mejor para**

* **Sincronización sencilla** en aplicaciones con bajo nivel de concurrencia o escenarios de poca competencia.
* Métodos o bloques de código que necesitan un candado (lock) simple y semánticamente claro.

### **Ventajas/Desventajas**

* **Ventaja:** Fácil de leer y mantener.
* **Desventaja:** Menos flexible que otras alternativas (no se puede, por ejemplo, probar `tryLock` o interrumpir la espera).

***

## Lock API (`ReentrantLock`)

### **Uso**

* Ofrece **bloqueo explícito** con políticas de equidad (fairness), además de soportar métodos como `tryLock()` (para no bloquear indefinidamente) e **interrupciones** de hilos en espera.
*   Ejemplo de uso:

    ```java
    javaCopiarEditarprivate final ReentrantLock lock = new ReentrantLock();

    public void increment() {
        lock.lock();
        try {
            // sección crítica
            counter++;
        } finally {
            lock.unlock();
        }
    }
    ```

### **Rendimiento**

* **Alto:** Soporta un uso más fino (fine-grained) en aplicaciones muy concurridas.
* Mayor control sobre la concurrencia que `synchronized`, lo cual puede traducirse en mejor rendimiento si se implementa correctamente.

### **Mejor para**

* **Escenarios de alta concurrencia** donde se requiera mayor control que el proporcionado por `synchronized`.
* Aplicaciones que necesiten bloqueo **interrumpible** o tiempos de espera (timeouts) para no quedar bloqueadas permanentemente.

### **Ventajas/Desventajas**

* **Ventaja:** Permite `tryLock()`, `lockInterruptibly()`, configuración de equidad, etc.
* **Desventaja:** Se debe liberar el lock manualmente, lo que incrementa el riesgo de errores si no se gestiona bien (olvido de `unlock()`).

***

## `ReadWriteLock`

### **Uso**

* Ofrece dos tipos de locks:
  * **Read lock** (compartido): Varios lectores pueden acceder simultáneamente si no hay escritor activo.
  * **Write lock** (exclusivo): Solo un escritor a la vez.
*   Ejemplo (utilizando `ReentrantReadWriteLock`):

    ```java
    javaCopiarEditarprivate final ReadWriteLock rwLock = new ReentrantReadWriteLock();
    private final Lock readLock = rwLock.readLock();
    private final Lock writeLock = rwLock.writeLock();

    public void readData() {
        readLock.lock();
        try {
            // múltiples lectores
        } finally {
            readLock.unlock();
        }
    }

    public void writeData() {
        writeLock.lock();
        try {
            // sólo un escritor
        } finally {
            writeLock.unlock();
        }
    }
    ```

### **Rendimiento**

* **Alto**: Mejora el rendimiento en escenarios donde predominan las operaciones de lectura, pues permite concurrencia en la lectura mientras no haya escrituras.
* Si se hacen muchas escrituras, la ganancia es menor.

### **Mejor para**

* **Sistemas con frecuencia alta de lecturas** y pocas escrituras, como bases de datos en memoria, sistemas de caché, etc.

### **Ventajas/Desventajas**

* **Ventaja:** Permite mayor paralelismo en lecturas.
* **Desventaja:** Si las escrituras son frecuentes, puede convertirse en un bloqueo similar a `synchronized`, con una lógica más compleja.

***

## Atomic Variables

### **Uso**

* Proporcionan **operaciones atómicas** sobre tipos primitivos (ej.: `AtomicInteger`, `AtomicLong`) y algunos tipos de referencia (`AtomicReference`).
* No requieren locks explícitos; internamente usan instrucciones atómicas a nivel de CPU.
*   Ejemplo:

    ```java
    javaCopiarEditarAtomicInteger atomicCounter = new AtomicInteger(0);

    public void increment() {
        atomicCounter.incrementAndGet();
    }
    ```

### **Rendimiento**

* **Muy alto**, especialmente en operaciones simples como incrementos, comparaciones y actualizaciones (`compareAndSet`).
* Minimiza la sobrecarga del GC y evita bloqueos pesados.

### **Mejor para**

* **Aplicaciones de alto rendimiento** que necesiten contadores o actualizaciones muy rápidas (ej.: contadores de concurrencia).
* Escenarios donde se quiera evitar el costo de un lock en operaciones básicas.

### **Ventajas/Desventajas**

* **Ventaja:** Operaciones no bloqueantes, gran velocidad.
* **Desventaja:** Solo resuelve escenarios sencillos; si la lógica es más compleja que una simple operación atómica, se puede requerir un lock tradicional.

***

## `volatile` Keyword

### **Uso**

* Asegura la **visibilidad de cambios** en una variable compartida entre hilos de manera inmediata.
* No proporciona exclusión mutua. Simplemente **sincroniza** el valor para que todos los hilos vean siempre la versión más reciente.
*   Ejemplo:

    ```java
    javaCopiarEditarprivate volatile boolean flag = true;

    public void stop() {
        flag = false;  // se actualiza para todos los hilos
    }
    ```

### **Rendimiento**

* **Alto**: la lectura/escritura de la variable es levemente más costosa que una variable normal, pero mucho más rápida que un lock.
* No protege contra condiciones de carrera si se requieren actualizaciones compuestas (ej.: `flag++`).

### **Mejor para**

* Variables de estado que se consultan en bucles o checkeos (ej.: un boolean para detener un hilo).
* **Escenarios de visibilidad**: Cuando basta con que el cambio sea visto por todos los hilos sin operaciones atómicas complejas.

### **Ventajas/Desventajas**

* **Ventaja:** Muy simple de usar; excelente para flags de control.
* **Desventaja:** **No** ofrece exclusión mutua, por lo que no es útil para operaciones que requieran atomicidad compleja.

***

## `Semaphore`

### **Uso**

* Controla el acceso a un recurso compartido mediante **permisos**. Un `Semaphore` con N permisos permite que hasta N hilos accedan simultáneamente al recurso.
*   Ejemplo:

    ```java
    javaCopiarEditarSemaphore semaphore = new Semaphore(3); // 3 recursos disponibles

    public void accessResource() throws InterruptedException {
        semaphore.acquire();
        try {
            // acceder al recurso
        } finally {
            semaphore.release();
        }
    }
    ```

### **Rendimiento**

* **Moderado**: Equilibrado, ya que sí hay bloqueos y colas internas cuando los permisos están agotados.
* Adecuado para escenarios donde se necesita limitar la concurrencia, pero no se busca un lock estricto uno-a-uno.

### **Mejor para**

* **Gestionar el acceso a un número limitado de recursos** (por ejemplo, un pool de conexiones a base de datos).
* También se pueden usar semáforos con 0 permisos como una forma de señalización entre hilos.

### **Ventajas/Desventajas**

* **Ventaja:** Permite controlar la **cantidad** de hilos concurrentes (más flexible que un lock que solo permite uno).
* **Desventaja:** Configurar y utilizar incorrectamente puede causar deadlocks si no se gestionan bien las adquisiciones y liberaciones.

***

## `CountDownLatch`

### **Uso**

* Sincroniza uno o más hilos, haciendo que esperen hasta que un conjunto de operaciones (contadas) se completen.
* Se inicializa con un **conteo** y cada vez que se completa una tarea se llama a `countDown()`. Los hilos que llaman a `await()` quedan **bloqueados** hasta que el contador llegue a cero.
*   Ejemplo:

    ```java
    javaCopiarEditarCountDownLatch latch = new CountDownLatch(3);

    public void doTask() {
        // ...
        latch.countDown(); // indica que se completó una de las 3 tareas
    }

    public void waitForAll() throws InterruptedException {
        latch.await(); // se bloquea hasta que latch llegue a 0
        // una vez liberado, continuar
    }
    ```

### **Rendimiento**

* **Moderado:** El enfoque se basa en bloquear y desbloquear hilos, lo cual es razonable para la sincronización de eventos.

### **Mejor para**

* **Coordinar la finalización** de varias tareas en paralelo antes de proceder (ej.: arranque de servicios en una aplicación).
* Escenarios donde N tareas deben completarse antes de un paso siguiente.

### **Ventajas/Desventajas**

* **Ventaja:** Ideal para la orquestación de procesos o fases en sistemas concurrentes.
* **Desventaja:** Es de un solo uso (una vez que llega a 0 no se puede “recargar”); para reutilizar la lógica se suele recurrir a otros mecanismos como `CyclicBarrier`.

***

## Resumen

| **Mecanismo**           | **Uso Principal**                                                        | **Rendimiento** | **Ideal Para**                                                                                            |
| ----------------------- | ------------------------------------------------------------------------ | --------------- | --------------------------------------------------------------------------------------------------------- |
| **Synchronized Blocks** | Exclusión mutua sencilla con `synchronized`                              | Moderado        | Escenarios con baja contención, fácil de implementar                                                      |
| **Lock API**            | `ReentrantLock`, bloqueo explícito, `tryLock()`, fairness, etc.          | Alto            | Control fino en concurrencia, bloqueo interrumpible, uso en aplicaciones concurridas                      |
| **ReadWriteLock**       | Bloqueos diferenciados para lectura (compartida) y escritura (exclusiva) | Alto            | Sistemas con muchas lecturas, acceso concurrente optimizado                                               |
| **Atomic Variables**    | Operaciones atómicas sin lock (ej.: `AtomicInteger`)                     | Muy Alto        | Contadores, flags y actualizaciones muy rápidas; escenarios de alto rendimiento                           |
| **Volatile**            | Garantiza visibilidad inmediata entre hilos, **sin** exclusión mutua     | Alto            | Variables de control que se leen/escriben con poca lógica (ej.: flags)                                    |
| **Semaphore**           | Control de acceso concurrente con número de permisos                     | Moderado        | Limitar cuántos hilos acceden simultáneamente a un recurso (pools, etc.)                                  |
| **CountDownLatch**      | Sincroniza hilos hasta que un conjunto de tareas reduce el conteo a 0    | Moderado        | Esperar a que múltiples operaciones finalicen antes de continuar (inicializaciones, procesos en paralelo) |

* **Elegir el mecanismo adecuado** según la **lógica de concurrencia**:
  * Para **bloqueos simples**, `synchronized`.
  * Para **control fino**, `ReentrantLock`.
  * Para **muchas lecturas**, `ReadWriteLock`.
  * Para **actualizaciones primitivas**, `Atomic`.
  * Para **limitar recursos**, `Semaphore`.
  * Para **sincronizar fases** de ejecución, `CountDownLatch`.
* **Evitar bloqueos innecesarios**: La sobre-sincronización puede degradar el rendimiento.
* **Diseñar con cuidado**: Cada mecanismo de concurrencia añade **complejidad** y **posibilidad de errores** como deadlocks o starvation.
* **Pruebas exhaustivas**: La concurrencia requiere pruebas detalladas (unitarias, de estrés) para garantizar la robustez.
