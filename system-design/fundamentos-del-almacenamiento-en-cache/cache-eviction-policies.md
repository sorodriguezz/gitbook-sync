# Cache Eviction Policies

**El almacenamiento en caché** es una técnica para hacer que las aplicaciones sean increíblemente rápidas, reducir la carga de la base de datos y mejorar la experiencia del usuario.

Pero la memoria caché es **limitada** : no se puede almacenar todo.

Entonces, ¿cómo decide qué artículos conservar y cuáles desechar cuando se acaba el espacio?

Aquí es donde entran en juego **las estrategias de desalojo de caché** . Estas determinan qué elementos se eliminan para dar cabida a los nuevos.

En este artículo, analizaremos las 7 **principales estrategias de eliminación de caché** y explicaremos qué son, cómo funcionan, sus ventajas y desventajas.

## **1. Uso menos reciente** (LRU)

**LRU** desaloja el artículo que no se ha utilizado durante más tiempo.

La idea es simple: si no has accedido a un elemento durante un tiempo, es menos probable que vuelvas a acceder a él pronto.

<figure><img src="../../.gitbook/assets/image (41).png" alt="" width="375"><figcaption></figcaption></figure>

**Cómo funciona**

* **Seguimiento de acceso** : LRU registra cuándo se accedió por última vez a cada elemento de la caché. Esto puede realizarse mediante diversas estructuras de datos, como una **lista doblemente enlazada** o una combinación de un **mapa hash** y una **cola** .
* **Impacto de caché (elemento encontrado en caché):** cuando se accede a un elemento, se mueve a la posición **utilizada más recientemente** en la estructura de datos de seguimiento (por ejemplo, se mueve al principio de una lista).
* **Error de caché (elemento no encontrado en caché):**
  * Si el elemento no está en el caché y éste tiene espacio libre, se agrega directamente.
  * Si la caché está llena, se elimina el **elemento utilizado menos recientemente para dejar espacio para el nuevo elemento.**
* **Desalojo:** el elemento al que se ha accedido menos recientemente (rastreado al principio de la lista) se elimina de la caché.

Consideremos un caché con una capacidad de 3:

1. **Estado inicial** : caché vacía.
2. Agregar **A** → Caché: \[A]
3. Agregar **B** → Caché: \[A, B]
4. Agregar **C** → Caché: \[A, B, C]
5. Acceso **A** → Caché: \[B, C, A] (A pasa a ser usado recientemente)
6. Agregar **D** → Caché: \[C, A, D] (B se elimina porque es el “menos usado recientemente”)

**Ventajas :**

1. **Intuitivo** : fácil de entender y ampliamente adoptado.
2. **Eficiente** : mantiene los elementos a los que se accede con frecuencia en la memoria caché.
3. **Optimizado para uso en el mundo real** : coincide con muchos patrones de acceso, como navegación web y llamadas API.

**Contras :**

1. **Sobrecarga de metadatos** : el seguimiento del orden de uso puede consumir memoria adicional.
2. **Costo de rendimiento** : para cachés grandes, mantener el orden de acceso puede generar una sobrecarga computacional.
3. **No adaptativo** : asume que los patrones de acceso pasados ​​predecirán el uso futuro, lo que puede no ser siempre cierto.

## **2. Uso menos frecuente** (LFU)

**LFU** expulsa el elemento con menor frecuencia de acceso. Se asume que los elementos con menor acceso en el pasado tienen menor probabilidad de ser accedidos en el futuro.

A diferencia de LRU, que se centra en **la actualidad** , LFU enfatiza **la frecuencia** de acceso.

<figure><img src="../../.gitbook/assets/image (42).png" alt="" width="375"><figcaption></figcaption></figure>

**Cómo funciona**

* **Frecuencia de acceso a la pista** : LFU mantiene un recuento de frecuencia para cada elemento en la memoria caché, incrementando el recuento cada vez que se accede al elemento.
* **Impacto de caché (elemento encontrado en caché):** cuando se accede a un elemento, su recuento de frecuencia aumenta.
* **Error de caché (elemento no encontrado en caché):**
  * Si el caché tiene espacio disponible, el nuevo elemento se agrega con un recuento de frecuencia inicial de 1.
  * Si la caché está llena, se elimina el **elemento con la frecuencia más baja** para dar cabida al nuevo. Si varios elementos comparten la misma frecuencia más baja, una estrategia secundaria (como LRU o FIFO) resuelve los empates.
* **Desalojo** : eliminar el elemento con el menor recuento de frecuencia.

Consideremos un caché con una capacidad de 3:

1. **Estado inicial** : caché vacía.
2. Agregar **A** → Caché: \[A (freq=1)]
3. Agregar **B** → Caché: \[A (frecuencia=1), B (frecuencia=1)]
4. Agregar **C** → Caché: \[A (frecuencia=1), B (frecuencia=1), C (frecuencia=1)]
5. Acceso **A** → Caché: \[A (frecuencia=2), B (frecuencia=1), C (frecuencia=1)]
6. Añadir **D** → Caché: \[A (freq=2), C (freq=1), D (freq=1)] (B se elimina porque tiene la frecuencia más baja).
7. Acceso **C** → Caché: \[A (frecuencia=2), C (frecuencia=2), D (frecuencia=1)]

**Ventajas :**

1. **Eficiente para patrones predecibles** : conserva datos a los que se accede con frecuencia, que suelen ser más relevantes.
2. **Altamente eficaz para datos populares** : funciona bien en escenarios con elementos claramente "populares".

**Contras :**

1. **Alto consumo de recursos** : requiere memoria adicional para realizar un seguimiento de los recuentos de frecuencia.
2. **Actualizaciones más lentas** : la frecuencia de seguimiento y actualización puede ralentizar las operaciones.
3. **No adaptable** : puede conservar elementos a los que se accedió con frecuencia en el pasado pero que ya no son relevantes.

## 3. Primero en entrar, primero en salir (FIFO)

FIFO expulsa el elemento que se agregó primero, independientemente de la frecuencia con la que se acceda a él.

FIFO funciona bajo el supuesto de que los artículos agregados primero tienen menos probabilidades de ser necesarios a medida que se llena el caché.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.50.07.png" alt="" width="375"><figcaption></figcaption></figure>

**Cómo funciona**

* **Inserción de elementos** : cuando se agrega un elemento a la caché, se coloca al final de la cola.
* **Accedido a caché (elemento encontrado en caché):** No se modifica el orden de los elementos. FIFO no prioriza los elementos accedidos recientemente.
* **Error de caché (elemento no encontrado en caché):**
  * Si hay espacio en la caché, el nuevo elemento se agrega al final de la cola.
  * Si el caché está lleno, el elemento que se encuentra al principio de la cola (el elemento más antiguo) se elimina para dejar espacio para el nuevo elemento.
* **Desalojo** : se elimina el elemento más antiguo, que ha estado en la memoria caché durante más tiempo, para dejar lugar para el nuevo elemento.

Supongamos un caché con una capacidad de 3:

1. Agregar **A** → Caché: \[A]
2. Agregar **B** → Caché: \[A, B]
3. Agregar **C** → Caché: \[A, B, C]
4. Agregar **D** → Caché: \[B, C, D] (A se elimina porque se agregó primero).
5. Acceso **B** → Caché: \[B, C, D] (El orden permanece sin cambios).
6. Agregar **E** → Caché: \[C, D, E] (B se elimina porque era el elemento restante más antiguo).

**Ventajas :**

1. **Fácil de implementar** : FIFO es sencillo y requiere una lógica mínima.
2. **Gastos generales bajos** : no es necesario realizar un seguimiento de metadatos adicionales como la frecuencia de acceso o la actualidad.
3. **Comportamiento determinista** : el desalojo sigue un orden predecible.

**Contras :**

1. **Ignora los patrones de acceso** : los elementos que aún se usan con frecuencia pueden eliminarse, lo que reduce la eficiencia del caché.
2. **Subóptimo para muchos casos de uso** : FIFO rara vez es ideal en los sistemas modernos donde la actualidad y la frecuencia importan.
3. **Puede desperdiciar espacio en caché** : si se eliminan elementos antiguos pero utilizados con frecuencia, el caché pierde su utilidad.

## 4. Reemplazo aleatorio (RR)

La estrategia de desalojo de caché de RR es la más simple de todas: cuando el caché está lleno, desaloja un elemento aleatorio para hacer espacio para uno nuevo.

No rastrea la actualidad, la frecuencia ni el orden de inserción, lo que lo convierte en un enfoque liviano con una sobrecarga computacional mínima.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.50.47.png" alt="" width="375"><figcaption></figcaption></figure>

Esta simplicidad a veces puede ser sorprendentemente efectiva, especialmente en sistemas con patrones de acceso impredecibles o altamente dinámicos.

**Cómo funciona**

* **Inserción de elementos** : cuando se agrega un elemento a la caché y hay espacio, se almacena directamente.
* **Impacto de caché:** si el elemento solicitado existe en la caché, se sirve y no se realizan cambios en la caché.
* **Error de caché:** si el elemento no está en el caché y este está lleno, se elimina un elemento aleatorio.
* **Desalojo** : se elimina el elemento seleccionado aleatoriamente y el nuevo elemento se agrega al caché.

