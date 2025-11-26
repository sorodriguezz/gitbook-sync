# IP Addresses

Cada vez que visita un sitio web, envía un correo electrónico o realiza una llamada API, su dispositivo utiliza una **dirección IP** para identificarse.

Esta dirección garantiza que los datos que usted solicita lleguen a usted y que los datos que envía lleguen a su destino previsto.

El **Protocolo de Internet (IP)** es el conjunto de reglas que rige este sistema de direccionamiento. Opera en la Capa 3 (Capa de Red) del Modelo OSI, donde su función principal es proporcionar direccionamiento lógico y enrutar paquetes de datos desde un dispositivo de origen a un dispositivo de destino a través de una o más redes.

## 1. ¿Qué es una dirección IP? <a href="#id-1-what-is-an-ip-address" id="id-1-what-is-an-ip-address"></a>

Una **dirección IP** es una etiqueta numérica única asignada a cada dispositivo conectado a una red que utiliza el Protocolo de Internet para comunicarse. Es como un número de teléfono para tu computadora o una dirección de correo para tu portátil.

Cumple dos funciones principales:

1. **Identificación** : Identifica de forma única un dispositivo (o, más precisamente, una interfaz de red) en la red. Esto indica a otros dispositivos quién envía o recibe información.
2. **Direccionamiento de ubicación** : especifica la ubicación del dispositivo en la red y proporciona una ruta para entregar los datos.

{% hint style="info" %}
#### Analogía del mundo real: el sistema postal <a href="#real-world-analogy-postal-system" id="real-world-analogy-postal-system"></a>

Tu dirección IP es como la dirección de tu casa. El nombre de la calle y la ciudad identifican tu barrio (la red), mientras que el número de tu casa identifica tu domicilio específico (el dispositivo).

Los carteros (enrutadores) utilizan esta dirección para entregar paquetes (paquetes de datos) a la ubicación correcta.
{% endhint %}

## 2. Estructura de la dirección IP <a href="#id-2-ip-address-structure" id="id-2-ip-address-structure"></a>

Una dirección IP no es solo un número aleatorio; tiene una estructura clara que se divide en dos partes principales:

* **ID de red** : Esta parte de la dirección identifica la red específica en la que se encuentra el dispositivo. Todos los dispositivos de la misma red local comparten el mismo ID de red.
* **ID de host** : esta parte de la dirección identifica un dispositivo específico (como una computadora, un servidor o un teléfono inteligente) dentro de esa red.

Por ejemplo, en la dirección IPv4 común `192.168.1.10`con una máscara de subred estándar, el desglose es:

* **ID de red** :`192.168.1`
* **ID de host** :`10`

Los enrutadores se fijan principalmente en el **ID de red** . No necesitan conocer todos los dispositivos del mundo, solo la red a la que se destina el paquete. Usan esta información para reenviarlo al siguiente enrutador más cercano a la red de destino.

## 3. IPv4: El formato de dirección clásico <a href="#id-3-ipv4-the-classic-address-format" id="id-3-ipv4-the-classic-address-format"></a>

**El Protocolo de Internet versión 4 (IPv4)** es el sistema de direccionamiento IP original y más utilizado.

Es una dirección **de 32 bits** , lo que significa que hay 232 (aproximadamente 4,3 mil millones) direcciones únicas posibles.

**Formato**

Se escribe como cuatro números separados por puntos (notación decimal punteada), donde cada número es un "octeto" que va de 0 a 255 (por ejemplo, `172.217.167.78`).

**Clases de direcciones**

Históricamente, las direcciones IPv4 se dividían en clases (A, B, C) para definir la división entre los identificadores de red y de host. Si bien este sistema ha sido reemplazado en gran medida por CIDR (véase más adelante), este contexto histórico resulta útil.

* **Clase A** : Para redes muy grandes (por ejemplo, `10.0.0.0`).
* **Clase B** : Para redes de tamaño mediano (por ejemplo, `172.16.0.0`).
* **Clase C** : Para redes pequeñas (por ejemplo, `192.168.1.0`).

**Limitación**

El mayor problema de IPv4 es **el agotamiento de direcciones** . Con la proliferación de dispositivos conectados a internet, los aproximadamente 4.300 millones de direcciones se han agotado.

## 4. IPv6: El estándar moderno <a href="#id-4-ipv6-the-modern-standard" id="id-4-ipv6-the-modern-standard"></a>

Para resolver el problema del agotamiento de direcciones, se desarrolló **el Protocolo de Internet versión 6 (IPv6) .**

Es una dirección **de 128 bits** , que proporciona la asombrosa cantidad de 2128 (o 340 undecillones) direcciones únicas. Esto es suficiente para asignar una dirección IP a cada átomo de la superficie de la Tierra y aún tener direcciones disponibles.

**Formato**

Se escribe como ocho grupos de cuatro dígitos hexadecimales, separados por dos puntos (p. ej., `2001:0db8:85a3:0000:0000:8a2e:0370:7334`). Los ceros se pueden comprimir para mayor brevedad.

IPv6 no solo es más grande, sino también mejor. Incluye varias mejoras integradas:

* **Configuración automática de direcciones sin estado (SLAAC)** : los dispositivos pueden generar su propia dirección IP sin necesidad de un servidor DHCP.
* **Sin NAT** : el enorme espacio de direcciones elimina la necesidad de traducción de direcciones de red (NAT), lo que simplifica las redes.
* **Seguridad incorporada** : IPsec, un conjunto de protocolos para proteger las comunicaciones, es una parte obligatoria de IPv6.
* **Enrutamiento eficiente** : los encabezados de paquetes simplificados permiten un procesamiento más rápido por parte de los enrutadores.

## 5. Direcciones IP públicas y privadas <a href="#id-5-public-vs-private-ip-addresses" id="id-5-public-vs-private-ip-addresses"></a>

No todas las direcciones IP son iguales. Se dividen en dos categorías principales:

* **Dirección IP pública** : Esta es una dirección única a nivel mundial, enrutable en internet. Tu proveedor de servicios de internet (ISP) asigna una a tu router. Cuando visitas un sitio web, tu solicitud se envía desde tu IP pública.
* **Dirección IP privada** : Se trata de una dirección que se utiliza dentro de una red local privada (como la red Wi-Fi de casa o la red LAN de una oficina). Estas direcciones no son enrutables en internet y se reutilizan en millones de redes privadas en todo el mundo.

Los rangos de IP privados estándar son:

* **10.0.0.0** a **10.255.255.255**
* **172.16.0.0** a **172.31.255.255**
* **192.168.0.0** a **192.168.255.255**

**Entonces, ¿cómo acceden a Internet los dispositivos con IP privadas?**

A través de **la traducción de direcciones de red (NAT)** .

Tu router doméstico actúa como una puerta de enlace NAT. Recibe las solicitudes de los dispositivos de tu red privada, reemplaza su IP de origen privada con su IP pública única y envía la solicitud a internet. Cuando recibe la respuesta, el router sabe a qué dispositivo privado reenviarla.

## 6. Direcciones IP estáticas vs. dinámicas <a href="#id-6-static-vs-dynamic-ip-addresses" id="id-6-static-vs-dynamic-ip-addresses"></a>

Las direcciones IP se pueden asignar de dos maneras:

* **IP estática** : Una dirección IP estática se configura manualmente para un dispositivo y no cambia. Esto es esencial para servidores, impresoras y otros dispositivos que necesitan estar siempre accesibles en la misma dirección. Son fiables, pero suelen ser más caras y requieren gestión manual.
* **IP dinámica : Un servidor DHCP (Protocolo de configuración dinámica de host)** asigna automáticamente una dirección IP dinámica . La mayoría de los dispositivos domésticos (portátiles, teléfonos) utilizan IP dinámicas. La dirección se alquila por un periodo y puede cambiar la próxima vez que se conecte. Esto resulta muy eficiente para gestionar un gran número de dispositivos.

## 7. Subredes y CIDR <a href="#id-7-subnetting-and-cidr" id="id-7-subnetting-and-cidr"></a>

A medida que las redes crecen, suele ser necesario dividirlas en segmentos más pequeños y manejables. Este proceso se denomina **subredes** . Ayuda a mejorar el rendimiento, optimizar la seguridad y organizar la red de forma lógica.

La subred implica tomar prestados bits del ID de host de una dirección IP para crear más ID de red. Una **máscara de subred** indica a los dispositivos qué parte de la dirección corresponde a la red y cuál al host.

Aquí es donde entra en juego **CIDR (Enrutamiento Interdominio sin Clases)** . CIDR abandonó el antiguo sistema con clases e introdujo una forma más flexible de definir la parte de red de una dirección. La notación CIDR utiliza una barra diagonal seguida de un número para representar la cantidad de bits del ID de red.

* `192.168.1.0/24`significa que los primeros 24 bits son el ID de red.
* Máscara de subred:`255.255.255.0`
* Anfitriones: 254
* `192.168.1.0/26`significa que los primeros 26 bits son el ID de la red.
* Máscara de subred:`255.255.255.192`
* Esto divide la `/24`red en cuatro subredes más pequeñas, cada una con 62 hosts.

## 8. Bucle invertido y direcciones especiales <a href="#id-8-loopback-and-special-addresses" id="id-8-loopback-and-special-addresses"></a>

Además de las direcciones públicas y privadas, existen varias direcciones IP para propósitos especiales:

* **Dirección de bucle invertido** : `127.0.0.1`(IPv4) e `::1`(IPv6). Esta dirección, también conocida como "localhost", siempre se refiere al dispositivo local. Se utiliza para probar aplicaciones de red sin enviar paquetes a la red.
* **Dirección de difusión** : una dirección utilizada para enviar un mensaje a todos los dispositivos en una subred local simultáneamente (por ejemplo, `192.168.1.255`para la `192.168.1.0/24`red).
* **Dirección local de enlace** : El `169.254.0.0/16`rango. Si un dispositivo está configurado para DHCP pero no encuentra un servidor DHCP, se asignará una dirección de este rango para comunicarse en la red local.
* **Direcciones de multidifusión** : un bloque especial de direcciones utilizado para la comunicación de uno a muchos, donde se envía un solo paquete a un "grupo" de receptores interesados.
