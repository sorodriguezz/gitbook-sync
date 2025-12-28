---
description: 'Arquitectura de API: Mejores prácticas de diseño para API REST'
---

# API Design

En general, los servicios web existen desde la existencia del protocolo HTTP. Sin embargo, desde la llegada de la computación en la nube, se han convertido en el método omnipresente para facilitar la interacción del cliente con servicios y datos.

Como desarrollador, he tenido la suerte de trabajar con algunos servicios SOAP que aún existen en el trabajo. Sin embargo, he experimentado principalmente con **REST** , un estilo arquitectónico basado en recursos para desarrollar API y servicios web.

Durante gran parte de mi carrera, **he estado involucrado en proyectos de construcción, diseño y uso de API.**

La mayoría de las API que he visto “afirman” ser “ **RESTful** ”,  _es decir, compatibles con los principios y restricciones de la arquitectura REST_ .

Sin embargo, hay unos pocos con los que he trabajado que **le dan a REST una muy, muy mala reputación** .

Uso incorrecto de códigos de estado HTTP, respuestas en texto plano, esquemas inconsistentes, verbos insertados en los puntos finales... **Siento que lo he visto todo** (o al menos, una buena parte).

## 1. Aprenda los conceptos básicos de HTTP <a href="#id-4dbf" id="id-4dbf"></a>

Si aspiras a crear una **API REST** bien diseñada , debes conocer los fundamentos del **protocolo HTTP** . Creo firmemente que **esto te ayudará a tomar buenas decisiones de diseño** .

Considero que la [Descripción general de HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview) en los documentos de Mozilla Developer Network es una referencia bastante completa para este tema.

Aunque, en lo que respecta **al diseño de API REST** , aquí hay un TLDR de **HTTP aplicado al diseño RESTful** :

* HTTP tiene **verbos** (acciones o métodos): GET, POST, PUT, PATCH y DELETE son los más comunes.
* REST está **orientado a recursos** y un recurso está representado por una **URI** :`/library/`
* Un **punto final** es la combinación de un verbo y un URI, ejemplo:`GET: /books/`
* Un punto final puede interpretarse como una _acción realizada en un recurso **.**_ Por ejemplo, `POST: /books/`podría significar "Crear un nuevo libro".
* En un nivel alto, **los verbos se asignan a operaciones CRUD** : `GET`significa `Read`, `POST`significa `Create`, significa `PUT`y significa .`PATCHUpdateDELETEDelete`
* El estado de una respuesta se especifica mediante su**código de estado**:`1xx`para**información**,`2xx`para**éxito**,`3xx`para**redirección**,`4xx`para**errores del cliente**y`5xx`para**errores del servidor**

Por supuesto, puedes utilizar otras cosas que ofrece el protocolo HTTP para el diseño de API REST, pero estas son las cosas básicas que creo que debes tener en cuenta.

## 2. No devuelva texto sin formato <a href="#e0c5" id="e0c5"></a>

Aunque esto no está impuesto ni exigido por ningún estilo arquitectónico REST, la mayoría de las API REST por convención utilizan JSON como formato de datos.

Sin embargo, no basta con devolver un cuerpo de respuesta que contenga una cadena con formato JSON. Debe **especificar el `Content-Type`encabezado.** Debe tener el valor **`application/json`**.

Esto es especialmente importante cuando se trata con **clientes de aplicaciones** o programáticos (por ejemplo, otro servicio/API que interactúa con su API a través de la biblioteca en Python); algunos de ellos dependen de este encabezado para decodificar con precisión la respuesta.`requests`

**Consejo** : Puedes verificar una respuesta `Content-Type`fácilmente con Firefox. Tiene una visualización atractiva integrada para las respuestas con `Content-Type: application/json`.&#x20;

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.45.45.png" alt=""><figcaption></figcaption></figure>

_En Firefox, “Content-Type: text/plain” parece… simple._

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.46.11.png" alt=""><figcaption></figcaption></figure>

_“Content-Type: application/json” ¡Genial, qué bonito y funcional es esto!_

## 3. No utilice verbos en las URI <a href="#id-49eb" id="id-49eb"></a>

A esta altura, si ya has comprendido los conceptos básicos, comenzarás a darte cuenta de que **no es RESTful** poner verbos en la URI.

Esto se debe a que los **verbos HTTP deben ser suficientes para describir con precisión la acción que se realiza en el recurso** .

**Ejemplo:** Supongamos que proporciona un punto final para generar y recuperar la portada de un libro. Anotaré  `:param`un marcador de posición para un parámetro URI (como un ID o un slug). Su primera idea podría ser crear un punto final similar a este:

