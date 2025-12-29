# Distributed Caching

**El almacenamiento en caché** se utiliza para almacenar temporalmente copias de **datos a los que se accede con frecuencia** en capas de almacenamiento de alta velocidad (como RAM) para **reducir la latencia** y la carga en el servidor o la base de datos.

<figure><img src="../../.gitbook/assets/image (46).png" alt="" width="375"><figcaption></figcaption></figure>

Cuando el tamaño del conjunto de datos es pequeño, generalmente es suficiente mantener todos los datos de caché en un servidor.

Pero a medida que el sistema se hace **más grande** , el tamaño del caché también se hace más grande y un **caché de un solo nodo** a menudo resulta insuficiente cuando se escala para manejar millones de usuarios y conjuntos de datos masivos.

En tales escenarios, necesitamos **distribuir** los datos de caché entre múltiples servidores.

Aquí es donde entra en juego **el almacenamiento en caché distribuido .**

En este artículo, exploraremos el almacenamiento en caché distribuido en detalle, incluido qué es, por qué es importante, cómo funciona, sus componentes, desafíos, mejores prácticas y soluciones de almacenamiento en caché distribuido populares.

### 1. ¿Qué es el almacenamiento en caché distribuido?

**El almacenamiento en caché distribuido** es una técnica en la que los datos de la caché se almacenan en **varios nodos** (servidores) en lugar de estar confinados a una sola máquina.

Esto permite que la caché se **escale horizontalmente** y se adapte a las necesidades de **aplicaciones de gran escala.**

<figure><img src="../../.gitbook/assets/image (47).png" alt="" width="375"><figcaption></figcaption></figure>

### 2. ¿Por qué utilizar almacenamiento en caché distribuido?

**1. Escalabilidad**

El almacenamiento en caché distribuido permite que las aplicaciones **escalen horizontalmente** agregando más nodos de caché.

Esto ayuda a gestionar más tráfico sin una caída significativa en el rendimiento.

**2. Tolerancia a fallos**

Dado que los datos se distribuyen entre **varios nodos** , la falla de un solo nodo no resulta en la pérdida de todo el caché.

Los nodos restantes pueden seguir atendiendo solicitudes, lo que permitirá que el sistema se recupere sin problemas.

**3. Equilibrio de carga**

Al distribuir la caché entre varios nodos, la carga se distribuye **de manera uniforme** .

Esto ayuda a evitar que cualquier nodo individual se convierta en un cuello de botella.

### 3. Componentes del almacenamiento en caché distribuido

Un sistema de caché distribuido normalmente consta de los siguientes componentes:

1. **Nodos de caché** : Son los **servidores individuales** donde se almacenan los datos de caché. Cada nodo forma parte del clúster de caché general.
2. **Biblioteca de cliente/Cliente de caché** : Las aplicaciones utilizan una **biblioteca de cliente** para comunicarse con la caché distribuida. Esta biblioteca gestiona la lógica de conexión a los nodos de caché, la distribución de datos y la recuperación de datos almacenados en caché.
3. **Hashing consistente** : Este método distribuye los datos **uniformemente** entre los nodos de caché. Garantiza que añadir o eliminar nodos tenga un impacto mínimo en el sistema.
4. **Replicación** : Para aumentar la fiabilidad del sistema, algunas cachés distribuidas **replican datos** en varios nodos. Si un nodo falla, los datos siguen disponibles en otro.
5. **Fragmentación** : Los datos se **dividen** en fragmentos, cada uno de los cuales se almacena en un nodo de caché diferente. Esto facilita la distribución **uniforme de los datos** y permite que la caché escale horizontalmente.
6. **Políticas de desalojo** : los cachés implementan políticas de desalojo como **LRU** (menos usado recientemente), **LFU** (menos usado frecuentemente) o **TTL** (tiempo de vida) para deshacerse de datos antiguos o menos utilizados y hacer espacio para datos nuevos.
7. **Coordinación y sincronización** : los mecanismos de coordinación como **los bloqueos distribuidos** o **los protocolos de consenso** garantizan que los nodos de caché permanezcan sincronizados, especialmente cuando varios nodos intentan cambiar los mismos datos al mismo tiempo.

### 4. Servidores de caché dedicados vs. caché coubicada

Al diseñar una estrategia de almacenamiento en caché para un sistema distribuido, una de las decisiones críticas que debe tomar es **dónde alojar el caché.**

Las dos opciones principales son utilizar **servidores de caché dedicados** o **ubicar el caché** junto con servidores de aplicaciones.

