# Caching Strategies

**El almacenamiento en caché** es una técnica poderosa para **reducir la latencia** y **mejorar el rendimiento del sistema**.

Hay varias **estrategias de almacenamiento en caché**, según las necesidades de un sistema, ya sea que el objetivo sea optimizar para cargas de trabajo **de lectura intensiva**, operaciones **de escritura intensiva** o garantizar **la consistencia de los datos**.

En este artículo, cubriremos las **cinco estrategias de almacenamiento en caché más comunes** que surgen con frecuencia en **las discusiones sobre diseño de sistemas** y se utilizan ampliamente en  **aplicaciones del mundo real**.

## 1. Leer detenidamente <a href="#id-1-read-through" id="id-1-read-through"></a>

En la  estrategia **de lectura continua**  , la caché actúa como intermediario entre la aplicación y la base de datos.

Cuando la aplicación solicita datos, primero busca en la memoria caché.

Si los datos están disponibles ( **acceso a caché** ), se devuelven a la aplicación.

Si los datos no están disponibles ( **falta de caché** ), la propia caché es responsable de obtener los datos de la base de datos, almacenarlos y devolverlos a la aplicación.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.44.48.png" alt=""><figcaption></figcaption></figure>

Este enfoque  **simplifica la lógica de la aplicación**  porque la aplicación no necesita manejar la lógica para obtener y actualizar el caché.

La caché gestiona automáticamente la lectura de la base de datos y el almacenamiento de los datos solicitados. Esto minimiza los datos innecesarios en la caché y garantiza que los datos a los que se accede con frecuencia estén fácilmente disponibles.

Para  **los aciertos de caché** , Read Through proporciona  acceso a datos **de baja latencia**  .

Sin embargo, en caso de  **fallos de caché** , existe un posible  **retraso**  mientras la caché consulta la base de datos y almacena los datos. Esto puede resultar en una mayor latencia durante las lecturas iniciales.

Para evitar que la caché muestre datos obsoletos, se puede añadir un  **tiempo de vida (TTL)** a las entradas almacenadas en caché. El TTL expira automáticamente los datos tras un periodo especificado, lo que permite recargarlos desde la base de datos cuando sea necesario.

> **El almacenamiento en caché de lectura continua**  es más adecuado para  **aplicaciones de lectura intensiva** en las que se accede a los datos con frecuencia pero se actualizan con menor frecuencia, como sistemas de distribución de contenido (CDN), feeds de redes sociales o perfiles de usuario.

## 2. Caché aparte <a href="#id-2-cache-aside" id="id-2-cache-aside"></a>

**La caché aparte** , también conocida como  **"carga diferida"** , es una estrategia en la que el  **código de la aplicación**  gestiona la interacción entre la caché y la base de datos. Los datos se cargan en la caché solo cuando son necesarios.

La aplicación primero busca datos en la caché. Si los datos existen ( **acceso a la caché** ), se devuelven a la aplicación.

Si los datos no se encuentran en el caché ( **error de caché** ), la aplicación los recupera de la base de datos (o del almacén de datos principal) y luego los carga en el caché para solicitudes posteriores.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.45.19.png" alt="" width="563"><figcaption></figcaption></figure>

La memoria caché actúa como un  **"sidecar"**  de la base de datos, y es responsabilidad de la aplicación administrar cuándo y cómo se escriben los datos en la memoria caché.

Para evitar datos obsoletos, podemos establecer un  **tiempo de vida (TTL)**  para los datos almacenados en caché. Una vez que este expira, los datos se eliminan automáticamente de la caché.

> **Cache Aside**  es ideal para sistemas con una  **alta**  tasa  **de lectura-escritura** y actualizaciones de datos poco frecuentes. Por ejemplo, en un sitio web de comercio electrónico, los datos de productos (como precios, descripciones o disponibilidad) suelen leerse con mucha más frecuencia de la que se actualizan.

## 3. Escribir a través de <a href="#id-3-write-through" id="id-3-write-through"></a>

En la  estrategia **Write Through**  , cada operación de escritura se ejecuta en la memoria caché y en la base de datos al mismo tiempo.

Este es un  **proceso sincrónico** , lo que significa que tanto la memoria caché como la base de datos se actualizan como parte de la misma operación, lo que garantiza que no haya demoras en la propagación de datos.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.45.52.png" alt=""><figcaption></figcaption></figure>

Este enfoque garantiza que el caché y la base de datos permanezcan  **sincronizados**  y que las solicitudes de lectura del caché siempre devolverán los  **datos más recientes** , evitando el riesgo de entregar datos obsoletos.

