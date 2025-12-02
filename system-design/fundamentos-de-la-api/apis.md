# APIs

## ¿Qué es una API?

API significa  **Interfaz de programación de aplicaciones** .

En esencia, una API es un  **conjunto de código**  que toma una  **entrada**  y ofrece  **resultados predecibles.**

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

Piense en una API como un  **intermediario**  que permite que las aplicaciones interactúen  **sin necesidad de acceso directo al código o a la base de datos de las demás** .

Casi todos los servicios digitales que utilizamos hoy en día (redes sociales, comercio electrónico, banca en línea, aplicaciones de transporte) son un conjunto de API que trabajan en conjunto.

**Ejemplos:**

* **API meteorológica**  : si proporciona el nombre de una ciudad como entrada ( `"New York"`), la API devuelve la  **temperatura, la humedad y las condiciones climáticas actuales** .
* **API de viajes de Uber**  : si proporcionas una  **dirección de recogida y destino** , la API encuentra el  **conductor disponible más cercano**  y calcula la tarifa estimada.
* `sorted()` **API de Python**  : si proporciona una lista de números ( `[5, 3, 8, 1]`), la API devuelve la  **lista ordenada**  ( `[1, 3, 5, 8]`).

Cuando los ingenieros crean API, definen claramente  **qué entradas acepta la API**  y  **qué salidas produce** , lo que garantiza un comportamiento consistente en diferentes aplicaciones.

Las API siguen un modelo simple  **de solicitud-respuesta**  :

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-01 a la(s) 21.44.49.png" alt=""><figcaption></figcaption></figure>

* Un cliente (como una aplicación web o una aplicación móvil) realiza una solicitud a una API.
* La API (alojada en un servidor API) procesa la solicitud, interactúa con las bases de datos o servicios necesarios y prepara una respuesta.
* La API envía la respuesta al cliente en un formato estructurado (generalmente JSON o XML).

**Entradas**

Cada API requiere  **tipos específicos de entradas** y pasar datos incorrectos puede generar errores.

Por ejemplo: si intentaras introducir tu nombre como entrada en la API de Google Maps, no funcionaría muy bien.

Algunas API también  **requieren entradas en un formato específico** .

Ejemplo: La  **API de cambio de divisas**  podría necesitar la entrada como  `"USD_TO_EUR"` en lugar de  `"usd to euro"`.

Las API a menudo  **validan las entradas**  para garantizar que sean correctas antes de procesarlas, lo que ayuda a mantener  **la precisión y la seguridad** .

**Salidas**

Así como las API requieren  **entradas específicas** , también devuelven  **salidas bien estructuradas** .

Por ejemplo, la  **API de Google Maps**  siempre devuelve  **las coordenadas en el mismo formato** .

```json
{   
  "latitude": 40.6892,   
  "longitude": -74.0445 
}
```

Si la API no puede encontrar la ubicación, proporciona una respuesta de error explicando el motivo.

```json
{
  "error": "Invalid address format",
  "code": 400 
}
```

## 1. Cómo las API impulsan las aplicaciones modernas <a href="#id-1-how-apis-power-modern-applications" id="id-1-how-apis-power-modern-applications"></a>

Las aplicaciones que utilizas todos los días (ya sea  **Gmail, Instagram, Uber o Spotify)** son esencialmente  **una colección de API con una interfaz de usuario (UI) pulida encima** .

La mayoría de las aplicaciones siguen la  **arquitectura frontend/backend** , donde:

* El  **backend**  consta de API que manejan  **el procesamiento de datos, la lógica empresarial y la comunicación con las bases de datos** .
* El  **frontend**  es una  **interfaz gráfica de usuario (GUI)**  que interactúa con estas API, haciendo que las aplicaciones sean fáciles de usar y accesibles  **sin necesidad de que los usuarios escriban código** .

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-01 a la(s) 21.47.14.png" alt=""><figcaption></figcaption></figure>

Analicemos esto con un ejemplo del mundo real:  **Uber** .

**El backend**

Antes de que la aplicación Uber existiera como una experiencia elegante y fácil de usar, la empresa primero creó  **las API principales que impulsan los servicios de transporte:**

* Encontrar conductores cercanos
* Cálculo de tarifas y rutas
* Procesar pago
* Seguimiento en tiempo real
* Emparejamiento de pasajeros y conductores

Estas API se ejecutan en los servidores de Uber y conforman la  **infraestructura de backend** . Cada vez que solicitas un viaje, rastreas a tu conductor o realizas un pago, estas API de backend gestionan la solicitud.

