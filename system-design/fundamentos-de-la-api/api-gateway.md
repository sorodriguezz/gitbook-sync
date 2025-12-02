# API Gateway

## ¿Qué es una API Gateway?

**Las API** , o **interfaces de programación de aplicaciones** , son un conjunto de reglas y protocolos que permiten que dos aplicaciones o servicios de software se comuniquen entre sí.

A medida que las aplicaciones crecen, también aumenta el número de API. Sin las herramientas y la infraestructura adecuadas, gestionar estas API puede convertirse rápidamente en un desafío.

Aquí es donde entra en juego **API Gateway .**

> Una API Gateway actúa como un **servidor central** que se ubica entre los clientes (por ejemplo, navegadores, aplicaciones móviles) y los servicios de backend.

En lugar de que los clientes interactúen directamente con múltiples microservicios, envían sus solicitudes a API Gateway. Esta puerta de enlace procesa estas solicitudes, refuerza la seguridad y las reenvía a los microservicios correspondientes.

En este artículo, exploraremos por qué necesitamos una API Gateway, las características clave que proporciona y cómo funciona paso a paso.

## 1. ¿Por qué necesitamos una API Gateway?

Las aplicaciones modernas, especialmente aquellas creadas utilizando una arquitectura de microservicios, tienen múltiples servicios de backend que administran diferentes funcionalidades.

Por ejemplo, en un servicio de comercio electrónico:

* Un servicio maneja **las cuentas de usuario** .
* Otro se encarga **de los pagos** .
* Otro gestiona **el inventario de productos** .

**Sin una API Gateway:**

<figure><img src="../../.gitbook/assets/image (38).png" alt="" width="375"><figcaption></figcaption></figure>

* Los clientes necesitarían saber la ubicación y los detalles de todos los servicios backend.
* Los desarrolladores tendrían que gestionar la autenticación, la limitación de velocidad y la seguridad para cada servicio individualmente.

**Con una API Gateway:**

<figure><img src="../../.gitbook/assets/image (39).png" alt="" width="375"><figcaption></figcaption></figure>

* Los clientes envían todas las solicitudes a un solo lugar: API Gateway.
* API Gateway se encarga del enrutamiento, la autenticación, la seguridad y otras tareas operativas, simplificando tanto las interacciones con el cliente como la gestión del backend.

## 2. Características principales de una API Gateway

<figure><img src="../../.gitbook/assets/image (40).png" alt="" width="375"><figcaption></figcaption></figure>

### **1. Autenticación y autorización**

API Gateway protege los sistemas backend al garantizar que solo los usuarios y clientes autorizados puedan acceder a los servicios backend.

Se encarga de tareas como:

* **Autenticación:** verificar la identidad del cliente mediante tokens (por ejemplo, OAuth, JWT), claves API o certificados.
* **Autorización:** Verificar los permisos del cliente para acceder a servicios o recursos específicos.

Al centralizar estas tareas, la puerta de enlace API elimina la necesidad de que servicios individuales manejen la autenticación, lo que reduce la redundancia y garantiza un control de acceso consistente en todo el sistema.

### **2. Limitación de velocidad**

Para evitar abusos y garantizar un uso justo de los recursos, la mayoría de las API Gateways implementan **una limitación de velocidad** .

Esta característica:

* Controla la frecuencia de las solicitudes que un cliente puede realizar dentro de un período de tiempo determinado.
* Protege los servicios backend para que no se vean sobrecargados por tráfico excesivo o posibles ataques de denegación de servicio (DoS).

> Por ejemplo, una API pública podría permitir un máximo de 100 solicitudes por minuto por usuario. Si un cliente supera este límite, API Gateway bloqueará las solicitudes adicionales hasta que se restablezca la tasa.

### **3. Equilibrio de carga**

Las aplicaciones de alto tráfico dependen del **equilibrio de carga** para distribuir las solicitudes entrantes de manera uniforme entre múltiples instancias de un servicio.

La API Gateway puede:

* Redirigir las solicitudes a instancias de servicio en buen estado, evitando aquellas que estén inactivas o sobrecargadas.
* Utilice algoritmos como round-robin, menor cantidad de conexiones o distribución ponderada para gestionar el tráfico de forma inteligente.

### **4. Almacenamiento en caché**

Para mejorar los tiempos de respuesta y reducir la tensión en los servicios backend, la mayoría de las API Gateways proporcionan **almacenamiento en caché** .

Almacenan temporalmente datos solicitados con frecuencia, como:

* Respuestas a puntos finales de acceso común (por ejemplo, catálogos de productos o datos meteorológicos).
* Recursos estáticos como imágenes o metadatos.

> El almacenamiento en caché ayuda a reducir la latencia y mejorar la experiencia del usuario al tiempo que reduce el costo operativo de los servicios de backend.

### **5. Transformación de la solicitud**

En sistemas con diversos clientes y servicios backend, **la transformación de solicitudes** es esencial para la compatibilidad.

Una API Gateway puede:

* Modificar la estructura o el formato de las solicitudes entrantes para que coincidan con los requisitos del servicio backend.
* Transforme las respuestas antes de enviarlas de vuelta al cliente, asegurándose de que cumplan con sus expectativas.

> Por ejemplo, podría convertir respuestas XML de un servicio heredado a JSON para aplicaciones frontend modernas.

### **6. Descubrimiento de servicios**

Los sistemas modernos a menudo implican microservicios que escalan dinámicamente.

La función **de descubrimiento de servicios** de un API Gateway identifica dinámicamente la instancia de servicio backend adecuada para manejar cada solicitud.

Esto garantiza un enrutamiento de solicitudes sin problemas incluso en entornos donde los servicios aumentan o reducen con frecuencia su escala.

### **7. Disrupción del circuito**

La interrupción de circuito es un mecanismo que detiene temporalmente el envío de solicitudes a un servicio backend cuando detecta fallas persistentes, como:

* Respuestas lentas o tiempos de espera.
* Errores del servidor (por ejemplo, códigos de estado HTTP 500).
* Alta latencia o indisponibilidad de un servicio.

API Gateway monitorea continuamente la salud y el rendimiento de los servicios backend y utiliza la interrupción de circuitos para bloquear solicitudes a un servicio que falla.

### **8. Registro y monitoreo**

Las puertas de enlace API brindan capacidades sólidas **de monitoreo y registro** para rastrear y analizar el comportamiento del sistema.

Estas capacidades incluyen:

* Registra información detallada sobre cada solicitud, como origen, destino y tiempo de respuesta.
* Recopilación de métricas como tasas de solicitud, tasas de error y latencia.

Estos datos ayudan a los administradores de sistemas a detectar anomalías, solucionar problemas y optimizar el rendimiento del sistema. Muchas API Gateways también se integran con herramientas de monitorización como Prometheus, Grafana o AWS CloudWatch.

## **3. ¿Cómo funciona una API Gateway?**

Imagina que usas una app de comida a domicilio para pedir la cena. Al pulsar "Realizar pedido", tu teléfono realiza una solicitud API. Pero en lugar de comunicarse directamente con varios servicios de backend, primero se comunica con una API Gateway.

### **Paso 1: Solicitar Recepción**

Cuando toca "Realizar pedido", la aplicación envía una solicitud a **API Gateway** para solicitarle que procese su pedido.

Esta solicitud incluye cosas como:

* Su ID de usuario
* Restaurantes y platos del menú seleccionados
* Dirección de entrega
* Método de pago
* Tokens de autenticación

API Gateway recibe la solicitud como único punto de entrada al sistema backend.

### **Paso 2: Solicitar validación**

Antes de reenviar la solicitud, API Gateway la valida para garantizar:

* Los parámetros o encabezados requeridos están presentes.
* Los datos están en el formato correcto (por ejemplo, JSON).
* La solicitud se ajusta a la estructura o esquema esperado.

```javascript
// Example of initial request handling
app.post('/api/v1/orders', async (req, res) => {
  // Check if request has required headers
  if (!req.headers['content-type'].includes('application/json')) {
    return res.status(400).send('Invalid content type');
  }
  // Continue processing...
});
```

Si falta alguna información o es incorrecta, la puerta de enlace rechaza inmediatamente la solicitud y notifica a la aplicación con un mensaje de error apropiado.

### **Paso 3: Autenticación y autorización**

La puerta de enlace ahora verifica su identidad y permisos para garantizar que solo los usuarios legítimos puedan realizar pedidos:

* Reenvía su token de autenticación (por ejemplo, OAuth o JWT) a un proveedor de identidad para confirmar su identidad.
* Verifica sus permisos para garantizar que esté autorizado a usar la aplicación para realizar un pedido.

```javascript
const authenticateRequest = async (req) => {
  // Extract JWT token from header
  const token = req.headers.authorization?.split(' ')[1];

  // Verify token and get user details
  const user = await verifyToken(token);

  // Check if user has permission to place orders
  return user.permissions.includes('place_orders');
};
```

