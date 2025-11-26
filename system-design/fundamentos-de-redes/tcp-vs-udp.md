# TCP vs UDP

Cuando envía un mensaje de WhatsApp, transmite un video de YouTube o juega un juego en línea, su dispositivo se basa en un conjunto de reglas para mover datos a través de Internet.

En el corazón de este proceso se encuentran dos protocolos fundamentales: **TCP** y **UDP** . Son los mensajeros invisibles que deciden _cómo_ se empaquetan y envían sus datos.

Tanto TCP (Protocolo de Control de Transmisión) como UDP (Protocolo de Datagramas de Usuario) operan en la **capa de transporte (Capa 4)** del modelo OSI. Su función es tomar datos de una aplicación en un dispositivo y entregarlos a una aplicación en otro.

Elegir entre ellos es una decisión crítica en el diseño del sistema.

* ¿Necesita un servicio de mensajería que garantice que cada pieza de su mensaje llegue en perfecto orden, incluso si tarda un poco más?
* ¿O necesita uno que se entregue lo más rápido posible, incluso si se pierden algunas piezas en el camino?

Esta elección es un acto de equilibrio constante entre **velocidad** , **confiabilidad** y **uso de recursos**.

## 1. El papel de la capa de transporte <a href="#id-1-the-role-of-the-transport-layer" id="id-1-the-role-of-the-transport-layer"></a>

Antes de profundizar en TCP y UDP, aclaremos la función de la **capa de transporte** . Mientras que la **capa de red (IP)** transfiere paquetes de datos de un ordenador a otro (de host a host), la capa de transporte transfiere datos de **una&#x20;**_**aplicación**_**&#x20;a otra (de proceso a proceso)** .

Sus responsabilidades clave incluyen:

* **Segmentación y reensamblaje** : dividir grandes fragmentos de datos de aplicación en paquetes más pequeños en el lado del remitente y reensamblarlos en el lado del receptor.
* **Modelo de entrega** : proporcionar un servicio **de entrega confiable** o una transmisión **con el máximo esfuerzo .**
* **Control de flujo y congestión** : gestión de la velocidad de transmisión de datos para evitar saturar al receptor o la propia red.

Aquí es donde TCP y UDP difieren. **TCP se centra en proporcionar un flujo de datos ordenado y altamente confiable, mientras que UDP prioriza la velocidad y la simplicidad con un enfoque de máximo esfuerzo.**

## 2. ¿Qué es TCP (Protocolo de control de transmisión)? <a href="#id-2-what-is-tcp-transmission-control-protocol" id="id-2-what-is-tcp-transmission-control-protocol"></a>

TCP es un protocolo **orientado a la conexión** que garantiza la entrega fiable de datos en el orden correcto. Es el mensajero meticuloso y fiable de internet. Antes de enviar cualquier dato, TCP establece una conexión formal entre el cliente y el servidor.

#### Características principales: <a href="#key-characteristics" id="key-characteristics"></a>

* **Establecimiento de la conexión** : TCP utiliza un **protocolo de enlace de tres vías** (SYN, SYN-ACK, ACK) para establecer una conexión fiable. Esta negociación inicial añade algo de latencia, pero garantiza que ambas partes estén listas para comunicarse.
* **Fiabilidad** : Cada paquete enviado se rastrea con un número de secuencia. El receptor envía acuses de recibo (ACK) por cada paquete que recibe. Si el emisor no recibe un ACK en un plazo determinado, **retransmite** el paquete perdido.
* **Entrega ordenada** : los números de secuencia también garantizan que los paquetes se vuelvan a ensamblar en el orden correcto en el destino, incluso si llegan fuera de orden.
* **Control de flujo y congestión** : TCP utiliza un mecanismo de "ventana deslizante" para evitar que el emisor sature al receptor ( **control de flujo** ). También ralentiza la transmisión de forma inteligente cuando detecta congestión en la red ( **control de congestión** ).

### Cómo funciona TCP <a href="#how-tcp-works" id="how-tcp-works"></a>

#### **1. Establecimiento de la conexión (protocolo de enlace de tres vías)** <a href="#id-1-connection-establishment-three-way-handshake" id="id-1-connection-establishment-three-way-handshake"></a>