```
OBTENER: /libros/:slug/generateBookCover/
```

Pero, el `GET`método es sintácticamente suficiente para indicar que estamos recuperando ("OBTENIENDO") la portada de un libro. Así que, simplemente usemos:

```
OBTENER: /libros/:slug/portadadellibro/
```

Del mismo modo, para un punto final que crea un nuevo libro:

```
# No hagas esto 
POST: /books/createNewBook/# Haz este 
POST: /libros/
```

Los verbos HTTP ( _verbalizan_ ) ¡todas las cosas!

## 4. Utilice sustantivos en plural para los recursos. <a href="#e2f3" id="e2f3"></a>

Puede resultar difícil determinar si se debe utilizar o no la forma plural o singular para los sustantivos de recursos.

¿Deberíamos utilizar `/book/:id/`(singular) o `/books/:id/`(plural)?

**Mi consejo personal es&#x20;**_**utilizar la forma plural**_**.**

¿Por qué? Porque se adapta perfectamente _a todo tipo de terminales._

Ya veo que `GET /book/2/`está bien. ¿Pero qué pasa con `GET /book/`...? ¿Vamos a conseguir el único libro de la biblioteca, un par de ellos o _todos_ ?

Para evitar este tipo de ambigüedad, **seamos coherentes** (💡¡Consejos profesionales de software!) y usemos el plural en todas partes:

```
OBTENER: /libros/2/ 
PUBLICAR: /libros/ 
...
```

## 5. Devuelve los detalles del error en el cuerpo de la respuesta.

Cuando un servidor API gestiona un error, es conveniente (\*y recomendable\*) devolver **los detalles del error** dentro del cuerpo JSON para **facilitar la depuración** . ¡Es aún mejor si se incluyen los campos afectados por el error!

```json
{ 
    "error": "Carga útil no válida.", 
    "detail": { 
        "name": "Este campo es obligatorio." 
    } 
}
```

## 6. Preste especial atención a los códigos de estado HTTP <a href="#id-9d71" id="id-9d71"></a>

Creo que esto es _muy importante_ . Si hay algo que debes recordar de este artículo, probablemente sea esto.

> _Lo peor que podría hacer tu API es_ devolver una respuesta de error _con un `200 OK`código de estado._

Es simplemente una semántica incorrecta. En su lugar, **devuelva un código de estado HTTP significativo** que describa _con precisión_ el tipo de error.

Aun así, probablemente te estés preguntando: _"Pero estoy enviando detalles del error en el cuerpo de la respuesta como recomendaste, entonces, ¿qué hay de malo en eso?"_

Déjame contarte una historia.

Una vez tuve que integrar una API que retornaba `200 OK`para cada respuesta e indicaba si la solicitud había tenido éxito a través de un `status`campo:

```json
{ 
    "estado": "éxito", 
    "datos": {} 
}
```

A pesar de que el código de estado HTTP estaba devolviendo `200 OK`, no podía estar _absolutamente_ seguro de que no hubiera fallado en procesar mi solicitud.

De hecho, la API podría devolver respuestas como esta:

```
HTTP/1.1 200 OK 
Tipo de contenido: texto/html
{
    "status": "failure", 
    "data": { 
        "error": "Se esperaban al menos tres elementos en la lista." 
    } 
}
```

(Sí, también devolvió contenido HTML. ¿Por qué no?)

Como resultado, tuve que verificar el código de estado **Y**`status` el campo ad-hoc para estar _absolutamente seguro_ de que todo estaba bien antes de leer el `data`.

¡¡¡MUY MOLESTO!!!

**Este tipo de diseño es totalmente inaceptable,** ya que **rompe la confianza entre la API y sus consumidores** . Se llega a temer que la API pueda estar mintiendo.

Todo esto es _tremendamente_ anti-REST. ¿Qué deberías hacer en su lugar?

**Utilice el código de estado HTTP y utilice el cuerpo de la respuesta únicamente para proporcionar detalles del error** .

```
HTTP/1.1 400 Solicitud incorrecta 
Tipo de contenido: aplicación/json
{ 
    "error": "Se esperaban al menos tres elementos en la lista." 
}
```

## 7. Debes usar códigos de estado HTTP de forma consistente <a href="#id-2fb6" id="id-2fb6"></a>

Una vez que domines los códigos de estado HTTP, debes intentar usarlos **de manera consistente** .