**Los ingenieros de backend**  son responsables de optimizar estas API, mejorar los algoritmos de búsqueda de viajes, proteger las transacciones y garantizar una experiencia fluida para millones de usuarios.

**El frontend**

Las API de backend gestionan  **toda la lógica compleja** , pero  **solo funcionan mediante código** , lo cual no resulta práctico para los usuarios habituales. Por eso, las empresas construyen un  **frontend (interfaz de usuario)**  sobre estas API, lo que permite a los usuarios interactuar con el sistema  **de forma visual e intuitiva** .

**Ejemplo:** cuando ingresa su dirección de recogida y destino, el frontend envía una solicitud de API para  **encontrar conductores cercanos**  y muestra los autos disponibles.

Una vez completado el viaje, el frontend puede llamar a la API de proceso de pago para mostrar el recibo.

## **2. Tipos de API** <a href="#id-2-types-of-apis" id="id-2-types-of-apis"></a>

Las API vienen en diferentes formas dependiendo de  **quién puede acceder a ellas** ,  **cómo se utilizan** y  **qué propósito cumplen** .

### 1. API abiertas (API públicas) <a href="#id-1-open-apis-public-apis" id="id-1-open-apis-public-apis"></a>

Las API abiertas, también conocidas como  **API públicas** , son accesibles para desarrolladores externos con restricciones mínimas.

Las empresas proporcionan estas API para alentar a  **los desarrolladores externos**  a integrar sus servicios y crear nuevas aplicaciones sobre ellas.

**Ejemplo: API de datos de YouTube**

Normalmente, al usar la  **app de YouTube** , esta realiza  **llamadas internas a la API**  para obtener tu feed de video, buscar contenido o publicar comentarios. Sin embargo, YouTube también ofrece una  **API pública**  que permite a los desarrolladores acceder a algunas de estas funciones  **fuera de la app** .

Por ejemplo, la  **API de búsqueda de YouTube**  permite a los desarrolladores obtener resultados de vídeo basados ​​en una palabra clave. Si envías una solicitud a la API con "  `"machine learning tutorial"` como término de búsqueda", devolverá una respuesta estructurada (formato JSON) con una lista de vídeos relevantes, incluyendo  **títulos, descripciones, miniaturas y enlaces** .

Esto es increíblemente útil porque permite a los desarrolladores crear aplicaciones personalizadas sobre YouTube.

### 2. API internas (API privadas) <a href="#id-2-internal-apis-private-apis" id="id-2-internal-apis-private-apis"></a>

**Las API internas** , también conocidas como  **API privadas** , están diseñadas  **exclusivamente** para uso interno de una organización. A diferencia de las API abiertas, estas no son accesibles para desarrolladores externos y se utilizan para facilitar la comunicación fluida entre los diferentes sistemas internos de una empresa.

Tomemos   como ejemplo  **a Amazon . Al realizar un pedido, se podría asumir que un solo sistema procesa la solicitud. En realidad, varias API internas**  (procesamiento de pedidos, inventario, pagos, logística, etc.) trabajan juntas en segundo plano para procesar el pedido eficientemente.

Cada una de estas API  **funciona de forma independiente** , pero se comunican a través de protocolos bien definidos para garantizar un proceso fluido y eficiente.

Las API internas permiten a las empresas dividir sus aplicaciones en  **servicios más pequeños y manejables** , lo que facilita su escalabilidad. Los desarrolladores pueden  **reutilizar las API internas**  en diferentes proyectos, lo que reduce  **la duplicación**  y acelera el desarrollo.

### 3. Interfaces de código <a href="#id-3-code-interfaces" id="id-3-code-interfaces"></a>

Los dos primeros tipos de API que analizamos ( **API abiertas y API internas)** son funcionales y sirven  **para casos de uso reales,**  como obtener datos meteorológicos o reservar un viaje.

Pero hay otra categoría de API que los desarrolladores utilizan a diario:  **Interfaces de código**  (también llamadas  **API de biblioteca**  o  **API de programación** ).

Estas API no conectan diferentes aplicaciones; en cambio, proporcionan funciones predefinidas dentro de un lenguaje o marco de programación para facilitar el desarrollo.

**Ejemplo:** API de lista incorporada de Python

Al trabajar con listas, Python proporciona un conjunto de  **funciones integradas (métodos) para manipular datos** .

