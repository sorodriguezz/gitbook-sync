---
description: >-
  Describe cada componente dentro de un flujo típico de Spring MVC, agregando
  más detalles sobre su uso, configuración y buenas prácticas.
---

# Principales componentes Spring MVC

## DispatcherServlet

### **Descripción**

El DispatcherServlet es el **Front Controller** principal en la arquitectura de Spring MVC. Es el encargado de recibir las solicitudes HTTP (requests) y enviar las respuestas (responses) adecuadas, delegando la lógica a los controladores correctos en función de las URL definidas.

### **Anotaciones/Interfaces Clave**

* Extiende `HttpServlet`.
* Generalmente se configura de dos maneras:
  * Vía `web.xml` (en aplicaciones con configuración XML).
  * De forma automática a través de Spring Boot (por ejemplo, con la anotación `@SpringBootApplication` y el uso del Starter `spring-boot-starter-web`).

### **Uso / Funcionalidad**

* **Enrutamiento de solicitudes:** El DispatcherServlet mapea las rutas (paths) definidas en la aplicación y las redirige a los controladores que correspondan.
* **Manejo del ciclo de vida de la petición:** Se encarga de crear el contexto de la solicitud, procesar interceptores (si los hay), invocar el controlador apropiado, y finalmente elegir la vista (o serialización JSON/XML) para la respuesta.
* **Configuración de excepciones y conversión de formatos:** A través de configuraciones adicionales (por ejemplo, `@ExceptionHandler`, `@ControllerAdvice`, `MessageConverters`), el DispatcherServlet también administra cómo se manejan las excepciones y cómo se convierten los datos en diferentes formatos (JSON, XML, etc.).

## Controller

### **Descripción**

Los controladores son clases que contienen los métodos capaces de manejar las solicitudes HTTP y devolver las respuestas. Son la parte central donde definimos la lógica de negocio aplicada a cada endpoint (o conjunto de endpoints).

### **Anotaciones/Interfaces Clave**

* `@Controller`: Indica que la clase maneja solicitudes web y, por defecto, retorna vistas (plantillas) al navegador.
* `@RestController`: Versión especializada de `@Controller` que asume que los métodos devuelven directamente datos (ej. JSON) en lugar de vistas.
* `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`: Se utilizan para mapear métodos HTTP (GET, POST, etc.) o rutas específicas de la aplicación.

### **Uso / Funcionalidad**

* **Procesar solicitudes:** Recibe la información (parámetros, cuerpos de solicitudes) y la usa para invocar lógica de negocio o servicios.
* **Interacción con el Modelo:** Toma o crea datos para pasarlos al modelo, de modo que la vista o la respuesta JSON tenga la información necesaria.
* **Retorno de vistas o datos:** Devuelve una vista (por ejemplo, el nombre de una plantilla Thymeleaf) o directamente objetos que el framework serializa (en caso de `@RestController`).

### **Buenas Prácticas**

* Mantener los métodos de controlador concisos, delegando la lógica compleja a servicios (capas de negocio).
* Utilizar las anotaciones de validación (`@Valid`, `@RequestBody`, etc.) para simplificar la gestión de datos de entrada y manejar errores de forma uniforme.
* Documentar con claridad cada endpoint a través de comentarios y, preferiblemente, con herramientas como Swagger/OpenAPI.

## Model

### **Descripción**

El modelo contiene la **información o datos** que se van a transferir entre el controlador y la vista. Normalmente son objetos POJO (Plain Old Java Object) con atributos, getters y setters.

### **Uso / Funcionalidad**

* **Transferencia de datos:** Los controladores pueden poblar el modelo con datos para pasarlos a la capa de vista.
* **Vinculación de datos (Data Binding):** Spring se encarga de enlazar datos de formularios o solicitudes HTTP a los campos de estos objetos, siempre que los nombres de campo coincidan.
* **Persistencia (opcional):** Dependiendo de la aplicación, estos mismos objetos pueden estar vinculados a una base de datos a través de JPA u otro framework ORM. Sin embargo, en muchos casos, se utilizan objetos diferentes para la capa de persistencia (entidades) y para la capa de presentación (DTOs).

### **Buenas Prácticas**

* Mantener las clases de modelo simples y enfocadas en representar datos (no mezclar lógica de negocio extensa).
* Agregar validaciones con anotaciones de Bean Validation (por ejemplo, `@NotNull`, `@Size`, etc.) cuando sea necesario.

