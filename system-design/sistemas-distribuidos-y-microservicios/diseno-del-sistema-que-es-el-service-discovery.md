# Diseño del sistema: ¿Qué es el Service Discovery?

Cuando las aplicaciones se ejecutaban en un **solo servidor**, la vida era sencilla.

Las aplicaciones modernas de hoy en día son mucho más complejas, que constan de **docenas o incluso cientos de servicios**, cada uno con múltiples instancias que se escalan hacia arriba y hacia abajo dinámicamente.

Esto dificulta que los servicios se **encuentren y se comuniquen** de manera eficiente entre sí a través de las redes.

Ahí es donde entra en juego **Service Discovery**.

En este artículo, profundizaremos en qué es el descubrimiento de servicios, por qué es importante, cómo funciona, los diferentes tipos (descubrimiento del lado del cliente y del servidor) y las mejores prácticas para implementarlo de manera efectiva.

## **1. ¿Qué es el descubrimiento de servicio?**

**El descubrimiento de servicios** es un mecanismo que permite que los servicios en un sistema distribuido se **encuentren y se comuniquen** entre sí de forma dinámica.

Oculta los complejos detalles de dónde se encuentran los servicios, para que puedan interactuar sin conocer los puntos exactos de la red del otro.

El descubrimiento de servicios registra y mantiene un registro de todos sus servicios en un **registro de servicios**. Este registro de servicios actúa como una única fuente de verdad que permite a sus servicios consultar y comunicarse entre sí.

**Ejemplo de registro de registro de servicio de un servicio:**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2026-03-14 a la(s) 11.22.54.png" alt="" width="375"><figcaption></figcaption></figure>

Un registro de servicios normalmente almacena:

* **Detalles básicos:** nombre del servicio, IP, puerto y estado.
* **Metadatos:** versión, entorno, región, etiquetas, etc.
* **Información de salud:** estado de salud, último chequeo de salud.
* **Información de equilibrio de carga:** Pesos, prioridades.
* **Comunicación segura:** Protocolos, certificados.

Esta abstracción es importante en entornos donde los servicios se agregan, eliminan o escalan constantemente.

## **2. ¿Por qué es importante el descubrimiento de servicios?**

Piensa en un sistema masivo como **Netflix**, con cientos de microservicios trabajando juntos. La codificación de las ubicaciones de estos servicios no es escalable. Si un servicio se mueve a un nuevo servidor o escala dinámicamente, podría romper todo el sistema.

**El descubrimiento** de **servicios** resuelve esto al permitir de forma dinámica y fiable que los servicios se localicen y se comuniquen entre sí.

Estos son sus beneficios clave:

* **Configuración manual reducida:** los servicios pueden descubrir y conectarse automáticamente entre sí, eliminando la necesidad de configuración manual y codificación dura de las ubicaciones de la red.
* **Escalabilidad mejorada:** A medida que se agregan o eliminan nuevas instancias de servicio, el descubrimiento de servicios garantiza que otros servicios puedan adaptarse sin problemas al entorno cambiante.
* **Tolerancia a fallas:** el descubrimiento de servicios a menudo incluye comprobaciones de estado, lo que permite a los sistemas redirigir automáticamente el tráfico lejos de las instancias de servicio fallidas.
* **Gestión simplificada:** Tener un registro central de servicios facilita el monitoreo, la administración y la resolución de problemas de todo el sistema.

## 3. Opciones de registro de servicio

El registro de servicio es el proceso en el que un servicio anuncia su disponibilidad a un **registro de servicios**, lo que lo hace descubrible por otros servicios.

El método de registro puede variar dependiendo de la arquitectura, las herramientas y el entorno de implementación.

Estas son las **opciones de registro de servicio** más comunes:

### 3.1. **Registro manual**

En el registro manual, los detalles del servicio son añadidos al registro manualmente por un desarrollador u operador. Este enfoque es simple, pero no es adecuado para sistemas dinámicos donde los servicios escalan o se mueven con frecuencia.

### 3.2. **Autoinscripción**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2026-03-14 a la(s) 11.29.52.png" alt=""><figcaption></figcaption></figure>

En el autoregistro, el servicio es responsable de registrarse en el registro de servicios cuando comienza. El servicio incluye lógica para interactuar con el registro, como enviar solicitudes de API para registrar sus datos.

**Cómo funciona**

