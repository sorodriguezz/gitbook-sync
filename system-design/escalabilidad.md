# Escalabilidad

A medida que un sistema crece, el rendimiento comienza a  **degradarse**  a menos que lo adaptemos para lidiar con ese crecimiento.

**La escalabilidad** es la propiedad de un sistema de manejar una cantidad creciente de carga  **agregando recursos**  al sistema.

> Un sistema que puede evolucionar continuamente para soportar una cantidad creciente de trabajo es escalable.

En este artículo, exploraremos diferentes formas en que un sistema puede crecer y formas comunes de hacer que un sistema sea escalable.

## ¿Cómo puede crecer un sistema? <a href="#how-can-a-system-grow" id="how-can-a-system-grow"></a>

Un sistema puede crecer en varias dimensiones.

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 19.58.04.png" alt=""><figcaption></figcaption></figure>

### **1. Crecimiento de la base de usuarios**

Más usuarios comenzaron a utilizar el sistema, lo que generó un mayor número de solicitudes.

* **Ejemplo:**  Una plataforma de redes sociales que está experimentando un aumento de nuevos usuarios.

### **2. Crecimiento en las características**

Se introdujeron más funciones para ampliar las capacidades del sistema.

* **Ejemplo:**  un sitio web de comercio electrónico que agrega soporte para un nuevo método de pago.

### **3. Crecimiento del volumen de datos**

Crecimiento en la cantidad de datos que el sistema almacena y administra debido a la actividad o el registro del usuario.

* **Ejemplo:**  una plataforma de transmisión de video como YouTube que almacena más contenido de video a lo largo del tiempo.

### **4. Crecimiento en complejidad**

La arquitectura del sistema evoluciona para adaptarse a nuevas características, escalas o integraciones, lo que genera componentes y dependencias adicionales.

* **Ejemplo:**  Un sistema que comenzó como una aplicación simple se divide en sistemas más pequeños e independientes.

### **5. Crecimiento del alcance geográfico**

El sistema se amplía para dar servicio a usuarios en nuevas regiones o países.

* **Ejemplo:**  Una empresa de comercio electrónico que lanza sitios web y distribución en nuevos mercados internacionales.

## ¿Cómo escalar un sistema? <a href="#how-to-scale-a-system" id="how-to-scale-a-system"></a>

A continuación se muestran 10 formas comunes de hacer que un sistema sea escalable:

### 1. Escalado vertical (escalamiento vertical) <a href="#id-1-vertical-scaling-scale-up" id="id-1-vertical-scaling-scale-up"></a>

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.02.14.png" alt="" width="375"><figcaption></figcaption></figure>

Esto significa agregar más potencia a sus máquinas existentes actualizando el servidor con más RAM, CPU más rápidas o almacenamiento adicional.

Es un buen enfoque para arquitecturas más simples, pero tiene limitaciones en cuanto a hasta dónde se puede llegar.

### 2. Escalamiento horizontal (escalamiento horizontal) <a href="#id-2-horizontal-scaling-scale-out" id="id-2-horizontal-scaling-scale-out"></a>

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.03.09.png" alt="" width="375"><figcaption></figcaption></figure>

Esto significa agregar más máquinas a su sistema para distribuir la carga de trabajo entre múltiples servidores.

A menudo se considera la forma más eficaz de escalar sistemas grandes.

> **Ejemplo:**  Netflix utiliza escalamiento horizontal para su servicio de streaming, agregando más servidores a sus clústeres para manejar el creciente número de usuarios y tráfico de datos.

### 3. Equilibrio de carga <a href="#id-3-load-balancing" id="id-3-load-balancing"></a>

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.05.27.png" alt="" width="375"><figcaption></figcaption></figure>

El equilibrio de carga es el proceso de distribuir el tráfico entre múltiples servidores para garantizar que ningún servidor se sature.