Por ejemplo, si elige que un `POST`punto final devuelva un `201 Created`lugar, utilice ese _mismo código de estado HTTP_ para cada `POST`punto final.

¿Por qué? Porque los consumidores no deberían preocuparse por _qué método, en qué punto final, devolverá qué código de estado en qué circunstancias_ .

Así que, sé predecible ( **coherente).** Si tienes que romper con las convenciones, **documentalo** en algún lugar con carteles grandes.

Normalmente sigo el siguiente patrón:

```
OBTENER: 200 OK 
PONER: 200 OK 
PUBLICAR: 201 Creado 
PARCHE: 200 OK 
ELIMINAR: 204 Sin contenido
```

## 8. No anide recursos <a href="#id-52f8" id="id-52f8"></a>

Probablemente ya te habrás dado cuenta de que las API REST gestionan recursos. Recuperar una lista o una sola instancia de un recurso es sencillo. Pero, ¿qué ocurre cuando se gestionan **recursos relacionados** ?

Por ejemplo, supongamos que queremos recuperar la lista de libros de un autor en particular (el que tiene ) `name=Cagan`. Básicamente, hay dos opciones.

La primera opción sería **anidar** el `books`recurso debajo del `authors`recurso, ejemplo:

```
OBTENER: /autores/Cagan/libros/
```

Algunos arquitectos recomiendan esta convención porque efectivamente representa la **relación de uno a muchos** entre un autor y sus libros.

Pero ya **no está claro** qué tipo de recurso solicita. ¿Son autores? ¿Son libros?

