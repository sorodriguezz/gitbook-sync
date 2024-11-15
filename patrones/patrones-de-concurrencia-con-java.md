# Patrones de Concurrencia con Java

¡Bienvenido a esta clase completa sobre **Patrones de Concurrencia** en Java! En esta guía exhaustiva, exploraremos en profundidad los patrones de concurrencia más importantes, cómo aplicarlos en Java y cómo pueden mejorar la eficiencia y seguridad de tus aplicaciones multihilo.

***

### Tabla de Contenidos

1. Introducción a la Concurrencia en Java
   * 1.1. ¿Qué es la Concurrencia?
   * 1.2. Desafíos de la Programación Concurrente
   * 1.3. Modelos de Concurrencia
2. Patrones de Concurrencia en Java
   * 2.1. Thread Pool (Grupo de Hilos)
   * 2.2. Producer-Consumer (Productor-Consumidor)
   * 2.3. Future y CompletableFuture
   * 2.4. Read-Write Lock (Bloqueo Lectura-Escritura)
   * 2.5. Monitor Object
   * 2.6. Immutable Object (Objeto Inmutable)
   * 2.7. Semaphore (Semáforo)
   * 2.8. Barrier (Barrera)
   * 2.9. Fork/Join
   * 2.10. Active Object
3. Implementación de Patrones de Concurrencia en Java
   * 3.1. Ejemplos Prácticos
   * 3.2. Buenas Prácticas
4. Consideraciones de Seguridad y Rendimiento
   * 4.1. Evitar Condiciones de Carrera
   * 4.2. Deadlocks y Livelocks
   * 4.3. Uso Eficiente de Recursos
5. Herramientas y Librerías para Concurrencia en Java
   * 5.1. Java Concurrency API
   * 5.2. Librerías Externas
6. Conclusión
   * 6.1. Pasos Siguientes
   * 6.2. Recursos Adicionales

***

## 1. Introducción a la Concurrencia en Java

### 1.1. ¿Qué es la Concurrencia?

La **concurrencia** es la capacidad de un programa para ejecutar varias tareas simultáneamente, mejorando el rendimiento y la capacidad de respuesta. En Java, esto se logra mediante el uso de **hilos** (threads).

### 1.2. Desafíos de la Programación Concurrente

* **Condiciones de Carrera**: Ocurren cuando múltiples hilos acceden y modifican datos compartidos sin la sincronización adecuada.
* **Deadlocks**: Situaciones donde dos o más hilos esperan indefinidamente por recursos bloqueados por el otro.
* **Visibilidad y Atomicidad**: Problemas relacionados con la correcta lectura y escritura de variables compartidas.

### 1.3. Modelos de Concurrencia

* **Multithreading**: Uso de múltiples hilos dentro de un proceso.
* **Actor Model**: Comunicación entre entidades independientes a través de mensajes.
* **Fork/Join**: Divide tareas grandes en subtareas más pequeñas que pueden ejecutarse en paralelo.

***

## 2. Patrones de Concurrencia en Java

### 2.1. Thread Pool (Grupo de Hilos)

**Definición**

Un **Thread Pool** es un conjunto de hilos que se mantienen listos para ejecutar tareas, reutilizando hilos en lugar de crear nuevos para cada tarea.

**Implementación en Java**

Utiliza el framework `java.util.concurrent.ExecutorService`.

**Ejemplo:**

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(5); // Pool de 5 hilos

        for (int i = 0; i < 10; i++) {
            Runnable worker = new WorkerThread("Tarea " + i);
            executor.execute(worker);
        }
        executor.shutdown();
    }
}

class WorkerThread implements Runnable {
    private String mensaje;

    public WorkerThread(String mensaje) {
        this.mensaje = mensaje;
    }

