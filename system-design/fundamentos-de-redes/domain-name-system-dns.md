# Domain Name System (DNS)

Al escribir la dirección de un sitio web en tu **navegador** , ocurre algo casi mágico. En cuestión de segundos, tu pantalla se llena de contenido de servidores que podrían estar a miles de kilómetros de distancia.

Detrás de esta experiencia perfecta hay un sistema crucial: **DNS (Sistema de nombres de dominio)** .

A menudo se la llama la “guía telefónica de Internet”, pero esa descripción apenas roza la superficie.

Vamos a desglosarlo y ver cómo funciona _realmente el DNS_ .

## 1. ¿Por qué necesitamos DNS?

En internet, las computadoras se comunican mediante **direcciones IP** como \[ `104.198.32.55`nombre del servidor]. Se pueden considerar como números de teléfono de servidores.

Sin embargo, los humanos recordamos mucho mejor nombres como `google.com`. No podemos esperar que los usuarios (ni siquiera los sistemas) memoricen una serie de números aleatorios para cada servicio al que se conectan.

Aquí es donde el **Sistema de Nombres de Dominio (DNS)** ayuda. Actúa como traductor entre **nombres de dominio legibles por humanos** y **direcciones IP compatibles con máquinas**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.35.53.png" alt="" width="375"><figcaption></figcaption></figure>

* Escribe una URL ( `algomaster.io`)
* El DNS lo asigna a la IP correcta ( `34.121.45.67`)
* Su navegador se conecta a esa IP
* Ves el sitio web

Sin DNS, todos nos veríamos obligados a escribir direcciones IP sin formato, como `34.121.45.67`en nuestros navegadores. Eso haría que usar internet fuera mucho menos cómodo.

## 2. El recorrido de una consulta DNS

Cuando ingresa un nombre de dominio (por ejemplo, google.com) en su navegador, este es el recorrido paso a paso de cómo ese nombre se resuelve en una dirección IP:

### 1. La caché del navegador

La primera comprobación del navegador es su propia caché. Comprueba si ha resuelto recientemente el dominio que intentas visitar.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.37.00.png" alt="" width="375"><figcaption></figcaption></figure>

En ese caso, utiliza directamente la IP almacenada en caché. Esta es la ruta más rápida, ya que no requiere trabajo adicional.

### 2. La caché del sistema operativo

Si el navegador no lo sabe, recurre al sistema operativo de su computadora.

El sistema operativo mantiene su propia caché local de búsquedas de dominio recientes, compartida entre aplicaciones. Si el registro existe aquí, el sistema operativo devuelve la IP y la búsqueda se completa.

<figure><img src="../../.gitbook/assets/image (3).png" alt="" width="375"><figcaption></figcaption></figure>

### 3. El solucionador recursivo

Si el sistema operativo no tiene la respuesta, su computadora envía la consulta a un servidor especial llamado **Recursive Resolver** .

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Este solucionador generalmente lo opera su proveedor de servicios de Internet (ISP) o un servicio DNS público como:

* **DNS de Google:** 8.8.8.8
* **DNS de Cloudflare:** 1.1.1.1
* **OpenDNS:** 208.67.222.222

La función del solucionador recursivo es realizar todo el trabajo duro de encontrar la dirección IP correcta. No se rendirá hasta encontrar la respuesta o confirmar que el dominio no existe.

### 4. Los servidores raíz

Si el solucionador aún no tiene la respuesta en su caché, comienza su búsqueda en la parte superior de la jerarquía de Internet: los **servidores raíz** .

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Sólo hay 13 conjuntos de estos servidores raíz a nivel mundial (aunque están replicados en cientos de ubicaciones para mayor confiabilidad).

* Los servidores raíz no conocen la IP final, pero saben dónde buscar a continuación.
* Analizan la última parte del dominio (.com) `google.com)`y dirigen el solucionador al **servidor de dominio de nivel superior (TLD) apropiado** .

### 5. Los servidores TLD

El solucionador ahora se comunica con los servidores TLD. El **servidor TLD** administra todos los dominios que terminan en una extensión específica, como `.com`, `.org`, `.gov`, `.in`, etc.

Entonces, el solucionador le pregunta al `.com`servidor TLD: "Oye, ¿dónde puedo encontrar información sobre `google.com`?"

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt="" width="375"><figcaption></figcaption></figure>

El servidor TLD tampoco tiene la dirección IP final. Sin embargo, sabe cuál _es_ el servidor oficial que guarda los registros del `google.com`dominio. Apunta al solucionador al **Servidor de Nombres Autorizado de ese dominio** .