```python
numbers = [5, 3, 8, 1, 4] numbers.sort()  # API call to sort the list  
fruits = ["apple", "banana"]
fruits.append("orange")  # API call to add an element
fruits.pop()  # API call to remove the last element
```

En lugar de escribir algoritmos de clasificación desde cero, los desarrolladores pueden usar  `sort()` o  `sorted()` en Python.

Las API de código no se limitan a las funciones integradas en los lenguajes de programación. Tomemos como ejemplo  **TensorFlow** , una biblioteca de IA/ML. Esta proporciona una  **API de alto nivel**  para entrenar modelos de aprendizaje automático sin necesidad de implementar operaciones matemáticas complejas desde cero.

Por ejemplo, crear una  **red neuronal**  utilizando la API de TensorFlow es tan simple como:

```python
import tensorflow as tf 

model = tf.keras.Sequential([tf.keras.layers.Dense(64, activation="relu")])
```

Las API de programación abstraen la complejidad para que los desarrolladores puedan centrarse en crear soluciones en lugar de reinventar la rueda.

## 3. Métodos de comunicación API <a href="#id-3-api-communication-methods" id="id-3-api-communication-methods"></a>

Las API se comunican utilizando diferentes  **protocolos y arquitecturas**  que definen cómo se envían las solicitudes, cómo se formatean las respuestas y cómo se intercambian los datos entre sistemas.

### 1. REST (Transferencia de estado representacional) <a href="#id-1-rest-representational-state-transfer" id="id-1-rest-representational-state-transfer"></a>

REST es el método de comunicación API más utilizado actualmente. Es  **ligero, sin estado y escalable** , lo que lo hace perfecto para servicios web y aplicaciones móviles.

Las API REST siguen un conjunto de principios de diseño y utilizan  **métodos HTTP**  (GET, POST, PUT, DELETE) para realizar operaciones.

Las API REST se basan en  **recursos** , y se accede a cada uno de ellos a través de una  **URL (punto final)** . La API sigue el  **modelo cliente-servidor** , es decir, el cliente envía una solicitud y el servidor la procesa y envía una respuesta.

**Ejemplo: API REST para una librería**

**Recuperar una lista de libros (solicitud GET):**

```
GET https://api.bookstore.com/books
```

**Respuesta (JSON):**

```json
[
  {
    "id": 1,
    "title": "Clean Code",
    "author": "Robert C. Martin"
  },
  {
    "id": 2,
    "title": "The Pragmatic Programmer",
    "author": "Andrew Hunt"
  }
]
```

### 2. SOAP (Protocolo simple de acceso a objetos) <a href="#id-2-soap-simple-object-access-protocol" id="id-2-soap-simple-object-access-protocol"></a>

SOAP es un método de comunicación API más antiguo que  **se basa en mensajería basada en XML** .

A diferencia de REST, que es liviano, SOAP es más estructurado y seguro, lo que lo hace ideal para aplicaciones bancarias, de atención médica y empresariales.

Los mensajes SOAP se envían utilizando  **formato XML**  y requieren un  **archivo WSDL (lenguaje de descripción de servicios web)** , que define las funciones disponibles de la API y la estructura de la solicitud.

**Ejemplo: API SOAP para un servicio bancario**

**Solicitud:**  Obtener el saldo de la cuenta

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:bank="http://bank.example.com/">
  <soapenv:Header/>
  <soapenv:Body>
    <bank:GetAccountBalance>
      <bank:accountNumber>123456</bank:accountNumber>
    </bank:GetAccountBalance>
  </soapenv:Body>
</soapenv:Envelope>
```

**Respuesta:**

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:bank="http://bank.example.com/">
  <soapenv:Body>
    <bank:GetAccountBalanceResponse>
      <bank:balance>5000.00</bank:balance>
    </bank:GetAccountBalanceResponse>
  </soapenv:Body>
</soapenv:Envelope>
```

### 3. GraphQL <a href="#id-3-graphql" id="id-3-graphql"></a>

GraphQL es una alternativa a REST que  **permite a los clientes solicitar exactamente los datos que necesitan** , lo que lo hace más eficiente para las aplicaciones modernas. A diferencia de REST, que requiere múltiples llamadas a la API para obtener datos relacionados, GraphQL puede  **obtener todos los datos necesarios en una sola solicitud** .

En lugar de puntos finales predefinidos, GraphQL expone un  **único punto final de API** y el cliente envía consultas para solicitar campos específicos.