Antes de que se produzca cualquier intercambio de datos, TCP establece una conexión entre el cliente y el servidor mediante un protocolo de enlace de tres pasos. Esto garantiza que ambas partes estén preparadas y puedan comunicarse de forma fiable.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.02.42.png" alt="" width="375"><figcaption></figcaption></figure>

* **SYN (Sincronizar):** El cliente inicia la conexión enviando un mensaje SYN, señalando su intención de iniciar la comunicación y compartiendo su número de secuencia inicial (ISN).
* **SYN-ACK (Synchronize–Acknowledge):** el servidor recibe el SYN, reserva recursos para la conexión y responde con un SYN-ACK para reconocer la solicitud y compartir su propio ISN.
* **ACK (Reconocimiento):** El cliente envía un ACK para confirmar la recepción del SYN-ACK, completando el protocolo de enlace.

En este punto, ambas partes tienen números de secuencia sincronizados y se establece un canal de comunicación confiable.

#### **2. Transferencia de datos** <a href="#id-2-data-transfer" id="id-2-data-transfer"></a>

Una vez establecida la conexión, TCP comienza a transmitir datos. El protocolo garantiza que todos los paquetes (segmentos) se entreguen **en orden, sin duplicaciones ni pérdidas** .

* **Segmentación:** los datos de gran tamaño se dividen en fragmentos más pequeños y manejables llamados _segmentos_ .
* **Acuses de recibo (ACK):** El receptor acusa recibo de cada segmento enviado. Si el emisor no recibe un ACK dentro del tiempo límite establecido, retransmite el segmento.
* **Ventana deslizante:** TCP utiliza un mecanismo de control de flujo basado en ventanas que permite que varios paquetes estén "en vuelo" antes de requerir un reconocimiento, lo que mejora el rendimiento.
* **Detección de errores:** Cada segmento incluye una suma de comprobación para detectar daños en tránsito. Los paquetes dañados se descartan y se reenvían.

En esencia, TCP se comporta como un servicio de mensajería confiable: entrega cada paquete, garantiza que llegue en el orden correcto y reenvía cualquiera que se pierda.

#### **3. Terminación de la conexión** <a href="#id-3-connection-termination" id="id-3-connection-termination"></a>

Una vez finalizada la comunicación, TCP cierra la conexión correctamente mediante un proceso de terminación de cuatro pasos. Esto garantiza que tanto el cliente como el servidor acepten que la sesión ha finalizado.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.03.19.png" alt="" width="375"><figcaption></figcaption></figure>

* **FIN (Finalizar):** El cliente inicia la terminación, indicando que no tiene más datos para enviar.
* **ACK:** El servidor reconoce el FIN, lo que permite procesar los datos restantes.
* **FIN:** El servidor envía su propio FIN para cerrar su lado de la conexión.
* **ACK:** El cliente confirma y ambas partes liberan sus recursos.

Este **apagado elegante** garantiza que no se pierdan datos y que ambas partes sepan que la conexión está cerrada.

## 3. ¿Qué es UDP (Protocolo de datagramas de usuario)? <a href="#id-3-what-is-udp-user-datagram-protocol" id="id-3-what-is-udp-user-datagram-protocol"></a>

UDP es un protocolo **sin conexión** que envía datos sin establecer una conexión formal. Funciona con el principio de "disparar y olvidar". Es el servicio de mensajería rápido y sencillo de internet.

#### Características principales: <a href="#key-characteristics" id="key-characteristics"></a>

* **Sin protocolo de enlace** : Los paquetes (denominados datagramas) se envían inmediatamente sin negociación previa. Esto reduce significativamente la latencia inicial.
* **Sin acuses de recibo** : UDP no espera acuses de recibo ni retransmite los paquetes perdidos. Si un paquete se pierde, se pierde definitivamente.
* **Sin orden** : no hay garantía de que los paquetes lleguen en el orden en que fueron enviados.
* **Ligero** : el encabezado UDP es mucho más pequeño (8 bytes) que el encabezado TCP (más de 20 bytes), lo que significa menos sobrecarga por paquete.

