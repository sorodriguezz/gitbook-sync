---
description: 'Guía completa del emisor de eventos NestJS: gestión eficaz de eventos'
---

# Gestionar eventos en NestJS

## ¿Qué es Event Emitter? <a href="#heading-what-is-event-emitter" id="heading-what-is-event-emitter"></a>

Un **Emisor de Eventos** es un patrón que permite que diferentes partes de tu aplicación se comuniquen asincrónicamente mediante la emisión y la escucha de eventos. Es especialmente útil para desacoplar módulos y gestionar tareas como el registro, las notificaciones y cualquier tipo de efecto secundario. Esto se logra separando la lógica que activa un evento de la lógica que lo gestiona.

NestJS proporciona una solución integrada y fácil de usar para el desarrollo impulsado por eventos mediante el `@nestjs/event-emitter`paquete.

## ¿Por qué utilizar emisores de eventos en NestJS? <a href="#heading-why-use-event-emitters-in-nestjs" id="heading-why-use-event-emitters-in-nestjs"></a>

Estas son algunas de las principales ventajas de utilizar el patrón Event Emitter en NestJS:

1. **Desacoplamiento** : Al emitir eventos, se permite que diferentes servicios o módulos se suscriban a ellos y respondan en consecuencia sin un acoplamiento estricto. Esto da como resultado un diseño más limpio y modular.
2. **Manejo de tareas asincrónicas** : si necesita ejecutar tareas que no están directamente relacionadas con el flujo principal de su aplicación, como enviar correos electrónicos o notificaciones, el uso de emisores de eventos puede manejar dichas tareas de forma asincrónica.
3. **Escalabilidad** : los emisores de eventos proporcionan una forma escalable de administrar efectos secundarios o tareas en segundo plano a medida que su aplicación crece, sin saturar la lógica comercial principal.

## Configuración del emisor de eventos en NestJS <a href="#heading-setting-up-event-emitter-in-nestjs" id="heading-setting-up-event-emitter-in-nestjs"></a>

Comencemos añadiendo el paquete Emisor de Eventos a nuestro proyecto NestJS. Necesitarás instalar la dependencia necesaria y configurarla en tu aplicación.

### Instalación <a href="#heading-installation" id="heading-installation"></a>

Para instalar el paquete Event Emitter en NestJS, ejecute el siguiente comando:

```bash
npm install @nestjs/event-emitter
```

### Configuración

Una vez instalado el paquete, debes importarlo `EventEmitterModule`en el módulo raíz ( `AppModule`) de tu aplicación NestJS.

```typescript
import { Module } from '@nestjs/common';
import { EventEmitterModule } from '@nestjs/event-emitter';

@Module({
  imports: [
    EventEmitterModule.forRoot(), // Register Event Emitter globally
  ],
})
export class AppModule {}

```

Aquí, el `EventEmitterModule.forRoot()`método inicializa el sistema emisor de eventos globalmente, haciéndolo disponible para toda la aplicación.

## Cómo usar el emisor de eventos en NestJS <a href="#heading-how-to-use-event-emitter-in-nestjs" id="heading-how-to-use-event-emitter-in-nestjs"></a>

### Paso 1: Definir un evento <a href="#heading-step-1-define-an-event" id="heading-step-1-define-an-event"></a>

Los eventos en NestJS pueden ser cualquier evento personalizado que se adapte a las necesidades de su aplicación. Normalmente, se crea una interfaz de TypeScript para definir la estructura del evento.

A continuación se muestra un ejemplo de un evento llamado `UserCreatedEvent`que se activa cuando se crea un nuevo usuario:

```typescript
export class UserCreatedEvent {
  constructor(
    public readonly userId: string,
    public readonly email: string,
  ) {}
}
```

Este evento contiene información sobre el usuario recién creado, como su ID y correo electrónico.

### Paso 2: Emisión de eventos <a href="#heading-step-2-emitting-events" id="heading-step-2-emitting-events"></a>

Para emitir un evento, inyecte el `EventEmitter2`servicio en su clase y llame al `emit()`método, pasando el nombre del evento y los datos del evento.

En el siguiente ejemplo, emitiremos `UserCreatedEvent`dentro de un servicio cuando se crea un usuario:

```typescript
import { Injectable } from '@nestjs/common';
import { EventEmitter2 } from '@nestjs/event-emitter';
import { UserCreatedEvent } from './events/user-created.event';

@Injectable()
export class UserService {
  constructor(private eventEmitter: EventEmitter2) {}

  createUser(userId: string, email: string) {
    // Business logic to create the user
    console.log(`User created: ${userId}, ${email}`);

    // Emit the event after the user is created
    const userCreatedEvent = new UserCreatedEvent(userId, email);
    this.eventEmitter.emit('user.created', userCreatedEvent);
  }
}
```

