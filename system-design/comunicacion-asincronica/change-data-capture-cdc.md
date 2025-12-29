# Change Data Capture (CDC)

Las aplicaciones modernas a menudo dependen de múltiples sistemas (por ejemplo, motores de búsqueda, cachés, lagos de datos, microservicios), todos los cuales necesitan  **datos actualizados** .

**Los trabajos ETL por lotes** tradicionales   son lentos, introducen latencia y a menudo generan datos obsoletos e inconsistencias.

**La captura de datos de cambio (CDC)**  es un patrón de diseño utilizado para  **rastrear y capturar cambios**  en una base de datos (inserciones, actualizaciones, eliminaciones) y  **transmitir esos cambios en tiempo real**  a los sistemas posteriores.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 10.16.13.png" alt=""><figcaption></figcaption></figure>

Esto garantiza que los sistemas posteriores permanezcan sincronizados sin necesidad de costosos trabajos por lotes.

En este artículo, profundizaremos en cómo funciona el CDC, exploraremos diferentes estrategias de implementación, sus casos de uso del mundo real, desafíos y consideraciones.

## **1. Cómo funcionan los CDC** <a href="#id-1-how-cdc-works" id="id-1-how-cdc-works"></a>

A un alto nivel, el CDC funciona monitoreando continuamente una base de datos para detectar cambios en los datos (inserciones, actualizaciones y eliminaciones).

Cuando ocurre un cambio, CDC captura el evento de cambio y pone la información a disposición para su procesamiento.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 10.16.44.png" alt=""><figcaption></figcaption></figure>

El proceso normalmente implica:

* **Monitoreo:**  Detectar cambios en los sistemas de origen. Esto se puede lograr mediante activadores de bases de datos, la lectura de registros de transacciones o el uso de herramientas especializadas de CDC.
* **Captura:**  extracción de detalles sobre el evento de cambio (como valores anteriores y posteriores) junto con metadatos (por ejemplo, marca de tiempo, tabla modificada).
* **Entrega:**  transmitir el evento de cambio a los consumidores, lo que puede incluir colas de mensajes, canales de datos o sistemas de análisis en tiempo real.

Esto ayuda a lograr  **arquitecturas basadas en eventos**  donde las aplicaciones responden a los cambios de datos a medida que ocurren.

## 2. Enfoques de implementación de los CDC <a href="#id-2-cdc-implementation-approaches" id="id-2-cdc-implementation-approaches"></a>

Hay tres enfoques principales para implementar el CDC:

### **2.1 CDC basado en marcas de tiempo** <a href="#id-2-1-timestamp-based-cdc" id="id-2-1-timestamp-based-cdc"></a>

Este enfoque se basa en agregar una   columna `last_updated` o a las tablas de su base de datos. `last_modified`

Cada vez que se inserta o modifica una fila, esta columna se actualiza con la fecha y hora actual. Las aplicaciones consultan la tabla para encontrar filas con una  `last_updated` fecha posterior a la última sincronización.

**Ejemplo de consulta:**

`SELECT * FROM orders WHERE last_updated > '2024-02-15 12:00:00';`

**Ventajas:**

* **Simplicidad:**  Fácil de implementar sin grandes cambios en la arquitectura de la base de datos.
* **Sin dependencias externas:**  se puede ejecutar con operaciones SQL básicas, lo que lo hace accesible para proyectos más pequeños o sistemas heredados.

**Contras:**

* **Captura incompleta:**  este método puede no capturar registros eliminados ya que la marca de tiempo normalmente se actualiza en las filas existentes.
* **Sobrecarga de rendimiento:**  a medida que sus datos crecen, las consultas frecuentes basadas en marcas de tiempo pueden afectar el rendimiento, especialmente si la indexación no está configurada de manera óptima.
* **Posibles lagunas de datos:**  Las actualizaciones de marcas de tiempo mal configuradas o los problemas de desfase de reloj pueden provocar que se pierdan cambios.

### **2.2 CDC basado en desencadenantes** <a href="#id-2-2-trigger-based-cdc" id="id-2-2-trigger-based-cdc"></a>

El CDC basado en activadores implica configurar activadores de bases de datos que registran automáticamente los cambios en una tabla de auditoría separada cada vez que se produce una operación de inserción, actualización o eliminación.

Esta tabla de auditoría sirve luego como una fuente confiable de registros de cambios, que pueden enviarse a otros sistemas según sea necesario.

**Ejemplo de disparador:**

```sql
CREATE TRIGGER order_changes 
AFTER UPDATE ON orders 
FOR EACH ROW 
INSERT INTO order_audit (order_id, old_status, new_status, changed_at) 
VALUES (OLD.id, OLD.status, NEW.status, NOW());
```

**Ventajas:**

