# OSI Model

Cuando envías un mensaje en WhatsApp, transmites un video o implementas una API, innumerables capas de red trabajan detrás de escena para que esa comunicación sea confiable.

Los datos no saltan mágicamente de una computadora a otra; pasan por un viaje meticuloso, transformándose y enrutando en cada paso.

El **modelo OSI (Interconexión de sistemas abiertos)** es un marco conceptual que ilustra maravillosamente este viaje.

Desarrollado por la Organización Internacional de Normalización (ISO), describe cómo los datos se mueven desde una aplicación en una computadora, a través de una red, a una aplicación en otra computadora.

Su propósito principal es:

* **Estandarizar la comunicación** : proporcionar un lenguaje y un marco comunes para diseñadores y desarrolladores de redes.
* **Garantizar la interoperabilidad** : permitir que distintos hardware y software de distintos proveedores se comuniquen sin problemas.
* **Promover el diseño modular** : dividir las tareas de red complejas en capas más pequeñas y manejables, lo que facilita el desarrollo y la resolución de problemas.

## 1. Descripción general del modelo OSI <a href="#id-1-overview-of-the-osi-model" id="id-1-overview-of-the-osi-model"></a>

El modelo OSI divide el proceso de comunicación de red en **siete capas distintas** , cada una con responsabilidades específicas. Imagínese una línea de montaje en una fábrica, donde cada estación realiza una tarea especializada antes de pasar el producto a la siguiente.

A continuación se muestra una descripción general rápida de las siete capas, desde la superior (la más cercana al usuario) hasta la inferior (la más cercana al hardware físico):

| **Capa** | **Nombre**          | **Función primaria**                                            | **Protocolos de ejemplo/conceptos**      |
| -------- | ------------------- | --------------------------------------------------------------- | ---------------------------------------- |
| **7**    | **Solicitud**       | Servicios orientados al usuario, interacción con aplicaciones   | HTTP, DNS, SMTP, FTP, SSH, gRPC          |
| **6**    | **Presentación**    | Formato de datos, cifrado, compresión                           | SSL/TLS, MIME, JSON, XML, Protobuf       |
| **5**    | **Sesión**          | Gestionar sesiones de comunicación                              | RPC, NetBIOS, Sockets (conceptualmente)  |
| **4**    | **Transporte**      | Entrega de datos confiable de extremo a extremo                 | TCP, UDP                                 |
| **3**    | **Red**             | Direccionamiento lógico y enrutamiento a través de redes        | IP, ICMP, OSPF, BGP                      |
| **2**    | **Enlace de datos** | Direccionamiento físico, detección de errores para enlace local | Ethernet, Wi-Fi (direcciones MAC), ARP   |
| **1**    | **Físico**          | Transmitir bits sin procesar a través de medios físicos         | Cables (Ethernet, Fibra), Ondas de Radio |

Un concepto crucial es **la encapsulación**: a medida que los datos descienden por la pila en el emisor, cada capa añade su propio encabezado (y, a veces, un final) que contiene información de control. Cuando los datos ascienden por la pila en el receptor, cada capa elimina su encabezado correspondiente.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.21.01.png" alt=""><figcaption></figcaption></figure>

### Capa 1 – Capa física <a href="#layer-1-physical-layer" id="layer-1-physical-layer"></a>

La capa física es responsable de la transmisión de **bits sin procesar (0 y 1)** a través del medio de comunicación físico. Define las especificaciones eléctricas, mecánicas, procedimentales y funcionales para la activación, el mantenimiento y la desactivación de los enlaces físicos.

**Ejemplos** : cables Ethernet (Cat 5e, Cat 6), fibra óptica, frecuencias de radio Wi-Fi, DSL, módems, concentradores, repetidores.

### Capa 2 – Capa de enlace de datos <a href="#layer-2-data-link-layer" id="layer-2-data-link-layer"></a>

La capa de enlace de datos garantiza la transmisión fiable de datos entre nodos conectados directamente (es decir, dispositivos en el mismo segmento de red local). Gestiona **el entramado** (dividiendo los datos en unidades manejables), **el direccionamiento físico** (mediante direcciones MAC) y **la detección y corrección de errores** en el enlace local.

**Ejemplos** : Ethernet, Wi-Fi (estándares 802.11), ARP (Protocolo de resolución de direcciones), conmutadores.

### Capa 3 – Capa de red <a href="#layer-3-network-layer" id="layer-3-network-layer"></a>

La capa de red es responsable del direccionamiento lógico (direcciones IP) y del enrutamiento de paquetes de datos a través de múltiples redes interconectadas. Determina la mejor ruta para que los datos viajen del origen al destino.

**Protocolos** : IP (Protocolo de Internet), ICMP (Protocolo de mensajes de control de Internet), OSPF (Primero la ruta más corta abierta), BGP (Protocolo de puerta de enlace de borde), enrutadores.

### Capa 4 – Capa de transporte

La capa de transporte proporciona comunicación de extremo a extremo entre procesos (aplicaciones) en diferentes hosts. Gestiona la segmentación de datos, la multiplexación (envío de datos desde múltiples aplicaciones a través de una conexión de red) y, a menudo, **la transferencia de datos fiable** .

