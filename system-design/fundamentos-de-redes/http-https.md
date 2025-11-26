# HTTP/HTTPS

Cada vez que interactúas con Internet, estás utilizando **HTTP** (Protocolo de transferencia de hipertexto) o su contraparte segura, **HTTPS** .

Estos protocolos son el lenguaje fundamental que se habla entre clientes y servidores y que define cómo se solicita y entrega la información.

En este capítulo, profundizaremos en sus principios básicos, diferencias clave y evolución crucial de HTTP/1.1 a HTTP/3.

## 1. ¿Qué es HTTP? <a href="#id-1-what-is-http" id="id-1-what-is-http"></a>

**HTTP (Protocolo de Transferencia de Hipertexto)** es un protocolo de capa de aplicación basado en texto y sin estado que se utiliza para la comunicación entre clientes web y servidores. Es el estándar para la transmisión de páginas web y datos a través de internet.

#### **Características principales:** <a href="#key-characteristics" id="key-characteristics"></a>

* **Protocolo de capa de aplicación** : HTTP opera en la capa 7 del modelo OSI.
* **Modelo cliente-servidor** : La comunicación siempre se inicia cuando un cliente (p. ej., su navegador, una aplicación móvil, un microservicio) envía una solicitud a un servidor. El servidor procesa la solicitud y envía una respuesta al cliente.
* **Basado en TCP** : HTTP se basa tradicionalmente en TCP (Protocolo de Control de Transmisión) en la capa de transporte, aprovechando la entrega confiable, ordenada y orientada a la conexión de TCP. De forma predeterminada, HTTP utiliza **el puerto 80** .

#### Ejemplo de solicitud y respuesta HTTP <a href="#example-http-request-and-response" id="example-http-request-and-response"></a>

**Pedido:**

```shell
GET /index.html HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36  Chrome/100.0.4896.75
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Connection: keep-alive
```

**Respuesta:**

```shell
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 1024
Date: Mon, 11 Apr 2023 10:00:00 GMT

<!DOCTYPE html>
<html>
<head><title>Example Domain</title></head>
<body><h1>Hello World!</h1></body>
</html>
```

## 2. Cómo funciona HTTP

En esencia, HTTP funciona según un modelo simple de solicitud/respuesta:

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.57.10.png" alt="" width="375"><figcaption></figcaption></figure>

1. **Conexión TCP** : el cliente primero establece una conexión TCP con el servidor (o reutiliza una existente).
2. **Solicitud HTTP** : El cliente envía un mensaje de solicitud HTTP. Este mensaje incluye:
3. Un **método HTTP** (por ejemplo, `GET`, `POST`).
4. La **URL** del recurso.
5. **Encabezados HTTP** (pares clave-valor que proporcionan contexto adicional, por ejemplo, `Host`, `User-Agent`).
6. Opcionalmente, un **cuerpo del mensaje** (por ejemplo, para `POST`solicitudes).
7. **Procesamiento del servidor** : el servidor recibe la solicitud y la procesa (por ejemplo, obtiene datos de una base de datos y ejecuta la lógica empresarial).
8. **Respuesta HTTP** : El servidor envía un mensaje de respuesta HTTP al cliente. Esto incluye:
9. Un **código de estado HTTP** (por ejemplo, `200 OK`, `404 Not Found`).
10. **Encabezados de respuesta** (por ejemplo, `Content-Type`, `Content-Length`).
11. El **cuerpo del mensaje** (por ejemplo, el contenido HTML solicitado, datos JSON).
12. **Cierre/reutilización de la conexión** : la conexión TCP se cierra después de la respuesta (HTTP/1.0, aunque poco común ahora) o se mantiene abierta para solicitudes posteriores ( **HTTP Keep-Alive** en HTTP/1.1+).

#### Apatridia: <a href="#statelessness" id="statelessness"></a>

HTTP es inherentemente **sin estado** . Esto significa que cada solicitud de un cliente a un servidor se trata como una transacción independiente; el servidor no "recuerda" ninguna solicitud previa de ese cliente.