* **Captura en tiempo real:**  los activadores capturan los cambios inmediatamente cuando ocurren.
* **Auditoría detallada:**  ofrece un registro granular de los cambios, incluidos los valores antes y después, lo que resulta útil para los registros de auditoría y la depuración.
* **Flexibilidad:**  Se puede adaptar para capturar columnas o cambios específicos.

**Contras:**

* **Impacto en el rendimiento:**  los activadores se ejecutan durante la transacción, lo que puede ralentizar las operaciones de escritura en la base de datos.
* **Complejidad en el mantenimiento:**  los cambios en el esquema de la base de datos a menudo requieren actualizaciones correspondientes para activar las definiciones.
* **Uso intensivo de recursos:**  en entornos de muchas transacciones, la carga adicional de mantener una tabla de auditoría puede llegar a ser significativa.

### **2.3 CDC basado en registros** <a href="#id-2-3-log-based-cdc" id="id-2-3-log-based-cdc"></a>

El CDC basado en registros lee los cambios directamente del  **registro de escritura anticipada (WAL)**  o  **del registro binario (binlog)** de la base de datos . Este método intercepta las operaciones de bajo nivel de la base de datos, lo que le permite capturar todos los cambios realizados sin interferir con el flujo de trabajo normal de la aplicación.

**Ejemplos de herramientas:**

* **Debezium:**  una plataforma de código abierto que transmite cambios en las bases de datos.
* **Kafka Connect:**  a menudo se utiliza junto con Debezium para integrarse con Apache Kafka.
* **AWS DMS:**  un servicio administrado para migración de datos y CDC en la nube.

**Ventajas:**

* **Alta eficiencia:**  minimiza el impacto en la base de datos principal ya que aprovecha los registros existentes.
* **Escalabilidad:**  ideal para entornos grandes con muchas transacciones.
* **Captura de cambios integral:**  captura con precisión cada cambio, incluidas inserciones, actualizaciones y eliminaciones.
* **Latencia mínima:**  proporciona movimiento de datos casi en tiempo real, esencial para sistemas dinámicos basados ​​en datos.

**Contras:**

* **Configuración compleja:**  puede requerir configuración adicional y comprensión de los registros de la base de datos subyacente.
* **Dependencia de las características de la base de datos:**  no todas las bases de datos exponen sus registros de una manera que sea fácil de consumir.
* **Gastos generales de herramientas:**  a menudo requiere la integración con herramientas o servicios adicionales, lo que puede aumentar la complejidad general del sistema.

> En las aplicaciones modernas, generalmente se prefiere el CDC basado en registros porque captura de manera eficiente todos los tipos de cambios (inserciones, actualizaciones y eliminaciones) directamente desde los registros de transacciones, minimiza el impacto en la base de datos principal y se escala bien con grandes volúmenes de datos.

## **3. Casos de uso real de los CDC** <a href="#id-3-real-world-use-cases-of-cdc" id="id-3-real-world-use-cases-of-cdc"></a>

### 3.1. Comunicación de microservicios <a href="#id-3-1-microservices-communication" id="id-3-1-microservices-communication"></a>

En una arquitectura de microservicios, los servicios individuales a menudo necesitan comunicarse y compartir cambios de estado sin estar estrechamente acoplados.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 10.18.11.png" alt="" width="563"><figcaption></figcaption></figure>

Con CDC en su lugar, el cambio se captura y se propaga a través de un sistema de mensajería (como Kafka) para que cada microservicio pueda mantenerse actualizado sobre los cambios relevantes en las bases de datos de otros servicios sin necesidad de llamadas directas de servicio a servicio.

### 3.2 Abastecimiento de eventos <a href="#id-3-2-event-sourcing" id="id-3-2-event-sourcing"></a>

El abastecimiento de eventos implica registrar cada cambio en el estado de una aplicación como una secuencia de eventos. CDC puede aprovecharse para capturar estos cambios en tiempo real, creando un registro completo de todas las modificaciones.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 10.18.50.png" alt="" width="563"><figcaption></figcaption></figure>

**Ejemplo:**

Considere una aplicación financiera que registra cada transacción. En lugar de simplemente actualizar el saldo de una cuenta, cada depósito, retiro o transferencia se registra como un evento. CDC captura estos eventos y crea un registro detallado de todos los cambios de estado. Este registro de auditoría puede utilizarse posteriormente para reconstruir el historial de cualquier cuenta o para depurar problemas.

### 3.3 Almacenamiento de datos <a href="#id-3-3-data-warehousing" id="id-3-3-data-warehousing"></a>

El almacenamiento de datos implica la consolidación de grandes volúmenes de datos transaccionales para su análisis y generación de informes. CDC puede capturar los cambios en la base de datos a medida que ocurren y transferirlos a un almacén de datos casi en tiempo real.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 10.19.24.png" alt="" width="563"><figcaption></figcaption></figure>

Los analistas y los responsables de la toma de decisiones utilizan luego datos actualizados para elaborar informes, análisis y paneles de control.