Además, [lo plano es mejor que lo anidado](https://www.python.org/dev/peps/pep-0020/#id3) , así que debe haber una forma mejor… ¡Y la hay! :)

Mi recomendación personal es **utilizar parámetros de cadena de consulta** para filtrar el `books`recurso directamente:

```
OBTENER: /books?author=Cagan
```

Y esto claramente significa: «Conseguir todos los libros del autor Cagan», ¿verdad?

## 9. Maneje las barras diagonales finales con elegancia

`/`No se debate si las URI deben o no tener barras diagonales finales . Simplemente se debe optar por una opción u otra (es decir, con o sin barra diagonal final), atenerse a ella y **redirigir correctamente a los clientes si usan la convención incorrecta** .

(Lo admito, yo mismo he sido culpable de esto más de una vez.)

¡Hora de contar historias! 📙 Un día, mientras integraba una API REST en uno de mis proyectos, recibía `HTTP 500 Internal Error`en _cada llamada \[error]_ . El punto final que usaba se veía así:

```
PUBLICACIÓN: /cubos
```

Estaba furioso y no podía entender qué diablos estaba haciendo mal.

Al final, resultó que **el servidor fallaba porque me faltaba una barra diagonal final.** Así que empecé a usar:

```
PUBLICACIÓN: /cubos/
```

Y todo salió bien después.

La API no se solucionó, pero esperamos _que puedas_ evitar este tipo de problemas para tus consumidores.

**Consejo:** La mayoría de los frameworks web (Angular, React, etc.) ofrecen una opción para redirigir correctamente a la versión final o no final de la URL. Encuentra esa opción y actívala lo antes posible.

## 10. Utilice la cadena de consulta para filtrar y paginar. <a href="#id-8023" id="id-8023"></a>

La mayoría de las veces, un punto final simple no es suficiente para satisfacer varios casos de negocios complejos.

Es posible que sus consumidores quieran recuperar artículos que cumplan una condición específica o recuperarlos en pequeñas cantidades a la vez para mejorar el rendimiento.

Precisamente para esto se crearon **el filtrado** y **la paginación .**

Con **el filtrado** , los consumidores pueden especificar parámetros (o propiedades) que deben tener los elementos devueltos.

**La paginación** permite a los consumidores recuperar _fracciones del conjunto de datos_ . El tipo más simple de paginación es **la paginación por número de página** , que se determina mediante a `page`y a `page_size`.

Ahora, la pregunta es: ¿Cómo **se incorporan dichas características en una API REST?**

Mi respuesta es: **utilice la cadena de consulta** .

Diría que es bastante obvio por qué deberías usar la cadena de consulta para la paginación. Se vería así:

```
OBTENER: /books?page=1&page_size=10
```

Sin embargo, puede que no sea tan obvio para el filtrado. Inicialmente, podrías considerar hacer algo como esto para recuperar una lista de solo libros _publicados :_

```
OBTENER: /libros/publicados/
```

Problema de diseño: **`published`¡No es** _un recurso!_ Es una _característica_ de los datos que se recuperan. Este tipo de información debería ir en la **cadena de consulta** .

Así, al final, un usuario podría recuperar “la segunda página de libros publicados que contienen 20 artículos” de la siguiente manera:

```
OBTENER: /libros?publicados=verdadero&página=2&tamaño_de_página=10
```

Hermosamente explícito, ¿no?

## 11. Conozca la diferencia entre `401 Unauthorized`y`403 Forbidden` <a href="#a0c4" id="a0c4"></a>

Si tuviera veinticinco centavos por cada vez que he visto a desarrolladores e incluso a algunos arquitectos experimentados arruinar esto...

Al manejar errores de seguridad en una API REST, es extremadamente fácil confundirse sobre si el error se relaciona con **la autenticación** o **la autorización** (también conocidos como _permisos_ ); esto me solía pasar todo el tiempo.

Esta es mi hoja de trucos para saber a qué me enfrento, dependiendo de la situación:

* ¿El consumidor no proporcionó sus credenciales de autenticación? ¿Su token SSO no era válido o se agotó el tiempo de espera? `401 Unauthorized`
* ¿El consumidor fue _autenticado correctamente_ , pero _no tiene los permisos necesarios/la autorización adecuada_ para acceder al recurso?  `403 Forbidden`.

## 12. Haz un buen uso del HTTP`202 Accepted` <a href="#id-9afc" id="id-9afc"></a>

Me parece `202 Accepted`una alternativa muy práctica a `201 Created`. Básicamente significa:

> Yo, el servidor, he entendido tu solicitud. Aún no he creado el recurso, pero no hay problema.

Hay dos escenarios principales que considero `202 Accepted`especialmente adecuados:

* Si el recurso se creará como resultado de un procesamiento futuro (por ejemplo, después de que un trabajo o proceso haya finalizado).
* Si el recurso ya existía de alguna manera, pero esto no debe interpretarse como un error.

## 13. Utilice un framework web especializado en APIs REST <a href="#e04b" id="e04b"></a>

Como última práctica recomendada, analicemos esta pregunta: **¿Cómo se implementan realmente las mejores prácticas en su API?**

La mayoría de las veces, desea crear una API rápida para que algunos servicios puedan interactuar entre sí.

Los desarrolladores de Python tomarían Flask, los desarrolladores de JavaScript tomarían Node (Express) e implementarían algunas rutas simples para manejar solicitudes HTTP.

El problema con este enfoque es que, **en general, el marco no está orientado a crear servidores API REST** .

Por ejemplo, tanto Flask como Express son dos marcos muy versátiles, pero no fueron diseñados _específicamente_ para ayudarle a crear API REST.

Como resultado, debes tomar **medidas adicionales** para implementar las mejores prácticas en tu API. Y, en la mayoría de los casos, **la pereza o la falta de tiempo significan que no harás el esfuerzo** y dejarás a tus consumidores con una API peculiar.

La solución es sencilla: **utilizar la herramienta adecuada para el trabajo** .

Han surgido nuevos frameworks en varios lenguajes, diseñados específicamente para crear API REST. **Estos te ayudan a seguir las mejores prácticas sin complicaciones y sin sacrificar la productividad.**

En Python, uno de los mejores frameworks de API que he encontrado es [Falcon](https://falconframework.org/) . Es tan fácil de usar como Flask, muy rápido y perfecto para crear API REST en minutos.

_Falcon: Aliviando nuestras API durante más de 0,0564 siglos._

Si te gusta más usar Django, la mejor opción es [Django REST Framework](http://www.django-rest-framework.org/) . No es tan intuitivo, pero es increíblemente potente.

En Node, [Restify](http://restify.com/) también parece ser un buen candidato, aunque todavía no he tenido tiempo de probarlo.

Te recomiendo encarecidamente que pruebes estos frameworks. Te ayudarán a crear API REST atractivas, elegantes y bien diseñadas.

## Reflexiones finales <a href="#d4dc" id="d4dc"></a>

Todos deberíamos esforzarnos por hacer que usar las API sea un placer, _**tanto para los consumidores como para nuestros propios desarrolladores**_ .

Espero que este artículo te haya ayudado a aprender algunos consejos y técnicas inspiradoras para crear **mejores API REST** . Para mí, todo se reduce a **una buena semántica** , **simplicidad** y **sentido común** .

El diseño de API REST es un **arte,** más que cualquier otra cosa.

Si tienes una perspectiva diferente a la de los consejos que compartí anteriormente, compártela. Me encantaría saberlo.

Mientras tanto, ¡sigan enviando API!&#x20;