    public void run() {
        System.out.println(Thread.currentThread().getName() + " ejecutando " + mensaje);
        // Simular trabajo
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

**Ventajas**

* **Eficiencia**: Evita el costo de creación y destrucción de hilos.
* **Control**: Limita el número de hilos activos.

### 2.2. Producer-Consumer (Productor-Consumidor)

**Definición**

El patrón **Producer-Consumer** separa el proceso que produce datos del que los consume, coordinando el acceso a una cola compartida.

**Implementación en Java**

Utiliza estructuras de datos thread-safe como `BlockingQueue`.

**Ejemplo:**

```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

public class ProducerConsumerExample {
    public static void main(String[] args) {
        BlockingQueue<Integer> cola = new LinkedBlockingQueue<>(5);

        Thread productor = new Thread(new Productor(cola));
        Thread consumidor = new Thread(new Consumidor(cola));

        productor.start();
        consumidor.start();
    }
}

class Productor implements Runnable {
    private BlockingQueue<Integer> cola;

    public Productor(BlockingQueue<Integer> cola) {
        this.cola = cola;
    }

    public void run() {
        int valor = 0;
        try {
            while (true) {
                cola.put(valor);
                System.out.println("Productor produjo: " + valor);
                valor++;
                Thread.sleep(1000);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class Consumidor implements Runnable {
    private BlockingQueue<Integer> cola;

    public Consumidor(BlockingQueue<Integer> cola) {
        this.cola = cola;
    }

    public void run() {
        try {
            while (true) {
                int valor = cola.take();
                System.out.println("Consumidor consumió: " + valor);
                Thread.sleep(1500);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

**Ventajas**

* **Desacoplamiento**: Productores y consumidores funcionan independientemente.
* **Buffering**: Maneja diferencias en la velocidad de producción y consumo.

### 2.3. Future y CompletableFuture

**Definición**

Permiten realizar operaciones asíncronas y obtener resultados futuros, facilitando la programación concurrente sin bloquear hilos.

**Implementación en Java**

**Uso de Future:**

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class FutureExample {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newSingleThreadExecutor();

        Future<Integer> futuro = executor.submit(new Callable<Integer>() {
            public Integer call() throws Exception {
                Thread.sleep(2000);
                return 42;
            }
        });

        System.out.println("Realizando otras tareas...");
        Integer resultado = futuro.get(); // Espera hasta que el resultado esté disponible
        System.out.println("Resultado: " + resultado);

        executor.shutdown();
    }
}
```

**Uso de CompletableFuture:**

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
    public static void main(String[] args) throws Exception {
        CompletableFuture<Integer> futuro = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) { }
            return 42;
        });

        futuro.thenAccept(resultado -> System.out.println("Resultado: " + resultado));

        System.out.println("Realizando otras tareas...");
        Thread.sleep(3000); // Esperar para ver el resultado
    }
}
```

**Ventajas**

* **No Bloqueante**: Permite continuar la ejecución sin esperar el resultado inmediato.
* **Composición**: Combinar y encadenar operaciones asíncronas.

### 2.4. Read-Write Lock (Bloqueo Lectura-Escritura)

**Definición**

Permite que múltiples hilos lean un recurso compartido simultáneamente, pero solo permite la escritura exclusiva.

**Implementación en Java**

Utiliza `ReentrantReadWriteLock` de `java.util.concurrent.locks`.

**Ejemplo:**

```java
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ReadWriteLockExample {
    private ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
    private int valor = 0;

    public void leer() {
        rwLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + " leyó: " + valor);
            Thread.sleep(1000);
        } catch (InterruptedException e) { }
        finally {
            rwLock.readLock().unlock();
        }
    }

    public void escribir(int nuevoValor) {
        rwLock.writeLock().lock();
        try {
            valor = nuevoValor;
            System.out.println(Thread.currentThread().getName() + " escribió: " + valor);
            Thread.sleep(1000);
        } catch (InterruptedException e) { }
        finally {
            rwLock.writeLock().unlock();
        }
    }
}
```

**Ventajas**

* **Mayor Concurrencia**: Permite lecturas concurrentes.
* **Seguridad**: Protege contra escrituras concurrentes.

### 2.5. Monitor Object

**Definición**

Encapsula la sincronización dentro de un objeto, controlando el acceso a sus métodos y variables.

**Implementación en Java**

Utiliza métodos `synchronized` y bloques de sincronización.

**Ejemplo:**

```java
public class Contador {
    private int cuenta = 0;

