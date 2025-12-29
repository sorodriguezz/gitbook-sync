# Caching 101

El almacenamiento en caché es una técnica que se utiliza para almacenar temporalmente copias de datos en  capas **de almacenamiento de alta velocidad**  (como RAM) para reducir el tiempo necesario para acceder a los datos.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.42.30.png" alt="" width="563"><figcaption></figcaption></figure>

El objetivo principal del almacenamiento en caché es mejorar el rendimiento del sistema reduciendo  **la latencia** , descargando el almacén de datos principal y proporcionando  **una recuperación de datos más rápida.**

## 1. ¿Por qué utilizar el almacenamiento en caché? <a href="#id-1-why-use-caching" id="id-1-why-use-caching"></a>

El almacenamiento en caché es esencial por las siguientes razones:

* **Rendimiento mejorado:**  al almacenar datos a los que se accede con frecuencia en un caché, el tiempo necesario para recuperar dichos datos se reduce significativamente.
* **Carga reducida en los sistemas back-end:**  el almacenamiento en caché reduce la cantidad de solicitudes que debe procesar el back-end, lo que libera recursos para otras operaciones.
* **Mayor escalabilidad** : los cachés ayudan a gestionar una gran cantidad de solicitudes de lectura, lo que hace que el sistema sea más escalable.
* **Eficiencia de costos** : al reducir la carga en los sistemas back-end, el almacenamiento en caché puede ayudar a reducir los costos de infraestructura.
* **Experiencia de usuario mejorada** : tiempos de respuesta más rápidos conducen a una mejor experiencia de usuario, especialmente para aplicaciones web y móviles.

## 2. Tipos de almacenamiento en caché <a href="#id-2-types-of-caching" id="id-2-types-of-caching"></a>

#### 2.1 Caché en memoria <a href="#id-2-1-in-memory-cache" id="id-2-1-in-memory-cache"></a>

Los cachés en memoria almacenan datos en la  **memoria principal (RAM)**  para un acceso extremadamente rápido.

Estos cachés se utilizan normalmente para la gestión de sesiones, el almacenamiento de objetos a los que se accede con frecuencia y como fachada para bases de datos.

> Ejemplos:  **Redis**  y  **Memcached** .

#### 2.2 Caché distribuida <a href="#id-2-2-distributed-cache" id="id-2-2-distributed-cache"></a>

Un caché distribuido abarca  **múltiples servidores**  y está diseñado para manejar sistemas a gran escala.

Asegura que los datos almacenados en caché estén disponibles en diferentes nodos de un sistema distribuido.

> Ejemplos:  **Redis Cluster**  y  **Amazon ElastiCache** .

#### 2.3 Caché del lado del cliente <a href="#id-2-3-client-side-cache" id="id-2-3-client-side-cache"></a>

El almacenamiento en caché del lado del cliente implica almacenar datos en el dispositivo del cliente, generalmente en forma de  **cookies** ,  **almacenamiento local** o cachés específicos de la aplicación.

Esto se usa comúnmente en navegadores web para almacenar en caché activos estáticos como imágenes, scripts y hojas de estilo.

#### 2.4 Caché de base de datos <a href="#id-2-4-database-cache" id="id-2-4-database-cache"></a>

El almacenamiento en caché de bases de datos implica almacenar en un caché los resultados de bases de datos consultados con frecuencia.

Esto reduce la cantidad de consultas realizadas a la base de datos, mejorando el rendimiento y la escalabilidad.

#### 2.5 Red de distribución de contenido (CDN) <a href="#id-2-5-content-delivery-network-cdn" id="id-2-5-content-delivery-network-cdn"></a>

CDN se utiliza para almacenar copias de contenido en servidores distribuidos en diferentes ubicaciones geográficas.

Esto reduce la latencia al servir contenido desde un servidor más cercano al usuario.

## 3. Estrategias de almacenamiento en caché <a href="#id-3-caching-strategies" id="id-3-caching-strategies"></a>

