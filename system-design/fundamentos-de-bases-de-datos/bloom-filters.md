# Bloom Filters

¿Alguna vez te has preguntado cómo sabe **Netflix** qué series ya has visto? ¿O cómo **Amazon** evita mostrarte productos que ya has comprado?

El uso de una estructura de datos tradicional, como una  **tabla hash,**  para estas comprobaciones podría consumir una cantidad significativa de  **memoria** , especialmente con millones de usuarios y elementos.

En cambio, muchos sistemas se basan en una estructura de datos más eficiente: un  **filtro Bloom**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.32.13.png" alt="" width="563"><figcaption></figcaption></figure>

En este artículo, aprenderemos qué es un filtro Bloom, cómo funciona, cómo implementarlo en código, sus aplicaciones y limitaciones en el mundo real.

## 1. ¿Qué es un filtro Bloom? <a href="#id-1-what-is-a-bloom-filter" id="id-1-what-is-a-bloom-filter"></a>

Un  **filtro Bloom**  es una  **estructura de datos probabilística**  que le permite comprobar rápidamente si un elemento podría estar en un conjunto.

Es útil en situaciones en las que necesitas  **búsquedas rápidas**  y no quieres utilizar una gran cantidad de memoria, pero no te importa que haya  **falsos positivos** ocasionales .

**Componentes clave de un filtro Bloom**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.32.57.png" alt="" width="375"><figcaption></figcaption></figure>

1. **Matriz de bits** : El filtro Bloom consiste en una matriz de bits de tamaño fijo, inicializada a ceros. Esta matriz indica si ciertos elementos están en el conjunto.
2. **Funciones hash** : Para añadir o comprobar un elemento, un filtro Bloom utiliza múltiples funciones hash. Cada función hash asigna un elemento a un índice en la matriz de bits.

## 2. ¿Cómo funciona un filtro Bloom?

Un filtro Bloom funciona utilizando múltiples funciones hash para mapear cada elemento del conjunto a una matriz de bits.

**1. Inicialización:**

* Un filtro Bloom comienza con una matriz de bits vacía de tamaño  `m` (todos los bits se establecen inicialmente en 0).
* También requiere  `k` funciones hash independientes, cada una de las cuales asigna un elemento a una de las  `m` posiciones en la matriz de bits.

**2. Insertar un elemento:**

* Para insertar un elemento en el filtro Bloom, lo pasa a través de cada una de las  `k` funciones hash para obtener  `k` posiciones en la matriz de bits.
* Los bits en estas posiciones se establecen en 1.

**3. Comprobación de membresía:**

* Para comprobar si un elemento está en el conjunto, lo pasa nuevamente a través de las  `k` funciones hash para obtener  `k` posiciones.
* Si todos los bits en estas posiciones están establecidos en 1, se considera que el elemento está en el conjunto (aunque existe la posibilidad de que sea un falso positivo).
* Si algún bit en estas posiciones es 0, el elemento definitivamente no está en el conjunto.

## 3. Ejemplo: uso de un filtro Bloom para la comprobación de URL <a href="#id-3-example-using-a-bloom-filter-for-url-checking" id="id-3-example-using-a-bloom-filter-for-url-checking"></a>

Imagina que estás construyendo un rastreador web que necesita realizar un seguimiento de las URL que ya ha visitado.

En lugar de almacenar cada URL (lo que requeriría mucha memoria), decide utilizar un filtro Bloom.

**Paso 1: Configurar el filtro Bloom**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.33.59.png" alt="" width="375"><figcaption></figcaption></figure>

* **Inicializar una matriz de bits** : supongamos que nuestro filtro Bloom utiliza una matriz de bits de tamaño  **10** , inicialmente configurada en 0.
* **Elegir funciones hash** : En este ejemplo, usaremos dos funciones hash. Estas funciones hash toman una entrada (como una URL) y generan un índice en la matriz de bits.

**Paso 2: Agregar una URL al filtro Bloom**

Supongamos que queremos agregar la URL  `example.com` a nuestro filtro Bloom.

