# Proxy vs Reverse Proxy

**Los proxies** y **los proxies inversos** son servidores que se ubican entre clientes y servidores para mejorar la seguridad, la privacidad y el rendimiento.

Un **servidor proxy** (a veces llamado **proxy de avance** ) actúa en nombre de los clientes, mientras que un **proxy inverso** actúa en **nombre** de los servidores.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.46.43.png" alt="" width="375"><figcaption></figcaption></figure>

En este artículo, analizaremos las diferencias clave entre **los proxies** y **los proxies inversos** y cómo funcionan con ejemplos del mundo real e ilustraciones simples.

## 1. ¿Qué es un servidor proxy?

> Un **apoderado** es una entidad que tiene la autoridad de actuar en nombre de otra.

En términos informáticos, un **proxy** (o **proxy de reenvío** ) es un servidor que actúa en nombre de los clientes en una red.

Cuando envías una solicitud, como abrir una página web, el proxy la intercepta, la reenvía al servidor de destino y luego te transmite la respuesta del servidor.

<figure><img src="../../.gitbook/assets/image (5).png" alt="" width="375"><figcaption></figcaption></figure>

Piense en un servidor proxy como un **intermediario** que se encuentra entre una red privada e Internet pública.

Veamos un ejemplo simplificado de cómo un servidor proxy maneja una solicitud:

1. El usuario escribe la URL de un sitio web en su navegador. El servidor proxy intercepta la solicitud en lugar de dirigirla directamente al sitio web.
2. El servidor proxy examina la solicitud para decidir si debe reenviarla, rechazarla o servir una copia en caché.
3. Si el proxy decide reenviar la solicitud, contacta con el sitio web de destino. Este solo ve la IP del servidor proxy, no la del usuario.
4. Cuando el sitio web de destino responde, el proxy recibe la respuesta y la transmite al usuario.

### **Beneficios clave de los servidores proxy:**

1. **Privacidad y anonimato:** los servidores proxy ocultan su dirección IP utilizando la suya propia, por lo que el servidor de destino no puede conocer su ubicación o identidad real.
2. **Control de acceso:** las organizaciones utilizan servidores proxy para imponer restricciones de contenido y monitorear el uso de Internet.
3. **Seguridad:** Los proxies pueden filtrar contenido malicioso y bloquear sitios sospechosos, proporcionando una capa adicional de seguridad.
4. **Rendimiento mejorado:** los servidores proxy almacenan en caché el contenido al que se accede con frecuencia, lo que reduce la latencia y mejora los tiempos de carga de los sitios web.

### **¿Es una VPN lo mismo que un Proxy?**

No. Aunque ambos ocultan tu IP, una **VPN** cifra todo tu tráfico de internet, haciéndolo más seguro. Un proxy solo reenvía solicitudes específicas sin cifrarlas necesariamente.

### Aplicaciones reales de los servidores proxy

### **1. Evitar restricciones geográficas**

Uno de los usos más comunes de los servidores proxy es **eludir las restricciones geográficas** en sitios web y contenidos.

Los servicios de streaming, por ejemplo, suelen ofrecer contenido diferente según la ubicación del usuario. Con un servidor proxy ubicado en la región de destino, puedes acceder a la biblioteca de contenido de esa región como si fueras un usuario local.

<figure><img src="../../.gitbook/assets/image (6).png" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="info" %}
**Ejemplo:** Supongamos que estás en India y quieres acceder al catálogo estadounidense de una plataforma de streaming (p. ej., Netflix). Al conectarte a un servidor proxy ubicado en EE. UU., tu solicitud a la plataforma de streaming parecerá provenir de EE. UU., lo que te permitirá acceder a su contenido como si estuvieras en EE. UU.
{% endhint %}

### **2. Optimización de velocidad y rendimiento (almacenamiento en caché)**

Los proxies pueden almacenar versiones en caché de contenido al que se accede con frecuencia, lo que permite tiempos de carga más rápidos y reduce el uso del ancho de banda.

<figure><img src="../../.gitbook/assets/image (7).png" alt="" width="375"><figcaption></figcaption></figure>

Cuando un usuario solicita contenido almacenado en caché, el servidor proxy sirve la copia almacenada en lugar de buscarla en el servidor de destino, lo que reduce la latencia.

Para evitar contenido obsoleto, utiliza un valor de tiempo de vida (TTL), que expira automáticamente los datos almacenados en caché después del tiempo configurado.

> **Ejemplo:** Una organización con cientos de empleados que acceden frecuentemente a los mismos recursos en línea puede implementar un proxy de caché. Este proxy almacena en caché los sitios web comunes en su base de datos, de modo que las solicitudes posteriores se atienden rápidamente desde el almacenamiento del proxy, ahorrando tiempo y ancho de banda.

## 2. ¿Qué es un proxy inverso?