Supongamos un caché con una capacidad de 3:

1. Agregar **A** → Caché: \[A]
2. Agregar **B** → Caché: \[A, B]
3. Agregar **C** → Caché: \[A, B, C]
4. Añadir **D** → Caché: \[B, C, D] (A se expulsa aleatoriamente).
5. Añadir **E** → Caché: \[C, E, D] (B se elimina aleatoriamente).

**Ventajas :**

1. **Fácil de implementar** : no se necesitan metadatos como frecuencia de acceso o actualidad.
2. **Bajos costos operativos** : los requisitos computacionales y de memoria son mínimos.
3. **Justo para patrones de acceso impredecibles** : evita el sesgo hacia la actualidad o la frecuencia, lo que puede ser útil en algunos escenarios.

**Contras :**

1. **Desalojo impredecible** : un elemento usado con frecuencia podría ser desalojado, lo que reduciría la eficiencia del caché.
2. **Ineficiente para patrones de acceso estables** : no se adapta bien cuando se accede de manera constante a ciertos elementos.
3. **Alto riesgo de tasas de acierto de caché bajas** : la expulsión aleatoria suele conducir a una retención subóptima de elementos importantes.

## 5. Usado más recientemente (MRU)

**MRU** es lo opuesto a **LRU (menos usado recientemente)** . En MRU, el elemento al que se accedió más recientemente es el primero en ser eliminado cuando la caché está llena.

La idea detrás de MRU es que el elemento al que se accedió más recientemente probablemente sea una necesidad temporal y no se volverá a acceder a él pronto, por lo que desalojarlo libera espacio para datos potencialmente más valiosos.

<figure><img src="../../.gitbook/assets/image (43).png" alt="" width="375"><figcaption></figcaption></figure>

**Cómo funciona**

* **Inserción de elementos** : cuando se agrega un nuevo elemento a la caché, se marca como el utilizado más recientemente.
* **Impacto de caché (elemento encontrado en caché):** cuando se accede a un elemento, este se marca como el utilizado más recientemente.
* **Error de caché (elemento no encontrado en caché):**
  * Si el caché tiene espacio disponible, el nuevo elemento se agrega directamente.
  * Si la caché está llena, se elimina el elemento utilizado más recientemente para dejar lugar para el nuevo elemento.
* **Desalojo** : se elimina el elemento al que se accedió o agregó más recientemente.

Supongamos un caché con una capacidad de 3:

1. Agregar **A** → Caché: \[A]
2. Agregar **B** → Caché: \[A, B]
3. Agregar **C** → Caché: \[A, B, C]
4. Acceso **C** → Caché: \[A, B, C] (C está marcado como el utilizado más recientemente).
5. Agregar **D** → Caché: \[A, B, D] (C se elimina porque fue el utilizado más recientemente).
6. Acceso **B** → Caché: \[A, B, D] (B se convierte en el utilizado más recientemente).
7. Agregar **E** → Caché: \[A, D, E] (B se elimina porque fue el utilizado más recientemente).

**Ventajas :**

1. **Eficaz en escenarios específicos** : conserva datos más antiguos, que podrían ser más valiosos en determinadas cargas de trabajo.
2. **Implementación simple** : requiere metadatos mínimos.

**Contras :**

1. **Subóptimo para la mayoría de los casos de uso** : MRU supone que los datos recientes son menos valiosos, lo que a menudo no es cierto para muchas aplicaciones.
2. **Baja tasa de aciertos en patrones predecibles** : falla en escenarios en los que es más probable que se reutilicen los datos a los que se accedió recientemente.
3. **Rara vez utilizado en la práctica** : aplicabilidad limitada en comparación con otras estrategias como LRU o LFU.

## 6. Tiempo de vida (TTL)

**TTL** es una estrategia de desalojo de caché donde a cada elemento almacenado se le asigna una vida útil fija. Una vez que la vida útil de un elemento expira, se elimina automáticamente de la caché, independientemente de los patrones o la frecuencia de acceso.

Esto garantiza que los datos almacenados en caché permanezcan actualizados y evita que los datos obsoletos permanezcan en el caché indefinidamente.

<figure><img src="../../.gitbook/assets/image (44).png" alt="" width="375"><figcaption></figcaption></figure>

**Cómo funciona**

* **Inserción de elementos** : Cuando se añade un elemento a la caché, se le asigna un valor TTL (p. ej., 10 segundos). El tiempo de expiración suele calcularse como `current time + TTL`.
* **Acceso a caché (éxito o error)** : cuando se accede a un elemento, la caché verifica su tiempo de expiración:
  * Si el elemento ha expirado, se elimina de la memoria caché y se registra un error de memoria caché.
  * Si el artículo es válido, se sirve como un acierto de caché.