1. **La función hash 1**  genera un índice de  `3` para  `example.com`.
2. **La función hash 2**  genera un índice de  `7` para  `example.com`.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.34.48.png" alt="" width="375"><figcaption></figcaption></figure>

Establecemos los bits en los índices 3 y 7 de la matriz de bits en 1.

**Paso 3: Agregar otra URL**

Ahora, agreguemos otra URL,  `algomaster.io`.

1. **La función hash 1**  genera un índice de  `1` para `algomaster.io.`
2. **La función hash 2**  genera un índice de  `4` para `algomaster.io.`

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.35.13.png" alt="" width="375"><figcaption></figcaption></figure>

Establecemos los bits en los índices 1 y 4 de la matriz de bits en 1.

**Paso 4: Comprobación de una URL en el filtro Bloom**

Supongamos que queremos comprobar si  `example.com` ya está en el filtro Bloom.

1. **La función hash 1**  genera un índice  `3` para  `example.com`.
2. **La función hash 2**  genera un índice  `7` para  `example.com`.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.35.37.png" alt="" width="375"><figcaption></figcaption></figure>

Dado que ambos bits en los índices 3 y 7 están establecidos en 1, podemos decir que  `example.com` **probablemente esté en el conjunto**  (existe una pequeña posibilidad de un falso positivo).

**Paso 5: Comprobación de una URL inexistente**

Ahora, verifiquemos si  `nonexistent.com` está en el filtro Bloom.

1. **La función hash 1**  genera un índice  `2` para  `nonexistent.com`.
2. **La función hash 2**  genera un índice  `5` para  `nonexistent.com`.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.36.01.png" alt="" width="375"><figcaption></figcaption></figure>

Dado que los bits en los índices 2 y 5 son ambos 0, podemos decir con seguridad que  `nonexistent.com` **no está en el conjunto** .

## 4. Implementación de código (Java) <a href="#id-4-code-implementation-java" id="id-4-code-implementation-java"></a>

```java
import java.util.BitSet;
import java.util.function.Function;

class BloomFilter {
    private final BitSet bitArray;
    private final int size;
    private final Function<String, Integer>[] hashFunctions;

    public BloomFilter(int size, Function<String, Integer>... hashFunctions) {
        this.size = size;
        this.bitArray = new BitSet(size);
        this.hashFunctions = hashFunctions;
    }

    // Method to add an element to the Bloom Filter
    public void add(String item) {
        for (Function<String, Integer> hashFunction : hashFunctions) {
            int index = Math.abs(hashFunction.apply(item) % size);
            bitArray.set(index);
        }
    }

    // Method to check if an element is possibly in the Bloom Filter
    public boolean mightContain(String item) {
        for (Function<String, Integer> hashFunction : hashFunctions) {
            int index = Math.abs(hashFunction.apply(item) % size);
            if (!bitArray.get(index)) {
                return false; // If any bit is 0, the item is definitely not in the set
            }
        }
        return true; // All bits are 1, so the item is probably in the set
    }
}

public class Main {
    public static void main(String[] args) {
        // Create a Bloom Filter with 3 simple hash functions
        BloomFilter bloom = new BloomFilter(100,
            s -> s.hashCode(),
            s -> s.length() * 31,
            s -> (s.hashCode() * 17)
        );

        // Add elements
        bloom.add("apple");
        bloom.add("banana");

        // Test membership
        System.out.println("apple? " + bloom.mightContain("apple"));   // true
        System.out.println("banana? " + bloom.mightContain("banana")); // true
        System.out.println("grape? " + bloom.mightContain("grape"));   // maybe false
    }
}
```

**Explicación**

1. **BitSet** : Java  `BitSet` se utiliza para que la matriz de bits almacene y manipule bits de manera eficiente.
2. **Funciones hash** : El código utiliza dos funciones hash simples. Se pueden añadir funciones más complejas para una mejor distribución.
3. **add(String item)** : este método toma un elemento, aplica cada función hash y establece el bit correspondiente en la matriz de bits.
4. **mightContain(String item)** : Este método comprueba si un elemento podría estar en el conjunto comprobando si todos los bits correspondientes son 1. Si algún bit es 0, el elemento  **definitivamente no está en el conjunto** . Si todos los bits son 1, el elemento probablemente esté  **en el conjunto**  (con una pequeña probabilidad de un falso positivo).

