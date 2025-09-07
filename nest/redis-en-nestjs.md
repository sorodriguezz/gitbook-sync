# Redis en NestJS

## ¿Por qué utilizar Redis? <a href="#heading-why-use-redis" id="heading-why-use-redis"></a>

Redis ofrece varios beneficios para el almacenamiento en caché:

* **Velocidad:** dado que Redis es un almacén en memoria, es increíblemente rápido.
* **Persistencia:** admite la persistencia de datos, lo que le permite guardar instantáneas de sus datos en el disco.
* **Estructuras de datos:** Redis admite varias estructuras de datos, como cadenas, hashes, listas, conjuntos y más.
* **Escalabilidad:** Redis se puede escalar fácilmente para manejar grandes cantidades de datos y altas tasas de solicitud.

## Configuración de Redis <a href="#heading-setting-up-redis" id="heading-setting-up-redis"></a>

Antes de profundizar en el código, asegúrate de tener Redis instalado y en ejecución. Puedes descargar Redis desde su sitio web oficial o usar un contenedor Docker para una configuración rápida:

```bash
docker run --name redis -p 6379:6379 -d redis
```

## Configuración de NestJS <a href="#heading-setting-up-nestjs" id="heading-setting-up-nestjs"></a>

Si aún no tienes un proyecto [NestJS](https://blog.bytescrum.com/introduction-to-nestjs-a-beginners-guide) , creemos uno nuevo:

```bash
nest new redis-cache-example
cd redis-cache-example
```

## Instalación de dependencias <a href="#heading-installing-dependencies" id="heading-installing-dependencies"></a>

A continuación, necesitaremos instalar algunos paquetes para Redis y almacenamiento en caché:

```bash
npm install cache-manager cache-manager-redis-store redis
```

## Integración de Redis con NestJS <a href="#heading-integrating-redis-with-nestjs" id="heading-integrating-redis-with-nestjs"></a>

### 1. Configurar el módulo de caché <a href="#heading-1-configure-cache-module" id="heading-1-configure-cache-module"></a>

Primero, necesitamos configurar CacheModule en nuestro `app.module.ts`archivo para usar Redis:

```typescript
import { Module, CacheModule } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import * as redisStore from 'cache-manager-redis-store';

@Module({
  imports: [
    CacheModule.register({
      store: redisStore,
      host: 'localhost', // Redis server host
      port: 6379,        // Redis server port
      ttl: 600,          // Time to live (seconds)
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

Aquí, configuramos CacheModule para usar Redis como nuestro almacén de caché, especificando el host y el puerto donde se ejecuta Redis. También establecemos un tiempo de vida (TTL) de 600 segundos para los datos almacenados en caché.

### 2. Uso de caché en servicios <a href="#heading-2-using-cache-in-services" id="heading-2-using-cache-in-services"></a>

Ahora, usemos la caché en nuestro servicio. Inyectaremos el administrador de caché y lo usaremos para almacenar datos en caché. Aquí hay un ejemplo en `app.service.ts`:

```typescript
import { Injectable, Inject } from '@nestjs/common';
import { CACHE_MANAGER } from '@nestjs/cache-manager';
import { Cache } from 'cache-manager';

@Injectable()
export class AppService {
  constructor(@Inject(CACHE_MANAGER) private cacheManager: Cache) {}

  async getHello(): Promise<string> {
    const cacheKey = 'hello_cache';
    let cachedData = await this.cacheManager.get<string>(cacheKey);

    if (!cachedData) {
      cachedData = 'Hello World!';
      await this.cacheManager.set(cacheKey, cachedData, { ttl: 600 }); // Cache for 10 minutes
    }

    return cachedData;
  }
}
```

En este servicio, primero intentamos obtener los datos almacenados en caché usando `cacheManager.get`. Si no hay datos almacenados en caché, los configuramos usando `cacheManager.set`.

### 3. Manejo de caché en controladores <a href="#heading-3-handling-cache-in-controllers" id="heading-3-handling-cache-in-controllers"></a>

A continuación, usamos este método de servicio en nuestro controlador. Así es como `app.controller.ts`se ve:

```typescript
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  async getHello(): Promise<string> {
    return this.appService.getHello();
  }
}
```

Este controlador simplemente llama al `getHello`método de nuestro servicio para devolver los datos almacenados en caché o recientemente configurados.

## Técnicas avanzadas de almacenamiento en caché <a href="#heading-advanced-caching-techniques" id="heading-advanced-caching-techniques"></a>

### Resultados del método de almacenamiento en caché <a href="#heading-caching-method-results" id="heading-caching-method-results"></a>

NestJS proporciona un `@Cacheable`decorador para almacenar en caché los resultados de los métodos fácilmente. Puedes usarlo así:

```typescript
import { Injectable } from '@nestjs/common';
import { Cacheable } from 'nestjs-cacheable';

@Injectable()
export class AppService {
  @Cacheable({ ttl: 600 }) // Cache for 10 minutes
  async getHello(): Promise<string> {
    return 'Hello World!';
  }
}
```

Con el `@Cacheable`decorador, el resultado del método se almacena en caché automáticamente, lo que le ahorra el manejo manual de la lógica de caché.

### Invalidación de caché <a href="#heading-invalidating-cache" id="heading-invalidating-cache"></a>

A veces, es posible que necesite invalidar la caché cuando los datos subyacentes cambien. Úselo `cacheManager.del`para eliminar los datos almacenados en caché. A continuación, se muestra un ejemplo:

```typescript
import { Injectable, Inject, CACHE_MANAGER } from '@nestjs/common';
import { Cache } from 'cache-manager';

