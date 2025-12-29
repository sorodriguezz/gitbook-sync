# Pub/Sub

**Publicar-Suscribir (Pub/Sub)**  es un patrón arquitectónico utilizado para permitir la comunicación asincrónica entre componentes en un sistema distribuido.

En un sistema Pub/Sub, los componentes pueden publicar mensajes en un tema sin necesidad de saber quién los recibirá. De igual forma, pueden suscribirse a temas para recibir mensajes sin necesidad de conocer los datos de los publicadores.

Esta disociación entre editores y suscriptores ofrece varias ventajas, como una mejor escalabilidad, un mantenimiento más sencillo y la capacidad de construir sistemas más resistentes.

## 1. Componentes de Pub/Sub <a href="#id-1-components-of-pub-sub" id="id-1-components-of-pub-sub"></a>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 10.11.32.png" alt=""><figcaption></figcaption></figure>

#### Editores <a href="#publishers" id="publishers"></a>

Los publicadores son componentes o servicios que envían mensajes (o eventos) a un tema. Generan datos o eventos y los publican sin preocuparse de quién los consumirá.

**Ejemplo:** en un sistema de comercio electrónico, un servicio que maneja el procesamiento de pedidos podría publicar un evento "Pedido realizado" cada vez que se recibe un nuevo pedido.

#### Suscriptores <a href="#subscribers" id="subscribers"></a>

Los suscriptores son componentes o servicios que expresan interés en ciertos temas. Reciben y procesan los mensajes publicados en esos temas.

**Ejemplo:** Un servicio de notificación podría suscribirse al tema "Pedido realizado" para enviar correos electrónicos de confirmación a los clientes.

#### Agente de mensajes <a href="#message-broker" id="message-broker"></a>

El  **Message Broker**  (o cola de mensajes) es el componente principal que gestiona los temas, recibe mensajes de los publicadores y los distribuye a todos los suscriptores de dichos temas. Actúa como intermediario para garantizar que los mensajes fluyan eficientemente entre publicadores y suscriptores.

**Corredores de mensajes populares:**

* Apache Kafka
* RabbitMQ
* Publicación/suscripción de Google Cloud
* Amazon SNS/SQS

## 2. Cómo funciona Pub/Sub <a href="#id-2-how-pub-sub-works" id="id-2-how-pub-sub-works"></a>

Repasemos una interacción típica de Pub/Sub:

1. **Los editores envían mensajes:** un editor crea un mensaje y lo envía a un tema específico en el agente de mensajes.
2. **El agente de mensajes recibe y almacena mensajes:** El agente recibe el mensaje y lo almacena temporalmente en el tema. Según la implementación, los mensajes pueden persistir en el disco para mayor durabilidad.
3. **Los suscriptores reciben mensajes:** Los suscriptores suscritos al tema reciben el mensaje. Esto puede ocurrir en tiempo real o, si no se requiere la entrega en tiempo real, el suscriptor puede sondear al intermediario.
4. **Procesamiento y acuse de recibo:** Los suscriptores procesan el mensaje. En algunos sistemas, el suscriptor envía un acuse de recibo al intermediario para confirmar que el mensaje se ha procesado correctamente.

## 3. Beneficios de usar Pub/Sub <a href="#id-3-benefits-of-using-pub-sub" id="id-3-benefits-of-using-pub-sub"></a>

* **Acoplamiento flexible:** Los editores y suscriptores no necesitan conocerse entre sí. Esto facilita el desarrollo, el mantenimiento y el escalado de cada componente de forma independiente.
* **Manejo de alto rendimiento:** un agente de mensajes puede administrar y enrutar de manera eficiente un gran volumen de mensajes, lo que lo hace ideal para sistemas de alto tráfico.
* **Procesamiento asincrónico:** los sistemas pueden procesar mensajes a su propio ritmo, lo que ayuda a equilibrar las cargas durante las horas pico.
* **Fácil integración:** se pueden agregar nuevos suscriptores a un tema sin modificar los editores.
* **Comunicación en tiempo real:** permite la difusión de datos en tiempo real, lo cual es crucial para aplicaciones como notificaciones en vivo, seguimiento de eventos y recopilación de datos de IoT.
* **Durabilidad del mensaje:** muchos intermediarios pueden conservar los mensajes para garantizar que no se pierdan, incluso si fallan partes del sistema.
* **Tolerancia a fallas:** la naturaleza desacoplada de Pub/Sub puede ayudar a aislar y gestionar fallas de manera más efectiva.

## 4. Casos de uso comunes <a href="#id-4-common-use-cases" id="id-4-common-use-cases"></a>

**Notificaciones en tiempo real**

* **Ejemplo:** Plataformas de redes sociales que utilizan Pub/Sub para enviar notificaciones instantáneas sobre Me gusta, comentarios o mensajes.

**Registro y análisis de eventos**

* **Ejemplo:** Se utilizan sistemas como Apache Kafka para transmitir registros de varios servicios para realizar análisis y monitoreo en tiempo real.

**Comunicación de microservicios**

* **Ejemplo:** En una arquitectura de microservicios, los servicios pueden comunicarse de forma asincrónica mediante Pub/Sub, lo que permite flujos de trabajo escalables y resilientes.

**Agregación de datos de IoT**

* **Ejemplo:** Los dispositivos en una red de IoT publican datos de sensores en temas, que luego pueden agregarse y procesarse en tiempo real.