### Cómo funciona UDP <a href="#how-udp-works" id="how-udp-works"></a>

UDP no realiza un protocolo de enlace como TCP. No hay fase de configuración, ni intercambio de números de secuencia, ni confirmación de conexión.

Cuando una aplicación quiere enviar datos:

* Crea un **datagrama** , le adjunta una IP y un puerto de destino y lo envía directamente a la red.
* La aplicación receptora (si está escuchando en ese puerto) simplemente procesa los datos entrantes.

Esta simplicidad elimina la sobrecarga de conexión, lo que permite que UDP entregue datos casi instantáneamente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.03.52.png" alt="" width="375"><figcaption></figcaption></figure>

UDP trata cada paquete (denominado **datagrama** ) como un mensaje independiente. Cada datagrama lleva su propia información de encabezado y se enruta individualmente a través de la red.

Esto hace que UDP sea ideal para la comunicación en tiempo real, donde es aceptable descartar algunos paquetes, pero no así los retrasos.

## 4. Comparación directa: TCP vs UDP <a href="#id-4-head-to-head-comparison-tcp-vs-udp" id="id-4-head-to-head-comparison-tcp-vs-udp"></a>

Esta tabla resume las diferencias principales:

<table><thead><tr><th width="249">Característica</th><th>TCP (Protocolo de control de transmisión)</th><th>UDP (Protocolo de datagramas de usuario)</th></tr></thead><tbody><tr><td><strong>Tipo</strong></td><td>Orientado a la conexión</td><td>Sin conexión</td></tr><tr><td><strong>Fiabilidad</strong></td><td>Confiable (entrega confirmada)</td><td>No confiable (sin ACK, "máximo esfuerzo")</td></tr><tr><td><strong>Orden</strong></td><td>Garantizado (paquetes secuenciados)</td><td>No garantizado</td></tr><tr><td><strong>Velocidad</strong></td><td>Más lento debido a los apretones de manos y los gastos generales</td><td>Más rápido, mínima sobrecarga</td></tr><tr><td><strong>Manejo de pérdida de paquetes</strong></td><td>Retransmite paquetes perdidos</td><td>Ignora los paquetes perdidos</td></tr><tr><td><strong>Control de flujo y congestión</strong></td><td>Sí</td><td>No</td></tr><tr><td><strong>Casos de uso</strong></td><td>Web, correo electrónico, transferencia de archivos, API, bases de datos</td><td>Transmisión, juegos, VoIP, DNS</td></tr></tbody></table>

En esencia, **TCP prioriza la corrección, mientras que UDP prioriza la puntualidad.**

## 5. Innovaciones modernas <a href="#id-5-modern-innovations" id="id-5-modern-innovations"></a>

El clásico debate entre TCP y UDP ha evolucionado. Los protocolos modernos buscan lo mejor de ambos mundos.

#### QUIC (Conexiones rápidas a Internet UDP) <a href="#quic-quick-udp-internet-connections" id="quic-quick-udp-internet-connections"></a>

Desarrollado por Google y ahora base de **HTTP/3** , QUIC es un cambio radical.

* Se ejecuta a través de **UDP** para evitar la latencia inicial del protocolo de enlace TCP.
* Incorpora confiabilidad, control de congestión y gestión de flujo directamente en su propia capa.
* Cuenta con cifrado obligatorio incorporado (TLS 1.3).
* Admite **multiplexación** , donde se pueden enviar múltiples flujos de datos a través de una única conexión sin que uno bloquee a los demás.

Básicamente, **QUIC proporciona muchos de los beneficios de TCP con la velocidad de UDP.**

Algunas aplicaciones también construyen sus propias **capas de confiabilidad personalizadas** sobre UDP. Por ejemplo, un juego multijugador podría usar UDP para el movimiento de los jugadores, pero implementar un sistema de confirmación de confirmación (ACK) simple para eventos críticos como "el jugador usó una habilidad especial".

## 6. Elegir el protocolo adecuado <a href="#id-6-choosing-the-right-protocol" id="id-6-choosing-the-right-protocol"></a>

Al decidir entre TCP y UDP, tenga en cuenta estos factores:

**Estado latente**

