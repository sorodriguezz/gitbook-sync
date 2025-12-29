# Databases Types

Las bases de datos son componentes fundamentales del diseño de sistemas que se utilizan para almacenar, gestionar y recuperar datos de manera eficiente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.29.02.png" alt="" width="563"><figcaption></figcaption></figure>

En este artículo, exploraremos 15 tipos comunes de bases de datos y analizaremos cuándo usarlas, junto con ejemplos.

### 1. Bases de datos relacionales (RDBMS)

Las bases de datos relacionales estructuran los datos en una o más tablas de filas y columnas, con una clave única que identifica cada fila. Las filas de una tabla pueden vincularse con las de otras tablas mediante claves foráneas, estableciendo así una relación entre ellas.

Esta estructura permite que las bases de datos relacionales manejen grandes cantidades de datos estructurados, refuercen la integridad de los datos y admitan consultas complejas y transacciones ACID.

Utilizan el lenguaje de consulta estructurado (SQL) para definir, manipular y consultar datos, lo que los hace muy versátiles y ampliamente utilizados en diversas aplicaciones.

**Casos de uso comunes:**

* **Aplicaciones empresariales:** para gestionar datos de clientes, inventario, registros de empleados y transacciones financieras, donde la integridad y las relaciones de los datos son fundamentales.
* **Plataformas de comercio electrónico:** Manejo de catálogos de productos, pedidos de clientes y transacciones de pago, lo que requiere consultas complejas y procesamiento de transacciones.
* **Servicios Bancarios y Financieros:** Gestión de cuentas, transacciones y datos de usuarios, donde las propiedades ACID aseguran la confiabilidad y consistencia de las operaciones financieras.

> **Ejemplos:** MySQL, PostgreSQL, base de datos Oracle.

### 2. Almacén de clave-valor

Los almacenes de clave-valor son bases de datos NoSQL que almacenan datos como pares clave-valor, lo que proporciona una recuperación rápida de valores basados ​​en claves únicas.

Se utilizan principalmente cuando el modelo de datos se basa en pares clave-valor y requiere alta escalabilidad, disponibilidad y rendimiento.

Sin embargo, es posible que no sean la opción más adecuada para aplicaciones que requieren consultas complejas, relaciones de datos o fuertes garantías de consistencia.

**Casos de uso comunes:**

* **Almacenamiento de sesión:** almacenamiento y gestión de información de la sesión del usuario, como preferencias de usuario, carritos de compra o tokens de autenticación en aplicaciones web.
* **Almacenamiento en caché:** implementación de mecanismos de almacenamiento en caché para mejorar el rendimiento de las aplicaciones web almacenando datos a los que se accede con frecuencia en la memoria para una rápida recuperación.
* **Procesamiento de datos en tiempo real:** los almacenes de clave-valor pueden almacenar y recuperar datos rápidamente para análisis en tiempo real, procesamiento de eventos o colas de mensajes.

> **Ejemplos:** Redis, DynamoDB.

### 3. Bases de datos de documentos

Las bases de datos de documentos, un subconjunto de la familia NoSQL más amplia, están diseñadas para almacenar, administrar y recuperar información orientada a documentos.

Estas bases de datos manejan datos en un formato semiestructurado, normalmente JSON, XML o BSON, lo que permite un esquema más flexible que las bases de datos relacionales tradicionales.

Las bases de datos de documentos son particularmente útiles en escenarios donde el modelo de datos evoluciona con frecuencia y donde el rendimiento rápido de lectura y escritura es fundamental.

Sin embargo, es posible que no sean la opción más adecuada para datos altamente estructurados o transacciones complejas que abarcan múltiples documentos o colecciones.

**Casos de uso comunes:**