    public synchronized void incrementar() {
        cuenta++;
    }

    public synchronized int obtenerValor() {
        return cuenta;
    }
}
```

**Ventajas**

* **Encapsulación**: La sincronización está contenida dentro del objeto.
* **Simplicidad**: Facilita la gestión de la sincronización.

### 2.6. Immutable Object (Objeto Inmutable)

**Definición**

Un objeto que, una vez creado, no puede ser modificado. Es inherentemente thread-safe.

**Implementación en Java**

Declarar clases con campos `final` y sin métodos que modifiquen el estado.

**Ejemplo:**

```java
public final class Punto {
    private final int x;
    private final int y;

    public Punto(int x, int y) {
        this.x = x;
        this.y = y;
    }

    // Métodos getters sin setters
    public int getX() { return x; }
    public int getY() { return y; }
}
```

**Ventajas**

* **Thread-Safe**: No requiere sincronización.
* **Simplicidad**: Reduce errores asociados con modificaciones concurrentes.

### 2.7. Semaphore (Semáforo)

**Definición**

Controla el acceso a un recurso compartido mediante un contador, permitiendo un número limitado de hilos simultáneos.

**Implementación en Java**

Utiliza `java.util.concurrent.Semaphore`.

**Ejemplo:**

```java
import java.util.concurrent.Semaphore;

public class SemaphoreExample {
    public static void main(String[] args) {
        Semaphore semaforo = new Semaphore(3); // Permite hasta 3 hilos

        for (int i = 0; i < 10; i++) {
            new Thread(new Tarea(semaforo)).start();
        }
    }
}

class Tarea implements Runnable {
    private Semaphore semaforo;

    public Tarea(Semaphore semaforo) {
        this.semaforo = semaforo;
    }

    public void run() {
        try {
            semaforo.acquire();
            System.out.println(Thread.currentThread().getName() + " accediendo al recurso");
            Thread.sleep(2000);
        } catch (InterruptedException e) { }
        finally {
            System.out.println(Thread.currentThread().getName() + " liberando el recurso");
            semaforo.release();
        }
    }
}
```

**Ventajas**

* **Control**: Limita el acceso a recursos.
* **Flexibilidad**: Puede utilizarse para implementar varios patrones de sincronización.

### 2.8. Barrier (Barrera)

**Definición**

Sincroniza un conjunto de hilos en un punto determinado, haciendo que todos esperen hasta que todos hayan alcanzado la barrera.

**Implementación en Java**

Utiliza `CyclicBarrier`.

**Ejemplo:**

```java
import java.util.concurrent.CyclicBarrier;

public class BarrierExample {
    public static void main(String[] args) {
        int numeroHilos = 3;
        CyclicBarrier barrera = new CyclicBarrier(numeroHilos, () -> {
            System.out.println("Todos los hilos han alcanzado la barrera");
        });

        for (int i = 0; i < numeroHilos; i++) {
            new Thread(new Tarea(barrera)).start();
        }
    }
}

class Tarea implements Runnable {
    private CyclicBarrier barrera;

    public Tarea(CyclicBarrier barrera) {
        this.barrera = barrera;
    }

    public void run() {
        try {
            System.out.println(Thread.currentThread().getName() + " realizando trabajo");
            Thread.sleep(1000);
            System.out.println(Thread.currentThread().getName() + " esperando en la barrera");
            barrera.await();
            System.out.println(Thread.currentThread().getName() + " continuando");
        } catch (Exception e) { e.printStackTrace(); }
    }
}
```

**Ventajas**

* **Coordinación**: Sincroniza hilos en puntos específicos.
* **Reutilizable**: `CyclicBarrier` puede reutilizarse después de liberar a los hilos.

### 2.9. Fork/Join

**Definición**

Permite dividir tareas grandes en subtareas más pequeñas que pueden ejecutarse en paralelo y luego combinar los resultados.

**Implementación en Java**

Utiliza el framework `java.util.concurrent.ForkJoinPool`.

**Ejemplo:**

```java
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ForkJoinPool;