En el `createUser()`método, después de crear el usuario, emitimos el `user.created`evento junto con los datos del evento (es decir, `UserCreatedEvent`).

### Paso 3: Escuchar los eventos <a href="#heading-step-3-listening-to-events" id="heading-step-3-listening-to-events"></a>

Ahora que hemos emitido un evento, necesitamos una forma de escucharlo. En NestJS, podemos crear fácilmente detectores de eventos usando el `@OnEvent()`decorador proporcionado por el `@nestjs/event-emitter`paquete.

A continuación se explica cómo podemos escuchar el `user.created`evento y realizar algunas acciones, como enviar un correo electrónico de bienvenida:

```typescript
import { Injectable } from '@nestjs/common';
import { OnEvent } from '@nestjs/event-emitter';
import { UserCreatedEvent } from './events/user-created.event';

@Injectable()
export class NotificationService {
  @OnEvent('user.created')
  handleUserCreatedEvent(event: UserCreatedEvent) {
    // Handle the event, e.g., send a welcome email
    console.log(`Sending welcome email to ${event.email}`);
  }
}
```

En el ejemplo anterior, el `handleUserCreatedEvent()`método detecta el `user.created`evento. Cuando este se activa, registra un mensaje que indica que se está enviando un correo electrónico de bienvenida al usuario.

## Caso de uso real: Envío de notificaciones sobre acciones del usuario <a href="#heading-real-world-use-case-sending-notifications-on-user-actions" id="heading-real-world-use-case-sending-notifications-on-user-actions"></a>

Consideremos un caso práctico donde un sistema basado en eventos puede ser muy eficaz. Supongamos que tienes una red social y, cada vez que un usuario crea una publicación, quieres notificar a sus seguidores de forma asincrónica.

A continuación te mostramos cómo puedes configurar esto con los emisores de eventos NestJS:

1.  **Define el evento** : necesitarás un evento que contenga información sobre la publicación que se creó:

    ```typescript
    export class PostCreatedEvent {
     constructor(
       public readonly postId: string,
       public readonly authorId: string,
     ) {}
    }
    ```
2.  **Emitir el evento** : cuando un usuario crea una nueva publicación, emite el `PostCreatedEvent`:

    ```typescript
    import { Injectable } from '@nestjs/common';
    import { EventEmitter2 } from '@nestjs/event-emitter';
    import { PostCreatedEvent } from './events/post-created.event';

    @Injectable()
    export class PostService {
     constructor(private eventEmitter: EventEmitter2) {}

     createPost(postId: string, authorId: string) {
       // Business logic for creating the post

       // Emit the post.created event
       const event = new PostCreatedEvent(postId, authorId);
       this.eventEmitter.emit('post.created', event);
     }
    }
    ```
3.  **Escuchar el evento** : ahora, puedes tener un servicio de notificaciones que escucha el evento y notifica a los seguidores:

    ```typescript
    import { Injectable } from '@nestjs/common';
    import { OnEvent } from '@nestjs/event-emitter';
    import { PostCreatedEvent } from './events/post-created.event';

    @Injectable()
    export class NotificationService {
     @OnEvent('post.created')
     handlePostCreatedEvent(event: PostCreatedEvent) {
       // Business logic to notify followers of the author
       console.log(`Notifying followers of author ${event.authorId} about the new post ${event.postId}`);
     }
    }
    ```

En este caso de uso, el enfoque basado en eventos garantiza que la lógica de creación posterior permanezca limpia y enfocada en su tarea, mientras que la lógica de notificación es manejada independientemente por otro módulo.

## Manejo de errores con emisores de eventos <a href="#heading-error-handling-with-event-emitters" id="heading-error-handling-with-event-emitters"></a>

La gestión de errores es crucial al trabajar con emisores de eventos, ya que diferentes partes de la aplicación pueden fallar y es necesario garantizar que estos fallos se gestionen correctamente. NestJS permite detectar errores durante la emisión y el procesamiento de eventos.

A continuación te indicamos cómo puedes gestionar los errores de forma efectiva:

### 1. Envolviendo emisores de eventos en bloques Try-Catch <a href="#heading-1-wrapping-event-emitters-in-try-catch-blocks" id="heading-1-wrapping-event-emitters-in-try-catch-blocks"></a>

Al emitir un evento, envuelva la emisión en un `try-catch`bloque para manejar cualquier error inesperado:

```typescript
try {
  this.eventEmitter.emit('user.created', userCreatedEvent);
} catch (error) {
  console.error('Error emitting user.created event:', error);
}
```

Esto garantiza que si ocurre un error durante la emisión del evento, no bloqueará toda la aplicación.

