# Spring Boot

¡Bienvenido a esta clase completa sobre **Spring Boot**! En esta guía exhaustiva, profundizaremos en cada aspecto de Spring Boot, desde los fundamentos hasta temas avanzados. Cada sección incluye explicaciones detalladas, ejemplos de código y ejercicios prácticos para que puedas aplicar lo aprendido.

***

### Tabla de Contenidos

1. Introducción y Conceptos Básicos
2. Estructura de un Proyecto Spring Boot
3. Fundamentos de Spring Boot
4. Bases de Datos y JPA/Hibernate
5. Controladores y API REST
6. Validación de Datos
7. Manejo de Excepciones
8. Configuración Avanzada y Propiedades
9. Transacciones y Manejo de Transaccionalidad
10. Seguridad con Spring Security
11. Testing
12. Despliegue
13. Temas Avanzados

***

### 1. Introducción y Conceptos Básicos

#### 1.1. ¿Qué es Spring Boot?

**Spring Boot** es un módulo del **Spring Framework** que simplifica la creación de aplicaciones Java basadas en Spring. Proporciona una plataforma de desarrollo rápida para aplicaciones independientes y de producción, reduciendo la cantidad de configuración necesaria.

**Características Clave de Spring Boot:**

* **Autoconfiguración**: Spring Boot automáticamente configura tu aplicación basada en las dependencias que has agregado.
* **Dependencias Starter**: Proporciona un conjunto de dependencias agrupadas para simplificar la gestión de bibliotecas.
* **Servidor Integrado**: Permite ejecutar aplicaciones web sin necesidad de desplegar en un servidor externo (Tomcat, Jetty, etc.).
* **Actuator**: Ofrece monitoreo y métricas sobre tu aplicación.

#### 1.2. Relación con el Ecosistema de Spring

Spring Framework es un conjunto de proyectos que cubren diversas necesidades en el desarrollo de aplicaciones Java:

* **Spring Core**: Contiene las funcionalidades básicas de inversión de control (IoC) y inyección de dependencias (DI).
* **Spring MVC**: Framework para construir aplicaciones web y RESTful.
* **Spring Data**: Abstracción para trabajar con diferentes tecnologías de persistencia (JPA, MongoDB, Redis).
* **Spring Security**: Framework de autenticación y autorización.

**Spring Boot** se basa en estos proyectos para proporcionar una experiencia cohesiva y simplificada.

#### 1.3. Ventajas de Usar Spring Boot

* **Productividad**: Menos código y configuración para empezar.
* **Facilidad de Pruebas**: Herramientas integradas para testing.
* **Despliegue Simplificado**: Aplicaciones ejecutables como JAR o WAR.
* **Comunidad Activa**: Gran cantidad de recursos y soporte.

#### 1.4. Instalación y Configuración Inicial

**Requisitos Previos**