* **Desalojo** : los elementos vencidos se eliminan automáticamente durante la limpieza periódica o al acceder.

Supongamos un caché con un TTL de 5 segundos:

1. Agregar **A** con TTL = 5 s → Caché: \[A (caduca en 5 s)]
2. Sumar **B** con TTL = 10 s → Caché: \[A (5 s), B (10 s)]
3. Después de 6 segundos → Caché: \[B (caduca en 4 s)] (A se elimina porque su TTL expiró).
4. Sumar **C** con TTL = 5 s → Caché: \[B (4 s), C (5 s)]

Si se accede a un elemento después de que expire su TTL, se produce una falla de caché.

TTL a menudo se implementa en sistemas de almacenamiento en caché como **Redis** o **Memcached** , donde se pueden especificar tiempos de expiración para cada clave.

**Ventajas :**

1. **Garantiza la frescura** : elimina automáticamente los datos obsoletos, garantizando así que solo los elementos nuevos permanezcan en el caché.
2. **Fácil de configurar** : los valores TTL son fáciles de asignar durante la inserción de caché.
3. **Gastos generales bajos** : no es necesario realizar un seguimiento de los patrones de uso ni de la frecuencia de acceso.
4. **Previene fugas de memoria** : los datos obsoletos se borran sistemáticamente, lo que evita que se sature la memoria caché.

**Contras :**

1. **Vida útil fija** : los elementos pueden eliminarse prematuramente incluso si se accede a ellos con frecuencia.
2. **Desalojo innecesario** : los elementos que no han expirado pero que aún son irrelevantes ocupan espacio en caché.
3. **Flexibilidad limitada** : TTL no se adapta a cargas de trabajo dinámicas ni a patrones de uso.

## 7. Almacenamiento en caché de dos niveles

El almacenamiento en caché de dos niveles combina dos capas de caché: normalmente un **caché local** (en memoria) y un **caché remoto** (distribuido o compartido).

<figure><img src="../../.gitbook/assets/image (45).png" alt="" width="332"><figcaption></figcaption></figure>

El caché local actúa como la primera capa (caché activo) y proporciona acceso ultrarrápido a datos utilizados con frecuencia, mientras que el caché remoto actúa como la segunda capa (caché frío) para elementos que no se encuentran en el caché local pero que aún así se necesitan con relativa rapidez.

**Cómo funciona**

1. **Caché local (primer nivel)** :
   * Reside en el mismo servidor que la aplicación, a menudo en la memoria (por ejemplo, `HashMap`, `LRUCache`en la aplicación).
   * Proporciona acceso ultrarrápido a datos a los que se accede con frecuencia, lo que reduce la latencia y la carga del servidor.
   * Ejemplos: estructuras de datos en memoria como `HashMap`o marcos como **Guava Cache** .
2. **Caché remota (segundo nivel)** :
   * Se comparte entre varios servidores del sistema. Es un poco más lento debido a la sobrecarga de la red, pero ofrece mayor almacenamiento y consistencia compartida.
   * Se utiliza para almacenar datos que no están en la memoria caché local pero que aún así se necesitan con frecuencia.
   * Ejemplos: sistemas de caché distribuidos como **Redis** o **Memcached** .

**Flujo de trabajo** :

* Una solicitud de cliente verifica primero la **memoria caché local .**
* Si no se encuentran los datos (falta de caché), se consulta la **caché remota** .
* Si aún no se encuentran los datos (otro error de caché), recupera los datos de la fuente de datos principal (por ejemplo, una base de datos), los almacena en los cachés local y remoto y los devuelve al cliente.

**Ventajas :**

1. **Acceso ultrarrápido** : la caché local proporciona tiempos de respuesta casi instantáneos para solicitudes frecuentes.
2. **Almacenamiento escalable** : el caché remoto agrega escalabilidad y permite compartir datos entre múltiples servidores.
3. **Reduce la carga de la base de datos** : el almacenamiento en caché de dos niveles minimiza significativamente las llamadas a la base de datos back-end.
4. **Tolerancia a fallos** : si falla el caché local, el caché remoto actúa como respaldo.

**Contras :**

1. **Complejidad** : administrar dos cachés genera más sobrecarga, incluidos problemas de sincronización y consistencia.
2. **Datos obsoletos** : las actualizaciones inconsistentes entre niveles pueden generar que se brinden datos obsoletos.
3. **Mayor latencia para los accesos a la caché remota** : el acceso a la caché remota de segundo nivel es más lento que al caché local.