**Ejemplo:** obtener el perfil de un usuario y sus publicaciones recientes en una sola solicitud.

```graphql
{
  user(id: 123) {
    name
    email
    posts {
      title
      likes
    }
  }
}
```

**Respuesta:**

```json
{
  "data": {
    "user": {
      "name": "Alice",
      "email": "alice@example.com",
      "posts": [
        {
          "title": "Hello World",
          "likes": 100
        },
        {
          "title": "GraphQL is Amazing!",
          "likes": 200
        }
      ]
    }
  }
}
```

### 4. gRPC <a href="#id-4-grpc" id="id-4-grpc"></a>

gRPC (Google Remote Procedure Call) es un  **método de comunicación API de alto rendimiento**  que utiliza  **búferes de protocolo (Protobuf)**  en lugar de JSON o XML, lo que lo hace más rápido y eficiente.

gRPC utiliza  **formato de datos binarios**  en lugar de formatos basados ​​en texto, lo que reduce el tamaño de la carga útil y admite  **transmisión bidireccional** , lo que significa que el cliente y el servidor pueden enviar datos al mismo tiempo.

## 4. Cómo usar una API (Guía paso a paso) <a href="#id-4-how-to-use-an-api-step-by-step-guide" id="id-4-how-to-use-an-api-step-by-step-guide"></a>

El uso de una API puede parecer complejo al principio, pero sigue un patrón simple  **de solicitud-respuesta**  .

Aquí tienes una guía sobre **cómo encontrar, acceder e interactuar con una API**  paso a paso:

### **Paso 1: Encuentra una API para usar** <a href="#step-1-find-an-api-to-use" id="step-1-find-an-api-to-use"></a>

Antes de usar una API, debe  **identificar la adecuada**  para sus necesidades. Existen API para diferentes servicios, como datos meteorológicos, finanzas, redes sociales, etc.

**¿Dónde encontrar API?**

**Directorios de API públicas:**