## ViewResolver

### **Descripción**

El ViewResolver se encarga de resolver y mapear los nombres lógicos de vistas a plantillas reales que se van a renderizar. Por ejemplo, si el controlador devuelve `"home"`, el ViewResolver determinará que esa vista corresponde a `home.html` en Thymeleaf o `home.jsp` en un entorno JSP.

### **Implementaciones / Anotaciones Clave**

* `InternalResourceViewResolver`: Comúnmente utilizado para JSP (mapea los nombres de vista a archivos .jsp).
* `BeanNameViewResolver`: Usa el nombre de bean para resolver vistas.
* `FreeMarkerViewResolver`: Para aplicaciones que usan plantillas FreeMarker.
* `ThymeleafViewResolver`: Para aplicaciones con plantillas Thymeleaf.

### **Uso / Funcionalidad**

* **Mapeo de vistas:** Cuando un método del controlador devuelve un `String` con el nombre de la vista, el ViewResolver busca la plantilla correspondiente y crea el objeto `View`.
* **Soporte para múltiples ViewResolvers:** Spring puede configurar varios resolutores de vistas en cadena. El primero que consiga resolver el nombre de la vista será el que se utilice.
* **Personalización:** Se pueden configurar prefijos y sufijos. Por ejemplo, en `application.properties` se puede especificar `spring.thymeleaf.prefix=classpath:/templates/` y `spring.thymeleaf.suffix=.html`.

### **Buenas Prácticas**

* Mantener un naming convention claro para los archivos de vista.
* Evitar lógica excesiva en la capa de vista: esta debe ser lo más ligera posible y enfocarse en la presentación.

## Interceptors

### **Descripción**

Los interceptores se utilizan para realizar acciones antes y/o después de que una solicitud llegue al controlador (y en ocasiones también después de que se haya generado la vista). Son útiles para tareas transversales como autenticación, logging y modificación de requests.

### **Anotaciones/Interfaces Clave**

* `HandlerInterceptor`: Interfaz que define métodos clave (`preHandle`, `postHandle`, `afterCompletion`) para inyectar lógica en diferentes etapas del ciclo de la petición.
* `HandlerInterceptorAdapter`: Clase abstracta que facilita la implementación de interceptores, evitando la necesidad de sobreescribir todos los métodos.
* `@Component`: Se puede utilizar para marcar un interceptor personalizado y registrarlo dentro del `WebMvcConfigurer` en Spring Boot.

### **Uso / Funcionalidad**

* **Pre-procesamiento de Requests:** Mediante `preHandle()`, se puede verificar o modificar la solicitud antes de que llegue al controlador (por ejemplo, comprobando tokens de autenticación).
* **Post-procesamiento de Responses:** Con `postHandle()`, es posible operar sobre la respuesta, o agregar atributos al modelo justo antes de renderizar la vista.
* **Acciones finales:** `afterCompletion()` se ejecuta tras finalizar el procesamiento de la vista, útil para tareas de limpieza o registro de métricas.

### **Buenas Prácticas**

* Utilizar interceptores para lógica transversal que no sea parte del núcleo de negocio (por ejemplo, logging, métricas, seguridad).
* Evitar cargar demasiado los interceptores con lógica compleja para no dificultar el mantenimiento.

## Flujo General de una Petición en Spring MVC

1. **Usuario/Cliente envía una solicitud** (por ejemplo, accediendo a `https://miaplicacion.com/home`).
2. La **solicitud llega al DispatcherServlet**, que identifica el controlador apropiado (en función de la URL y la configuración de mapeo).
3. **Se ejecutan los Interceptors** (si existen) antes de llegar al controlador (preHandle).
4. El **Controlador** procesa la solicitud, accede/crea/transforma datos en el **Modelo** y decide qué vista (o JSON) devolver.
5. Si es una respuesta con vista, el **ViewResolver** determina qué plantilla se va a utilizar para la vista final. Si es un `@RestController`, Spring devuelve datos serializados.
6. **Se ejecutan los Interceptors** nuevamente (postHandle y afterCompletion) para tareas posteriores (logging, etc.).
7. Se renderiza la **respuesta** (HTML, JSON, XML, etc.) y se envía al usuario/cliente.
