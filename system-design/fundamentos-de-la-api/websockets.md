# WebSockets

## ¿Qué son los WebSockets y por qué se utilizan?

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.03.07.png" alt=""><figcaption></figcaption></figure>

Imagina un juego multijugador en línea donde la tabla de clasificación **se actualiza instantáneamente** a medida que los jugadores obtienen puntos, mostrando **clasificaciones en tiempo real** de todos los jugadores.

Esta actualización instantánea se siente perfecta y te mantiene involucrado, pero ¿cómo funciona realmente?

La magia detrás de esta experiencia en tiempo real a menudo está impulsada por WebSockets.

Los WebSockets permiten una comunicación **bidireccional y full-duplex** entre un cliente (normalmente un navegador web) y un servidor a través de una **única conexión TCP** .

A diferencia del protocolo HTTP tradicional, donde el cliente envía una solicitud al servidor y espera una respuesta, WebSockets permite que tanto el cliente como el servidor se envíen mensajes entre sí de forma independiente y continua después de que se establece la conexión.

En este artículo, exploraremos cómo funcionan los websockets, por qué/dónde se utilizan, cómo se comparan con otros métodos de comunicación, desafíos y consideraciones, y cómo implementarlos en el código.

## 1. ¿Cómo funcionan los WebSockets?

La conexión WebSocket comienza con una solicitud HTTP estándar del cliente al servidor.

Sin embargo, en lugar de completar la solicitud y cerrar la conexión, el servidor responde con un código de estado [**HTTP 101**](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/101) , indicando que el protocolo está cambiando a WebSockets.

Después de este protocolo de enlace, se establece una conexión WebSocket y tanto el cliente como el servidor pueden enviarse mensajes entre sí a través de la conexión abierta.

<figure><img src="../../.gitbook/assets/image (2).png" alt="" width="375"><figcaption></figcaption></figure>

### Proceso paso a paso:

#### **1. Apretón de manos**

El cliente inicia una solicitud de conexión utilizando una solicitud HTTP GET estándar con un encabezado "Upgrade" establecido en "websocket".

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.05.06.png" alt="" width="375"><figcaption></figcaption></figure>

Si el servidor admite WebSockets y acepta la solicitud, responde con un código de estado especial 101, que indica que el protocolo se cambiará a WebSocket.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.05.41.png" alt=""><figcaption></figcaption></figure>

#### **2. Conexión**

Una vez completado el protocolo de enlace, se establece la conexión WebSocket. Esta conexión permanece abierta hasta que el cliente o el servidor la cierren explícitamente.

#### **3. Transferencia de datos**

Tanto el cliente como el servidor ahora pueden enviar y recibir mensajes en tiempo real.

Estos mensajes se envían en paquetes pequeños llamados **marcos** y tienen una sobrecarga mínima en comparación con las solicitudes HTTP tradicionales.

#### **4. Cierre**

La conexión puede ser cerrada en cualquier momento tanto por el cliente como por el servidor, normalmente con un **marco "cerrar"** que indica el motivo del cierre.

## 2. ¿Por qué se utilizan WebSockets?

Los WebSockets ofrecen varias ventajas que los hacen ideales para ciertos tipos de aplicaciones:

* **Actualizaciones en tiempo real** : los WebSockets permiten la transmisión instantánea de datos, lo que los hace perfectos para aplicaciones que requieren actualizaciones en tiempo real, como chat en vivo, juegos o plataformas de comercio financiero.
* **Latencia reducida** : como la conexión es persistente, no es necesario establecer una nueva conexión para cada mensaje, lo que reduce significativamente la latencia.
* **Uso eficiente de recursos** : los WebSockets son más eficientes que las técnicas de sondeo tradicionales, ya que no requieren que el cliente solicite continuamente actualizaciones al servidor.
* **Comunicación bidireccional** : tanto el cliente como el servidor pueden iniciar la comunicación, lo que permite aplicaciones más dinámicas e interactivas.
* **Menor sobrecarga** : después del protocolo de enlace inicial, los marcos WebSocket tienen un encabezado pequeño (tan solo 2 bytes), lo que reduce la cantidad de datos transferidos.

## 3. WebSockets vs. HTTP, sondeo y sondeo largo

Para comprender las ventajas de WebSockets, es útil compararlos con otros métodos de comunicación:

### **HTTP:**

* **Modelo de solicitud-respuesta** : En HTTP, el cliente envía una solicitud y el servidor responde, cerrando posteriormente la conexión. Este modelo no tiene estado y no es adecuado para la comunicación en tiempo real.
* **Latencia** : dado que cada interacción requiere una nueva solicitud, HTTP tiene una mayor latencia en comparación con WebSockets.

### **Votación:**

<figure><img src="../../.gitbook/assets/image (1).png" alt="" width="375"><figcaption></figcaption></figure>

* **Solicitudes repetidas** : El cliente envía solicitudes repetidas al servidor a intervalos fijos para buscar actualizaciones. Si bien esto puede simular actualizaciones en tiempo real, resulta ineficiente, ya que muchas solicitudes no devuelven datos nuevos.
* **Latencia** : el sondeo introduce retrasos porque las actualizaciones solo se verifican periódicamente.

### **Sondeo largo:**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.08.26.png" alt="" width="375"><figcaption></figcaption></figure>