### 2. Manejo de errores en los escuchas de eventos <a href="#heading-2-handling-errors-in-event-listeners" id="heading-2-handling-errors-in-event-listeners"></a>

De igual forma, conviene gestionar los posibles errores en el detector de eventos. Puedes hacerlo añadiendo un `try-catch`bloque dentro del método del detector:

```typescript
@OnEvent('user.created')
handleUserCreatedEvent(event: UserCreatedEvent) {
  try {
    // Process event
    console.log(`Processing user created event for ${event.email}`);
  } catch (error) {
    console.error('Error handling user.created event:', error);
  }
}
```

Esto hace que su aplicación sea más resistente a fallas durante el procesamiento de eventos.

## Funciones avanzadas <a href="#heading-advanced-features" id="heading-advanced-features"></a>

El emisor de eventos de NestJS ofrece funciones avanzadas para mejorar la arquitectura basada en eventos. Analicémoslas brevemente:

### 1. Priorización de eventos <a href="#heading-1-event-prioritization" id="heading-1-event-prioritization"></a>

De forma predeterminada, los oyentes de eventos se invocan en el orden en que se registran. Sin embargo, NestJS permite priorizar ciertos oyentes sobre otros. Esto puede ser útil cuando se desea que un oyente específico se ejecute antes que otros.

Para priorizar un detector de eventos, pase un segundo argumento al `@OnEvent()`decorador:

```typescript
@OnEvent('user.created', { priority: 1 }) // Higher priority
handleCriticalEvent(event: UserCreatedEvent) {
  console.log('Critical event listener executed first');
}
```

Los oyentes con un valor de prioridad más alto se invocarán primero.

### 2. Escuchadores de eventos comodín <a href="#heading-2-wildcard-event-listeners" id="heading-2-wildcard-event-listeners"></a>

NestJS también admite detectores de eventos comodín, lo que permite detectar múltiples eventos con un único controlador. Esto resulta útil cuando se desea reaccionar a un grupo de eventos relacionados.

He aquí un ejemplo:

```typescript
@OnEvent('user.*')
handleUserEvents(event: any) {
  console.log('A user-related event occurred:', event);
}
```

En este caso, el `handleUserEvents`método responderá a cualquier evento que comience con `user.`, como `user.created`, `user.updated`, etc.

### 3. Transformación de la carga útil del evento <a href="#heading-3-event-payload-transformation" id="heading-3-event-payload-transformation"></a>

También puedes transformar las cargas útiles de los eventos antes de procesarlas en los oyentes. Esto puede ser útil si deseas modificar los datos transmitidos a un oyente sin afectar la lógica original de emisión de eventos.

Simplemente transforme los datos del evento dentro de su oyente:

```typescript
@OnEvent('user.created')
handleUserCreatedEvent(event: UserCreatedEvent) {
  const transformedData = {
    ...event,
    timestamp: new Date().toISOString(),
  };

  console.log('User created with additional timestamp:', transformedData);
}
```

## Mejores prácticas para el uso de emisores de eventos <a href="#heading-best-practices-for-using-event-emitters" id="heading-best-practices-for-using-event-emitters"></a>

Para aprovechar al máximo el patrón Event Emitter en NestJS, es importante seguir las mejores prácticas:

1. **Desacoplar módulos** : Utilice emisores de eventos para desacoplar la lógica de negocio de procesos secundarios como el registro, el envío de notificaciones o la auditoría. Esta separación hace que su código sea más limpio y fácil de mantener.
2. **Evite la emisión excesiva de eventos** : Si bien los eventos son útiles, emitir demasiados puede saturar el código y dificultar su seguimiento. Utilice emisores de eventos para procesos críticos y evite emisiones innecesarias.
3. **Gestionar errores con elegancia** : Envuelva siempre la emisión de eventos y la lógica del receptor en `try-catch`bloques para gestionar los errores con elegancia. Esto evita que un solo fallo bloquee toda la aplicación.
4. **Usar priorización de eventos** : si ciertos oyentes son más críticos que otros, aproveche esta `priority`opción `@OnEvent()`para garantizar que se ejecuten primero.

Las arquitecturas basadas en eventos ofrecen una forma eficaz de crear aplicaciones escalables y fáciles de mantener mediante la desvinculación de diferentes componentes. El emisor de eventos de NestJS proporciona una forma sencilla pero flexible de implementar estos patrones. Al emitir y escuchar eventos, se pueden gestionar efectos secundarios como notificaciones, registros y tareas en segundo plano de forma asíncrona, manteniendo la lógica principal de la aplicación limpia y modular.

Ya sea que esté manejando acciones de usuario, notificaciones o flujos de trabajo complejos, el emisor de eventos en NestJS puede mejorar la arquitectura de su aplicación, haciéndola más fácil de mantener y escalable.