1. Cuando un servicio o una instancia se inicia, recupera su propia información de red (por ejemplo, dirección IP, puerto).
2. Envía una solicitud de registro al registro de servicios (por ejemplo, a través de HTTP o gRPC).
3. Para garantizar que el registro tenga información actualizada, el servicio puede enviar periódicamente señales de latidos para confirmar que está activo y sano.

### 3.3. **Registro de terceros (patrón de sidecar)**

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

En el registro de terceros, un agente externo o un proceso de "sidecar" maneja el registro del servicio. El servicio en sí no interactúa directamente con el registro. En su lugar, el sidecar detecta el servicio y lo registra en su nombre.

**Cómo funciona**

1. El sidecar funciona junto al servicio (por ejemplo, en el mismo contenedor o en el mismo host).
2. El sidecar detecta cuándo comienza el servicio y recopila los detalles de su red.
3. Envía la solicitud de registro al registro de servicios.

### 3.4. **Registro automático por parte de los orquestadores**

En entornos orquestados modernos como **Kubernetes**, el registro del servicio ocurre automáticamente. La plataforma de orquestación gestiona el ciclo de vida de los servicios y actualiza el registro de servicios a medida que los servicios comienzan, se detienen o escalan.

**Cómo funciona**

1. El orquestador (por ejemplo, Kubernetes) detecta cuándo se implementa un servicio o contenedor.
2. Asigna al servicio una dirección IP y un puerto.
3. Registra el servicio automáticamente con su mecanismo de descubrimiento de servicios incorporado (por ejemplo, Kubernetes DNS).

### 3.5. **Sistemas de gestión de configuración**

Algunos sistemas utilizan herramientas de gestión de configuración (por ejemplo, Chef, Puppet, Ansible) para registrar servicios. Estas herramientas gestionan el ciclo de vida del servicio y actualizan el registro de servicios cada vez que se añaden o eliminan servicios.

## **4. Tipos de descubrimiento de servicios**

Hay dos tipos principales de descubrimiento de servicios: descubrimiento del lado del cliente y descubrimiento del lado del servidor.

### **4.1. Descubrimiento del lado del cliente**

En este modelo, la responsabilidad de descubrir y conectarse a un servicio recae completamente en el cliente.

**Cómo funciona**

<figure><img src="../../.gitbook/assets/image (4).png" alt="" width="375"><figcaption></figcaption></figure>

1. **Registro de servicio:** Los servicios (por ejemplo, `UserService`, `PaymentService`) se registran con un **registro de servicio** centralizado.
   1. Proporcionan los detalles de su red (dirección IP y puerto) junto con metadatos como el estado del servicio o la versión.
2. **El cliente consulta el registro:** El cliente (un microservicio o puerta de enlace API) envía una solicitud al **registro de servicios** para encontrar las instancias de un servicio de destino (por ejemplo, `PaymentService`).
   1. El registro responde con una lista de instancias disponibles, incluidas sus direcciones IP y puertos.
3. **El cliente enruta la solicitud:** en función de la información recuperada, el cliente selecciona una de las instancias de servicio (a menudo utilizando un algoritmo de equilibrio de carga) y se conecta directamente a ella.

El cliente mantiene el control sobre cómo se enrutan las solicitudes, como distribuir el tráfico de manera uniforme entre instancias o priorizar la instancia más cercana.

**Ejemplo de flujo de trabajo**

Consideremos un ejemplo real de una aplicación de entrega de alimentos:

* Un **servicio de pago** tiene tres instancias que se ejecutan en diferentes servidores.
* Cuando el **Servicio de Pedidos** necesita procesar un pago, consulta el **registro de servicios** para la ubicación del `Payment Service`.
* The service registry responds with a list of available instances (e.g., `IP1:Port1`, `IP2:Port2`, `IP3:Port3`).
* The **Order Service** chooses an instance (e.g., `IP1:Port1`) and sends the payment request directly to it.

**Ventajas:**

* Fácil de implementar y entender.
* Reduce la carga en un equilibrador de carga central.

**Desventajas:**

* Los consumidores necesitan implementar la lógica de descubrimiento.
* Los cambios en el protocolo de registro requieren cambios en los clientes.

> **Ejemplo:** La biblioteca de código abierto de Netflix, **Eureka**, es una herramienta popular para el descubrimiento de servicios del lado del cliente.

### **4.2. Descubrimiento del lado del servidor**

En este modelo, el cliente delega la responsabilidad de descubrir y enrutar las solicitudes a una instancia de servicio específica a un **servidor centralizado o equilibrador de carga**.

A diferencia del descubrimiento del lado del cliente, el cliente no necesita consultar el registro de servicios directamente ni realizar ningún equilibrio de carga por sí mismo.