#### 1. Servidores de caché dedicados

**Los servidores de caché dedicados** son máquinas independientes o instancias virtuales que se utilizan únicamente para almacenamiento en caché.

Están separados de los servidores de aplicaciones y están **optimizados para el almacenamiento en caché.**

<figure><img src="../../.gitbook/assets/image (48).png" alt="" width="375"><figcaption></figcaption></figure>

**Ventajas:**

* **Escalabilidad:** Pueden escalarse independientemente de los servidores de aplicaciones. Se pueden añadir más nodos de caché sin afectar la capa de aplicación.
* **Aislamiento de recursos:** mantener el caché en servidores separados significa que no ralentizará sus servidores principales.

**Desventajas:**

* **Costo:** ejecutar servidores de caché dedicados puede ser costoso, especialmente si necesita muchos de esos nodos.
* **Latencia de la red** : dado que están separados de los servidores de aplicaciones, puede llevar **más tiempo** obtener datos del caché.

#### 2. Caché coubicada

**La caché coubicada** significa ejecutar la caché y la aplicación en el mismo servidor.

En esta configuración, la aplicación y la caché comparten los **mismos recursos de hardware** , como CPU, memoria e interfaces de red.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.56.27.png" alt="" width="375"><figcaption></figcaption></figure>

**Ventajas:**

* **Baja latencia** : Dado que tanto la caché como la aplicación se encuentran en el mismo servidor, el acceso a la caché es muy **rápido** . Esto es ideal para aplicaciones donde cada milisegundo cuenta, como juegos en tiempo real o plataformas de trading de alta frecuencia.
* **Rentabilidad** : ubicar la memoria caché junto con la aplicación puede resultar más **rentable** , especialmente para aplicaciones pequeñas y medianas.

**Desventajas:**

* **Contención de recursos** : La caché y la aplicación utilizan los mismos recursos del servidor, como la memoria de la CPU y las E/S. Esto puede ralentizar el sistema bajo alta carga, si ambos consumen una cantidad considerable de recursos.
* **Escalabilidad limitada:** El almacenamiento en caché coubicado es menos escalable que los servidores de caché dedicados. Escalar la caché podría requerir la actualización completa del servidor.
* **Invalidación de caché compleja** : cuando se utilizan cachés ubicados en el mismo lugar en un entorno distribuido, mantener la caché actualizada y deshacerse de datos antiguos puede volverse complicado.

Si necesita que su sistema gestione muchos usuarios, mantenga los recursos separados y tenga el presupuesto para ello, utilizar servidores de caché dedicados probablemente sea la mejor opción.

Pero si tu aplicación es más pequeña, quieres ahorrar dinero o necesitas que sea súper rápida, colocar el caché y la aplicación en el mismo servidor puede funcionar bien.

### 5. ¿Cómo funciona el almacenamiento en caché distribuido?

1. **Distribución de datos:** cuando se almacenan en caché los datos, la biblioteca cliente generalmente **aplica un hash** a la clave asociada con los datos para determinar qué nodo de caché los almacenará.
2. **Replicación de datos:** Para mayor confiabilidad, el sistema de caché replica los datos almacenados en caché en múltiples nodos. Por lo tanto, si un nodo, por ejemplo, el A, almacena los datos, también podrían copiarse a otro nodo, como el B, como respaldo.
3. **Recuperación de datos:** Para obtener datos de la caché, la aplicación proporciona la **clave** a la biblioteca cliente. Esta utiliza esta clave para **encontrar y consultar** el nodo que contiene los datos. Si los datos están presentes (un acierto de caché), se devuelven a la aplicación. Si no están presentes (un error de caché), se recuperan del almacén de datos principal (por ejemplo, una base de datos) y se pueden almacenar en caché para su uso futuro.
4. **Invalidación de caché:** Para mantener la sincronización de los datos de la caché con la fuente de datos principal, es necesario invalidarlos o actualizarlos periódicamente. Los sistemas de caché implementan estrategias como **la expiración basada en tiempo** o **en eventos** para la invalidación de la caché.
5. **Expulsión de caché** : Dado que las cachés tienen espacio limitado, necesitan una política de expulsión para liberar espacio para nuevos datos. Las políticas de expulsión más comunes incluyen:
   1. **Menos utilizado recientemente (LRU)** : expulsa los datos a los que no se ha accedido durante más tiempo.
   2. **Uso menos frecuente (LFU)** : expulsa los datos a los que se ha accedido con menos frecuencia.
   3. **Tiempo de vida (TTL)** : expulsa los datos que han estado en la memoria caché durante más tiempo que una duración especificada.