* **Caché de lectura directa** : La aplicación primero busca datos en la caché. Si no están presentes (error de caché), los recupera de la base de datos y actualiza la caché.
* **Caché de escritura simultánea** : los datos se escriben en la memoria caché y en la base de datos simultáneamente, lo que garantiza la coherencia pero potencialmente afecta el rendimiento de escritura.
* **Caché de escritura diferida** : los datos se escriben primero en la caché y luego se sincronizan con la base de datos, lo que mejora el rendimiento de escritura pero implica el riesgo de pérdida de datos.
* **Cache-Aside (Lazy Loading)** : la aplicación es responsable de leer y escribir tanto desde el caché como desde la base de datos.

## 4. Políticas de desalojo de caché <a href="#id-4-cache-eviction-policies" id="id-4-cache-eviction-policies"></a>

Para administrar el tamaño limitado de un caché, se utilizan políticas de desalojo para determinar qué datos deben eliminarse cuando el caché está lleno.

1. **Uso Menos Recientemente (LRU)** : LRU elimina los datos menos utilizados cuando la caché está llena. Se asume que es probable que los datos usados ​​recientemente se vuelvan a usar pronto.
2. **Uso menos frecuente (LFU):** LFU expulsa los datos a los que se ha accedido menos veces, bajo el supuesto de que es menos probable que se necesiten los datos a los que se accede con poca frecuencia.
3. **Primero en entrar, primero en salir (FIFO):** FIFO expulsa primero los datos más antiguos de la memoria caché, independientemente de la frecuencia o la fecha en que se haya accedido a ellos.
4. **Tiempo de vida (TTL):** TTL es una política de desalojo basada en el tiempo donde los datos se eliminan de la memoria caché después de una duración específica, independientemente del uso.

## 5. Desafíos y consideraciones <a href="#id-5-challenges-and-considerations" id="id-5-challenges-and-considerations"></a>

1. **Coherencia de caché** : garantizar que los datos en la caché permanezcan consistentes con la fuente de verdad (por ejemplo, la base de datos).
2. **Invalidación de caché** : determinar cuándo y cómo actualizar o eliminar datos obsoletos de la caché.
3. **Inicio en frío** : manejo de escenarios en los que la memoria caché está vacía, como por ejemplo después de reiniciar el sistema.
4. **Políticas de desalojo de caché** : decidir qué elementos eliminar cuando el caché alcanza su capacidad (por ejemplo, los menos utilizados recientemente, los menos utilizados con frecuencia).
5. **Penetración de caché** : evita intentos maliciosos de consultar repetidamente datos inexistentes, lo que podría saturar el backend.
6. **Cache Stampede** : gestión de situaciones en las que muchas solicitudes simultáneas intentan reconstruir la caché simultáneamente.

## 6. Mejores prácticas para implementar el almacenamiento en caché <a href="#id-6-best-practices-for-implementing-caching" id="id-6-best-practices-for-implementing-caching"></a>

* **Almacene en caché los datos correctos:**  concéntrese en almacenar en caché los datos que son costosos de calcular o recuperar y a los que se accede con frecuencia.
* **Establecer TTL apropiados:**  utilice TTL para invalidar automáticamente las entradas de caché y evitar datos obsoletos.
* **Considere el calentamiento de caché** : precargue datos esenciales en el caché para evitar inicios en frío.
* **Supervisar el rendimiento de la caché:**  supervisar periódicamente las tasas de aciertos y errores de la caché y ajustar las estrategias de almacenamiento en caché en función de los patrones de uso.
* **Utilice almacenamiento en caché en capas:**  implemente el almacenamiento en caché en múltiples capas (por ejemplo, del lado del cliente, del lado del servidor, CDN) para maximizar los beneficios de rendimiento.
* **Manejar errores de caché con elegancia:**  asegúrese de que el sistema pueda manejar errores de caché de manera eficiente sin una degradación significativa del rendimiento.

## 7. Conclusión <a href="#id-7-conclusion" id="id-7-conclusion"></a>

El almacenamiento en caché es una técnica poderosa en el diseño de sistemas que, cuando se implementa correctamente, puede mejorar drásticamente el rendimiento, la escalabilidad y la rentabilidad de un sistema.

Sin embargo, esto conlleva sus propios desafíos, particularmente en torno a la coherencia y la invalidación.

Al comprender los diferentes tipos de cachés, las estrategias de ubicación de caché y las mejores prácticas, puede diseñar una estrategia de almacenamiento en caché sólida que satisfaga las necesidades de su aplicación.