En su lugar, el cliente simplemente envía una solicitud a un servidor central (equilibrador de carga o puerta de enlace API), que se encarga del resto.

**Cómo funciona**

<figure><img src="../../.gitbook/assets/image (5).png" alt="" width="375"><figcaption></figcaption></figure>

1. **Registro de servicio:** Los servicios se registran con un **registro de servicio** centralizado, similar al descubrimiento del lado del cliente.
   * El registro de servicios realiza un seguimiento de todas las instancias de servicio, sus direcciones IP, puertos y metadatos.
2. **El cliente envía una solicitud:** El cliente envía una solicitud a un **equilibrador de carga** o **puerta de enlace API**, especificando el servicio con el que desea comunicarse (por ejemplo, `payment-service`).
   * El cliente no consulta el registro de servicios ni conoce la ubicación específica de las instancias de servicio.
3. **El servidor consulta el registro de servicios:** el equilibrador de carga o la puerta de enlace consulta el registro de servicios para encontrar instancias disponibles del servicio solicitado.
4. **Enrutamiento:** El equilibrador de carga selecciona una instancia de servicio adecuada (basada en factores como la carga, la proximidad o la salud) y dirige la solicitud del cliente a esa instancia.
5. **Respuesta:** La instancia de servicio procesa la solicitud y envía la respuesta de vuelta al cliente a través del equilibrador de carga o la puerta de enlace.

**Ejemplo de flujo de trabajo**

Tomemos un ejemplo de una plataforma de comercio electrónico con microservicios para "Gestión de pedidos" y "Procesamiento de pagos".

1. **Registration:** The `PaymentService` registers two instances with the service registry:
   * Instancia 1:`IP1:8080`
   * Instancia 2:`IP2:8081`
2. **Client Request:** The `OrderService` sends a request to the **load balancer** or API gateway, specifying the `PaymentService`.
3. **Discovery and Routing:** The load balancer queries the service registry and retrieves the list of available `PaymentService` instances.
   * It selects one instance (e.g., `IP1:8080`) and routes the request to it.
4. **Processing and Response:** The selected instance of `PaymentService` processes the request and sends the response back to the `OrderService` via the load balancer.

**Ventajas:**

* Centraliza la lógica de descubrimiento, reduciendo la complejidad para los consumidores.
* Más fácil de gestionar y actualizar los protocolos de descubrimiento.

**Desventajas:**

* Introduce un salto de red adicional.
* El equilibrador de carga puede convertirse en un único punto de fallo.

> **Ejemplo**: AWS Elastic Load Balancer (ELB) se integra con el registro de servicios de AWS para el descubrimiento del lado del servidor.

## **5. Mejores prácticas para implementar el descubrimiento de servicios**

1. **Elija el modelo correcto:** Utilice el descubrimiento del lado del cliente para el equilibrio de carga personalizado y el lado del servidor para el enrutamiento centralizado.
2. **Garantizar la alta disponibilidad:** Replicar el registro de servicio y probar los escenarios de conmutación por error para evitar el tiempo de inactividad. Implemente múltiples instancias del registro de servicios para evitar puntos de fallo únicos.
3. **Automatice el registro:** Utilice herramientas de autorregistro, sidecars u orquestación para entornos dinámicos. Garantizar la cancelación adecuada de los servicios rancios.
4. **Usar comprobaciones de estado:** Supervise el estado del servicio y elimine las instancias fallidas automáticamente.
5. **Siga las convenciones de denominación:** Utilice nombres de servicio claros y únicos con control de versiones para evitar conflictos (por ejemplo, `payment-service-v1`
6. **Almacenamiento en caché**: utilice mecanismos de almacenamiento en caché para reducir la carga en el registro de servicios y mejorar el rendimiento del descubrimiento.
7. **Escalabilidad**: Asegúrese de que el sistema de descubrimiento de servicios pueda escalar con el crecimiento de sus servicios.

## 6. Conclusión

Puede que el descubrimiento de servicios no sea el aspecto más glamoroso de los sistemas distribuidos, pero es sin duda uno de los más esenciales.

Piense en el descubrimiento de servicios como la libreta de direcciones de su arquitectura de microservicios. Sin él, escalar y mantener los sistemas distribuidos sería caótico.

Sirve como la columna vertebral que permite la comunicación y coordinación sin problemas entre los servicios, lo que permite que las aplicaciones complejas funcionen de manera confiable y eficiente.