public class ForkJoinExample {
    public static void main(String[] args) {
        ForkJoinPool pool = new ForkJoinPool();
        Fibonacci tarea = new Fibonacci(10);
        Integer resultado = pool.invoke(tarea);
        System.out.println("Resultado: " + resultado);
    }
}

class Fibonacci extends RecursiveTask<Integer> {
    private int n;

    public Fibonacci(int n) {
        this.n = n;
    }

    protected Integer compute() {
        if (n <= 1)
            return n;
        Fibonacci f1 = new Fibonacci(n - 1);
        f1.fork(); // Ejecutar en paralelo
        Fibonacci f2 = new Fibonacci(n - 2);
        return f2.compute() + f1.join();
    }
}
```

**Ventajas**

* **Eficiencia**: Aprovecha múltiples núcleos.
* **Escalabilidad**: Maneja grandes cantidades de trabajo dividido.

### 2.10. Active Object

**Definición**

Permite que los métodos de un objeto se ejecuten de forma asíncrona, utilizando colas y hilos separados para procesar solicitudes.

**Implementación en Java**

Puede implementarse utilizando colas y hilos personalizados.

**Ejemplo Simplificado:**

```java
public class ActiveObject {
    private final LinkedBlockingQueue<Runnable> cola = new LinkedBlockingQueue<>();

    public ActiveObject() {
        Thread thread = new Thread(() -> {
            while (true) {
                try {
                    Runnable tarea = cola.take();
                    tarea.run();
                } catch (InterruptedException e) { }
            }
        });
        thread.start();
    }

    public void metodoAsincrono(int parametro) {
        cola.offer(() -> {
            System.out.println("Procesando " + parametro);
            // Lógica del método
        });
    }
}
```

**Ventajas**

* **Desacoplamiento**: Separa la invocación de la ejecución.
* **Control de Concurrencia**: Centraliza la gestión de hilos.

***

## 3. Implementación de Patrones de Concurrencia en Java

### 3.1. Ejemplos Prácticos

**Caso de Estudio: Procesamiento de Archivos en Paralelo**

**Escenario:** Una aplicación que necesita procesar grandes cantidades de archivos de forma concurrente.

**Solución:**

* Utilizar un **Thread Pool** para gestionar los hilos.
* Aplicar el patrón **Producer-Consumer** para leer y procesar los archivos.
* Emplear **Semaphore** para limitar el número de archivos procesados simultáneamente.

**Implementación Simplificada:**

```java
public class FileProcessingApp {
    private ExecutorService executor;
    private Semaphore semaphore;

    public FileProcessingApp(int numHilos, int maxArchivosSimultaneos) {
        executor = Executors.newFixedThreadPool(numHilos);
        semaphore = new Semaphore(maxArchivosSimultaneos);
    }

    public void procesarArchivos(List<File> archivos) {
        for (File archivo : archivos) {
            executor.submit(() -> {
                try {
                    semaphore.acquire();
                    procesarArchivo(archivo);
                } catch (InterruptedException e) { }
                finally {
                    semaphore.release();
                }
            });
        }
        executor.shutdown();
    }