Un **proxy inverso** es lo **opuesto** a un proxy directo. Regula el tráfico que entra a una red.

Se ubica frente a los servidores, intercepta las solicitudes de los clientes y las reenvía a los servidores back-end según reglas predefinidas.

<figure><img src="../../.gitbook/assets/image (8).png" alt="" width="375"><figcaption></figcaption></figure>

Piense en un proxy inverso como un **guardián** . En lugar de ocultar los clientes al servidor, oculta los servidores a los clientes.

Permitir el acceso directo a los servidores puede suponer **riesgos de seguridad** , exponiéndolos a amenazas como **piratas informáticos** y **ataques DDoS** .

Un proxy inverso mitiga estos riesgos al crear un único punto de entrada controlado que filtra y regula el tráfico entrante mientras mantiene ocultas las direcciones IP del servidor.

Con un proxy inverso, los clientes ya no interactúan directamente con los servidores. Solo se comunican con el proxy inverso.

Veamos un ejemplo simplificado de cómo un servidor proxy maneja una solicitud:

1. Un usuario escribe la URL de un sitio web en su navegador, lo que envía una solicitud al servidor.
2. El servidor proxy inverso recibe la solicitud antes de que llegue a los servidores back-end.
3. Según reglas predefinidas (como equilibrio de carga o disponibilidad del servidor), el proxy inverso reenvía la solicitud al servidor back-end apropiado.
4. El servidor backend procesa la solicitud y envía una respuesta al proxy inverso.
5. El proxy inverso retransmite la respuesta al cliente, y el cliente nunca interactúa directamente con los servidores back-end.

### Beneficios clave del proxy inverso

* **Seguridad mejorada:** al actuar como una capa protectora, un proxy inverso oculta los servidores back-end a los clientes, lo que reduce el riesgo de ataques dirigidos directamente a la infraestructura back-end.
* **Equilibrio de carga:** un proxy inverso puede distribuir las solicitudes entrantes de manera uniforme entre múltiples servidores back-end, mejorando la confiabilidad del sistema y evitando la sobrecarga del servidor.
* **Almacenamiento en caché de contenido estático:** los servidores proxy inversos pueden almacenar en caché recursos estáticos como imágenes, CSS y JavaScript, lo que reduce la necesidad de buscar estos archivos desde el backend repetidamente.
* **Terminación SSL:** Los servidores proxy inversos pueden gestionar el cifrado SSL, descargando este trabajo de los servidores back-end.
* **Firewall de aplicaciones web (WAF):** los servidores proxy inversos pueden inspeccionar las solicitudes entrantes y actuar como un firewall para detectar y bloquear el tráfico malicioso.

### Ejemplo real de un proxy inverso

**El proxy inverso de Cloudflare** es ampliamente utilizado por sitios web y aplicaciones globales para mejorar la velocidad, la seguridad y la confiabilidad.

Su **firewall de aplicaciones web (WAF)** y **protección DDoS** bloquea el tráfico malicioso antes de que llegue a los servidores del sitio, lo que lo protege contra ataques y mejora el tiempo de actividad.

El almacenamiento en caché de contenido global de Cloudflare almacena contenido estático y dinámico en más de 200 centros de datos en todo el mundo, almacenando los archivos de acceso frecuente (como imágenes, CSS y JavaScript) más cerca de los usuarios. Esto reduce significativamente los tiempos de carga y la latencia, ya que las solicitudes no siempre tienen que viajar al servidor de origen.

Una de las herramientas de proxy inverso más populares es **Nginx** .

A continuación se explica cómo configurar un proxy inverso básico usando Nginx en un servidor Linux.

**1. Instalar Nginx**

```
sudo apt update sudo apt install nginx
```

**2. Agregar configuración de proxy inverso**

```
server {
    listen 80;

    location / {
        proxy_pass http://backend_server_ip;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

**3. Probar y recargar Nginx**

```
sudo nginx -t sudo systemctl reload nginx
```

#### Equilibrio de carga entre varios servidores

Para un sitio web con mucho tráfico, distribuir las solicitudes entrantes entre múltiples servidores back-end es crucial.

Un proxy inverso puede implementar algoritmos de equilibrio de carga como round-robin, menor cantidad de conexiones o hash de IP, lo que garantiza una distribución óptima del tráfico.

```
upstream backend_servers {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Nginx usa **round robin** por defecto. Para cambiarlo, simplemente podemos añadir el algoritmo requerido (p. ej., **ip\_hash** ) en el `upstream`bloque.

Con esta configuración, Nginx equilibrará las solicitudes entre `backend1`, `backend2`, y `backend3`, garantizando que ningún servidor se vea sobrecargado.

## 3. Resumen

A continuación se muestra una tabla que resume los detalles clave:

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.54.48.png" alt=""><figcaption></figcaption></figure>