* **Plataformas de comercio electrónico:** almacenamiento de catálogos de productos con diversos atributos, reseñas de usuarios y datos de inventario, lo que permite una representación flexible de la información del producto.
* **Sistemas de gestión de contenidos (CMS):** ideales para gestionar artículos, perfiles de usuarios y comentarios, donde cada pieza de contenido puede almacenarse como un documento.
* **Análisis en tiempo real e IoT:** manejo de diversas estructuras de datos generadas por dispositivos IoT y soporte de análisis en tiempo real de estos datos.

> **Ejemplos:** MongoDB, Couchbase y Apache CouchDB

### 4. Bases de datos gráficas

Las bases de datos gráficas son un tipo de base de datos NoSQL que se especializa en almacenar, administrar y consultar redes complejas de datos interconectados.

Representan datos como gráficos, que constan de nodos (entidades), aristas (relaciones entre entidades) y propiedades (información asociada a nodos y aristas).

Al aprovechar la estructura del gráfico, las bases de datos de gráficos permiten un recorrido, una consulta y un análisis eficientes de datos interconectados.

Son muy útiles en aplicaciones como redes sociales y motores de recomendación.

**Casos de uso comunes:**

* **Redes sociales:** gestión de perfiles de usuarios y sus conexiones, habilitando funciones como recomendaciones de amigos y análisis de gráficos sociales.
* **Sistemas de recomendación:** análisis de las preferencias de los clientes, inventarios de productos e historiales de compras para generar recomendaciones personalizadas de productos o contenidos.
* **Gráficos de conocimiento:** creación de grandes repositorios de datos interconectados para búsquedas semánticas, recuperación de información y sistemas de apoyo a la toma de decisiones.

> **Ejemplos:** Neo4j, Amazon Neptune.

### 5. Tiendas de columnas anchas

Los almacenes de columnas anchas representan un tipo de base de datos NoSQL optimizada para almacenar y consultar grandes cantidades de datos en muchas máquinas.

Organizan los datos en tablas con una estructura de columnas flexible y dinámica. Están diseñados para gestionar el almacenamiento de datos distribuido a gran escala y ofrecen alta escalabilidad y rendimiento.

Su arquitectura orientada a columnas, su esquema flexible y su modelo de consistencia final los hacen ideales para aplicaciones que requieren un alto rendimiento de escritura y procesamiento de datos en tiempo real.

Sin embargo, es posible que no sean la opción más adecuada para casos de uso que requieren uniones complejas, gran consistencia o transacciones ACID estrictas.

**Casos de uso comunes:**

* **Análisis web y seguimiento de usuarios:** ideal para capturar y analizar datos de eventos en tiempo real, como análisis web, registros de actividad de usuarios y monitoreo de red.
* **Análisis en tiempo real:** pueden agregar y analizar datos rápidamente, lo que los hace adecuados para paneles de control, sistemas de alerta y análisis operativos.

> **Ejemplos:** Apache Cassandra, Apache HBase, Google Bigtable.

### 6. Bases de datos en memoria

Las bases de datos en memoria almacenan datos directamente en la memoria principal (RAM) de la computadora, a diferencia del almacenamiento basado en disco.

Están diseñados para proporcionar acceso a datos extremadamente rápido y baja latencia al eliminar la necesidad de operaciones de E/S de disco.

Las bases de datos en memoria son particularmente adecuadas para aplicaciones que requieren procesamiento en tiempo real, transacciones de alta velocidad y acceso a datos de baja latencia, como almacenamiento en caché, análisis en tiempo real y comercio de alta frecuencia.

Sin embargo, son costosos y la memoria principal puede no tener capacidad suficiente para almacenar todo el conjunto de datos.

**Casos de uso comunes:**

* **Juegos en línea:** para administrar las sesiones de usuario y el estado del juego en tiempo real, garantizando experiencias de juego rápidas y receptivas.
* **Trading de alta frecuencia:** Permiten una gran cantidad de transacciones financieras por segundo con una latencia mínima.

> **Ejemplos:** Redis, Memcached.

### 7. Bases de datos de series temporales