En una estrategia de almacenamiento en caché de escritura simultánea, las políticas de expiración de caché (como TTL) generalmente no son necesarias. Sin embargo, si le preocupa el uso de la memoria caché, puede implementar una política TTL para eliminar los datos a los que se accede con poca frecuencia después de un período determinado.

La mayor ventaja de Write Through es que garantiza una fuerte  **consistencia de datos**  entre la memoria caché y la base de datos.

Dado que la caché siempre contiene los datos más recientes,  **las operaciones de lectura**  se benefician de  **una baja latencia**  porque los datos se pueden recuperar directamente de la caché.

Sin embargo,  **la latencia de escritura**  puede ser  **mayor**  debido a la sobrecarga de escritura tanto en la memoria caché como en la base de datos.

> **Write Through es** ideal para  **sistemas donde la consistencia es crítica** , como aplicaciones financieras o sistemas de procesamiento de transacciones en línea, donde la memoria caché y la base de datos deben tener siempre los datos más recientes.

## 4. Escribe alrededor <a href="#id-4-write-around" id="id-4-write-around"></a>

**Write Around**  es una estrategia de almacenamiento en caché donde los datos se escriben directamente en la base de datos, sin pasar por el caché.

La caché solo se actualiza cuando se solicitan los datos más tarde durante una operación de lectura, momento en el que se utiliza la estrategia **Cache Aside para cargar los datos en la caché.**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.46.26.png" alt="" width="563"><figcaption></figcaption></figure>

Este enfoque garantiza que solo  **los datos a los que se accede con frecuencia**  residan en la memoria caché, lo que evita que se contaminen con datos a los que es posible que no se pueda acceder nuevamente en el corto plazo.

Mantiene la caché limpia evitando datos innecesarios que podrían no solicitarse después de escribirse.

**Las escrituras**  son relativamente  **más rápidas**  porque solo apuntan a la base de datos y no incurren en la sobrecarga de escribir en el caché.

**El TTL**  se puede usar para garantizar que los datos no permanezcan en la caché indefinidamente. Una vez que el TTL expira, los datos se eliminan de la caché, lo que obliga al sistema a recuperarlos de la base de datos si es necesario.

> **El almacenamiento en caché de escritura alternativa**  se utiliza mejor en  **sistemas con mucha escritura,**  donde los datos se escriben o actualizan con frecuencia, pero  **no se leen de inmediato o con frecuencia,**  como en los sistemas de registro.

## 5. Escribir de nuevo <a href="#id-5-write-back" id="id-5-write-back"></a>

En la  estrategia **de escritura diferida**  , los datos primero se escriben en la memoria caché y luego  se escriben **de manera asincrónica**  en la base de datos en un momento posterior.

Esta estrategia se centra en  **minimizar la latencia de escritura**  al posponer las escrituras en la base de datos.

Esta escritura diferida significa que la caché actúa como almacenamiento principal durante las operaciones de escritura, mientras que la base de datos se actualiza periódicamente en segundo plano.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.47.02.png" alt=""><figcaption></figcaption></figure>

La principal ventaja de Write Back es que  **reduce significativamente la latencia de escritura** , ya que las escrituras se completan rápidamente en la memoria caché y las actualizaciones de la base de datos se retrasan o se realizan en lotes.

Sin embargo, con este enfoque existe el riesgo de  **pérdida de datos**  si la memoria caché falla antes de que los datos se hayan escrito en la base de datos.

Esto se puede mitigar mediante el uso de soluciones de almacenamiento en caché persistente como  **Redis con AOF (Append Only File)** , que registra cada operación de escritura en el disco, lo que garantiza la durabilidad de los datos incluso si falla el caché.

Write Back no requiere la invalidación de las entradas de caché, ya que la caché en sí misma es la fuente de verdad durante el proceso de escritura.

> **El almacenamiento en caché de escritura diferida**  es ideal para  escenarios **de mucha escritura**  donde las operaciones de escritura deben ser  **rápidas**  y  **frecuentes** , pero  **la coherencia inmediata**  con la base de datos no es fundamental, como en los sistemas de registro y los feeds de redes sociales.

## Resumen <a href="#summary" id="summary"></a>

La elección de la estrategia de almacenamiento en caché adecuada depende de los requisitos específicos de su sistema.

A continuación se muestra un resumen tabular:

<figure><img src="https://algomaster.io/_next/image?url=https%3A%2F%2Fpayload.algomaster.io%2Fapi%2Fmedia%2Ffile%2Flsd-503-5.webp&#x26;w=3840&#x26;q=90" alt=""><figcaption></figcaption></figure>
