# Rate Limiting

La limitación de velocidad ayuda a proteger los servicios para que no se vean sobrecargados por demasiadas solicitudes de un solo usuario o cliente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.32.54.png" alt="" width="237"><figcaption></figcaption></figure>

En este artículo, analizaremos 5 de los algoritmos de limitación de velocidad más comunes, sus ventajas y desventajas, y aprenderemos cómo implementarlos en el código.

## 1. Token Bucket

<figure><img src="../../.gitbook/assets/image.png" alt="" width="375"><figcaption></figcaption></figure>

El algoritmo Token Bucket es uno de los enfoques de limitación de velocidad más populares y ampliamente utilizados debido a su simplicidad y eficacia.

**Cómo funciona :**

* Imagínese un cubo que contiene fichas.
* El bucket tiene una capacidad máxima de tokens.
* Los tokens se agregan al depósito a un ritmo fijo (por ejemplo, 10 tokens por segundo).
* Cuando llega una solicitud, debe obtener un token del depósito para continuar.
* Si hay suficientes tokens, se permite la solicitud y se eliminan los tokens.
* Si no hay suficientes tokens, se descarta la solicitud.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.35.26.png" alt="" width="563"><figcaption></figcaption></figure>

**Ventajas:**

* Relativamente sencillo de implementar y comprender.
* Permite ráfagas de solicitudes hasta la capacidad del depósito, adaptándose a picos de corto plazo.

**Contras:**

* El uso de memoria se escala con el número de usuarios si se implementa por usuario.
* No garantiza un ritmo de solicitudes perfectamente uniforme.

## 2. Leaky Bucket

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.36.23.png" alt="" width="563"><figcaption></figcaption></figure>

El algoritmo Leaky Bucket es similar a Token Bucket pero se centra en suavizar el tráfico en ráfagas.

**Cómo funciona:**

1. Imagínese un cubo con un pequeño agujero en el fondo.
2. Las solicitudes ingresan al contenedor desde la parte superior.
3. El cubo procesa ("filtra") las solicitudes a un ritmo constante a través del orificio.
4. Si el depósito está lleno, se descartan las nuevas solicitudes.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.36.55.png" alt="" width="563"><figcaption></figcaption></figure>

**Ventajas:**

* Procesa las solicitudes a un ritmo constante, evitando que ráfagas repentinas saturen el sistema.
* Proporciona una tasa consistente y predecible de procesamiento de solicitudes.

**Contras:**

* No gestiona bien las ráfagas repentinas de solicitudes; las solicitudes en exceso se descartan inmediatamente.
* Un poco más complejo de implementar en comparación con Token Bucket.

## 3. Fixed Window Counter

<figure><img src="../../.gitbook/assets/image (1).png" alt="" width="563"><figcaption></figcaption></figure>

El algoritmo Contador de ventana fija divide el tiempo en ventanas fijas y cuenta las solicitudes en cada ventana.

**Cómo funciona:**

1. El tiempo se divide en ventanas fijas (por ejemplo, intervalos de 1 minuto).
2. Cada ventana tiene un contador que comienza en cero.
3. Las nuevas solicitudes incrementan el contador de la ventana actual.
4. Si el contador excede el límite, las solicitudes se rechazan hasta la siguiente ventana.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.38.19.png" alt="" width="563"><figcaption></figcaption></figure>

**Ventajas:**

* Fácil de implementar y comprender.
* Proporciona límites de velocidad claros y fáciles de entender para cada ventana de tiempo.

**Contras:**

* No gestiona bien las ráfagas de solicitudes en el límite de las ventanas. Puede permitir el doble de solicitudes en los bordes de las ventanas.

## 4. Sliding Window Log

<figure><img src="../../.gitbook/assets/image (2).png" alt="" width="563"><figcaption></figcaption></figure>

El algoritmo de registro de ventana deslizante mantiene un registro de marcas de tiempo para cada solicitud y lo utiliza para determinar si se debe permitir una nueva solicitud.

**Cómo funciona:**

1. Mantenga un registro de las marcas de tiempo de las solicitudes.
2. Cuando llega una nueva solicitud, elimine todas las entradas que sean más antiguas que el tamaño de la ventana.
3. Cuente las entradas restantes.
4. Si el recuento es menor que el límite, permita la solicitud y agregue su marca de tiempo al registro.
5. Si el recuento excede el límite, se rechaza la solicitud.



<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.39.56.png" alt="" width="563"><figcaption></figcaption></figure>

**Ventajas:**

* Muy preciso, sin bordes rugosos entre las ventanas.
* Funciona bien para API de bajo volumen.

**Contras:**

* Puede consumir mucha memoria para API de gran volumen.
* Requiere almacenar y buscar a través de marcas de tiempo.

## 5. Sliding Window Counter

<figure><img src="../../.gitbook/assets/image (3).png" alt="" width="563"><figcaption></figcaption></figure>

Este algoritmo combina los enfoques del contador de ventana fija y del registro de ventana deslizante para obtener una solución más precisa y eficiente.

En lugar de realizar un seguimiento de la marca de tiempo de cada solicitud individual como lo hace el registro deslizante, se centra en la cantidad de solicitudes de la última ventana.

Entonces, si estás en el 75% de la ventana actual, el 25% del peso provendría de la ventana anterior y el resto de la actual:

```
weight = (100 - 75)% * lastWindowRequests + currentWindowRequests
```

Ahora, cuando llega una nueva solicitud, se suma uno a ese peso (peso + 1). Si este nuevo total supera nuestro límite establecido, debemos rechazar la solicitud.

**Cómo funciona:**

1. Realice un seguimiento del número de solicitudes para la ventana actual y la anterior.
2. Calcular la suma ponderada de solicitudes en función de la superposición con la ventana deslizante.
3. Si la suma ponderada es menor que el límite, permitir la solicitud.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.41.39.png" alt="" width="563"><figcaption></figcaption></figure>

**Ventajas:**

* Más preciso que el contador de ventana fija.
* Más eficiente en el uso de memoria que Sliding Window Log.
* Suaviza los bordes entre las ventanas.

**Contras:**

* Un poco más complejo de implementar.

Al implementar la limitación de velocidad, considere factores como la escala de su sistema, la naturaleza de sus patrones de tráfico y la granularidad del control que necesita.

Por último, comunique siempre claramente sus límites de velocidad a los usuarios de su API, preferiblemente a través de encabezados de respuesta, para que puedan implementar estrategias de reintento y retroceso adecuadas en sus clientes.