### 6. Desafíos del almacenamiento en caché distribuido

* **Coherencia de los datos** : garantizar que todos los nodos de caché tengan datos consistentes puede ser un desafío, especialmente en una aplicación **con mucha escritura .**
* **Invalidación de caché** : decidir cuándo invalidar o actualizar la caché puede ser complejo, en particular cuando se trata de múltiples nodos de caché.
* **Particionado de red** : en un sistema distribuido, pueden ocurrir particiones de red, lo que genera situaciones en las que los nodos de caché no pueden comunicarse entre sí.
* **Escalabilidad y equilibrio de carga** : a medida que el sistema escala, garantizar que la memoria caché esté equilibrada de manera uniforme entre los nodos sin que ninguno se convierta en un cuello de botella requiere estrategias sofisticadas de equilibrio de carga.

### 7. Mejores prácticas para implementar el almacenamiento en caché distribuido

Para aprovechar al máximo su sistema de almacenamiento en caché distribuido, tenga en cuenta las siguientes prácticas recomendadas:

1. **Almacene en caché con criterio** : No todos los datos se benefician del almacenamiento en caché. Concéntrese en los datos relativamente estáticos y de acceso frecuente.
2. **Establecer TTL apropiados** : utilice valores de tiempo de vida (TTL) para hacer caducar automáticamente los datos almacenados en caché y reducir la obsolescencia.
3. **Implementar el patrón Cache-Aside** : cargar datos en el caché solo cuando se solicita, para evitar el almacenamiento en caché innecesario.
4. **Supervisar y ajustar** : supervise periódicamente las tasas de aciertos de caché, el uso de memoria y el tráfico de red para optimizar el rendimiento.
5. **Plan para fallas** : diseñe su sistema para manejar con elegancia las fallas del nodo de caché sin un impacto significativo en la aplicación.
6. **Implementar el calentamiento de caché** : desarrollar estrategias para rellenar previamente datos críticos en el caché para evitar inicios en frío.

### 8. Soluciones populares de almacenamiento en caché distribuido

**1. Redis**

[**Redis**](https://redis.io/) es un almacén de datos en memoria, de código abierto, que admite el almacenamiento de una amplia gama de estructuras de datos, incluidas cadenas, hashes, listas, conjuntos, conjuntos ordenados, mapas de bits, hiperlogs e índices geoespaciales.

Admite la replicación y persistencia de datos, lo que lo convierte en una opción popular para aplicaciones que requieren durabilidad de datos y tolerancia a fallas.

**2. Memcached**

[**Memcached**](https://memcached.org/) es otro sistema de almacenamiento en caché en memoria de código abierto, diseñado para brindar velocidad y simplicidad.

Se utiliza ampliamente para almacenar en caché pequeños fragmentos de datos, como resultados de consultas de bases de datos, llamadas API o representación de páginas.

Memcached es una caché pura en memoria sin capa de persistencia. Esto la hace ideal para casos donde los datos no necesitan almacenarse permanentemente, como el almacenamiento en caché de los resultados de las consultas a bases de datos.

**3. Amazon ElastiCache**

[**Amazon ElastiCache**](https://aws.amazon.com/elasticache/) es un servicio de almacenamiento en caché en memoria totalmente administrado, proporcionado por AWS. Es compatible con Redis y Memcached, lo que permite a los desarrolladores elegir el motor de almacenamiento en caché que mejor se adapte a sus necesidades.

ElastiCache admite implementaciones multi-AZ (zona de disponibilidad) con conmutación por error automática, lo que garantiza que el caché permanezca disponible incluso en el caso de una interrupción de la AZ.

Puede escalar automáticamente la cantidad de nodos en el clúster de caché según la demanda, lo que permite que el caché gestione distintos niveles de tráfico sin intervención manual.

### 9. Conclusión

El almacenamiento en caché distribuido es una técnica esencial para crear aplicaciones escalables y de alto rendimiento.

Al distribuir datos entre múltiples nodos, permite una mayor **escalabilidad** , **tolerancia a fallas** y **equilibrio de carga** .

Sin embargo, también introduce complejidades, como **la consistencia de los datos** , **la invalidación de la caché** y **la partición de la red** , que deben gestionarse con cuidado.

Asegúrese de evaluar cuidadosamente sus necesidades, elegir la arquitectura adecuada y seguir las mejores prácticas para garantizar una implementación exitosa.