* **Conexión persistente** : En el sondeo prolongado, el cliente envía una solicitud y el servidor mantiene la conexión abierta hasta que tenga datos para enviar. Una vez enviados los datos o se agota el tiempo de espera, la conexión se cierra y el cliente envía inmediatamente una nueva solicitud.
* **Latencia:** este enfoque reduce la frecuencia de las solicitudes, pero aún sufre una mayor latencia en comparación con WebSockets, ya que requiere que el cliente envíe repetidamente nuevas solicitudes HTTP después de que se complete cada solicitud anterior.
* **Uso de recursos** : el sondeo prolongado puede provocar el agotamiento de los recursos en el servidor, ya que debe administrar muchas conexiones abiertas y manejar reconexiones frecuentes.

### **WebSockets:**

<figure><img src="../../.gitbook/assets/image.png" alt="" width="375"><figcaption></figcaption></figure>

* **Bidireccional** : a diferencia de HTTP, el sondeo y el sondeo largo, los WebSockets permiten la comunicación bidireccional.
* **Baja latencia** : debido a que la conexión permanece abierta, los datos se pueden enviar y recibir con un retraso mínimo.
* **Eficiencia** : Los WebSockets son más eficientes en términos de uso de recursos y ancho de banda.

## 4. Desafíos y consideraciones

Si bien los WebSockets ofrecen numerosos beneficios, existen algunos desafíos a tener en cuenta:

* **Servidores proxy** : algunos servidores proxy no admiten conexiones WebSocket y ciertos firewalls pueden bloquearlos.
* **Preocupaciones de escalabilidad** : Gestionar un gran número de conexiones WebSocket puede ser un desafío. Considere el uso de **balanceadores de carga** y servidores WebSocket distribuidos para gestionar implementaciones a gran escala.
* **Mecanismo de respaldo** : No todos los clientes o redes admiten WebSockets, lo que puede causar problemas de conectividad. Implemente mecanismos de respaldo, como el sondeo prolongado, para los clientes que no pueden establecer conexiones WebSocket.
* **Fiabilidad de la red:** Los WebSockets dependen de una conexión persistente, que puede verse interrumpida por problemas de red. Implementar **estrategias de reconexión** y **mecanismos de latido** (mensajes ping/pong regulares) puede ayudar a mantener la estabilidad de la conexión y detectar cuándo se pierde.
* **Seguridad:** Los WebSockets son vulnerables a ataques como el secuestro de WebSockets entre sitios y los ataques de denegación de servicio distribuido (DDoS). Implemente **conexiones WebSocket seguras (wss://)** , autentique a los usuarios y valide la entrada para protegerse contra vulnerabilidades comunes.

## 5. ¿Dónde se utilizan los WebSockets?

### **1. Herramientas de colaboración en tiempo real**

Aplicaciones como Google Docs pueden usar WebSockets para que varios usuarios editen un documento simultáneamente. Los cambios realizados por un usuario se reflejan instantáneamente para todos los demás, creando una experiencia colaborativa fluida.

### **2. Aplicaciones de chat en tiempo real**

Uno de los usos más populares de WebSockets es en aplicaciones de chat en tiempo real.

Las plataformas de mensajería como Slack utilizan WebSockets para enviar mensajes al instante. Esto permite conversaciones en tiempo real y notificaciones de entrega inmediatas.

### **3. Notificaciones en vivo**

Las plataformas de redes sociales utilizan WebSockets para enviar notificaciones en tiempo real a los usuarios cuando reciben un nuevo mensaje, me gusta o comentario.

En lugar de que el cliente busque constantemente nuevas notificaciones, el servidor puede enviar actualizaciones al cliente tan pronto como se producen.

### **4. Juegos multijugador en línea**

En los juegos multijugador en línea, la baja latencia es crucial para una experiencia de juego fluida.

Los WebSockets proporcionan la comunicación en tiempo real necesaria entre el servidor del juego y los jugadores, garantizando que todos los jugadores vean el mismo estado del juego simultáneamente.

### **5. Fuentes de datos del mercado financiero**

Los WebSockets se utilizan ampliamente en aplicaciones financieras para transmitir datos del mercado en tiempo real, como precios de acciones, tipos de cambio y valores de criptomonedas.

### **6. Aplicaciones de IoT (Internet de las cosas)**

En las aplicaciones de IoT, los dispositivos a menudo necesitan comunicarse con un servidor en tiempo real.

Los WebSockets proporcionan un canal de comunicación liviano y eficiente para enviar datos de sensores, recibir comandos y sincronizar estados de dispositivos.

### **7. Transmisión y retransmisión en directo**

Si bien la transmisión de video real generalmente utiliza otros protocolos, los WebSockets se pueden usar para chat en tiempo real, recuento de espectadores y otras funciones interactivas durante transmisiones en vivo.

## 6. Implementación de WebSockets

Para demostrar cómo funcionan los WebSockets, veamos una implementación simple utilizando Node.js en el lado del servidor y JavaScript en el lado del cliente **.**

### **Del lado del servidor (Node.js):**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.15.32.png" alt="" width="375"><figcaption></figcaption></figure>

### **Lado del cliente (JavaScript):**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.16.19.png" alt="" width="375"><figcaption></figcaption></figure>

## 7. Conclusión

Los WebSockets han revolucionado la forma en que construimos aplicaciones web en tiempo real, proporcionando un canal de comunicación eficiente y de baja latencia que admite el intercambio de datos full-duplex.

Desde aplicaciones de chat y notificaciones en vivo hasta juegos en línea y dispositivos IoT, los WebSockets permiten la creación de experiencias de usuario atractivas y receptivas.

Sin embargo, un gran poder conlleva una gran responsabilidad. Implementar WebSockets requiere una cuidadosa consideración de la escalabilidad, la seguridad y la gestión de recursos para garantizar el buen rendimiento de la aplicación en cualquier situación.