    private void procesarArchivo(File archivo) {
        // Lógica de procesamiento
        System.out.println("Procesando " + archivo.getName());
    }
}
```

### 3.2. Buenas Prácticas

* **Evitar Sincronización Excesiva**: Sincronizar solo lo necesario para mejorar el rendimiento.
* **Utilizar Estructuras Thread-Safe**: Aprovechar las clases de `java.util.concurrent`.
* **Preferir Objetos Inmutables**: Reduce la necesidad de sincronización.

***

## 4. Consideraciones de Seguridad y Rendimiento

### 4.1. Evitar Condiciones de Carrera

* **Sincronizar Accesos**: Utilizar `synchronized`, locks u otras técnicas para proteger datos compartidos.
* **Variables Atómicas**: Utilizar clases como `AtomicInteger` para operaciones atómicas.

**Ejemplo:**

```java
import java.util.concurrent.atomic.AtomicInteger;

public class ContadorAtomic {
    private AtomicInteger cuenta = new AtomicInteger(0);

    public void incrementar() {
        cuenta.incrementAndGet();
    }

    public int obtenerValor() {
        return cuenta.get();
    }
}
```

### 4.2. Deadlocks y Livelocks

* **Evitar Orden de Bloqueos Circular**: Adquirir locks en un orden consistente.
* **Utilizar Timeouts**: Establecer límites de tiempo al intentar adquirir locks.

#### 4.3. Uso Eficiente de Recursos

* **Liberar Recursos**: Asegurarse de liberar locks y semáforos en bloques `finally`.
* **No Crear Hilos Excesivamente**: Utilizar pools de hilos en lugar de crear hilos nuevos constantemente.

***

## 5. Herramientas y Librerías para Concurrencia en Java

### 5.1. Java Concurrency API

La **Java Concurrency API** ofrece un amplio conjunto de herramientas:

* **Executor Framework**: Gestiona la ejecución de tareas asíncronas.
* **Locks y Synchronizers**: Incluye `ReentrantLock`, `Semaphore`, `CountDownLatch`, etc.
* **Concurrent Collections**: Clases thread-safe como `ConcurrentHashMap`, `CopyOnWriteArrayList`.

### 5.2. Librerías Externas

* **Akka**: Implementa el modelo de actores para concurrencia y escalabilidad.
* **RxJava**: Programación reactiva para manejar flujos de datos asíncronos.
* **Quasar**: Fibers y actores ligeros para concurrencia avanzada.

***

## 6. Conclusión

### 6.1. Pasos Siguientes

* **Practicar**: Implementa estos patrones en proyectos personales.
* **Analizar Código Existente**: Observa cómo frameworks y aplicaciones utilizan estos patrones.
* **Mantenerse Actualizado**: La concurrencia en Java evoluciona; aprende sobre nuevas herramientas y técnicas.

### 6.2. Recursos Adicionales

* **Libro**: _Java Concurrency in Practice_ por Brian Goetz.
* **Documentación Oficial**: [Java Concurrency Utilities](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html)
* **Tutoriales en Línea**: Sitios como Baeldung, donde encontrarás guías detalladas.

***

**Ejercicio Práctico**

1. **Implementar una aplicación que calcule números primos** utilizando el patrón Fork/Join para paralelizar el cálculo.
2. **Crear un servicio web** que maneje solicitudes concurrentes utilizando un Thread Pool y sincronización adecuada.
3. **Simular un sistema bancario** donde múltiples hilos realizan transacciones y se asegura la consistencia de los saldos.

***

**Preguntas Frecuentes**

**1. ¿Cuál es la diferencia entre `synchronized` y los locks de `java.util.concurrent.locks`?**

* `synchronized` es una forma sencilla de lograr sincronización, pero es menos flexible. Los locks de `java.util.concurrent.locks` ofrecen más control, como intentar adquirir un lock con timeout o de manera no bloqueante.

**2. ¿Cómo puedo evitar condiciones de carrera?**

* Asegurando que las operaciones sobre datos compartidos sean atómicas o estén protegidas mediante sincronización adecuada.

**3. ¿Es siempre mejor usar objetos inmutables para concurrencia?**

* Los objetos inmutables son thread-safe por naturaleza, pero pueden no ser prácticos en todos los casos. Evalúa el equilibrio entre inmutabilidad y rendimiento según el contexto.
