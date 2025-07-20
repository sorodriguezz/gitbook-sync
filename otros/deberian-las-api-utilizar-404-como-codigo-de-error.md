---
description: El humilde error 404, tan familiar en la web, es un problema para una API
---

# ¿Deberían las API utilizar 404 como código de error?

Desde que conocí un navegador web, me familiaricé con el error **404**. Es lo primero que aprendes, el primer código de error que encuentras, porque prácticamente todo el mundo comete errores ortográficos de vez en cuando.

La definición del error 404 se remonta a los primeros estándares web, incluido el **RFC 1945**, que forma parte del proceso de normalización del IETF. Parece tener autoridad.

Si lees el breve resumen, aprenderás que el código de error tiene una definición de dos palabras: **"No encontrado"**.

Entonces, cuando nuestros desarrolladores de API escriben un endpoint que busca o recupera objetos, parece natural que devolvamos un error **404** si no se encuentra el objeto. ¿Es esto positivo?

### Temporal o permanente

La definición del código de error HTTP 404 incluye esta interesante frase que debería ayudarnos a discutir el tema:

> **No se indica si la condición es temporal o permanente.**

#### ¿Qué significa esto?

¿Cuál es la diferencia entre una **ausencia temporal** o **permanente** de datos?\
Hipotéticamente, podría imaginar un blog diario con una URL para cada día, y esta URL devolvería un error 404 hasta que llegue ese día y el blog esté disponible.

En este mundo, podríamos imaginar que una solicitud HTTP [https://example.com/daily/blog/2022–01–01](https://example.com/daily/blog/2022%E2%80%9301%E2%80%9301) recibiría un código de error **404** hasta el 1 de enero. Un desarrollador podría solicitar esa URL a diario y no sorprenderse si el resultado cambiara repentinamente de **404** a **200**.

### Bases de datos y datos faltantes

Esta teoría de datos faltantes debe mucho a la definición original de **NULL** en SQL, donde **EF Codd** lo utilizó como un marcador especial para indicar que los datos podrían estar aquí pero faltan.

Sin embargo, en el mundo de las bases de datos, **NULL** se considera un dato válido.\
No es un código de error. Se puede ejecutar una solicitud perfectamente válida y obtener NULL como resultado; pero una solicitud inválida **nunca** puede devolver NULL; una solicitud inválida siempre genera un error diferente y único.

```sql
-- Esta llamada SELECT puede devolver datos o NULL 
SELECT ending_date FROM empleado WHERE nombre = 'Bob Smith';

-- Esta llamada SELECT generará un error porque es incorrecta 
SELECT empleado FROM WHERE nombre = 'termination_date';
```

¿Qué pasaría si nuestra base de datos SQL devolviera NULL para consultas no válidas?

Los desarrolladores no sabrían si hay un error tipográfico en su sentencia SQL o si el dato consultado realmente falta.

Cuando diseñamos API web, tenemos un problema similar:

```http
-- Esta llamada API tiene el formato correcto 
GET /api/v1/companies?id=12345

-- Esta llamada API contiene un error tipográfico 
GET /api/v1/cmopanies?id=12345
```

En este escenario corremos un riesgo:

Si nuestra API devuelve exactamente el mismo código de error 404 para ambos casos de uso, es posible que un desarrollador no se dé cuenta de que ha cometido un error tipográfico en la llamada a la API.

Esto significa que los diseñadores de API necesitan códigos de error que distingan claramente entre:

* “Solicitaste un registro inexistente”
* “Llamaste a una API inexistente”

Una forma de lograrlo es especificar que el código 404 solo se use para API inexistentes y usar códigos de error diferentes para datos faltantes.

### ¿Cuántos códigos de error HTTP debemos utilizar?

La IANA mantiene un registro de 65 códigos de error HTTP definidos, algunos de los cuales son relativamente desconocidos.

Estos códigos pueden ser significativos para los desarrolladores de navegadores web, pero escribir una declaración case extensa para gestionar cada código específicamente requiere mucho tiempo.

### ¿Podemos realmente esperar que cada desarrollador gestione todos los tipos de errores?

En la práctica, los desarrolladores que trabajan contra una API utilizan clases de código de error. Según la IANA, las clases se definen como:

* 1xx: Informativo — Solicitud recibida, proceso en curso
* 2xx: Éxito — La acción se recibió, entendió y aceptó correctamente
* 3xx: Redirección — Se deben tomar medidas adicionales para completar la solicitud
* 4xx: Error del cliente — La solicitud contiene una sintaxis incorrecta o no se puede cumplir
* 5xx: Error del servidor — El servidor no pudo cumplir con una solicitud aparentemente válida

Para el diseño de API, generalmente podemos ignorar los errores 100 y 300, que son principalmente útiles para navegadores web.

Debemos manejar:

* 200 a 299 → “Éxito”
* 400 a 499 → “Solicitud incorrecta”
* 500 a 599 → “Servidor roto”

Nuestras API nunca deberían devolver un código de error 500, excepto cuando hayamos alertado a nuestros desarrolladores de un error en nuestro código.

Sin embargo, un error 400 puede significar dos cosas:

* El desarrollador tiene un error en su solicitud
* El usuario realizó una solicitud incorrecta con la aplicación del desarrollador

### Diseño de API y códigos de error

Para distinguir entre estos dos casos de uso, recomiendo que los diseñadores de API incluyan un método ping. Los métodos ping siempre devuelven 200 OK y un breve mensaje de estado, independientemente de cómo se llamen o qué encabezados se envíen. Si un desarrollador llama a ping y recibe un valor distinto de 200 OK, sabe que algo impide que la aplicación acceda a la API.

También recomiendo que los desarrolladores de API de consulta siempre devuelvan 200 OK, incluso si no se encuentran filas que coincidan con la consulta. Un desarrollador que trabaja con esta API no necesita mostrar un código de error si no encuentra registros; simplemente muestra cero registros, porque ese es el resultado correcto y no ocurrió ningún error.

### ¿Cómo implementarlo en la práctica?

Esto significa que el desarrollador que crea una aplicación puede escribir código de la siguiente manera:

1. Al iniciar, llame a Ping.
   * Si Ping devuelve un valor distinto de 200 OK, muestre un mensaje al usuario y pídale que revise su red.
   * Podría ser un problema con el router, DNS o un fallo del servidor proxy.
2. Llame a una API y verifique su código de estado:
   * Si está entre 200 y 299, los datos recibidos en el cuerpo de la respuesta se consideran válidos.
     * Los resultados pueden incluir valores nulos o ser una matriz vacía.
     * En cualquier caso, significa que todo funcionó correctamente.
   * Si está entre 400 y 499, existe un problema del lado del cliente y es responsabilidad del desarrollador que llama a la API solucionarlo.
     * Ejemplo: credenciales caducadas, consulta mal formada, URL incorrecta.
   * Si está entre 500 y 599, existe un error y el desarrollador de la API debe corregirlo.
     * Cualquier servicio que reporte un error 500 generalmente debe actualizar su página de estado si la interrupción no es temporal.

### Conclusión

El propósito de esta discusión es animarte a ser cuidadoso con el uso de códigos de estado.

* No utilices muchos códigos de estado solo porque existen.
* Intenta encontrar maneras de que tu API sea fácil de entender e implementar.