### 3.4 Invalidación de caché <a href="#id-3-4-cache-invalidation" id="id-3-4-cache-invalidation"></a>

Las cachés se utilizan para mejorar el rendimiento de las aplicaciones almacenando datos a los que se accede con frecuencia. Sin embargo, los datos de caché obsoletos pueden causar problemas, provocando que se muestre información obsoleta o incorrecta.

CDC puede activar actualizaciones de caché automáticamente cada vez que cambien los datos subyacentes.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 10.19.53.png" alt="" width="563"><figcaption></figcaption></figure>

**Ejemplo:**

Una plataforma de noticias en línea utiliza el almacenamiento en caché para acelerar la carga de las páginas de artículos populares. Sin embargo, cuando se actualiza un artículo (por ejemplo, al publicar una corrección o añadir contenido nuevo), el caché debe invalidarse para evitar que se publique contenido obsoleto.

Con CDC, los cambios en la base de datos de contenido se capturan y activan automáticamente actualizaciones de caché, lo que garantiza que los lectores siempre vean la información más actualizada.

## **4. Implementación de CDC con Debezium y Kafka** <a href="#id-4-implementing-cdc-with-debezium-and-kafka" id="id-4-implementing-cdc-with-debezium-and-kafka"></a>

[**Debezium**](https://debezium.io/)  es una popular herramienta de código abierto que proporciona CDC basado en registros para varias bases de datos como MySQL, PostgreSQL y MongoDB.

Cuando se integra con Apache Kafka, Debezium puede capturar y transmitir cambios en la base de datos casi en tiempo real.

#### **Paso 1: Configurar Kafka y Debezium** <a href="#step-1-set-up-kafka-and-debezium" id="step-1-set-up-kafka-and-debezium"></a>

Antes de configurar Debezium, necesita tener un clúster de Kafka en ejecución.

```shellscript
# Start Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties &

# Start Kafka Broker
bin/kafka-server-start.sh config/server.properties &
```

#### **Paso 2: Configurar el conector de Debezium para MySQL** <a href="#step-2-configure-debezium-connector-for-mysql" id="step-2-configure-debezium-connector-for-mysql"></a>

A continuación, cree una configuración de conector de Debezium para capturar los cambios de su base de datos MySQL. Esta configuración indica a Debezium qué base de datos y tablas debe supervisar, junto con los detalles de conexión necesarios.

```json
{
  "name": "inventory-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "database.hostname": "localhost",
    "database.port": "3306",
    "database.user": "cdc_user",
    "database.password": "password",
    "database.server.id": "184054",
    "database.include.list": "ecommerce",
    "table.include.list": "ecommerce.orders",
    "database.history.kafka.bootstrap.servers": "localhost:9092",
    "database.history.kafka.topic": "dbhistory.inventory"
  }
}
```

#### **Paso 3: Escuche los cambios** <a href="#step-3-listen-for-changes" id="step-3-listen-for-changes"></a>

Una vez que el conector Debezium está configurado y funcionando correctamente, comienza a capturar eventos de cambio de la base de datos MySQL.

Estos eventos se publican en los temas de Kafka. Puedes consumirlos mediante las herramientas de línea de comandos de Kafka o cualquier aplicación de consumo de Kafka.

```shell
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic dbhistory.inventory --from-beginning
```

## **5. Desafíos y consideraciones** <a href="#id-5-challenges-and-considerations" id="id-5-challenges-and-considerations"></a>

Si bien CDC es una herramienta poderosa para la integración de datos en tiempo real, su implementación conlleva varios desafíos que deben gestionarse con cuidado:

1. **Evolución del esquema** : Las bases de datos evolucionan con el tiempo, con cambios como la adición o modificación de columnas. Una canalización de CDC robusta debe adaptarse con fluidez a las modificaciones del esquema, garantizando que no se pierdan ni se malinterpreten los cambios en los datos.
2. **Manejo de alto rendimiento** : En entornos con un alto volumen de transacciones, grandes volúmenes de cambios de datos pueden ocurrir rápidamente. Es fundamental diseñar un sistema CDC que pueda procesar y retransmitir estos eventos eficientemente para evitar la sobrecarga de los sistemas posteriores.
3. **Garantías de orden** : Mantener la secuencia correcta de eventos es fundamental, especialmente en arquitecturas distribuidas. La solución CDC debe garantizar que los eventos se procesen en el orden exacto en que ocurrieron, preservando la integridad de los datos en todos los servicios que los consumen.
4. **Seguridad y cumplimiento** : Dado que el CDC implica la captura de cambios detallados en los datos, puede exponer información confidencial si no se gestiona adecuadamente. Implementar medidas de seguridad robustas, como el cifrado, el enmascaramiento de datos y controles de acceso estrictos, es vital para proteger los datos y cumplir con los requisitos regulatorios.