En el diseño de sistemas, esto es al mismo tiempo una bendición y una maldición:

* **Ventajas** : Es más fácil de escalar, ya que cualquier servidor puede manejar cualquier solicitud sin necesidad de contexto previo.
* **Contras** : Requiere mecanismos como **cookies** , **ID de sesión** o **JWT (JSON Web Tokens)** para mantener el estado del usuario en el nivel de la aplicación.

## 3. Métodos HTTP y códigos de estado <a href="#id-3-http-methods-and-status-codes" id="id-3-http-methods-and-status-codes"></a>

Los métodos (o verbos) HTTP indican la acción deseada que se realizará en un recurso. Los códigos de estado HTTP indican el resultado de una solicitud HTTP.

#### Métodos HTTP comunes: <a href="#common-http-methods" id="common-http-methods"></a>

| `GET`    | Recuperar datos                               | Sí |
| -------- | --------------------------------------------- | -- |
| `POST`   | Enviar nuevos datos, crear recursos           | No |
| `PUT`    | Actualizar/reemplazar un recurso existente    | Sí |
| `DELETE` | Eliminar un recurso                           | Sí |
| `PATCH`  | Aplicar modificaciones parciales a un recurso | No |
| `HEAD`   | Obtener solo encabezados (sin cuerpo)         | Sí |

**La idempotencia** es crucial en el diseño de sistemas. Una operación idempotente puede ejecutarse varias veces sin modificar el resultado más allá de la aplicación inicial. Esto es vital para los reintentos en sistemas distribuidos donde las fallas de red son frecuentes. `GET`, `PUT`, y `DELETE`son idempotentes, mientras que `POST`y `PATCH`generalmente no lo son.

#### Códigos de estado HTTP comunes: <a href="#common-http-status-codes" id="common-http-status-codes"></a>

* **1xx (Informativo)** : Solicitud recibida, proceso en curso.
* **2xx (Éxito)** :
* `200 OK`:Solicitud exitosa.
* `201 Created`:Recurso creado exitosamente.
* **3xx (Redirección)** : Se necesitan más acciones para completar la solicitud.
* `301 Moved Permanently`:Recurso movido.
* `302 Found`:Redireccionamiento temporal.
* **4xx (Error del cliente)** :
* `400 Bad Request`:Sintaxis inválida.
* `401 Unauthorized`:Se requiere autenticación.
* `403 Forbidden`:El servidor lo entendió, pero se niega a autorizar.
* `404 Not Found`:Recurso no encontrado.
* **5xx (Error del servidor)** :
* `500 Internal Server Error`:Error genérico del servidor.
* `502 Bad Gateway`:El servidor que actúa como puerta de enlace recibió una respuesta no válida.
* `503 Service Unavailable`:El servidor no puede manejar la solicitud temporalmente.

## 4. Limitaciones de HTTP <a href="#id-4-limitations-of-http" id="id-4-limitations-of-http"></a>

Si bien fue revolucionario, el HTTP tenía limitaciones significativas que se volvieron críticas a medida que Internet evolucionó hacia una plataforma para transacciones confidenciales y datos personales:

1. **Sin cifrado** : Todos los datos (solicitudes, respuestas, encabezados y cuerpo) se envían en **texto plano** . Esto significa que cualquier persona con acceso a la ruta de red (p. ej., su proveedor de internet, un operador de wifi público o un atacante) puede leer fácilmente su información.
2. **Sin autenticación** : No hay forma de verificar la identidad del servidor con el que te comunicas. Podrías estar hablando con un impostor sin saberlo.
3. **Sin integridad** : Los datos transmitidos por HTTP pueden modificarse durante el tránsito sin ser detectados. Un agente malicioso podría cambiar el contenido de una página web o una respuesta de API.

Estas vulnerabilidades expusieron a los usuarios a graves riesgos de seguridad:

* **Escuchas clandestinas** : se podría robar información confidencial (contraseñas, números de tarjetas de crédito).
* **Ataques Man-in-the-Middle (MITM)** : un atacante podría interceptar la comunicación, suplantar a ambas partes y leer o alterar datos.
* **Ataques de inyección** : se podría inyectar código malicioso en el tráfico web no cifrado.

A medida que las aplicaciones web escalaron y comenzaron a manejar datos más confidenciales, la necesidad de seguridad y privacidad sólidas se volvió primordial, lo que llevó al desarrollo y la adopción generalizada de HTTPS.

## 5. ¿Qué es HTTPS? <a href="#id-5-what-is-https" id="id-5-what-is-https"></a>

**HTTPS (HTTP Seguro)** no es un protocolo independiente, sino HTTP superpuesto a **SSL/TLS (Capa de Conexión Segura / Seguridad de la Capa de Transporte)** . Este protocolo criptográfico proporciona comunicación segura a través de una red informática. De forma predeterminada, HTTPS utiliza **el puerto 443** .

HTTPS ofrece tres garantías fundamentales:

1. **Cifrado (Confidencialidad)** : Todos los datos intercambiados entre el cliente y el servidor están cifrados, lo que los hace ilegibles para terceros que puedan interceptar el tráfico. Es como sellar su mensaje en un sobre digital irrompible.
2. **Integridad** : Garantiza que los datos no hayan sido manipulados ni dañados durante su transferencia. Si se produce algún cambio, tanto el cliente como el servidor lo detectarán.
3. **Autenticación** : Verifica la identidad del servidor (y, opcionalmente, del cliente) mediante certificados digitales. Esto garantiza al cliente que se comunica con el servidor legítimo y no con un impostor.

La base de HTTPS es **la infraestructura de clave pública (PKI)** , que utiliza una combinación de claves públicas y privadas, junto con certificados digitales emitidos por **autoridades de certificación (CA)** confiables , para establecer conexiones seguras.

## 6. Cómo funciona HTTPS <a href="#id-6-how-https-works" id="id-6-how-https-works"></a>

La conexión segura en HTTPS se establece a través de un proceso llamado **TLS Handshake**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.59.29.png" alt="" width="375"><figcaption></figcaption></figure>

1. **Hola del cliente** : el cliente inicia la conexión, enviando un mensaje de "Hola del cliente" que incluye sus versiones de TLS compatibles, conjuntos de cifrado y un número aleatorio.
2. **Servidor Hola** : El servidor responde con un "Servidor Hola", seleccionando la mejor versión de TLS y conjunto de cifrado, junto con su propio número aleatorio y su **certificado SSL/TLS** .
3. **Verificación del certificado** : El cliente verifica el certificado del servidor comprobando su validez, fecha de caducidad y si fue emitido por una **autoridad de certificación (CA)** de confianza . Este paso garantiza que el cliente se comunique con el servidor legítimo.
4. **Intercambio de claves** : El cliente y el servidor utilizan criptografía de clave pública (del certificado del servidor) para intercambiar de forma segura un **secreto pre-maestro** . Ambas partes obtienen, de forma independiente, una **clave de sesión simétrica compartida** utilizando este secreto y los números aleatorios.
5. **Comunicación cifrada** : una vez que se establece la clave de sesión, todos los datos de la aplicación posteriores (solicitudes y respuestas HTTP) se cifran y descifran utilizando esta clave **de cifrado simétrico mucho más rápida.**

Las versiones modernas de TLS (especialmente TLS 1.3) han optimizado significativamente este proceso de protocolo de enlace, permitiendo a menudo conexiones **0-RTT (tiempo de ida y vuelta cero)** para clientes que se han conectado previamente a un servidor, lo que reduce la latencia.

## 7. HTTP vs. HTTPS: Diferencias clave <a href="#id-7-http-vs-https-key-differences" id="id-7-http-vs-https-key-differences"></a>