### 6. El servidor de nombres autorizado

Finalmente, el solucionador contacta con el **servidor de nombres autorizado** . Este servidor es la fuente de información veraz definitiva para un dominio específico. Contiene los registros DNS oficiales `google.com`y conoce la dirección IP exacta.

<figure><img src="../../.gitbook/assets/image (4).png" alt="" width="375"><figcaption></figcaption></figure>

* El servidor autorizado responde con la dirección IP correcta (por ejemplo, `142.250.183.100`).
* Estos registros pueden incluir múltiples IP para equilibrar la carga y la conmutación por error.

> El servidor autorizado devuelve el **registro A** (para IPv4) o **el registro AAAA** (para IPv6). También puede devolver otros registros según la consulta (p. ej., MX para correo electrónico, CNAME para alias, TXT para verificación).

### 7. Volver al navegador

El solucionador recursivo ahora tiene la dirección IP. Envía esta información a tu computadora. Esta **almacena** la respuesta en caché para no tener que repetir todo el proceso la próxima vez.

Finalmente, su navegador utiliza la dirección IP para conectarse al servidor de Google y la página web comienza a cargarse.

Todo esto sucede en milisegundos.

### Resumen

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.43.22.png" alt="" width="375"><figcaption></figcaption></figure>

## 3. Tipos de registros DNS

Los servidores de nombres autorizados no almacenan solo un tipo de registro, sino diferentes tipos de instrucciones, cada una con una función específica:

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.44.09.png" alt="" width="375"><figcaption></figcaption></figure>

## 4. ¿Qué hace que el DNS sea rápido y confiable?

La magia del DNS no está sólo en traducir nombres a números, sino en cómo lo hace **de manera rápida y confiable** para miles de millones de usuarios todos los días.

Varias opciones de diseño hacen que el DNS sea rápido y resistente:

### 1. Redes globales Anycast

Los servidores raíz y los solucionadores públicos (como Cloudflare o Google DNS) utilizan **enrutamiento anycast** , lo que significa que la misma dirección IP se anuncia desde muchas ubicaciones alrededor del mundo.

* Cuando envías una consulta, ésta va automáticamente al servidor disponible más cercano.
* Esto reduce la latencia y garantiza que su solicitud no tenga que viajar al otro lado del mundo.

### 2. Servidores autorizados redundantes

Los dominios generalmente tienen más de un servidor de nombres autorizado, distribuido en diferentes regiones.

* Si un servidor falla o se vuelve inaccesible, otro puede responder.
* Esta redundancia garantiza alta disponibilidad y tolerancia a fallos.

### 3. GeoDNS

Algunos dominios utilizan **respuestas DNS basadas en la ubicación geográfica** .

* El mismo dominio puede resolverse en diferentes direcciones IP dependiendo de dónde se origine la solicitud.
* Esto puede mejorar el rendimiento (al dirigirlo al servidor más cercano) o satisfacer las necesidades de cumplimiento (al dirigirlo a un centro de datos específico del país).

### 4. Equilibrio de carga con DNS

El DNS puede devolver múltiples direcciones IP para un solo dominio.

* Con múltiples **registros A** o **CNAME** , el tráfico se distribuye entre varios servidores.
* Esta sencilla forma de equilibrar la carga distribuye las solicitudes y evita que un solo servidor se sature.

### 5. Redes de distribución de contenido (CDN)

Muchos sitios web dependen de CDN para acelerar la entrega de contenido.

* Las consultas DNS se resuelven en un **servidor perimetral** ubicado cerca del usuario.
* De esta manera, los archivos estáticos (como imágenes, vídeos, scripts) se cargan desde la ubicación más cercana, lo que reduce la latencia y mejora la experiencia del usuario.

## 5. Reflexiones finales

La próxima vez que escriba una URL, recuerde lo que sucede detrás de escena:

* Su navegador primero verifica si ya conoce la IP.
* En caso contrario, su computadora transmite la pregunta hasta que finalmente una fuente autorizada responda.
* Gracias al almacenamiento en caché y a una jerarquía de servidores, todo este proceso es rápido, eficiente y prácticamente invisible para usted.

El DNS es uno de esos héroes silenciosos de internet: siempre trabajando en segundo plano, garantizando que los humanos puedan usar nombres mientras las máquinas usan números. Sin él, internet tal como lo conocemos simplemente no existiría.