## 5. Aplicaciones reales de los filtros Bloom <a href="#id-5-real-world-applications-of-bloom-filters" id="id-5-real-world-applications-of-bloom-filters"></a>

Los filtros Bloom se utilizan ampliamente en aplicaciones del mundo real donde la eficiencia del espacio y la velocidad son esenciales y los falsos positivos ocasionales son aceptables.

A continuación se muestran algunos escenarios comunes en los que se emplean filtros Bloom:

#### 1.  **Almacenamiento en caché web** <a href="#id-1-web-caching" id="id-1-web-caching"></a>

**Problema** : Los servidores web suelen almacenar en caché las páginas o recursos a los que se accede con frecuencia para mejorar los tiempos de respuesta. Sin embargo, revisar la caché de cada recurso podría resultar costoso y lento a medida que crece.

**Solución** : Se puede usar un filtro Bloom para comprobar rápidamente si una URL podría estar en la caché. Al recibir una solicitud, se comprueba primero el filtro Bloom. Si el filtro Bloom indica que la URL probablemente esté en la caché, se realiza una búsqueda en la caché.

Si indica que la URL “definitivamente no está en el caché”, el servidor omite la búsqueda en el caché y obtiene el recurso del almacenamiento principal, ahorrando tiempo y recursos.

#### 2. Filtrado de spam en sistemas de correo electrónico <a href="#id-2-spam-filtering-in-email-systems" id="id-2-spam-filtering-in-email-systems"></a>

**Problema** : los sistemas de correo electrónico necesitan filtrar los correos electrónicos no deseados sin tener que revisar constantemente grandes bases de datos de correo no deseado.

**Solución** : Un filtro Bloom puede almacenar hashes de direcciones de correo electrónico no deseado conocidas. Al recibir un nuevo correo electrónico, el filtro Bloom comprueba si la dirección del remitente podría estar en la lista de spam.

Esto permite que el sistema de correo electrónico determine rápidamente si es probable que un correo electrónico sea spam o legítimo.

#### 3. Bases de datos <a href="#id-3-databases" id="id-3-databases"></a>

**Problema** : Las bases de datos, especialmente las distribuidas, suelen necesitar comprobar la existencia de una clave antes de acceder o modificar los datos. Realizar estas comprobaciones para cada clave directamente en la base de datos puede ser lento.

**Solución** : Muchas bases de datos, como  **Cassandra** ,  **HBase** y  **Redis** , utilizan filtros Bloom para evitar búsquedas innecesarias en disco de claves inexistentes. El filtro Bloom comprueba rápidamente si una clave podría estar presente. Si el filtro Bloom indica "no presente", puede omitir la búsqueda en la base de datos.

#### 4. Sistemas de recomendación de contenido <a href="#id-4-content-recommendation-systems" id="id-4-content-recommendation-systems"></a>

**Problema** : Los sistemas de recomendación, como los que utilizan los servicios de streaming, deben evitar recomendar contenidos que los usuarios ya han consumido.

**Solución** : Un Filtro Bloom puede rastrear el contenido que cada usuario ha visto o con el que ha interactuado previamente. Al generar nuevas recomendaciones, el Filtro Bloom comprueba rápidamente si un elemento ya se ha consumido.

#### 5. Recomendaciones de amigos en redes sociales <a href="#id-5-social-network-friend-recommendations" id="id-5-social-network-friend-recommendations"></a>

**Problema** : Las redes sociales como Facebook o LinkedIn recomiendan amigos o conexiones a los usuarios, pero deben evitar recomendar a personas que ya son amigos.

**Solución** : Se utiliza un filtro Bloom para almacenar la lista de conexiones existentes de cada usuario. Antes de sugerir nuevos amigos, se puede comprobar el filtro Bloom para asegurarse de que el usuario no esté conectado con ellos.