* **Java Development Kit (JDK) 8 o superior**: [Descargar aquí](https://www.oracle.com/java/technologies/javase-downloads.html)
* **IDE**: Recomendados:
  * [IntelliJ IDEA](https://www.jetbrains.com/idea/download/)
  * [Eclipse](https://www.eclipse.org/downloads/)
  * [Spring Tool Suite (STS)](https://spring.io/tools)
* **Maven**: Para la gestión de dependencias ([Instalar Maven](https://maven.apache.org/install.html))
* **Git**: Control de versiones ([Instalar Git](https://git-scm.com/downloads))

**Creación de un Proyecto con Spring Initializr**

Spring Initializr es una herramienta que te permite generar proyectos Spring Boot rápidamente.

1. **Acceder a Spring Initializr**: [start.spring.io](https://start.spring.io)
2. **Configurar el Proyecto**:
   * **Project**: Maven Project
   * **Language**: Java
   * **Spring Boot**: Selecciona la última versión estable
   * **Project Metadata**:
     * **Group**: com.ejemplo
     * **Artifact**: demo
     * **Name**: demo
     * **Description**: Proyecto Demo con Spring Boot
     * **Package Name**: com.ejemplo.demo
     * **Packaging**: Jar
     * **Java Version**: 11 (o la versión que tengas instalada)
3. **Agregar Dependencias**:
   * **Spring Web**: Para construir aplicaciones web y RESTful
   * **Spring Data JPA**: Para interactuar con bases de datos usando JPA
   * **H2 Database**: Base de datos en memoria para pruebas
   * **Spring Boot DevTools**: Herramientas de desarrollo (reinicio automático, etc.)
4. **Generar el Proyecto**: Haz clic en **"Generate"** para descargar un archivo ZIP con tu proyecto.
5. **Importar el Proyecto en tu IDE**:
   * **IntelliJ IDEA**:
     * Archivo > Abrir > Selecciona la carpeta del proyecto
     * Espera a que Maven descargue las dependencias
   * **Eclipse/STS**:
     * Archivo > Importar > Proyecto Maven existente > Selecciona la carpeta del proyecto

**Verificación de la Configuración**

1. **Ejecutar la Aplicación**:
   * En tu clase principal `DemoApplication.java`, haz clic derecho y selecciona **"Run"**.
   *   Deberías ver en la consola algo como:

       ```
       Tomcat started on port(s): 8080 (http) with context path ''
       Started DemoApplication in 2.345 seconds (JVM running for 2.987)
       ```
2. **Prueba en el Navegador**:
   * Abre tu navegador y visita `http://localhost:8080`
   * Deberías obtener un error 404, lo cual es normal ya que no hemos definido ningún endpoint aún.

**¡Felicidades!** Has configurado tu primer proyecto Spring Boot.

***

### 2. Estructura de un Proyecto Spring Boot

#### 2.1. Estructura de Carpetas y Archivos

Comprender la estructura de un proyecto Spring Boot es crucial para organizar adecuadamente tu código.

```
demo
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── ejemplo
│   │   │           └── demo
│   │   │               ├── DemoApplication.java
│   │   │               ├── controller
│   │   │               ├── service
│   │   │               └── repository
│   │   └── resources
│   │       ├── application.properties
│   │       ├── static
│   │       └── templates
│   └── test
│       └── java
│           └── com
│               └── ejemplo
│                   └── demo
├── pom.xml
```

**Descripción de Carpetas:**

* **src/main/java**: Contiene el código fuente de la aplicación.
  * **com.ejemplo.demo**: Paquete base donde se encuentra la clase principal y otros subpaquetes.
    * **DemoApplication.java**: Clase principal con el método `main()`.
    * **controller**: Controladores REST.
    * **service**: Clases de servicio (lógica de negocio).
    * **repository**: Interfaces de repositorio para acceso a datos.
* **src/main/resources**: Recursos de la aplicación.
  * **application.properties**: Archivo de configuración.
  * **static**: Archivos estáticos (CSS, JavaScript, imágenes).
  * **templates**: Plantillas Thymeleaf o Freemarker.
* **src/test/java**: Código fuente para pruebas unitarias e integradas.
* **pom.xml**: Archivo de configuración de Maven, donde se declaran las dependencias.

#### 2.2. Archivos Clave: `application.properties` y `application.yml`

Estos archivos se utilizan para configurar aspectos de tu aplicación sin modificar el código fuente.

**`application.properties`**

Formato de clave-valor:

```properties
server.port=8081
spring.datasource.url=jdbc:h2:mem:testdb
spring.jpa.hibernate.ddl-auto=update
logging.level.org.springframework=DEBUG
```

**`application.yml`**

Formato YAML (requiere cuidado con la indentación):

```yaml
server:
  port: 8081

spring:
  datasource:
    url: jdbc:h2:mem:testdb
  jpa:
    hibernate:
      ddl-auto: update

logging:
  level:
    org.springframework: DEBUG
```

**Nota**: Puedes elegir cualquiera de los dos formatos según tu preferencia.

#### 2.3. Organización del Código: Controladores, Servicios y Repositorios

Seguir una arquitectura bien definida facilita el mantenimiento y escalabilidad de la aplicación.

**Controladores (`controller`)**

Se encargan de manejar las solicitudes HTTP y devolver respuestas.

```java
package com.ejemplo.demo.controller;

import com.ejemplo.demo.service.UserService;
import com.ejemplo.demo.model.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService userService;

    // Inyección de dependencias por constructor
    @Autowired
    public UserController(UserService userService) {
        this.userService = userService;
    }

    // Obtener todos los usuarios
    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    // Crear un nuevo usuario
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.createUser(user);
    }
}
```

**Servicios (`service`)**

Contienen la lógica de negocio y procesan datos entre los controladores y los repositorios.

```java
package com.ejemplo.demo.service;

import com.ejemplo.demo.model.User;
import com.ejemplo.demo.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    private final UserRepository userRepository;

    // Inyección de dependencias por constructor
    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // Obtener todos los usuarios
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    // Crear un nuevo usuario
    public User createUser(User user) {
        return userRepository.save(user);
    }
}
```

**Repositorios (`repository`)**

Interfases que extienden de `JpaRepository` para interactuar con la base de datos.

```java
package com.ejemplo.demo.repository;

import com.ejemplo.demo.model.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long>{
    // Métodos de consulta personalizados (si es necesario)
}
```

**Buenas Prácticas:**

* **Seguir el principio de responsabilidad única**: Cada capa debe tener una única responsabilidad.
* **Utilizar inyección de dependencias por constructor**: Facilita las pruebas y la inmutabilidad.
* **Nombrar paquetes y clases de forma coherente**: Facilita la lectura y mantenimiento del código.
* **Evitar lógica de negocio en los controladores**: Mantén los controladores ligeros y delega la lógica a los servicios.

**Ejercicio Práctico**:

1. Crear un modelo `Producto` con campos `id`, `nombre`, `precio`.
2. Implementar un controlador, servicio y repositorio para `Producto`.
3. Añadir endpoints para listar todos los productos y crear uno nuevo.

***

### 3. Fundamentos de Spring Boot

#### 3.1. Anotaciones Clave en Spring Boot

Las anotaciones son esenciales en Spring Boot para indicar comportamientos y configuraciones.

**`@SpringBootApplication`**

Combina tres anotaciones:

* `@Configuration`: Indica que la clase puede utilizar definiciones de beans.
* `@EnableAutoConfiguration`: Permite a Spring Boot configurar automáticamente los beans basándose en las dependencias.
* `@ComponentScan`: Escanea los paquetes en busca de componentes (controladores, servicios, etc.).

**Ejemplo**:

```java
package com.ejemplo.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    // Método principal
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

**`@RestController`**

Combina `@Controller` y `@ResponseBody`. Indica que la clase manejará solicitudes REST y que los métodos devolverán datos directamente en el cuerpo de la respuesta.

```java
@RestController
public class MiControlador {
    // Métodos manejadores
}
```

**`@Service`**

Marca una clase como un servicio, componente de la capa de negocio.

```java
@Service
public class MiServicio {
    // Lógica de negocio
}
```

**`@Repository`**

Indica que la clase es un componente de acceso a datos y que puede lanzar excepciones específicas.

```java
@Repository
public interface MiRepositorio extends JpaRepository<Entidad, Long> {
    // Métodos de acceso a datos
}
```

**Otras Anotaciones Importantes**

* `@Component`: Marca una clase como un componente general.
* `@Autowired`: Inyecta dependencias automáticamente.
* `@Qualifier`: Diferencia entre múltiples beans del mismo tipo.
* `@Value`: Inyecta valores desde las propiedades.

#### 3.2. Inyección de Dependencias en Spring Boot

La inyección de dependencias es un patrón que permite a los componentes recibir sus dependencias en lugar de crearlas.

**Inyección por Constructor (Recomendada)**

```java
@Service
public class UserService {

    private final UserRepository userRepository;

    // Inyección de dependencias
    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

**Inyección por Campo (No Recomendada)**

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;
}
```

**Ventajas de la Inyección por Constructor**:

* **Inmutabilidad**: Las dependencias se pueden declarar como `final`.
* **Facilita las Pruebas Unitarias**: Permite inyectar mocks fácilmente.
* **Claridad**: Las dependencias requeridas son explícitas en el constructor.

#### 3.3. Ciclo de Vida de un Bean en Spring

* **Instanciación**: Spring crea una instancia del bean.
* **Inyección de Dependencias**: Se inyectan las dependencias marcadas.
* **Inicialización**: Se llama a métodos de inicialización si existen (`@PostConstruct`).
* **Uso**: El bean está listo para ser utilizado.
* **Destrucción**: Al apagar el contexto, se llama a métodos de destrucción (`@PreDestroy`).

***

### 4. Bases de Datos y JPA/Hibernate

#### 4.1. Introducción a JPA y Hibernate

* **JPA (Java Persistence API)**: Especificación de Java para el mapeo objeto-relacional (ORM).
* **Hibernate**: Implementación popular de JPA que facilita la interacción con bases de datos relacionales.

#### 4.2. Configuración de la Base de Datos

**Dependencias Necesarias**

En tu `pom.xml`:

```xml
<!-- Dependencia para Spring Data JPA -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Dependencia para la base de datos H2 -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>

<!-- Otras bases de datos, por ejemplo, MySQL -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

**Configuración en `application.properties`**

Para H2 Database:

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.h2.console.enabled=true
```

Para MySQL:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mi_base_de_datos?useSSL=false&serverTimezone=UTC
spring.datasource.username=usuario
spring.datasource.password=contraseña
spring.jpa.database-platform=org.hibernate.dialect.MySQL5Dialect
```

**Configuración de Hibernate**

* `spring.jpa.hibernate.ddl-auto`:
  * **create**: Crea el esquema de la base de datos en cada inicio.
  * **update**: Actualiza el esquema sin perder datos.
  * **create-drop**: Crea y luego elimina el esquema al cerrar.
  * **validate**: Valida el esquema existente.
*   **Ejemplo**:

    ```properties
    spring.jpa.hibernate.ddl-auto=update
    ```

#### 4.3. Creación de Entidades y Relaciones

**Entidad Simple**

```java
package com.ejemplo.demo.model;

import javax.persistence.*;

@Entity
@Table(name = "usuarios")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name="nombre", nullable=false)
    private String name;

    @Column(name="correo", unique=true, nullable=false)
    private String email;

    // Constructores, Getters y Setters
}
```

**Relaciones entre Entidades**

**One-to-One**

Usuario y Perfil:

```java
@Entity
public class Profile {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String bio;

    // Relación inversa
    @OneToOne(mappedBy = "profile")
    private User user;

    // Getters y Setters
}

@Entity
public class User {

    // Otros campos...

    @OneToOne
    @JoinColumn(name = "profile_id")
    private Profile profile;

    // Getters y Setters
}
```

**One-to-Many y Many-to-One**

Un Usuario tiene muchos Pedidos (Orders):

```java
@Entity
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String product;

    // Relación Many-to-One
    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;

    // Getters y Setters
}

@Entity
public class User {

    // Otros campos...

    // Relación One-to-Many
    @OneToMany(mappedBy = "user")
    private List<Order> orders;

    // Getters y Setters
}
```

**Many-to-Many**

Usuarios y Roles:

```java
@Entity
public class Role {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String roleName;

    // Relación inversa
    @ManyToMany(mappedBy = "roles")
    private Set<User> users;

    // Getters y Setters
}

@Entity
public class User {

    // Otros campos...

    @ManyToMany
    @JoinTable(
        name = "user_roles",
        joinColumns = @JoinColumn(name = "user_id"),
        inverseJoinColumns = @JoinColumn(name = "role_id")
    )
    private Set<Role> roles;

    // Getters y Setters
}
```

**Nota**: Siempre es importante definir bien las relaciones y considerar el rendimiento y la coherencia de los datos.

#### 4.4. Uso de `JpaRepository` y Consultas Personalizadas con `@Query`

**Extensión de `JpaRepository`**

```java
public interface UserRepository extends JpaRepository<User, Long> {
    // Métodos personalizados
}
```

**Métodos de Consulta Derivados**

Spring Data JPA genera automáticamente consultas basadas en el nombre del método.

*   **Encontrar por nombre**:

    ```java
    List<User> findByName(String name);
    ```
*   **Encontrar por correo y nombre**:

    ```java
    User findByEmailAndName(String email, String name);
    ```
*   **Contar por rol**:

    ```java
    long countByRole(String role);
    ```

**Consultas Personalizadas con `@Query`**

```java
@Query("SELECT u FROM User u WHERE u.email = ?1")
User findByEmail(String email);
```

**Uso de Consultas Nativas**

```java
@Query(value = "SELECT * FROM usuarios WHERE correo = ?1", nativeQuery = true)
User findByEmailNative(String email);
```

**Paginación y Ordenación**

```java
Page<User> findAll(Pageable pageable);
```

**Ejemplo**:

```java
Pageable pageable = PageRequest.of(0, 10, Sort.by("name"));
Page<User> users = userRepository.findAll(pageable);
```

**Ejercicio Práctico**:

1. Crear una entidad `Producto` con campos `id`, `nombre`, `precio`, `stock`.
2. Implementar métodos en `ProductoRepository` para:
   * Encontrar productos por rango de precios.
   * Buscar productos cuyo nombre contenga una cadena específica.
   * Actualizar el stock de un producto.

***

### 5. Controladores y API REST

#### 5.1. Creación de Controladores y Endpoints REST

Los controladores son el punto de entrada para las solicitudes HTTP.

**Ejemplo de Controlador `UserController`**

```java
package com.ejemplo.demo.controller;

import com.ejemplo.demo.model.User;
import com.ejemplo.demo.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService userService;

    // Inyección de dependencias
    @Autowired
    public UserController(UserService userService) {
        this.userService = userService;
    }

    // Obtener todos los usuarios
    @GetMapping
    public ResponseEntity<List<User>> getAllUsers() {
        List<User> users = userService.getAllUsers();
        return ResponseEntity.ok(users);
    }

    // Obtener un usuario por ID
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        return userService.getUserById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }

    // Crear un nuevo usuario
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User createdUser = userService.createUser(user);
        return ResponseEntity.status(201).body(createdUser);
    }

    // Actualizar un usuario existente
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User userDetails) {
        return userService.updateUser(id, userDetails)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }

    // Eliminar un usuario
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        if (userService.deleteUser(id)) {
            return ResponseEntity.noContent().build();
        } else {
            return ResponseEntity.notFound().build();
        }
    }
}
```

#### 5.2. Uso de `ResponseEntity` y Manejo de Respuestas HTTP

`ResponseEntity` es una clase que permite manipular el código de estado HTTP, los encabezados y el cuerpo de la respuesta.

**Ejemplo:**

```java
@GetMapping("/{id}")
public ResponseEntity<User> getUserById(@PathVariable Long id) {
    Optional<User> user = userService.getUserById(id);
    if (user.isPresent()) {
        return ResponseEntity.ok(user.get());
    } else {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                             .body(null);
    }
}
```

**Métodos Estáticos de `ResponseEntity`:**

* `ok(T body)`: Retorna 200 OK con el cuerpo especificado.
* `created(URI location)`: Retorna 201 Created.
* `noContent()`: Retorna 204 No Content.
* `badRequest()`: Retorna 400 Bad Request.
* `notFound()`: Retorna 404 Not Found.

#### 5.3. Conceptos RESTful y Buenas Prácticas en Diseño de APIs

**Principios de Diseño RESTful:**

* **Uniformidad de Interfaz**: Usar métodos HTTP estándar (GET, POST, PUT, DELETE).
* **Identificación de Recursos**: Cada recurso debe tener una URI única.
* **Sin Estado**: Cada solicitud debe contener toda la información necesaria.
* **Representaciones Múltiples**: Los recursos pueden tener diferentes representaciones (JSON, XML).

**Buenas Prácticas:**

* **Uso Correcto de Verbos HTTP**:
  * **GET**: Obtener recursos.
  * **POST**: Crear nuevos recursos.
  * **PUT**: Actualizar recursos existentes.
  * **DELETE**: Eliminar recursos.
* **URI Semánticas y Consistentes**:
  * Evitar verbos en las URIs.
  * Utilizar nombres en plural para colecciones: `/api/users`
  * Anidar recursos si es necesario: `/api/users/{id}/orders`
* **Versionamiento de la API**:
  * Incluir la versión en la URI: `/api/v1/users`
  * O en el encabezado `Accept`: `Accept: application/vnd.company.app-v1+json`
* **Documentación**:
  * Utilizar herramientas como **Swagger** para generar documentación interactiva.

**Ejercicio Práctico**:

1. Implementar un controlador `ProductoController` con endpoints para CRUD de productos.
2. Asegurar que los endpoints siguen las convenciones RESTful.
3. Documentar la API utilizando Swagger.

***

### 6. Validación de Datos

#### 6.1. Uso de Validaciones con `@Valid` y `@Validated`

La validación asegura que los datos que ingresan a nuestra aplicación cumplen con ciertas reglas.

**Dependencia Necesaria**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

**Validación en el Modelo**

```java
import javax.validation.constraints.*;

public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotBlank(message = "El nombre es obligatorio")
    private String name;

    @Email(message = "El correo debe ser válido")
    @NotNull(message = "El correo es obligatorio")
    private String email;

    @Size(min = 6, message = "La contraseña debe tener al menos 6 caracteres")
    private String password;

    // Otros campos y métodos
}
```

**Validación en Controladores**

```java
@PostMapping
public ResponseEntity<User> createUser(@Valid @RequestBody User user) {
    User createdUser = userService.createUser(user);
    return ResponseEntity.status(HttpStatus.CREATED).body(createdUser);
}
```

#### 6.2. Anotaciones de Validación Comunes

* `@Null` / `@NotNull`: El elemento debe ser nulo / no nulo.
* `@AssertTrue` / `@AssertFalse`: El elemento debe ser true / false.
* `@Min(value)` / `@Max(value)`: El número debe ser mayor / menor que el valor especificado.
* `@DecimalMin(value)` / `@DecimalMax(value)`: Para números decimales.
* `@Size(min, max)`: Tamaño de colecciones, mapas, cadenas, arrays.
* `@Pattern(regex)`: La cadena debe coincidir con la expresión regular.
* `@Email`: La cadena debe ser un correo electrónico válido.
* `@NotEmpty`: La colección o cadena no debe estar vacía.

#### 6.3. Manejo de Mensajes de Error

Puedes personalizar los mensajes de error usando el atributo `message` o mediante archivos de mensajes.

**Archivo `messages.properties`**

Crea un archivo en `src/main/resources` llamado `messages.properties`:

```properties
NotBlank.user.name=El nombre es obligatorio
Email.user.email=El correo electrónico no es válido
```

Luego, en tu modelo:

```java
public class User {

    @NotBlank(message = "{NotBlank.user.name}")
    private String name;

    @Email(message = "{Email.user.email}")
    private String email;

    // Otros campos y métodos
}
```

#### 6.4. Validación a Nivel de Servicio

En ocasiones, necesitas validar lógica más compleja.

```java
@Service
@Validated
public class UserService {

    public User createUser(@Valid User user) {
        // Lógica para crear usuario
    }
}
```

#### 6.5. Manejo de Errores de Validación

**Controlador de Excepciones**

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<?> handleValidationErrors(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> 
            errors.put(error.getField(), error.getDefaultMessage()));
        return ResponseEntity.badRequest().body(errors);
    }
}
```

**Ejercicio Práctico**:

1. Añadir validaciones a la entidad `Producto`:
   * `nombre`: no puede estar en blanco.
   * `precio`: debe ser positivo.
2. Implementar manejo de errores de validación en `ProductoController`.
3. Probar los endpoints con datos inválidos y verificar los mensajes de error.

***

### 7. Manejo de Excepciones

#### 7.1. Creación de Excepciones Personalizadas

```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

#### 7.2. Controlador Global de Excepciones con `@ControllerAdvice`

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    // Manejo de ResourceNotFoundException
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<?> handleResourceNotFound(ResourceNotFoundException ex) {
        ErrorDetails errorDetails = new ErrorDetails(
            LocalDateTime.now(),
            ex.getMessage(),
            "El recurso solicitado no existe"
        );
        return new ResponseEntity<>(errorDetails, HttpStatus.NOT_FOUND);
    }

    // Manejo de otras excepciones
    @ExceptionHandler(Exception.class)
    public ResponseEntity<?> handleGlobalException(Exception ex) {
        ErrorDetails errorDetails = new ErrorDetails(
            LocalDateTime.now(),
            ex.getMessage(),
            "Error interno del servidor"
        );
        return new ResponseEntity<>(errorDetails, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

**Clase `ErrorDetails`**

```java
public class ErrorDetails {

    private LocalDateTime timestamp;
    private String message;
    private String details;

    // Constructor, Getters y Setters

    public ErrorDetails(LocalDateTime timestamp, String message, String details) {
        super();
        this.timestamp = timestamp;
        this.message = message;
        this.details = details;
    }
}
```

#### 7.3. Lanzando Excepciones en el Servicio

```java
public Optional<User> getUserById(Long id) {
    return userRepository.findById(id)
        .orElseThrow(() -> new ResourceNotFoundException("Usuario no encontrado con id: " + id));
}
```

#### 7.4. Personalización de Mensajes de Error

Puedes añadir más detalles al mensaje de error, incluyendo sugerencias o pasos a seguir.

**Ejercicio Práctico**:

1. Crear una excepción personalizada `ProductoNoEncontradoException`.
2. Implementar el manejo de esta excepción en el controlador global.
3. Modificar el servicio de `Producto` para lanzar la excepción cuando no se encuentre un producto por ID.
4. Probar el endpoint y verificar la respuesta de error.

***

### 8. Configuración Avanzada y Propiedades

#### 8.1. Perfiles de Spring Boot

Los perfiles permiten tener diferentes configuraciones para distintos entornos (desarrollo, pruebas, producción).

**Definición de Perfiles**

* **application.properties**: Configuración por defecto.
* **application-dev.properties**: Configuración para el perfil `dev`.
* **application-prod.properties**: Configuración para el perfil `prod`.

**Activar un Perfil**

*   En `application.properties`:

    ```properties
    spring.profiles.active=dev
    ```
*   Al ejecutar la aplicación:

    ```bash
    java -jar demo.jar --spring.profiles.active=prod
    ```

**Ejemplo de Configuración**

**application.properties**:

```properties
spring.profiles.active=@activatedProperties@
```

**application-dev.properties**:

```properties
server.port=8080
spring.datasource.url=jdbc:h2:mem:devdb
```

**application-prod.properties**:

```properties
server.port=80
spring.datasource.url=jdbc:mysql://prodserver:3306/proddb
```

#### 8.2. Propiedades Personalizadas

**Definir Propiedades**

En `application.properties`:

```properties
app.name=Mi Aplicación
app.description=Esta es una aplicación de ejemplo
app.version=1.0.0
```

**Acceder a Propiedades con `@Value`**

```java
@Component
public class AppInfo {

    @Value("${app.name}")
    private String name;

    @Value("${app.description}")
    private String description;

    @Value("${app.version}")
    private String version;

    // Métodos para usar estas propiedades
}
```

#### 8.3. Uso de `@ConfigurationProperties`

Permite agrupar propiedades relacionadas en una clase.

**Definir la Clase de Configuración**

```java
@Configuration
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String name;
    private String description;
    private String version;

    // Getters y Setters
}
```

**Habilitar `@EnableConfigurationProperties`**

En la clase principal:

```java
@SpringBootApplication
@EnableConfigurationProperties(AppProperties.class)
public class DemoApplication { ... }
```

**Usar las Propiedades**

```java
@RestController
public class AppController {

    private final AppProperties appProperties;

    @Autowired
    public AppController(AppProperties appProperties) {
        this.appProperties = appProperties;
    }

    @GetMapping("/info")
    public ResponseEntity<?> getAppInfo() {
        Map<String, String> info = new HashMap<>();
        info.put("name", appProperties.getName());
        info.put("description", appProperties.getDescription());
        info.put("version", appProperties.getVersion());
        return ResponseEntity.ok(info);
    }
}
```

#### 8.4. Cifrado de Propiedades Sensibles

Para proteger información sensible como contraseñas, puedes utilizar herramientas como **Jasypt**.

**Añadir Dependencia**

```xml
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>3.0.4</version>
</dependency>
```

**Cifrar una Propiedad**

1.  Genera una contraseña cifrada:

    ```bash
    java -cp jasypt-1.9.3.jar org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input="mi_contraseña" password="clave_maestra" algorithm=PBEWithMD5AndDES
    ```
2.  En `application.properties`:

    ```properties
    spring.datasource.password=ENC(contraseña_cifrada)
    ```
3.  Al iniciar la aplicación, proporciona la clave maestra:

    ```bash
    java -jar demo.jar --jasypt.encryptor.password=clave_maestra
    ```

**Ejercicio Práctico**:

1. Crear propiedades personalizadas para configurar la cantidad máxima de conexiones y el tiempo de espera.
2. Acceder a estas propiedades en el código y usarlas para configurar el `DataSource`.
3. Implementar perfiles para `dev` y `prod` con diferentes configuraciones de base de datos.

***

### 9. Transacciones y Manejo de Transaccionalidad

#### 9.1. Uso de `@Transactional`

La anotación `@Transactional` se utiliza para gestionar transacciones de base de datos de forma declarativa.

**En Métodos de Servicio**

```java
@Service
public class BankService {

    @Autowired
    private AccountRepository accountRepository;

    @Transactional
    public void transfer(Long fromAccountId, Long toAccountId, BigDecimal amount) {
        Account fromAccount = accountRepository.findById(fromAccountId)
            .orElseThrow(() -> new AccountNotFoundException("Cuenta origen no encontrada"));
        Account toAccount = accountRepository.findById(toAccountId)
            .orElseThrow(() -> new AccountNotFoundException("Cuenta destino no encontrada"));

        fromAccount.debit(amount);
        toAccount.credit(amount);

        accountRepository.save(fromAccount);
        accountRepository.save(toAccount);
    }
}
```

#### 9.2. Propagación y Aislamiento de Transacciones

**Propagación (`propagation`)**

Define cómo se comporta una transacción cuando ya existe una transacción activa.

* **REQUIRED** (por defecto): Usa la transacción existente o crea una nueva si no hay.
* **REQUIRES\_NEW**: Siempre crea una nueva transacción, suspendiendo la actual si existe.
* **MANDATORY**: Debe ejecutarse dentro de una transacción existente, si no lanza excepción.
* **SUPPORTS**: Usa una transacción si existe, si no, ejecuta sin transacción.
* **NOT\_SUPPORTED**: Ejecuta sin transacción, suspendiendo cualquier transacción existente.

**Ejemplo:**

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void saveAuditLog(AuditLog log) {
    auditLogRepository.save(log);
}
```

**Aislamiento (`isolation`)**

Controla cómo una transacción es aislada de otras operaciones.

* **READ\_UNCOMMITTED**: Permite lecturas sucias.
* **READ\_COMMITTED**: Evita lecturas sucias.
* **REPEATABLE\_READ**: Evita lecturas no repetibles.
* **SERIALIZABLE**: Aislamiento completo, evita todas las anomalías.

**Ejemplo:**

```java
@Transactional(isolation = Isolation.SERIALIZABLE)
public void performSensitiveOperation() {
    // Operaciones críticas
}
```

#### 9.3. Manejo de Excepciones en Transacciones

* **Rollback Automático**: Spring hace rollback en transacciones cuando una excepción no verificada (RuntimeException) es lanzada.
*   **Especificar Excepciones**:

    ```java
    @Transactional(rollbackFor = { Exception.class })
    public void process() {
        // Código que puede lanzar excepciones
    }
    ```

#### 9.4. Métodos de Transacciones

* **`@Transactional` a Nivel de Clase**: Se aplica a todos los métodos.
* **Métodos Privados**: No se aplican transacciones a métodos privados.
* **Llamadas Internas**: Si un método transaccional llama a otro método en la misma clase, las transacciones no se aplican al segundo método a menos que uses aspectos (AOP) avanzados.

**Ejercicio Práctico**:

1. Implementar una operación de transferencia entre cuentas bancarias que sea transaccional.
2. Simular una falla durante la transferencia y verificar que se hace rollback.
3. Configurar diferentes niveles de propagación y observar el comportamiento.

***

### 10. Seguridad con Spring Security

#### 10.1. Introducción a Spring Security

Spring Security es un framework que proporciona autenticación y control de acceso.

#### 10.2. Configuración Básica

**Añadir la Dependencia**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

**Configuración por Defecto**

Por defecto, Spring Security:

* Protege todas las rutas.
* Genera un usuario con nombre `user` y una contraseña aleatoria que se muestra en la consola.

**Personalización de Seguridad**

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // Definir usuarios en memoria
        auth.inMemoryAuthentication()
            .withUser("user").password(passwordEncoder().encode("password")).roles("USER")
            .and()
            .withUser("admin").password(passwordEncoder().encode("admin")).roles("ADMIN");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf().disable() // Desactivar CSRF para simplificar (no recomendado en producción)
            .authorizeRequests()
                .antMatchers("/api/public/**").permitAll() // Rutas públicas
                .antMatchers("/api/admin/**").hasRole("ADMIN") // Solo para ADMIN
                .anyRequest().authenticated() // Cualquier otra ruta requiere autenticación
            .and()
            .httpBasic(); // Autenticación básica
    }

    // Configurar el codificador de contraseñas
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

#### 10.3. Autenticación y Autorización

**Enfoques de Autenticación:**

* **En Memoria**: Definición de usuarios en la configuración.
* **En Base de Datos**: Cargar usuarios desde una base de datos.
* **LDAP**: Integración con servicios LDAP.

**Autorización con Roles**

*   **Anotaciones en Métodos**:

    ```java
    @RestController
    @RequestMapping("/api/admin")
    @PreAuthorize("hasRole('ADMIN')")
    public class AdminController {
        // Métodos solo accesibles por ADMIN
    }
    ```
*   **Configuración en `HttpSecurity`**:

    ```java
    http.authorizeRequests()
        .antMatchers("/api/admin/**").hasRole("ADMIN")
        .antMatchers("/api/users/**").hasAnyRole("USER", "ADMIN")
        .anyRequest().authenticated();
    ```

#### 10.4. Manejo de Sesiones

**Configuración de Sesiones**

```java
http.sessionManagement()
    .sessionCreationPolicy(SessionCreationPolicy.STATELESS); // Para APIs RESTful
```

**Protección contra CSRF**

* **CSRF (Cross-Site Request Forgery)**: Ataque que fuerza a un usuario autenticado a ejecutar acciones no deseadas.
*   **Desactivar CSRF**: En aplicaciones RESTful donde el cliente es externo.

    ```java
    http.csrf().disable();
    ```

#### 10.5. Autenticación con JWT (JSON Web Tokens)

**Dependencias Necesarias**

```xml
<!-- JWT -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

**Flujo de Autenticación con JWT**

1. El usuario envía credenciales al endpoint `/login`.
2. Si son válidas, el servidor genera un JWT y lo devuelve.
3. El cliente envía el JWT en el encabezado `Authorization` en solicitudes subsecuentes.
4. El servidor valida el JWT y autoriza el acceso.

**Implementación Básica**

Por la complejidad, se recomienda seguir un tutorial específico para JWT con Spring Security.

**Ejercicio Práctico**:

1. Implementar seguridad básica con usuarios en memoria.
2. Proteger el endpoint de creación de productos solo para usuarios con rol `ADMIN`.
3. Probar el acceso a los endpoints con y sin autenticación.

***

### 11. Testing

#### 11.1. Pruebas Unitarias y de Integración

Las pruebas son esenciales para asegurar la calidad y funcionamiento correcto de la aplicación.

**Dependencias Necesarias**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

#### 11.2. Pruebas de Servicios con `@SpringBootTest`

```java
@SpringBootTest
public class UserServiceTest {

    @Autowired
    private UserService userService;

    @MockBean
    private UserRepository userRepository;

    @Test
    public void testGetAllUsers() {
        // Configurar datos simulados
        List<User> users = Arrays.asList(new User("John"), new User("Jane"));
        Mockito.when(userRepository.findAll()).thenReturn(users);

        // Ejecutar el método
        List<User> result = userService.getAllUsers();

        // Verificar resultados
        Assertions.assertEquals(2, result.size());
        Mockito.verify(userRepository, Mockito.times(1)).findAll();
    }
}
```

#### 11.3. Pruebas de Controladores con `@WebMvcTest`

```java
@WebMvcTest(UserController.class)
public class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    public void testGetAllUsers() throws Exception {
        // Configurar datos simulados
        List<User> users = Arrays.asList(new User("John"), new User("Jane"));
        Mockito.when(userService.getAllUsers()).thenReturn(users);

        // Realizar solicitud y verificar respuesta
        mockMvc.perform(get("/api/users"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$", hasSize(2)))
            .andExpect(jsonPath("$[0].name", is("John")))
            .andExpect(jsonPath("$[1].name", is("Jane")));
    }
}
```

#### 11.4. Pruebas de Repositorios con `@DataJpaTest`

```java
@DataJpaTest
public class UserRepositoryTest {

    @Autowired
    private TestEntityManager entityManager;

    @Autowired
    private UserRepository userRepository;

    @Test
    public void testFindByName() {
        // Preparar datos
        User user = new User("John", "john@example.com");
        entityManager.persist(user);
        entityManager.flush();

        // Ejecutar consulta
        List<User> found = userRepository.findByName("John");

        // Verificar resultados
        Assertions.assertEquals(1, found.size());
        Assertions.assertEquals(user.getEmail(), found.get(0).getEmail());
    }
}
```

#### 11.5. Base de Datos H2 en Memoria para Pruebas

**Configuración**

En `application-test.properties`:

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=create-drop
```

**Usar el Perfil de Prueba**

```java
@SpringBootTest
@ActiveProfiles("test")
public class UserServiceTest { ... }
```

**Ejercicio Práctico**:

1. Escribir pruebas unitarias para el servicio de `Producto`.
2. Simular el repositorio y verificar que los métodos del servicio funcionan correctamente.
3. Escribir pruebas de integración para los endpoints de `ProductoController`.

***

### 12. Despliegue

#### 12.1. Empaquetar la Aplicación como JAR

**Usando Maven**

```bash
mvn clean package
```

El archivo JAR se generará en `target/demo-0.0.1-SNAPSHOT.jar`.

**Ejecutar el JAR**

```bash
java -jar target/demo-0.0.1-SNAPSHOT.jar
```

#### 12.2. Despliegue en Heroku

**Pre-requisitos**

* Cuenta en Heroku
* Heroku CLI instalado

**Pasos**

1.  **Crear un archivo `Procfile`** en la raíz del proyecto:

    ```
    web: java -jar target/demo-0.0.1-SNAPSHOT.jar
    ```
2.  **Iniciar sesión en Heroku**:

    ```bash
    heroku login
    ```
3.  **Crear una nueva aplicación**:

    ```bash
    heroku create nombre-de-tu-aplicacion
    ```
4.  **Agregar Git al proyecto**:

    ```bash
    git init
    git add .
    git commit -m "Initial commit"
    ```
5.  **Agregar el remoto de Heroku**:

    ```bash
    heroku git:remote -a nombre-de-tu-aplicacion
    ```
6.  **Desplegar la aplicación**:

    ```bash
    git push heroku master
    ```
7.  **Verificar el despliegue**:

    ```bash
    heroku open
    ```

#### 12.3. Despliegue en AWS Elastic Beanstalk

**Pre-requisitos**

* Cuenta en AWS
* AWS CLI o Elastic Beanstalk CLI instalado

**Pasos Básicos**

1.  **Crear un entorno de aplicación**:

    ```bash
    eb init
    ```
2.  **Crear un entorno**:

    ```bash
    eb create nombre-del-entorno
    ```
3.  **Desplegar la aplicación**:

    ```bash
    eb deploy
    ```

#### 12.4. Despliegue con Docker

**Crear un `Dockerfile`**

```dockerfile
FROM openjdk:11-jre-slim
ARG JAR_FILE=target/demo-0.0.1-SNAPSHOT.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

**Construir la Imagen**

```bash
docker build -t demo-app .
```

**Ejecutar el Contenedor**

```bash
docker run -p 8080:8080 demo-app
```

**Configurar `docker-compose.yml`**

```yaml
version: '3'
services:
  app:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      - db
  db:
    image: mysql:5.7
    environment:
      MYSQL_DATABASE: demo
      MYSQL_ROOT_PASSWORD: secret
    ports:
      - "3306:3306"
```

**Ejecutar con Docker Compose**

```bash
docker-compose up
```

**Ejercicio Práctico**:

1. Crear un `Dockerfile` para la aplicación.
2. Configurar `docker-compose.yml` para levantar la aplicación y una base de datos MySQL.
3. Desplegar la aplicación en Heroku o AWS.

***

### 13. Temas Avanzados

#### 13.1. Uso de Caché con `@Cacheable`

**Configuración**

1.  **Habilitar el Caché**:

    ```java
    @SpringBootApplication
    @EnableCaching
    public class DemoApplication { ... }
    ```
2.  **Añadir Dependencia (por ejemplo, Ehcache)**:

    ```xml
    <dependency>
        <groupId>net.sf.ehcache</groupId>
        <artifactId>ehcache</artifactId>
    </dependency>
    ```

**Uso de `@Cacheable` en Métodos**

```java
@Service
public class UserService {

    @Cacheable("users")
    public User getUserById(Long id) {
        // Método que accede a la base de datos
    }
}
```

**Configuración del Caché**

En `ehcache.xml` o mediante propiedades.

#### 13.2. Integración con Servicios Externos mediante REST y `WebClient`

**Uso de `RestTemplate` (Sincrónico)**

```java
@RestController
public class ExternalApiController {

    @GetMapping("/api/data")
    public ResponseEntity<?> getData() {
        RestTemplate restTemplate = new RestTemplate();
        String resourceUrl = "https://api.externa.com/data";
        ResponseEntity<String> response = restTemplate.getForEntity(resourceUrl, String.class);
        return ResponseEntity.ok(response.getBody());
    }
}
```

**Uso de `WebClient` (Recomendado para aplicaciones reactivas)**

```java
@Service
public class ExternalService {

    private final WebClient webClient;

    public ExternalService(WebClient.Builder webClientBuilder) {
        this.webClient = webClientBuilder.baseUrl("https://api.externa.com").build();
    }

    public Mono<String> getData() {
        return webClient.get()
            .uri("/data")
            .retrieve()
            .bodyToMono(String.class);
    }
}
```

#### 13.3. Introducción a Microservicios y Spring Cloud

**Componentes de Spring Cloud**

* **Config Server**: Centraliza la configuración.
* **Eureka**: Registro y descubrimiento de servicios.
* **Ribbon**: Balanceo de carga.
* **Feign**: Cliente HTTP declarativo.
* **Zuul**: Puerta de enlace API.
* **Hystrix**: Tolerancia a fallos y circuit breaker.

**Ejemplo de Configuración de Eureka**

1.  **Agregar Dependencia**:

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
    ```
2.  **Habilitar Eureka Server**:

    ```java
    @SpringBootApplication
    @EnableEurekaServer
    public class EurekaServerApplication { ... }
    ```
3.  **Configurar Clientes**:

    ```properties
    eureka.client.service-url.defaultZone=http://localhost:8761/eureka/
    ```

**Ejercicio Práctico**:

1. Implementar caché en el servicio de `Producto`.
2. Crear un cliente que consume un API externo usando `WebClient`.
3. Explorar Spring Cloud y considerar cómo dividir la aplicación en microservicios.

***

### Conclusión

Has completado una inmersión profunda en **Spring Boot**, explorando desde los conceptos básicos hasta temas avanzados. Con las habilidades adquiridas, estás preparado para desarrollar aplicaciones robustas y escalables.

**Recomendaciones**:

* **Practicar**: Implementa proyectos personales para consolidar lo aprendido.
* **Actualizarte**: Spring Boot y sus componentes evolucionan; mantente al día con las últimas versiones.
* **Explorar**: Investiga otros módulos como Spring Batch, Spring Integration o Spring WebFlux.

**Recursos Adicionales**:

* [Documentación Oficial de Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)
* [Guías de Spring](https://spring.io/guides)
* [Comunidad de Spring en Stack Overflow](https://stackoverflow.com/questions/tagged/spring-boot)