* **TCP** : Introduce latencia de antemano con su protocolo de enlace de tres vías. Cada confirmación y posible retransmisión incrementa el tiempo de ida y vuelta (RTT).
* **UDP** : Tiene una latencia inicial casi nula. Los datos se envían inmediatamente. Esto supone una gran ventaja para aplicaciones con tiempos de respuesta limitados.

**Rendimiento**

* **TCP** : Su mecanismo de control de congestión puede limitar el rendimiento para adaptarse a las condiciones de la red. Esto favorece la salud general de internet, pero puede limitar la velocidad máxima de una sola conexión.
* **UDP** : Puede alcanzar un mayor rendimiento, ya que no se ralentiza por la congestión. El riesgo es que enviar demasiado rápido puede provocar una mayor pérdida de paquetes.

**Confiabilidad vs. Eficiencia**

* **TCP** : proporciona confiabilidad "gratis" en la capa de transporte, pero utiliza más CPU, memoria y ancho de banda para administrar las conexiones y el estado.
* **UDP** : es muy eficiente, pero traslada la responsabilidad de gestionar la pérdida de paquetes y ordenarlos a la capa de aplicación.

**Seguridad**

* **TCP** : Es la base de **TLS (Seguridad de la Capa de Transporte)** , que impulsa HTTPS. La naturaleza confiable y ordenada de TCP es un requisito previo para el funcionamiento de TLS.
* **UDP** : No cuenta con un mecanismo de seguridad integrado. Las aplicaciones deben usar un protocolo como **DTLS (Datagrama TLS)** para añadir una capa de cifrado.

A continuación se muestra un marco de decisión simple para su próxima entrevista o proyecto de diseño de sistemas:

| Si su aplicación requiere...                   | Luego usa...     |
| ---------------------------------------------- | ---------------- |
| **Entrega garantizada y ordenada**             | **TCP**          |
| **La latencia más baja posible**               | **UDP (o QUIC)** |
| **Tolerancia para cierta pérdida de paquetes** | **UDP**          |
| **Alta precisión e integridad de datos**       | **TCP**          |
| **Comunicación en tiempo real**                | **UDP**          |
| **Control de flujo/congestión integrado**      | **TCP**          |

Muchos sistemas a gran escala utilizan un **enfoque híbrido** :

* **TCP** para autenticación de usuarios críticos y llamadas API.
* **UDP** para enviar telemetría y métricas no esenciales.
* **QUIC/HTTP/3** para contenido web orientado al usuario y transmisión en tiempo real.

## 7. Casos de uso del mundo real <a href="#id-7-real-world-use-cases" id="id-7-real-world-use-cases"></a>

La elección entre TCP y UDP está determinada enteramente por los requisitos de la aplicación.

**Sistemas basados ​​en TCP:**

* **Tráfico web (HTTP/HTTPS)** : Al cargar una página web, cada byte de HTML, CSS y JavaScript debe llegar correctamente y en orden. Un paquete faltante podría afectar el diseño o la funcionalidad de la página.
* **Bases de datos** : Una consulta o transacción de base de datos debe ser 100 % completa y correcta. No puede permitirse tener un registro parcialmente actualizado debido a la pérdida de un paquete.
* **Correo electrónico (SMTP, IMAP)** : al igual que una página web, un mensaje de correo electrónico debe entregarse en su totalidad para ser legible.

**Sistemas basados ​​en UDP:**

* **Transmisión de video (YouTube, Netflix)** : Si se pierde un solo fotograma de un video, el usuario podría ver un pequeño fallo o nada en absoluto. Es mejor omitir ese fotograma y mostrar el siguiente en tiempo real que pausar toda la transmisión para esperar una retransmisión.
* **Juegos en línea** : En un juego de ritmo rápido, es mejor recibir información ligeramente desactualizada que recibir información perfecta demasiado tarde. El cliente del juego suele poder interpolar o predecir los datos faltantes.
* **Voz sobre IP (VoIP - Zoom, llamadas de WhatsApp)** : una pequeña y momentánea pérdida de audio es preferible a una demora prolongada y molesta mientras el sistema intenta recuperar un paquete perdido.