Las bases de datos de series de tiempo (TSDB) se especializan en almacenar, recuperar y administrar datos con marcas de tiempo o series de tiempo.

Los datos de series temporales son una secuencia de puntos de datos recopilados durante intervalos de tiempo.

Las TSDB se utilizan comúnmente en aplicaciones que generan y procesan datos de series temporales, como sistemas de monitoreo, redes de sensores, plataformas de comercio financiero y dispositivos IoT (Internet de las cosas).

Proporcionan el rendimiento, la escalabilidad y las funciones especializadas necesarias para gestionar las características únicas de los datos de series de tiempo.

**Casos de uso comunes:**

* **Plataformas de negociación financiera:** para rastrear precios de acciones, volúmenes comerciales e indicadores de mercado a lo largo del tiempo, lo que permite el análisis de tendencias y estrategias comerciales algorítmicas.
* **Gestión de datos de sensores e IoT:** recopilación y análisis de datos de sensores y dispositivos IoT, útiles en hogares inteligentes, automatización industrial y monitoreo ambiental.
* **Monitoreo del rendimiento:** En TI e infraestructura de red, para monitorear las métricas del sistema (uso de CPU, consumo de memoria, tráfico de red) a lo largo del tiempo, lo que ayuda en la planificación de la capacidad y la detección de anomalías.

> **Ejemplos:** InfluxDB, TimescaleDB, Prometheus.

### 8. Bases de datos orientadas a objetos

Las bases de datos orientadas a objetos (OODB) son bases de datos que almacenan y manipulan datos como objetos.

Estos objetos son instancias de clases, que pueden encapsular tanto datos (atributos) como comportamientos (métodos), reflejando la estructura y los conceptos de lenguajes de programación orientados a objetos como Java, C++ o Python.

Las bases de datos OODB son especialmente adecuadas para aplicaciones donde se necesitan modelos de datos complejos o donde la lógica de la aplicación depende en gran medida de principios orientados a objetos.

Al permitir que los desarrolladores trabajen directamente con objetos en la base de datos, las OODB pueden simplificar el proceso de desarrollo y proporcionar una forma más natural y eficiente de gestionar estructuras y relaciones de datos complejas.

**Casos de uso comunes:**

* **Aplicaciones orientadas a objetos:** aplicaciones desarrolladas utilizando lenguajes OOP que requieren un mecanismo de persistencia continuo para almacenar y recuperar objetos sin la necesidad de convertirlos a un formato diferente (mapeo objeto-relacional).
* **Bases de datos multimedia:** almacenamiento, organización y recuperación de elementos multimedia como imágenes, vídeos y archivos de audio, que pueden beneficiarse de la encapsulación de datos y comportamientos (por ejemplo, métodos para reproducir o editar).

> **Ejemplos:** ObjectDB, db4o

### 9. Bases de datos de búsqueda de texto

Las bases de datos de búsqueda de texto son sistemas especializados diseñados para el almacenamiento, indexación y recuperación eficiente de grandes volúmenes de datos de texto no estructurados o semiestructurados.

Proporcionan capacidades de búsqueda rápidas y escalables, permitiendo a los usuarios consultar y encontrar información relevante en grandes colecciones de documentos, páginas web u otro contenido basado en texto.

**Casos de uso comunes:**

* **Comercio electrónico:** para búsquedas de productos dentro de tiendas en línea, ayudando a los clientes a encontrar productos según descripciones, reseñas y metadatos.
* **Búsqueda web:** se utilizan en motores de búsqueda como Google, Bing y DuckDuckGo para indexar y buscar la gran cantidad de contenido disponible en Internet, lo que permite a los usuarios encontrar páginas web relevantes en función de sus consultas.
* **Análisis de registros:** se pueden utilizar para indexar y buscar grandes volúmenes de datos de registro, como registros de aplicaciones o registros del sistema, con fines de resolución de problemas, monitoreo y análisis.

> **Ejemplos:** Elasticsearch, Apache Solr, Sphinx.