> **Ejemplo:**  Google utiliza ampliamente el equilibrio de carga en su infraestructura global para distribuir las consultas de búsqueda y el tráfico de manera uniforme entre sus enormes granjas de servidores.

### 4. Almacenamiento en caché

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.06.31.png" alt="" width="375"><figcaption></figcaption></figure>

El almacenamiento en caché es una técnica para almacenar datos a los que se accede con frecuencia en la memoria (como RAM) para reducir la carga en el servidor o la base de datos.

La implementación del almacenamiento en caché puede mejorar drásticamente los tiempos de respuesta.

> **Ejemplo:**  Reddit utiliza almacenamiento en caché para almacenar contenido al que se accede con frecuencia, como publicaciones y comentarios destacados, para que se puedan publicar rápidamente sin tener que consultar la base de datos cada vez.

### 5. Redes de distribución de contenido (CDN)

La CDN distribuye recursos estáticos (imágenes, vídeos, etc.) más cerca de los usuarios. Esto puede reducir la latencia y generar tiempos de carga más rápidos.

> **Ejemplo:**  Cloudflare proporciona servicios de CDN, acelerando el acceso a sitios web para usuarios de todo el mundo almacenando en caché el contenido en servidores ubicados cerca de los usuarios.

### 6. Fragmentación

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.07.52.png" alt="" width="375"><figcaption></figcaption></figure>

La fragmentación significa dividir datos o funcionalidades entre múltiples nodos/servidores para distribuir la carga de trabajo y evitar cuellos de botella.

> **Ejemplo:**  Amazon DynamoDB utiliza fragmentación para distribuir datos y tráfico para su servicio de base de datos NoSQL entre muchos servidores, lo que garantiza un rendimiento rápido y escalabilidad.

### 7. Comunicación asincrónica <a href="#id-7-asynchronous-communication" id="id-7-asynchronous-communication"></a>

La comunicación asincrónica significa aplazar tareas de larga duración o no críticas a colas en segundo plano o intermediarios de mensajes.

Esto garantiza que su aplicación principal siga respondiendo a los usuarios.

> **Ejemplo:**  Slack utiliza comunicación asincrónica para la mensajería. Al enviar un mensaje, la interfaz del remitente no se bloquea; sigue respondiendo mientras el mensaje se procesa y se entrega en segundo plano.

### 8. Arquitectura de microservicios <a href="#id-8-microservices-architecture" id="id-8-microservices-architecture"></a>

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.09.12.png" alt="" width="375"><figcaption></figcaption></figure>

La arquitectura de microservicios divide la aplicación en servicios más pequeños e independientes que pueden escalarse de forma independiente.

Esto mejora la resiliencia y permite que los equipos trabajen en componentes específicos en paralelo.

> **Ejemplo:**  Uber ha desarrollado su arquitectura en microservicios para manejar diferentes funciones como facturación, notificaciones y búsqueda de viajes de forma independiente, lo que permite un escalamiento eficiente y un desarrollo rápido.

### 9. Escalado automático <a href="#id-9-auto-scaling" id="id-9-auto-scaling"></a>

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.09.57.png" alt="" width="375"><figcaption></figcaption></figure>

El escalamiento automático significa ajustar automáticamente la cantidad de servidores activos en función de la carga actual.

Esto garantiza que el sistema pueda gestionar picos de tráfico sin intervención manual.

> **Ejemplo:**  AWS Auto Scaling monitorea las aplicaciones y ajusta automáticamente la capacidad para mantener un rendimiento constante y predecible al menor costo posible.

### 10. Implementación multirregional <a href="#id-10-multi-region-deployment" id="id-10-multi-region-deployment"></a>

Implemente la aplicación en múltiples centros de datos o regiones de nube para reducir la latencia y mejorar la redundancia.

> **Ejemplo:**  Spotify utiliza implementaciones multirregionales para garantizar que su servicio de transmisión de música siga teniendo alta disponibilidad y capacidad de respuesta para los usuarios de todo el mundo, independientemente de dónde se encuentren.
