# Data Replication

### ¿Qué es la replicación de datos?

**La replicación de datos, también conocida como replicación de bases de datos, es un método para copiar datos y garantizar que toda la información se mantenga idéntica en tiempo real entre todos los recursos de datos.** Piense en [la replicación de bases de datos](https://redis.io/redis-enterprise/technology/highly-available-redis/) como la red que captura su información y evita que se pierda. Los datos no suelen permanecer estancados. Están en constante cambio. Es un proceso continuo que garantiza que los datos de una base de datos principal se reflejen en una réplica, incluso si se encuentra en el otro extremo del planeta.

Hoy en día, la rapidez no es suficiente. Reducir la latencia a intervalos de menos de un milisegundo es el objetivo universal. Todos hemos visto esta situación: presionar el botón de actualización en un sitio web y esperar una eternidad (segundos) para ver la información actualizada. [La latencia](https://redis.io/docs/latency-is-the-new-outage/) reduce la productividad del usuario. El objetivo es lograr **un tiempo casi real** . El nuevo ideal para cualquier caso de uso es un retraso cero.

### ¿Cómo funciona la replicación de datos?

La replicación de datos consiste en copiar datos de un host a otro, por ejemplo, entre dos servidores locales, o de uno local a la nube, y así sucesivamente. El objetivo es lograr consistencia en tiempo real con los datos para todos los usuarios, independientemente de dónde accedan a ellos. Los modelos de negocio basados ​​en datos (MBD), como el de la [industria del gaming](https://redis.io/industries/gaming/) , dependen en gran medida de los análisis obtenidos a través de datos en tiempo real. Para comprender mejor la necesidad de la accesibilidad en tiempo real para un MBD, vea el siguiente vídeo:

### ¿Cuáles son los beneficios de la replicación de datos?

#### Mayor confiabilidad y recuperación ante desastres

En caso de emergencia, si su instancia principal se ve comprometida, es vital proteger las aplicaciones críticas con una réplica que pueda reemplazarla. Los métodos [de replicación para la recuperación ante desastres](https://redis.io/redis-enterprise/technology/backup-disaster-recovery/) funcionan de forma similar a un generador de respaldo; imagine que se funde un fusible crítico o que se corta la red eléctrica: no tendrá que preocuparse porque cuenta con un generador de respaldo que puede sustituirlo y mantener la electricidad en funcionamiento.

Dado que las instancias de réplica son copias exactas de las instancias principales, puede garantizar que el rendimiento no se vea afectado, independientemente de lo que le ocurra a la instancia principal. Incluso si se interrumpe el enlace entre una instancia principal y una réplica, el rendimiento está garantizado, ya que la instancia principal realizará una resincronización parcial, recopilando los comandos que no se entregaron a la réplica durante la desconexión. De no ser posible, se iniciará una resincronización completa mediante una instantánea.

#### Mayor rendimiento de la aplicación

Al distribuir los datos en varias instancias, se optimiza el rendimiento de lectura. El rendimiento también se optimiza al tener los datos accesibles en múltiples ubicaciones, lo que minimiza los problemas de latencia. Además, cuando las réplicas se encargan de procesar la mayoría de las lecturas, se libera espacio para que la instancia principal se encargue de la mayor parte de las escrituras.

#### Equipos de TI más eficientes

Reducción de la mano de obra de TI para replicar datos manualmente.

### Comprender la replicación completa de datos frente a la replicación parcial

**La replicación completa de bases de datos** se produce cuando se replica una base de datos primaria completa en cada instancia de réplica disponible. Este enfoque holístico replica datos preexistentes, nuevos y actualizados en todos los destinos. Si bien este enfoque es muy completo, también requiere una capacidad de procesamiento considerable y sobrecarga la red debido al gran tamaño de los datos copiados.

A diferencia de la replicación completa, **la replicación parcial** solo refleja algunas partes de los datos, generalmente datos actualizados recientemente. La replicación parcial aísla fragmentos específicos de datos según su importancia en una ubicación específica. Por ejemplo, una gran empresa financiera con sede en Londres podría tener numerosas oficinas satélite operando en todo el mundo, con una oficina en Boston, otra en Kuala Lumpur, etc.

La replicación parcial permite a los analistas de Londres tener en sus instalaciones únicamente datos relevantes del Reino Unido y que solo esos datos se repliquen consistentemente para sus necesidades. Las demás oficinas satélite en Estados Unidos y Malasia, respectivamente, pueden hacer lo mismo sin sobrecargar ningún sistema, lo que mejora el rendimiento y minimiza el tráfico de red.

### Ejemplos de replicación de datos

#### Replicación transaccional

Este tipo de replicación de bases de datos ve los datos de una base de datos primaria replicando los datos en tiempo real a una instancia de réplica, reflejando estos cambios en el orden en que se realizaron en la base de datos primaria. Esto optimiza la consistencia. La replicación toma lo que se denomina una "instantánea" de los datos en la base de datos primaria y la utiliza como modelo de lo que debe replicarse en otro lugar. Con la replicación transaccional, puede rastrear y distribuir los cambios según sea necesario.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.20.40.png" alt="" width="375"><figcaption></figcaption></figure>

Se comparte una instantánea de la primaria con la réplica.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.21.30.png" alt="" width="375"><figcaption></figcaption></figure>

El primario envía datos recopilados después de la instantánea a la réplica

Dada la naturaleza incremental de este proceso, la replicación transaccional no es la mejor opción para una base de datos de respaldo. Es una opción útil cuando se necesita consistencia en tiempo real en todas las ubicaciones de datos, donde se debe contabilizar cada pequeño cambio, no solo su impacto general, y si los datos cambian regularmente desde una ubicación específica.

#### Replicación de instantáneas

Como su nombre indica, la replicación de instantáneas toma una instantánea de los datos del servidor principal tal como aparecen en un momento específico y la transfiere a la réplica. Al igual que una fotografía, la replicación de instantáneas captura el aspecto de los datos en un momento dado, tal como se ven al transferirse del servidor principal a la réplica, pero no tiene en cuenta cómo se actualizan posteriormente. Por lo tanto, no utilice la replicación de instantáneas para crear una copia de seguridad.

En caso de fallo de almacenamiento, la replicación de instantáneas no tendrá acceso a la información actualizada. Para mantener la coherencia de la información, puede comenzar con una instantánea, pero luego asegúrese de que todos los cambios realizados en la copia de seguridad principal se transfieran a todas las réplicas.

Por otro lado, este método es bastante útil para recuperar archivos en caso de borrado accidental. Piensa en ello como tu Historial de versiones en Google Docs. ¿Te gustaría poder trabajar en tu presentación como se veía hace cuatro horas? Si Google Docs toma una instantánea de tu trabajo cada hora, podrías volver a esa versión, o "instantánea", de hace cuatro horas y ver cómo se veía tu información en ese momento.

#### Replicación de fusión

Este método suele comenzar con una instantánea de los datos y distribuirlos a sus réplicas, manteniendo la sincronización de datos en todo el sistema. La diferencia de la replicación de fusión reside en que permite que cada nodo modifique los datos de forma independiente, pero fusiona todas esas actualizaciones en un todo unificado.

La replicación de combinación también contabiliza cada cambio realizado en cada nodo. Volviendo a nuestro ejemplo anterior de Google Docs, si alguna vez compartiste un documento con compañeros que luego dejaron comentarios y modificaciones, verás quién realizó qué cambios y en qué momento. La replicación de combinación funciona de forma muy similar.

#### Replicación basada en claves

También conocido como replicación incremental de datos basada en claves, este método utiliza una clave de replicación para identificar, localizar y modificar únicamente los datos específicos modificados desde la última actualización. Al aislar dicha información, facilita el proceso de copia de seguridad, trabajando solo con la carga necesaria. Si bien la replicación basada en claves permite actualizar rápidamente los datos nuevos, presenta la desventaja de no poder replicar los datos eliminados.

#### Geodistribución activa-activa

[La geodistribución activo-activo](https://redis.io/active-active/) , también conocida como **replicación punto a punto** , funciona de forma similar a la replicación transaccional, ya que se basa en datos transaccionales constantes a través de nodos. Con la geodistribución activo-activo, todos los nodos de la misma red se envían datos constantemente entre sí mediante la sincronización de la base de datos con los nodos correspondientes. Además, todos los nodos tienen permisos de escritura, lo que significa que cualquiera puede modificar los datos en cualquier parte del mundo y se reflejará en todos los demás nodos. Esto garantiza la consistencia en tiempo real, independientemente del lugar del mundo donde se produzca el cambio.

Los tipos de datos replicados sin conflictos (CRDT) definen cómo se replican estos datos. En caso de fallo de red en una de las réplicas o nodos, las demás réplicas tendrán todos los datos necesarios listos para replicarse una vez que el nodo vuelva a estar en línea. Esta es una solución sólida para empresas que necesitan varios centros de datos distribuidos por todo el mundo. Vea el vídeo a continuación para ver ejemplos de casos de uso de la geodistribución activa-activa.

#### Replicación sincrónica y asincrónica

Con la replicación síncrona, los datos se escriben tanto en el servidor principal como en la réplica simultáneamente, de ahí su nombre. La replicación asíncrona, en cambio, solo copia los datos a la réplica una vez que se han realizado las escrituras en el servidor principal. La replicación asíncrona no suele ocurrir en tiempo real, aunque es posible. Debido a las operaciones de escritura programadas que suelen ocurrir en lotes con la replicación asíncrona, a veces se pierden datos, generalmente cuando se produce una conmutación por error. Aun así, la replicación asíncrona es una solución adecuada cuando se necesita replicar datos a largas distancias, ya que el componente de tiempo real no es un factor crítico.

### ¿Cuáles son los desafíos comunes en la implementación de la replicación de datos?

Mantener datos en múltiples instancias requiere un conjunto consistente de recursos. El **costo** de tener una instancia principal con múltiples réplicas puede ser bastante alto en muchos casos. Mantener estas operaciones y garantizar que no se produzcan fallos del sistema requiere un **equipo dedicado** de expertos. Además, dependiendo de la arquitectura, el **ancho de banda** de la red podría sobrecargarse al implementar nuevos procesos, lo que podría afectar las latencias, las lecturas y las escrituras.

### Preguntas frecuentes

* ¿Qué es la replicación de datos?
  * La replicación de datos es un método de copia de datos para garantizar que toda la información se mantenga idéntica en tiempo real entre todos los recursos de datos. Piense en la [replicación de bases de datos](https://redis.io/redis-enterprise/technology/highly-available-redis/) como la red que captura su información y evita que se pierda. Los datos no suelen permanecer estancados. Están en constante cambio. Es un proceso continuo que garantiza que los datos de una base de datos principal se reflejen en una réplica, incluso si se encuentra en el otro extremo del planeta.
* ¿Qué es una base de datos replicada?
  * Una base de datos replicada es un tipo de sistema de bases de datos que mantiene múltiples copias de los mismos datos en diferentes nodos o servidores. El propósito de replicar los datos es mejorar la disponibilidad, la tolerancia a fallos y el rendimiento.

Redis facilita la replicación de datos, [la hace rentable](https://redis.io/redis-enterprise/technology/highly-available-redis/) y fácil de implementar. ¿Listo para el tratamiento Redis Enterprise?