### 10. Bases de datos espaciales

Las bases de datos espaciales están diseñadas para almacenar, gestionar y analizar datos que representan información geográfica o espacial. Amplían las capacidades de las bases de datos tradicionales para gestionar tipos de datos espaciales complejos, como puntos, líneas, polígonos y otras formas geométricas, junto con sus atributos y relaciones asociados.

Las bases de datos espaciales emplean técnicas de indexación eficientes, como árboles R o árboles cuádruples, para optimizar las consultas espaciales y mejorar el rendimiento.

Se utilizan ampliamente en servicios que se basan en la ubicación del usuario, como mapear rutas, encontrar restaurantes cercanos o rastrear movimientos de vehículos en tiempo real.

**Casos de uso comunes:**

* **Sistemas de Información Geográfica (SIG):** Para mapear, analizar y gestionar datos relacionados con lugares en la superficie de la Tierra para la planificación urbana, la gestión ambiental y la planificación de respuesta a emergencias.
* **Servicios basados ​​en la ubicación (LBS):** para proporcionar servicios basados ​​en la ubicación del usuario, como mapear rutas y encontrar restaurantes cercanos.
* **Logística y transporte:** Las bases de datos espaciales se utilizan en los sistemas de logística y transporte para optimizar rutas, rastrear los movimientos de vehículos y analizar patrones de tráfico.

> **Ejemplos:** PostGIS (extensión para PostgreSQL), Oracle Spatial.

### 11. Almacén de datos de blobs

Los almacenes de datos Blob (Binary Large Object) están diseñados para almacenar, administrar y recuperar grandes bloques de datos no estructurados, como imágenes, archivos de audio, videos y documentos.

A diferencia de las bases de datos tradicionales que manejan datos estructurados con campos y registros bien definidos, los almacenes de datos blob están optimizados para blobs de datos grandes y complejos que no encajan perfectamente en los esquemas de bases de datos estándar.

Proporcionan una solución escalable, altamente disponible, duradera y rentable para gestionar cantidades masivas de datos no estructurados.

**Casos de uso comunes:**

* **Redes de distribución de contenido (CDN):** para almacenar y entregar archivos multimedia de gran tamaño, como vídeos e imágenes, a usuarios de todo el mundo.
* **Almacenamiento de big data:** los almacenes de datos blob pueden almacenar grandes conjuntos de datos, como archivos de registro, datos de sensores y datos científicos, para análisis de big data y canales de procesamiento.
* **Copia de seguridad y archivado:** Los almacenes de datos blob ofrecen una solución fiable y escalable para el almacenamiento de datos de copia de seguridad, archivos y la retención de datos a largo plazo. También ofrecen opciones de almacenamiento rentables para datos de acceso poco frecuente.

> **Ejemplos:** Amazon S3, Azure Blob Storage, HDFS.

### 12. Bases de datos de contabilidad

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.30.58.png" alt=""><figcaption></figcaption></figure>

Las bases de datos de contabilidad, también conocidas como bases de datos blockchain, están diseñadas para proporcionar un registro inmutable y de solo anexos de las transacciones.

Están diseñados para garantizar que una vez que se registra una transacción, no se pueda alterar ni eliminar, proporcionando un historial verificable y a prueba de manipulaciones de todos los cambios a lo largo del tiempo.

Las bases de datos de contabilidad son particularmente adecuadas para aplicaciones que requieren un alto nivel de confianza, transparencia e inmutabilidad.

**Casos de uso comunes:**

* **Gestión de la cadena de suministro:** las bases de datos de contabilidad pueden rastrear el movimiento de bienes y materiales a lo largo de una cadena de suministro, lo que garantiza la transparencia y la trazabilidad.
* **Atención médica:** gestión de registros de pacientes, formularios de consentimiento e historiales de tratamiento con un registro claro e inmutable.
* **Sistemas de votación:** Las bases de datos de contabilidad pueden proporcionar una plataforma segura y transparente para llevar a cabo procesos de votación al garantizar la integridad de los resultados de la votación y evitar alteraciones o manipulaciones.