**Protocolos** : TCP (Protocolo de control de transmisión) y UDP (Protocolo de datagramas de usuario).

### Capa 5 – Capa de sesión <a href="#layer-5-session-layer" id="layer-5-session-layer"></a>

La capa de sesión se encarga de establecer, gestionar y finalizar las sesiones de comunicación entre aplicaciones. Gestiona aspectos como la autenticación, la autorización y el seguimiento de qué aplicación envió qué datos.

**Si bien** son menos claros en las implementaciones modernas de TCP/IP, conceptos como sesiones RPC (llamada a procedimiento remoto) o conexiones WebSocket se alinean con el propósito de esta capa.

### Capa 6 – Capa de presentación <a href="#layer-6-presentation-layer" id="layer-6-presentation-layer"></a>

La capa de presentación garantiza que los datos estén en un formato utilizable para la capa de aplicación. Gestiona la traducción, el cifrado/descifrado, la compresión/descompresión y la serialización/deserialización de datos.

**Ejemplos** : SSL/TLS (cifrado), JSON, XML, Protocol Buffers (serialización), MIME (especificación de formato de datos).

### Capa 7 – Capa de aplicación <a href="#layer-7-application-layer" id="layer-7-application-layer"></a>

La capa de aplicación es la más cercana al usuario final. Define cómo las aplicaciones interactúan con la red y proporciona servicios orientados al usuario. Aquí es donde suele residir la lógica real del software.

**Ejemplos** : HTTP, HTTPS, DNS, SMTP, FTP, SSH, Telnet, gRPC, WebSocket.

## 2. Encapsulación y desencapsulación en acción <a href="#id-2-encapsulation-and-decapsulation-in-action" id="id-2-encapsulation-and-decapsulation-in-action"></a>

Rastreemos una simple solicitud HTTP GET desde su navegador a un servidor web:

1. **Capa de aplicación (L7)** : su navegador crea una solicitud HTTP GET ( `GET /index.html HTTP/1.1`).
2. **Capa de presentación (L6)** : Los datos se preparan, posiblemente se comprimen, y luego se cifran mediante TLS. La solicitud HTTP sin procesar se convierte en datos cifrados.
3. **Capa de sesión (L5)** : se administra una sesión TLS.
4. **Capa de Transporte (L4)** : Los datos cifrados se segmentan (si son demasiado grandes) y se añade un encabezado TCP, que incluye los números de puerto de origen y destino (p. ej., `SRC=50000, DEST=443`). Esta unidad se convierte en un segmento TCP.
5. **Capa de red (L3)** : Se añade un encabezado IP que contiene la dirección IP de origen de su ordenador y la dirección IP de destino del servidor web. Esta unidad ahora es un paquete IP.
6. **Capa de enlace de datos (L2)** : Se añaden un encabezado y un final de Ethernet, que contienen la dirección MAC de origen de la tarjeta de red y la dirección MAC de destino del router (para el primer salto). Esta unidad ahora es una trama Ethernet.
7. **Capa física (L1)** : la trama se convierte en señales eléctricas sin procesar o pulsos de luz y se envía a través del medio físico (por ejemplo, ondas de radio Wi-Fi, cable Ethernet).

En el servidor web, el proceso se invierte:

1. **Capa física (L1)** : se reciben señales eléctricas y se convierten nuevamente en una trama Ethernet.
2. **Capa de enlace de datos (L2)** : Se elimina el encabezado/final de Ethernet, se verifican las direcciones MAC y se extrae el paquete IP.
3. **Capa de red (L3)** : Se elimina el encabezado IP y se verifica la IP de destino. Se extrae el segmento TCP.
4. **Capa de Transporte (L4)** : Se elimina el encabezado TCP, se verifica el puerto de destino (443) y se reensamblan los segmentos. Los datos cifrados se transmiten.
5. **Capa de sesión (L5)** : se administra la sesión TLS.
6. **Capa de presentación (L6)** : los datos se descifran mediante TLS y se descomprimen.
7. **Capa de aplicación (L7)** : la solicitud HTTP GET original se entrega a la aplicación del servidor web, que luego la procesa.

## 3. Modelo OSI vs. TCP/IP

Si bien el modelo OSI es una herramienta teórica potente, el **modelo TCP/IP** es el que realmente se implementa y utiliza en Internet. Es un modelo más pragmático y consolidado, generalmente con cuatro capas:

| **Capa TCP/IP**     | **Capas OSI correspondientes** | **Enfocar**                                          |
| ------------------- | ------------------------------ | ---------------------------------------------------- |
| **Solicitud**       | L5, L6, L7                     | Aplicaciones orientadas al usuario, formato de datos |
| **Transporte**      | L4                             | Comunicación de extremo a extremo (TCP/UDP)          |
| **Internet**        | L3                             | Direccionamiento lógico y enrutamiento (IP)          |
| **Acceso a la red** | L1, L2                         | Medios físicos, enlaces de red local                 |

En sistemas reales, la línea entre capas puede difuminarse. Por ejemplo, **HTTPS** opera técnicamente entre las capas 5 a 7 (Sesión, Presentación, Aplicación), ya que implica las solicitudes de aplicación, el cifrado y el establecimiento de sesiones.

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>