| **Seguridad**               | Sin cifrar, datos en texto plano                                | Cifrado mediante SSL/TLS                                                       |
| --------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| **Puerto**                  | 80                                                              | 443                                                                            |
| **Actuación**               | Un poco más rápido debido a una menor sobrecarga                | Latencia ligeramente superior (mejora con TLS 1.3)                             |
| **Autenticación**           | Ninguno                                                         | Autenticación del servidor mediante certificados digitales                     |
| **Integridad de los datos** | Sin garantía, susceptible de manipulación.                      | Garantizado contra manipulaciones                                              |
| **SEO y confianza**         | Clasificación más baja, advertencias del navegador, "No seguro" | Preferido por los motores de búsqueda, icono de candado, confianza del usuario |
| **Caso de uso**             | Desarrollo local, interno, aplicaciones no sensibles            | Todo el tráfico web de producción, API, datos confidenciales                   |

En las aplicaciones modernas, HTTPS es el valor predeterminado indiscutible para todas las comunicaciones, especialmente en sistemas distribuidos que manejan cualquier tipo de datos confidenciales.

## 8. Evolución de los protocolos HTTP <a href="#id-8-evolution-of-http-protocols" id="id-8-evolution-of-http-protocols"></a>

HTTP ha experimentado una evolución significativa para satisfacer las crecientes demandas de la web.

**HTTP/1.1 (1997)**

* Se introdujeron **conexiones persistentes** (keep-alive), que permiten múltiples solicitudes en una única conexión TCP, lo que reduce la sobrecarga.
* **Mecanismos de almacenamiento en caché** habilitados y **alojamiento virtual** .
* **Limitación** : Aún sufría **bloqueos de cabecera** . Incluso con conexiones persistentes, las solicitudes se procesaban secuencialmente. Si una solicitud era lenta, bloqueaba todas las solicitudes posteriores en esa conexión.

**HTTP/2 (2015)**

* Se abordaron las limitaciones de HTTP/1.1 mediante la introducción de **tramas binarias** , **multiplexación** y **compresión de encabezado** .
* **Enmarcado binario** : los datos ya no se envían como texto simple, sino en formato binario, lo que hace que su análisis sea más eficiente.
* **Multiplexación** : permite múltiples solicitudes y respuestas simultáneas en una _única conexión TCP_ , eliminando el bloqueo de cabecera de línea.
* **Compresión de encabezado (HPACK)** : reduce los datos de encabezado redundantes, lo que ahorra ancho de banda.
* **Server Push** : los servidores pueden enviar de manera proactiva recursos al cliente que anticipan que el cliente necesitará (por ejemplo, CSS antes de que se solicite en el HTML).
* **Impacto en el diseño del sistema** : Mejora significativa de la latencia y la eficiencia al cargar páginas web complejas con muchos recursos. Esta es la base de muchas API y microservicios modernos.

**HTTP/3 (2018, RFC 9114)**

* El cambio más radical: se ejecuta sobre **UDP (User Datagram Protocol)** en lugar de TCP, utilizando el **protocolo QUIC (Quick UDP Internet Connections)** .
* **¿Por qué UDP?:** Para superar el bloqueo de cabecera de línea del propio TCP (donde un solo paquete perdido en una conexión TCP puede detener todos los flujos de esa conexión) y reducir la latencia del protocolo de enlace.
* **Establecimiento de conexión 0-RTT** : para conexiones repetidas, HTTP/3 a menudo puede enviar datos en el primer paquete, lo que reduce drásticamente la latencia de configuración.
* **Migración de conexión** : un cliente puede cambiar de red sin problemas (por ejemplo, de Wi-Fi a celular) sin perder la conexión, ya que las conexiones QUIC se identifican mediante una ID única, no por IP/puerto.
* **Impacto en el diseño del sistema** : Ideal para aplicaciones en tiempo real, establecimiento de conexión más rápido y mejor rendimiento en redes poco fiables. Google, YouTube y Cloudflare son grandes usuarios.
