# Webhooks

Imagina que estás construyendo una plataforma de comercio electrónico y utilizas un procesador de pagos externo como  [Stripe](https://stripe.com/)  para cobrar los pagos de los usuarios.

Una vez que un usuario completa un pago, su sistema debe:

* Marcar el pedido como pagado
* Enviar una factura
* Notificar al almacén para iniciar el embalaje.

**Pero aquí está el reto:** Stripe opera con su propia infraestructura. Tu sistema no la controla. Entonces, ¿cómo sabes  **al instante**  cuándo se realiza un pago?

Una solución ingenua sería seguir preguntando a Stripe cada pocos segundos:

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.18.19.png" alt="" width="375"><figcaption></figcaption></figure>

Esto se conoce como  **votación** .

Ahora imagina hacer esto para  **cada pedido**  en un sitio como Amazon.

Simplemente no escala y desperdicia recursos del servidor.

En lugar de que tu aplicación te pregunte repetidamente,  **¿qué pasaría si Stripe pudiera avisarte simplemente**  cuando el pago se realizó correctamente?

Eso es lo que  hacen **los webhooks**  .

En este artículo, exploraremos:

* ¿Qué es un Webhook?
* ¿Cómo funcionan los webhooks?
* ¿Cómo se ve una solicitud de Webhook?
* ¿Cómo configurar un receptor de Webhook?
* ¿Cómo diseñar una infraestructura de Webhook escalable?

## 1. ¿Qué es un Webhook? <a href="#id-1-what-is-a-webhook" id="id-1-what-is-a-webhook"></a>

> Un webhook es una forma sencilla para que un sistema (proveedor) notifique a otro sistema (receptor) en tiempo real cuando ocurre un evento mediante una solicitud HTTP.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.20.00.png" alt="" width="375"><figcaption></figcaption></figure>

En lugar de que un sistema pregunte repetidamente si sucedió algo, el otro sistema simplemente envía los datos tan pronto como se activa el evento.

**Analogía del mundo real**

Digamos que vas a un restaurante muy concurrido.

El anfitrión dice: «Hay una espera de 30 minutos. Por favor, deje su número y le avisaremos cuando su mesa esté lista».

No necesitas estar en el mostrador preguntando cada 2 minutos: "¿Ya está lista mi mesa?"

En lugar de eso, te alejas y, cuando llega tu turno,  **te avisan automáticamente** .

Esa es la idea detrás de los webhooks.

## 2. ¿Cómo funcionan los webhooks? <a href="#id-2-how-webhooks-work" id="id-2-how-webhooks-work"></a>

En un nivel alto, los webhooks funcionan a través  **del registro, la activación y la entrega** .

Veamos un ejemplo real para ver qué sucede realmente bajo el capó.

### Ejemplo: Webhook de GitHub para su aplicación <a href="#example-github-webhook-to-your-app" id="example-github-webhook-to-your-app"></a>

Digamos que has creado un sistema que necesita reaccionar cuando alguien abre una solicitud de extracción (PR) en un repositorio de GitHub, tal vez para iniciar una canalización de CI/CD, enviar una notificación de Slack o ejecutar pruebas automatizadas.

Así es como funciona el flujo del webhook paso a paso:

**Paso 1: Registra un webhook**

* Vaya a GitHub → Configuración → Webhooks
* Proporciona una  **URL de webhook**  (por ejemplo,  `https://myapp.com/github-webhook`)
* Tú eliges los  **eventos**  que te interesan, como  `pull_request`,  `push`, o `issue_comment`

En este punto, GitHub sabe dónde enviar los datos cuando ocurren esos eventos.

**Paso 2: GitHub monitorea eventos**

* GitHub monitorea su flujo de eventos interno para su repositorio
* Cuando alguien abre una nueva solicitud de extracción, el evento se registra internamente

**Paso 3: GitHub envía una solicitud POST**

* GitHub prepara una  **carga JSON**  con detalles sobre el evento: quién abrió el PR, qué rama, metadatos del repositorio, etc.
* Realiza una  `POST` solicitud a la URL de tu webhook:

**Paso 4: Su servidor procesa el evento**

* Su aplicación recibe la solicitud
* Verifica la firma (para asegurarte de que realmente proviene de GitHub)
* Procesa la carga útil, tal vez la pone en cola en una cola de trabajos, la registra o activa la lógica empresarial.
* Respondes con un HTTP  `200 OK` para confirmar la recepción.

## 3. Anatomía de una solicitud de webhook

Para integrarse con webhooks exitosamente, necesita comprender  **qué se envía realmente**  a su servidor cuando ocurre el evento.

La mayoría de los proveedores de webhook utilizan el  **método HTTP**  para enviar datos de eventos a su servidor.`POST`

* ¿Por qué usar POST? Porque permite enviar la carga útil en el cuerpo de la solicitud, que puede incluir datos estructurados (normalmente JSON) que describen lo sucedido.
* En raras ocasiones, algunos servicios permiten  `GET`,  `PUT`o incluso  `PATCH`, pero  `POST` es el estándar de facto para la entrega de webhooks.

### Encabezados de solicitud <a href="#request-headers" id="request-headers"></a>

Los encabezados de una solicitud de webhook a menudo incluyen  **metadatos**  e  **información relacionada con la seguridad** .

Los encabezados comunes incluyen:

* **Tipo de contenido:**  generalmente aplicación/json (o, a veces, aplicación/x-www-form-urlencoded)
* **Agente de usuario:**  identifica al remitente (por ejemplo, Stripe/1.0, GitHub-Hookshot)
* **X-Event-Type:**  describe qué tipo de evento ocurrió (payment\_intent.succeeded, pull\_request, etc.)
* **X-Signature / X-Hub-Signature:**  un hash de la carga útil que utiliza una clave secreta (HMAC) utilizada para verificar la autenticidad de la solicitud
* **X-Request-ID:**  ID único para el evento webhook (útil para registro/depuración)

### Cuerpo de la solicitud <a href="#request-body" id="request-body"></a>

El cuerpo de una solicitud de webhook suele contener  **datos de eventos**  en  **formato JSON** . Estos datos incluyen qué sucedió, cuándo sucedió y a qué usuario o recurso está relacionado.

**Ejemplo: Evento de relaciones públicas de GitHub**

```json
{
  "action": "opened",
  "number": 42,
  "pull_request": {
    "id": 11223344,
    "title": "Add login validation",
    "user": {
      "login": "octocat"
    },
    "created_at": "2025-04-21T12:00:00Z"
  },
  "repository": {
    "name": "awesome-project",
    "full_name": "octocat/awesome-project"
  },
  "sender": {
    "login": "octocat"
  }
}
```

**Ejemplo: Intención de pago de Stripe exitosa**

```json
{
  "id": "evt_1PoJkD2eZvKYlo2CmOJbvwD9",
  "object": "event",
  "api_version": "2024-08-01",
  "created": 1713681701,
  "data": {
    "object": {
      "id": "pi_3JhdNe2eZvKYlo2C1IqojYg9",
      "object": "payment_intent",
      "amount": 2000,
      "currency": "usd",
      "status": "succeeded"
    }
  },
  "livemode": false,
  "type": "payment_intent.succeeded"
}
```

## 4. Configuración de un receptor de webhook <a href="#id-4-setting-up-a-webhook-receiver" id="id-4-setting-up-a-webhook-receiver"></a>

Una vez que haya registrado la URL de su webhook con un proveedor como GitHub o Stripe, el siguiente paso es  **construir un punto final sólido en su servidor**  que pueda recibir y procesar estos eventos de manera confiable.

Esto suena simple: solo recibir una solicitud POST, ¿verdad?

Pero en producción, las cosas se complican:

* Los webhooks pueden llegar  **varias veces** o **fuera de orden**
* Es posible que su servidor esté  **bajo carga** o  **no disponible por un breve período.**
* **Los actores maliciosos**  podrían intentar falsificar las solicitudes

Analicemos cómo configurar su receptor de webhook de la  manera _correcta_  .

### 4.1 Configuración básica <a href="#id-4-1-basic-setup" id="id-4-1-basic-setup"></a>

**Crear un punto final dedicado**

Comience por exponer un punto final HTTP simple como:

`POST /webhook`

Este punto final debería:

* Aceptar únicamente solicitudes POST
* Acepta solo JSON (vía  `Content-Type`)
* Ser accesible a través de HTTPS

**Hazlo idempotente**

&#x20;Los proveedores  pueden  **reintentar** ,  **duplicar** o incluso  **reproducir eventos de webhook. Su controlador debe garantizar que procesar el mismo evento varias veces**  no tenga efectos secundarios.

¿Cómo?

* Cada evento de webhook incluye un  **ID único**  (por ejemplo,  `evt_1234` en Stripe,  `delivery_id` en GitHub)
* Almacenar un registro de identificaciones procesadas en una base de datos o caché
* Antes de procesar, verifique si el ID del evento ya se ha manejado

```java
if (eventAlreadyProcessed(eventId)) {
    return "200 OK";
}
```

Esto garantiza  **un procesamiento exactamente único** , incluso si recibe el mismo evento más de una vez.

**Manejar los códigos de estado HTTP correctamente**

* Devolución  `200 OK` después de una gestión exitosa
* Regresar  `400 Bad Request` si la carga útil no es válida o está mal formada
* Evite los errores 5xx a menos que algo realmente haya fallado porque la mayoría de los proveedores  **vuelven a intentarlo**  en 5xx

### 4.2 Consideraciones de seguridad <a href="#id-4-2-security-considerations" id="id-4-2-security-considerations"></a>

Los webhooks son puntos finales públicos. Esto significa que  **cualquier persona en internet puede publicar en ellos** . Debes validar las solicitudes entrantes.

**Verificar la firma (HMAC)**

La mayoría de los proveedores incluyen una  **firma criptográfica**  de la carga útil, utilizando un secreto compartido. Esto garantiza que la carga útil provenga de la fuente real.

**Ejemplo (Raya):**

* Configura un secreto de webhook: `whsec_abc123`
* Stripe firma la carga útil utilizando HMAC-SHA256
* Verifica la firma de tu parte:

```java
String expectedSignature = hmacSha256(secret, payload);

if (!expectedSignature.equals(signatureFromHeader)) {
    return "403 Forbidden";
}
```

Esto evita  **webhooks falsificados o alterados** .

**IP de origen de la lista blanca (opcional)**

Algunos proveedores publican rangos de IP estáticos desde los que se envían los webhooks. Opcionalmente, puedes bloquear el resto de IP para restringir el acceso.

> Desventaja: Esto agrega complejidad a DevOps y puede causar problemas si las IP cambian.

**No exponga datos confidenciales**

* No registre cargas útiles de webhook completas en texto sin formato
* No devuelva seguimientos de pila ni errores internos en las respuestas
* No incluya secretos ni tokens en el cuerpo de la respuesta

## 5. Diseño de una infraestructura de webhook escalable <a href="#id-5-designing-a-scalable-webhook-infrastructure" id="id-5-designing-a-scalable-webhook-infrastructure"></a>

A medida que su aplicación crece y comienza a recibir miles o incluso millones de eventos de webhook diariamente, un simple controlador sincrónico no será suficiente.

Para que su sistema de webhook  **sea confiable, tolerante a fallas y escalable** , necesita un diseño que sea  **asincrónico, desacoplado y observable** .

Veamos cómo construir una canalización de procesamiento de webhooks de nivel de producción.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.24.27.png" alt=""><figcaption></figcaption></figure>

### 5.1 Cola de solicitudes entrantes <a href="#id-5-1-queue-incoming-requests" id="id-5-1-queue-incoming-requests"></a>

No realice un procesamiento pesado dentro del punto final de su webhook.

En cambio:

* Analizar y validar la solicitud
* Verificar su autenticidad (firma, ID del evento, etc.)
* Coloque inmediatamente el evento en una  **cola de mensajes**  como:
* **Kafka**  (para canales de eventos de alto rendimiento)
* **RabbitMQ**  (ligero y flexible)
* **AWS SQS**  (sin servidor y fácil de administrar)

> De esta manera, su servidor puede devolver una respuesta rápida  `200 OK`, mientras el procesamiento real se realiza en segundo plano.

### 5.2 Almacenar eventos para auditoría y reproducción <a href="#id-5-2-store-events-for-audit-replay" id="id-5-2-store-events-for-audit-replay"></a>

Para garantizar la trazabilidad y la recuperación, almacene cada evento entrante en una base de datos:

* Carga útil sin procesar
* ID y tipo de evento
* Información de origen (por ejemplo, dirección IP, encabezados)
* Estado ( `queued`,  `processed`,  `failed`)
* Marcas de tiempo

Utilice un almacén de datos duradero como PostgreSQL, MongoDB o DynamoDB.

> Esto le brinda la posibilidad de  **reproducir eventos fallidos**  y  **depurar problemas** .

### 5.3 Proceso con trabajadores asincrónicos <a href="#id-5-3-process-with-asynchronous-workers" id="id-5-3-process-with-asynchronous-workers"></a>

Los trabajadores en segundo plano (o grupos de trabajadores) extraen eventos de la cola y ejecutan la lógica comercial real:

* Desduplicar el evento usando eventID
* Ejecutar validaciones
* Actualizar bases de datos internas o llamar a servicios posteriores
* Activar notificaciones o flujos de trabajo

**¿Por qué utilizar trabajadores?**

* Puedes escalarlos horizontalmente
* Obtienes control sobre la carga y la velocidad de procesamiento
* Aísla los fallos desde el punto final del webhook

### 5.4 Reintentar con retroceso <a href="#id-5-4-retry-with-backoff" id="id-5-4-retry-with-backoff"></a>

A veces, el procesamiento de eventos falla temporalmente debido a un problema en la base de datos, un tiempo de espera o una API descendente no disponible.

Sus trabajadores deben  **volver a intentar**  esos eventos automáticamente, utilizando una  **estrategia de interrupción** :

* **Exponencial**  (recomendado): reintentar con retrasos crecientes (1 s, 2 s, 4 s…)
* **Lineal** : reintentar cada N segundos (por ejemplo, reintentar cada 30 s)
* **Jitter** : agrega aleatoriedad para evitar que todos los reintentos ocurran a la vez

> Limite siempre los reintentos y registre los fallos después del último intento.

### 5.5 Utilizar una cola de mensajes no entregados (DLQ) <a href="#id-5-5-use-a-dead-letter-queue-dlq" id="id-5-5-use-a-dead-letter-queue-dlq"></a>

Si un webhook falla constantemente después de varios reintentos, no sigas intentándolo eternamente.

En su lugar, envíelo a una  [**cola de cartas muertas**](https://aws.amazon.com/what-is/dead-letter-queue/) , una cola de retención especial para eventos problemáticos.

Desde allí, puedes:

* Alertas de activación
* Investigar la causa raíz
* Vuelva a intentarlo manualmente después de solucionar el problema.

> Esto evita que eventos malos obstruyan su canalización y garantiza que ningún evento se pierda silenciosamente.

### 5.6 Agregar observabilidad: registros, métricas y alertas <a href="#id-5-6-add-observability-logs-metrics-alerts" id="id-5-6-add-observability-logs-metrics-alerts"></a>

No se puede arreglar lo que no se ve. Añade observabilidad en cada etapa del proceso.

**Realizar un seguimiento de métricas como:**

* Total de eventos de webhook recibidos por hora
* Tasa de éxito vs. tasa de fracaso
* Latencia de procesamiento
* Longitud de la cola
* Recuento de reintentos y recuento de eventos DLQ

**Establecer alertas para:**

* Picos en la tasa de fracaso
* Caída repentina de eventos entrantes
* Tamaños de cola crecientes (lo que indica atraso)

**Herramientas de observabilidad populares:**

* **Prometheus + Grafana**  (código abierto)
* **Datadog** ,  **CloudWatch, New Relic**  (basado en la nube)
* **Pila ELK**  (Elasticsearch + Logstash + Kibana)

> Con una observabilidad adecuada, podrá detectar los problemas de forma temprana, antes de que afecten a sus usuarios.