* [RapidAPI](https://rapidapi.com/)  : un mercado para API con opciones gratuitas y pagas.
* [Postman API Network](https://www.postman.com/explore)  : una colección de API públicas.
* [Lista de API](https://apilist.fun/)  : una lista divertida de API públicas gratuitas.
* [Lista de API públicas de GitHub](https://github.com/public-apis/public-apis)  : colección de API de código abierto.

**Documentación oficial de la API:**

* [API de Google](https://developers.google.com/)
* [API X](https://developer.x.com/)
* [API de OpenWeather](https://openweathermap.org/api)
* `q`: Nombre de la ciudad (por ejemplo,  `London`)
* `appid`: Clave API (necesaria para acceder)

**Parámetros requeridos:**

```shell
https://api.openweathermap.org/data/3.0/weather?q=city_name&appid=YOUR_API_KEY
```

**URL de la API**

La API de OpenWeatherMap permite a los usuarios obtener datos meteorológicos en tiempo real. A continuación, se detallan sus componentes principales:

**Ejemplo:**  La  [**API de OpenWeatherMap**](https://openweathermap.org/api/one-call-3)

La documentación de la API explica  **cómo utilizar la API, los puntos finales disponibles, la autenticación y los formatos de respuesta** .

### **Paso 2: Lea la documentación de la API** <a href="#step-2-read-the-api-documentation" id="step-2-read-the-api-documentation"></a>

* Regístrate en [openweathermap](https://home.openweathermap.org/users/sign_up)
* Vaya a la  sección **de claves API**  y genere una clave.
* Utilice la clave API en las solicitudes:`GET "https://api.openweathermap.org/data/2.5/weather?q=London&appid=YOUR_API_KEY"`

**Ejemplo: Obtener una clave API (API de OpenWeather)**

* **Clave API:** una clave única proporcionada por el servicio API
* **OAuth 2.0 -** Inicio de sesión seguro a través de Google, Github, etc.
* **JWT (JSON Web Token):** autenticación basada en tokens
* **Autenticación básica:** nombre de usuario + contraseña (codificada en Base64)

**Métodos de autenticación comunes:**

La mayoría de las API  **requieren autenticación**  para evitar el acceso no autorizado y administrar los límites de uso.

### **Paso 3: Obtener acceso a la API (clave API/autenticación)** <a href="#step-3-get-api-access-api-key-authentication" id="step-3-get-api-access-api-key-authentication"></a>

La mayoría de las API  **requieren autenticación**  para evitar el acceso no autorizado y administrar los límites de uso.

**Métodos de autenticación comunes:**

* **Clave API:** una clave única proporcionada por el servicio API
* **OAuth 2.0 -** Inicio de sesión seguro a través de Google, Github, etc.
* **JWT (JSON Web Token):** autenticación basada en tokens
* **Autenticación básica:** nombre de usuario + contraseña (codificada en Base64)

**Ejemplo: Obtener una clave API (API de OpenWeather)**

* Regístrate en [openweathermap](https://home.openweathermap.org/users/sign_up)
* Vaya a la  sección **de claves API**  y genere una clave.
* Utilice la clave API en las solicitudes:`GET "https://api.openweathermap.org/data/2.5/weather?q=London&appid=YOUR_API_KEY"`

### **Paso 4: Pruebe la API usando Postman o cURL** <a href="#step-4-test-the-api-using-postman-or-curl" id="step-4-test-the-api-using-postman-or-curl"></a>

Antes de escribir el código,  **pruebe la API**  para ver cómo responde.

**Opción 1: Usar Postman (recomendado para principiantes)**

* Descargue e instale  [**Postman**](https://www.postman.com/) .
* Haga clic en  **"Nueva solicitud"** e ingrese la URL del punto final de la API ( `https://api.openweathermap.org/data/3.0/weather?q=London&appid=YOUR_API_KEY`).
* Seleccione  **GET**  como método HTTP.
* Haga clic en  **"Enviar"**  y vea la respuesta en  **formato JSON** .

#### **Opción 2: Usar cURL (para usuarios de línea de comandos)**

También puedes probar las API directamente desde la  **línea de comandos**  usando  [**cURL**](https://curl.se/) .

```
curl -X GET "https://api.openweathermap.org/data/3.0/weather?q=New+York&appid=YOUR_API_KEY"
```

### **Paso 5: Escribe el código para llamar a la API** <a href="#step-5-write-code-to-call-the-api" id="step-5-write-code-to-call-the-api"></a>

Ahora que has probado la API, es hora de  **integrarla en tu aplicación** .

**Ejemplo: Llamar a una API en Python**

Pitón

```python
import requests

API_KEY = "YOUR_API_KEY"
CITY = "New York"

url = f"https://api.openweathermap.org/data/3.0/weather?q={CITY}&appid={API_KEY}"

response = requests.get(url)

if response.status_code == 200:
    data = response.json()
    temperature = data['main']['temp']
    print(f"Current temperature in {CITY}: {temperature}°C")
else:
    print(f"Error retrieving data: Status code {response.status_code}")
```

* `requests.get(url)` – Envía una solicitud de API.
* `response.json()` – Convierte la respuesta a JSON.
* `if response.status_code == 200` – Comprueba si la solicitud fue exitosa.

### **Paso 6: Gestionar errores y límites de velocidad** <a href="#step-6-handle-errors-rate-limits" id="step-6-handle-errors-rate-limits"></a>

Las API  **no siempre devuelven respuestas perfectas** . Debes gestionar:

* **Entradas no válidas**  (por ejemplo, nombre de ciudad incorrecto).
* **Errores de autenticación**  (por ejemplo, claves API caducadas).
* **Límites de velocidad**  (por ejemplo, superar los límites de solicitud).

**Ejemplo: Manejo de errores de API en Python**

Pitón

```python
import requests

API_KEY = "YOUR_API_KEY"
CITY = "New York"

url = f"https://api.openweathermap.org/data/3.0/weather?q={CITY}&appid={API_KEY}"

response = requests.get(url)

if response.status_code == 200:
    data = response.json()
    weather_description = data['weather'][0]['description']
    print(f"Current weather in {CITY}: {weather_description}")
elif response.status_code == 401:
    print("Error: Invalid API key")
elif response.status_code == 404:
    print("Error: City not found")
else:
    print(f"Unexpected error occurred: Status code {response.status_code}")
```

### Paso 7: Utilice las respuestas de API en su aplicación <a href="#step-7-use-api-responses-in-your-application" id="step-7-use-api-responses-in-your-application"></a>

Una vez que obtienes datos de una API, puedes  **mostrarlos dinámicamente en una aplicación web o móvil** .

**Ejemplo:**  puedes crear un panel meteorológico utilizando la API de OpenWeatherMap.

* Obtenga datos meteorológicos en vivo desde la API.
* Analizar y extraer detalles relevantes (temperatura, humedad, condición).
* Muestra el informe meteorológico en un formato fácil de usar.
