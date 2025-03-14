---
description: >-
  Garbage Collector (GC) se encarga de administrar de forma automática la
  memoria en Java, liberando objetos que ya no están en uso.
---

# Recolector de basura (GC) en Java

## Serial GC

**Mejor para:** Aplicaciones pequeñas, de un solo hilo (single-threaded) o con baja carga de trabajo.

**Características principales:**

* Utiliza un solo hilo para realizar la recolección de basura.
* Es sencillo y consume menos recursos en ambientes con memoria limitada.
* Genera **pausas completas** (Stop-The-World) durante la recolección de basura, pero en aplicaciones pequeñas o con un solo hilo el impacto suele ser aceptable.

**Cuándo elegirlo:**

* Si la aplicación es muy sencilla o no requiere alta concurrencia.
* Usualmente se elige automáticamente en máquinas con poca RAM o CPUs con un solo núcleo.

**Configuración típica:**

```bash
bashCopiarEditar# Para habilitarlo explícitamente (antes de Java 9)
-XX:+UseSerialGC
```

## Parallel GC (también llamado Throughput GC)

**Mejor para:** Aplicaciones que necesitan **alto rendimiento** (high throughput) o procesamiento por lotes (batch processing).

**Características principales:**

* Usa **múltiples hilos** para la fase de recolección de basura, lo que reduce las pausas de la aplicación comparado con el Serial GC.
* Prioriza **maximizar el throughput** en lugar de minimizar la latencia.
* Adecuado para servidores con varios núcleos, ya que el GC se ejecuta en paralelo.

**Cuándo elegirlo:**

* Si la aplicación es intensiva en CPU y se beneficia de limpiar la memoria rápidamente con varios hilos.
* Cuando las pausas puntuales no son tan críticas y lo más importante es la capacidad de procesamiento general.

**Configuración típica:**

```bash
bashCopiarEditar-XX:+UseParallelGC
```

## G1 (Garbage First) GC

**Mejor para:** Aplicaciones grandes que necesitan un **equilibrio** entre rendimiento y pausas relativamente cortas.

**Características principales:**

* Divide el heap en **regiones** de tamaño similar. Esto permite priorizar la recolección en áreas que contienen más objetos “moribundos” (hence “Garbage First”).
* Ajuste (tuning) **adaptativo** que apunta a mantener las pausas de GC dentro de un objetivo de tiempo (por ejemplo, 200 ms).
* Ideal cuando se desea un buen compromiso entre baja latencia y alto rendimiento.

**Cuándo elegirlo:**

* Si la aplicación maneja heaps de tamaño mediano a grande y se busca un control más fino de las pausas.
* A partir de Java 9, es el GC **predeterminado** en la mayoría de distribuciones Java para heaps más grandes (aunque desde Java 9/10 se ha ido adoptando gradualmente).

**Configuración típica:**

```bash
bashCopiarEditar-XX:+UseG1GC
```

## ZGC

**Mejor para:** Aplicaciones de **ultra-baja latencia** o escenarios de tiempo real.

**Características principales:**

* Diseñado para manejar heaps de hasta **16 TB** de tamaño.
* Las pausas de GC pueden ser de **menos de 10 ms**, incluso con heaps muy grandes.
* Utiliza técnicas de punteros de colores (colored pointers) y carga de barrera (load barriers) para poder recolocar objetos sin detener la mayoría de las operaciones del programa.

**Cuándo elegirlo:**

* En aplicaciones donde incluso unas decenas de milisegundos de pausa resultan inaceptables.
* Ambientes financieros o de tiempo real, donde la latencia debe ser muy baja y predecible.

**Configuración típica:**

```bash
bashCopiarEditar-XX:+UseZGC
```

_(Disponible en versiones más recientes de Java 11+ en adelante, dependiendo de la distribución y la versión)._

## Shenandoah GC

**Mejor para:** Aplicaciones que requieren **baja latencia** y pausas casi imperceptibles, similar a ZGC pero con un enfoque distinto.

**Características principales:**

* Realiza **compactación concurrente**, reduciendo la necesidad de detener la aplicación por completo.
* Ofrece un comportamiento casi **sin pausas** (near-pause-less), aunque en la práctica puede presentarse alguna pausa muy corta.
* También diseñado para grandes heaps, aunque se comporta bien en una variedad de tamaños.

**Cuándo elegirlo:**

* Escenarios de latencia sensible en los que se prefiera la implementación de Shenandoah sobre ZGC (depende de la versión de OpenJDK y la configuración).
* Experimentos con GC de última generación en un entorno de desarrollo o pruebas de rendimiento.

**Configuración típica:**

```bash
bashCopiarEditar-XX:+UseShenandoahGC
```

_(Se introdujo inicialmente en OpenJDK para Java 12 y posteriores; su disponibilidad depende de la distribución)._

## Elección del GC Adecuado

La decisión de cuál recolector de basura usar depende de varios factores:

1. **Tamaño del heap:** Para heaps pequeños, el Serial GC o Parallel GC suele ser suficiente. Para heaps muy grandes, G1, ZGC o Shenandoah son más adecuados.
2. **Requisitos de latencia vs. throughput:**
   * Si necesitas **latencias muy bajas**, ZGC o Shenandoah son candidatas ideales.
   * Para aplicaciones donde las pausas puntuales no son tan críticas pero se necesita **alto rendimiento** global, Parallel GC puede ser preferible.
   * Para un **equilibrio** entre ambas, G1 es una elección segura.
3. **Requerimientos de hardware:** Algunos GC se benefician de múltiples núcleos (Parallel, G1, ZGC, Shenandoah). Otros como Serial GC trabajan de forma secuencial.
4. **Versión de Java y distribución:** No todos los GC están disponibles en versiones antiguas. Verifica la compatibilidad según la versión de Java que utilices.