@Injectable()
export class AppService {
  constructor(@Inject(CACHE_MANAGER) private cacheManager: Cache) {}

  async updateHello(newData: string): Promise<void> {
    const cacheKey = 'hello_cache';
    await this.cacheManager.del(cacheKey);
    // Update the data in your database or other persistent storage
  }
}
```

En este ejemplo, cuando `updateHello`se llama, se invalida el caché `hello_cache`, lo que garantiza que las solicitudes posteriores obtengan los datos más recientes.

<details>

<summary>Conclusión</summary>



</details>

¡Feliz codificación!

41

### Suscríbete a nuestro boletín

Lee artículos de **ByteScrum Technologies** directamente en tu bandeja de entrada. Suscríbete al boletín y no te lo pierdas.

Suscribir[nestjs](https://blog.bytescrum.com/tag/nestjs?source=tags_bottom_blogs)[Redis](https://blog.bytescrum.com/tag/redis?source=tags_bottom_blogs)[almacenamiento en caché](https://blog.bytescrum.com/tag/caching?source=tags_bottom_blogs)[Desarrollo web](https://blog.bytescrum.com/tag/web-development?source=tags_bottom_blogs)[Optimización del rendimiento](https://blog.bytescrum.com/tag/performance-optimization?source=tags_bottom_blogs)

#### Escrito por

[![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%27256%27%20height=%27256%27/%3e)![Tecnologías ByteScrum](https://cdn.hashnode.com/res/hashnode/image/upload/v1690559610804/4_zvDaFzQ.png?w=256\&h=256\&fit=crop\&crop=faces\&auto=compress,format\&format=webp)](https://hashnode.com/@bytescrum)

### [Tecnologías ByteScrum](https://hashnode.com/@bytescrum)

Nuestra empresa está formada por profesionales experimentados, cada uno experto en su campo. La satisfacción del cliente es nuestra máxima prioridad, superando sus necesidades. Garantizamos precios competitivos y calidad en desarrollo web y móvil sin concesiones.

Seguir

#### Publicado el

[![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%27256%27%20height=%27256%27/%3e)![Tecnologías ByteScrum](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://blog.bytescrum.com/)

### [Tecnologías ByteScrum](https://blog.bytescrum.com/)

La perspicacia comercial de ByteScrum llena las brechas de rendimiento, brindando soluciones personalizadas en desarrollo web y móvil, transformando tecnologías emergentes, cumpliendo plazos y garantizando la calidad.

Seguir Comparte esto

#### Más artículos

[![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%2770%27%20height=%2770%27/%3e)![Foto de Ajit Gupta](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://hashnode.com/@dev32)[Ajit Gupta](https://hashnode.com/@dev32)[![Las mejores herramientas de IA que realmente importan: una guía completa](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://blog.bytescrum.com/top-ai-tools-that-actually-matter-a-comprehensive-guide?source=more_articles_bottom_blogs)

## [Las mejores herramientas de IA que realmente importan: una guía completa](https://blog.bytescrum.com/top-ai-tools-that-actually-matter-a-comprehensive-guide?source=more_articles_bottom_blogs)

[El mundo de la IA avanza a un ritmo vertiginoso, con nuevas herramientas, actualizaciones y soluciones que surgen cada …](https://blog.bytescrum.com/top-ai-tools-that-actually-matter-a-comprehensive-guide?source=more_articles_bottom_blogs)

[![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%2770%27%20height=%2770%27/%3e)![Foto de ByteScrum Technologies](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://hashnode.com/@bytescrum)[Tecnologías ByteScrum](https://hashnode.com/@bytescrum)[![Las 10 mejores herramientas de IA que puedes usar gratis (edición 2025)](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://blog.bytescrum.com/top-10-ai-tools-you-can-use-for-free-2025-edition?source=more_articles_bottom_blogs)

## [Las 10 mejores herramientas de IA que puedes usar gratis (edición 2025)](https://blog.bytescrum.com/top-10-ai-tools-you-can-use-for-free-2025-edition?source=more_articles_bottom_blogs)

[En un mundo cada vez más impulsado por la inteligencia artificial, nunca ha habido un mejor momento para explo …](https://blog.bytescrum.com/top-10-ai-tools-you-can-use-for-free-2025-edition?source=more_articles_bottom_blogs)

[![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%2770%27%20height=%2770%27/%3e)![Foto de ByteScrum Technologies](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://hashnode.com/@bytescrum)[Tecnologías ByteScrum](https://hashnode.com/@bytescrum)[![Las 10 mejores pasarelas de pago para aplicaciones Next.js (2025)](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://blog.bytescrum.com/top-10-payment-gateways-for-nextjs-applications-2025?source=more_articles_bottom_blogs)

## [Las 10 mejores pasarelas de pago para aplicaciones Next.js (2025)](https://blog.bytescrum.com/top-10-payment-gateways-for-nextjs-applications-2025?source=more_articles_bottom_blogs)

[En 2025, el espacio de pagos en línea es más competitivo, poderoso y amigable para los desarrolladores que nunca. …](https://blog.bytescrum.com/top-10-payment-gateways-for-nextjs-applications-2025?source=more_articles_bottom_blogs)

[![Tecnologías ByteScrum](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://blog.bytescrum.com/)

© 2025 ByteScrum Technologies

[Archivo](https://blog.bytescrum.com/archive)·[Política de privacidad](https://hashnode.com/privacy?source=blog-footer)·[Términos](https://hashnode.com/terms?source=blog-footer)\