## 6. Limitaciones de los filtros Bloom <a href="#id-6-limitations-of-bloom-filters" id="id-6-limitations-of-bloom-filters"></a>

#### 1.  **Falsos positivos** <a href="#id-1-false-positives" id="id-1-false-positives"></a>

Los filtros Bloom pueden producir falsos positivos, lo que significa que pueden indicar incorrectamente que un elemento está presente en el conjunto cuando no lo está.

> **Ejemplo:** Considere un escenario donde se compara una clave inexistente con un filtro Bloom. Si todas las funciones hash se asignan a bits que ya están definidos como  `1`, el filtro indica erróneamente la presencia de la clave.

Estos falsos positivos pueden dar lugar a un procesamiento innecesario o a suposiciones incorrectas sobre los datos.

**Por ejemplo,**  en un sistema de base de datos, esto podría generar búsquedas de caché innecesarias o intentos inútiles de obtener datos que en realidad no existen.

La probabilidad de falsos positivos se puede reducir eligiendo un tamaño óptimo para la matriz de bits y una cantidad apropiada de funciones hash, pero nunca se pueden eliminar por completo.

#### 2.  **No hay soporte para eliminaciones** <a href="#id-2-no-support-for-deletions" id="id-2-no-support-for-deletions"></a>

Los filtros Bloom estándar no admiten la eliminación de elementos. Una vez que un bit se establece en 1 al añadir un elemento, no se puede anular su valor, ya que otros elementos también pueden depender de ese bit.

Esta limitación hace que los filtros Bloom no sean adecuados para conjuntos dinámicos donde se agregan y eliminan elementos con frecuencia.

Variantes como el  **filtro Bloom de conteo**  pueden permitir eliminaciones mediante el uso de contadores en lugar de bits, pero esto requiere más memoria.

#### 3. Limitado a consultas de membresía establecidas <a href="#id-3-limited-to-set-membership-queries" id="id-3-limited-to-set-membership-queries"></a>

Los filtros Bloom están diseñados específicamente para responder consultas de pertenencia a conjuntos. No proporcionan información sobre los elementos reales del conjunto ni admiten consultas u operaciones complejas más allá de las comprobaciones básicas de pertenencia.

**Ejemplo** : si necesita conocer los detalles de un elemento (por ejemplo, información completa sobre un ID de usuario), necesitará otra estructura de datos además del filtro Bloom.

#### **4. No apto para la membresía del conjunto exacto** <a href="#id-4-not-suitable-for-exact-set-membership" id="id-4-not-suitable-for-exact-set-membership"></a>

Los filtros Bloom son probabilísticos, lo que significa que no pueden proporcionar una respuesta definitiva de “sí” (solo un “probablemente sí” o “definitivamente no”).

Para aplicaciones que requieren información de membresía exacta, los filtros Bloom no son adecuados. Se recomiendan otras estructuras de datos como tablas hash o árboles balanceados.

#### 5. Vulnerable a colisiones de hash <a href="#id-5-vulnerable-to-hash-collisions" id="id-5-vulnerable-to-hash-collisions"></a>

Las colisiones de hash son más probables a medida que aumenta el número de elementos en el filtro Bloom. Múltiples elementos pueden configurar o depender de los mismos bits, lo que aumenta los falsos positivos.

A medida que se acumulan las colisiones hash, la eficacia del filtro disminuye. Con un factor de carga alto, el filtro puede tener un rendimiento deficiente y volverse poco fiable.

El uso de funciones hash adicionales puede ayudar a reducir las colisiones, pero aumentar la cantidad de funciones hash también aumenta la complejidad y los requisitos de memoria.

## 7. Conclusión <a href="#id-7-conclusion" id="id-7-conclusion"></a>

En resumen, los filtros Bloom son una herramienta potente para realizar pruebas de pertenencia a conjuntos que ahorran espacio, con una amplia gama de aplicaciones. Si bien pueden no ser adecuados para todas las aplicaciones debido a la posibilidad de falsos positivos, son excelentes en escenarios donde el espacio es limitado y una tasa de error baja es aceptable.