Si falla la autenticación o la autorización, API Gateway envía un mensaje de error `401 Unauthorized`o `403 Forbidden`error a la aplicación.

### **Paso 4: Limitación de velocidad**

Para evitar abusos, API Gateway comprueba cuántas solicitudes has realizado recientemente. Por ejemplo:

* Si ha realizado 10 solicitudes de "Realizar pedido" en el último minuto (tal vez por accidente), la puerta de enlace podría bloquear solicitudes adicionales temporalmente y devolver `429 Too Many Requests`una respuesta.

```javascript
const checkRateLimit = async (userId) => {
  const key = `rate_limit:order:${userId}`;
  const current = await redis.incr(key);

  // If first request in window, set expiry
  if (current === 1) {
    await redis.expire(key, 60); // 1 minute window
  }

  return current <= 10; // Allow 10 order requests per minute
};
```

Esto garantiza que el sistema permanezca estable y justo para todos los usuarios, especialmente durante picos de tráfico o ataques maliciosos, como intentos de denegación de servicio distribuido (DDoS).

### **Paso 5: Solicitar transformación (si es necesario)**

Si alguno de estos servicios de backend requiere formatos de datos específicos o detalles adicionales, API Gateway transforma la solicitud.

Por ejemplo:

* La aplicación envía la dirección de entrega en texto plano, pero el Servicio de Entrega espera coordenadas GPS. La API Gateway convierte la dirección en coordenadas antes de reenviar la solicitud.

```javascript
const transformRequest = async (originalRequest) => {
  const address = originalRequest.deliveryAddress;

  // Convert address to GPS coordinates using a geocoding API
  const coordinates = await getCoordinatesFromAddress(address);

  if (!coordinates) {
    throw new Error('Failed to fetch GPS coordinates');
  }

  // Transform the request for the Delivery Service
  return {
    orderId: originalRequest.orderId,
    customerName: originalRequest.customerName,
    deliveryLocation: {
      latitude: coordinates.lat,
      longitude: coordinates.lng
    },
    deliveryInstructions: originalRequest.instructions || ""
  };
};
```

### **Paso 6: Solicitar enrutamiento**

La API Gateway ahora necesita coordinar varios servicios backend para procesar su pedido.

Mediante **el descubrimiento de servicios** , se identifica:

* **Servicio de pedidos:** para crear un nuevo registro de pedido.
* **Servicio de Inventario:** Para verificar si el restaurante tiene disponibles los artículos seleccionados.
* **Servicio de pago:** Para procesar su pago.
* **Servicio de entrega:** para asignar un conductor de entrega a su pedido.

La puerta de enlace enruta dinámicamente la solicitud a estos servicios mediante un algoritmo **de equilibrio de carga** , lo que garantiza que se conecte a instancias de servicio disponibles y en buen estado.

```javascript
const routeRequest = async (req, serviceType) => {
  // Get service registry
  const services = await serviceDiscovery.getServices(serviceType);

  // Select instance
  const targetService = selectServiceInstance(services);

  // Forward request
  return await axios.post(
    `${targetService.url}/api/orders`,
    req.body,
    { headers: req.headers }
  );
};
```

### **Paso 7: Manejo de respuestas**

Una vez que API Gateway recibe las respuestas de los servicios backend, realiza las siguientes tareas:

* **Transformación:** Ajusta el formato o estructura de la respuesta para que coincida con los requisitos del cliente.
* **Almacenamiento en caché (opcional):** almacena la respuesta temporalmente para los datos a los que se accede con frecuencia, lo que reduce la latencia futura.

```javascript
const handleResponse = async (serviceResponse) => {
  // Transform response if needed
  const transformedResponse = {
    orderId: serviceResponse.order_reference,
    estimatedDelivery: serviceResponse.eta,
    status: serviceResponse.current_status
  };

  // Cache response if applicable
  if (serviceResponse.cacheable) {
    await cacheResponse(
      transformedResponse.orderId,
      transformedResponse
    );
  }

  return transformedResponse;
};
```

Finalmente, API Gateway envía la respuesta procesada al cliente en un formato que puede entender fácilmente.

### **Paso 8: Registro y monitoreo**

Durante todo este proceso, la pasarela registra métricas importantes para realizar el seguimiento de cada solicitud:

```javascript
const logRequest = async (req, res, timing) => {
  await logger.log({
    timestamp: new Date(),
    path: req.path,
    method: req.method,
    responseTime: timing,
    statusCode: res.statusCode,
    userId: req.user?.id
  });
};
```