> **Ejemplos:** Amazon Quantum Ledger Database (QLDB), Hyperledger Fabric.

### 13. Bases de datos jerárquicas

Las bases de datos jerárquicas organizan los datos en una estructura similar a un árbol, donde los datos se almacenan en registros y cada registro tiene un único registro padre pero puede tener varios registros hijos, lo que establece una relación de uno a muchos entre los registros.

Las bases de datos jerárquicas eran populares en los inicios de la informática, sobre todo en los sistemas mainframe. Se usaban comúnmente para sistemas de archivos, donde los directorios y archivos encajaban de forma natural en una estructura jerárquica.

Sin embargo, han sido reemplazados en gran medida por otros modelos de bases de datos, como bases de datos relacionales y bases de datos NoSQL, que ofrecen más flexibilidad y mejor soporte para relaciones complejas.

**Casos de uso comunes:**

* **Estructuras organizativas:** gestión de datos en organigramas donde cada entidad (por ejemplo, empleado) tiene una relación jerárquica clara.
* **Sistemas de archivos:** La estructura de directorios de los sistemas de archivos es un ejemplo clásico de datos jerárquicos, donde las carpetas tienen subcarpetas y archivos.

> **Ejemplos:** IBM IMS, Registro de Windows

### 14. Bases de datos vectoriales

Las bases de datos vectoriales son bases de datos especializadas diseñadas para almacenar y buscar vectores, que son matrices de números que representan datos en espacios de alta dimensión.

Están optimizados para la búsqueda de similitud y consultas de vecinos más cercanos, lo que permite una recuperación rápida de elementos similares en función de sus representaciones vectoriales.

Estas bases de datos son particularmente relevantes en el campo del aprendizaje automático y la inteligencia artificial (IA), donde las representaciones vectoriales se utilizan comúnmente para codificar las características de varios tipos de datos, incluidos texto, imágenes y audio.

**Casos de uso comunes:**

* **Búsqueda de imágenes y vídeos:** las bases de datos vectoriales permiten la recuperación de imágenes y vídeos basada en contenido almacenando características visuales como vectores de alta dimensión.
* **Sistemas de recomendación:** al representar a los usuarios y elementos (por ejemplo, productos, películas) como vectores, las bases de datos vectoriales pueden identificar y recomendar rápidamente elementos similares a los intereses de un usuario.
* **Detección de anomalías:** al comparar la similitud de nuevos puntos de datos con muestras normales conocidas, se pueden detectar anomalías en función de su disimilitud.

> **Ejemplos:** Faiss, Milvus, Piña.

### 15. Bases de datos integradas

Las bases de datos integradas son bases de datos especializadas diseñadas para integrarse estrechamente en aplicaciones de software. A diferencia de las bases de datos cliente-servidor tradicionales, que se ejecutan como procesos independientes, las bases de datos integradas están vinculadas y se ejecutan como parte de la propia aplicación.

Al estar estrechamente integrados en el proceso de aplicación, ofrecen acceso rápido a los datos, espacio reducido y una implementación simplificada.

Las bases de datos integradas son particularmente útiles en entornos con recursos limitados donde una base de datos cliente-servidor completa no es necesaria ni práctica.

**Casos de uso comunes:**

* **Juegos:** guardar estados de juego, progreso del jugador y configuraciones directamente dentro de la aplicación del juego.
* **Aplicaciones de escritorio:** almacenamiento de configuraciones, preferencias de usuario y datos de aplicaciones de forma local en la máquina de un usuario.

> **Ejemplos:** SQLite, RocksDB, Berkeley DB.

A la hora de elegir una base de datos, no existe una solución universal.\
La elección de una base de datos depende de su caso de uso específico, modelo de datos, necesidades de escalabilidad y presupuesto.
