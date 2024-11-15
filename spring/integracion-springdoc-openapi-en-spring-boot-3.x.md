# Integración Springdoc OpenAPI en Spring Boot 3.X

Esta guía te ayudará a integrar **Springdoc OpenAPI** en un proyecto con **Spring Boot 3.x** para documentar tus APIs. Cubriremos desde la configuración básica hasta la seguridad con JWT, personalización de Swagger UI y más.

***

### **1. Configuración Básica**

#### **1.1. Agregar Dependencias al `pom.xml`**

Para empezar, necesitas agregar las siguientes dependencias a tu archivo `pom.xml`:

```xml
<dependencies>
    <!-- Dependencia principal de Springdoc OpenAPI -->
    <dependency>
        <groupId>org.springdoc</groupId>
        <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
        <version>2.2.0</version>
    </dependency>
    <!-- Dependencia para trabajar con JWT (opcional para seguridad) -->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-api</artifactId>
        <version>0.11.5</version>
    </dependency>
    <!-- Otras dependencias necesarias -->
    <!-- ... -->
</dependencies>
```

#### **1.2. Configurar Springdoc OpenAPI para Detección Automática de Endpoints**

Springdoc OpenAPI detecta automáticamente los endpoints expuestos por tus controladores REST. No se requiere configuración adicional para la detección básica.

#### **1.3. Ejemplo Básico de un Controlador REST**

```java
@RestController
@RequestMapping("/api/public")
public class PublicController {

    @GetMapping("/hello")
    public ResponseEntity<String> sayHello() {
        return ResponseEntity.ok("¡Hola, mundo!");
    }
}
```

Al iniciar tu aplicación, puedes acceder a la documentación en `http://localhost:8080/swagger-ui.html`.

***

### **2. Rutas Públicas y Privadas**

#### **2.1. Configurar Grupos de APIs (`GroupedOpenApi`)**

Crea una clase de configuración para definir los grupos de APIs:

```java
@Configuration
public class OpenApiConfig {

    @Bean
    public GroupedOpenApi publicApi() {
        return GroupedOpenApi.builder()
                .group("public")
                .pathsToMatch("/api/public/**")
                .build();
    }

    @Bean
    public GroupedOpenApi privateApi() {
        return GroupedOpenApi.builder()
                .group("private")
                .pathsToMatch("/api/private/**")
                .build();
    }
}
```

#### **2.2. Reflejo de Rutas en Swagger**

Al configurar grupos, Swagger UI mostrará pestañas separadas para cada grupo:

* **public**: Contiene endpoints como `/api/public/**`.
* **private**: Contiene endpoints como `/api/private/**`.

***

### **3. Seguridad con JWT**

#### **3.1. Configurar Seguridad para Rutas Privadas**

Configura la seguridad en tu aplicación:

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/api/public/**", "/swagger-ui.html", "/v3/api-docs/**").permitAll()
            .antMatchers("/api/private/**").authenticated()
            .and()
            .oauth2ResourceServer()
            .jwt();
    }
}
```

#### **3.2. Definir Esquema de Seguridad en OpenAPI**

Añade el esquema de seguridad en tu configuración de OpenAPI:

```java
@Bean
public OpenAPI customOpenAPI() {
    return new OpenAPI()
            .components(new Components()
                    .addSecuritySchemes("bearerAuth",
                            new SecurityScheme()
                                    .type(SecurityScheme.Type.HTTP)
                                    .scheme("bearer")
                                    .bearerFormat("JWT")))
            .addSecurityItem(new SecurityRequirement().addList("bearerAuth"));
}
```

#### **3.3. Swagger UI Permitiendo JWT**

Con esta configuración, Swagger UI mostrará un botón **"Authorize"** que permite ingresar un token JWT para probar los endpoints protegidos.

***

### **4. Personalización de Swagger UI**

#### **4.1. Personalizar Detalles de la API**

Modifica el método `customOpenAPI` para incluir información adicional:

```java
@Bean
public OpenAPI customOpenAPI() {
    return new OpenAPI()
            .info(new Info()
                    .title("Título de tu API")
                    .description("Descripción detallada de tu API.")
                    .version("1.0")
                    .termsOfService("https://www.tusitio.com/terminos")
                    .license(new License().name("Apache 2.0").url("http://springdoc.org")))
            // ... esquema de seguridad ...
            ;
}
```

#### **4.2. Cambiar la URL Predeterminada de Swagger UI**

En el archivo `application.properties` o `application.yml`, añade:

```properties
springdoc.swagger-ui.path=/docs
```

Ahora, Swagger UI estará disponible en `http://localhost:8080/docs`.

***

### **5. Documentación de Parámetros y Respuestas**

#### **5.1. Uso de Anotaciones para Documentar**

Ejemplo de un endpoint documentado:

```java
@RestController
@RequestMapping("/api/public")
public class PublicController {

    @Operation(summary = "Saluda al usuario", description = "Endpoint que saluda al usuario por su nombre.")
    @ApiResponses(value = {
        @ApiResponse(responseCode = "200", description = "Saludo exitoso", content = @Content(schema = @Schema(implementation = String.class))),
        @ApiResponse(responseCode = "400", description = "Solicitud incorrecta")
    })
    @GetMapping("/greet/{name}")
    public ResponseEntity<String> greet(
            @Parameter(description = "Nombre del usuario", example = "Juan")
            @PathVariable String name) {
        return ResponseEntity.ok("¡Hola, " + name + "!");
    }
}
```

#### **5.2. Reutilizar Respuestas Comunes con `Components`**

Define respuestas comunes en tu configuración:

```java
@Bean
public OpenAPI customOpenAPI() {
    return new OpenAPI()
            .components(new Components()
                    .addResponses("BadRequestResponse", new ApiResponse().description("Solicitud incorrecta"))
                    .addResponses("UnauthorizedResponse", new ApiResponse().description("No autorizado")))
            // ... demás configuraciones ...
            ;
}
```

Y reutilízalas en tus endpoints:

```java
@ApiResponses(value = {
    @ApiResponse(responseCode = "200", description = "Operación exitosa"),
    @ApiResponse(responseCode = "400", ref = "#/components/responses/BadRequestResponse"),
    @ApiResponse(responseCode = "401", ref = "#/components/responses/UnauthorizedResponse")
})
```

***

### **6. Ocultar Rutas No Deseadas**

Configura `pathsToExclude` en tus grupos de APIs:

```java
@Bean
public GroupedOpenApi publicApi() {
    return GroupedOpenApi.builder()
            .group("public")
            .pathsToMatch("/api/public/**")
            .pathsToExclude("/api/public/internal/**", "/api/public/health")
            .build();
}
```

***

### **7. Deshabilitar Swagger en Producción**

Utiliza perfiles de Spring para controlar la carga de Swagger:

```java
@Configuration
@Profile("!prod")
public class OpenApiConfig {
    // Configuración de OpenAPI
}
```

En tu entorno de producción, define el perfil `prod` para desactivar Swagger.

***

### **8. Ejemplo Final**

#### **8.1. Controlador con Rutas Públicas y Privadas**

```java
@RestController
@RequestMapping("/api")
public class ApiController {

    // Ruta pública
    @Operation(summary = "Obtener mensaje público")
    @GetMapping("/public/message")
    public ResponseEntity<String> publicMessage() {
        return ResponseEntity.ok("Este es un mensaje público.");
    }

    // Ruta privada
    @Operation(summary = "Obtener mensaje privado", security = { @SecurityRequirement(name = "bearerAuth") })
    @GetMapping("/private/message")
    public ResponseEntity<String> privateMessage() {
        return ResponseEntity.ok("Este es un mensaje privado.");
    }
}
```

#### **8.2. Configuración de Seguridad JWT**

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            // Configuración de seguridad
            .authorizeRequests()
            .antMatchers("/api/public/**", "/docs", "/v3/api-docs/**").permitAll()
            .antMatchers("/api/private/**").authenticated()
            .and()
            .oauth2ResourceServer()
            .jwt();
    }
}
```

#### **8.3. Documentación de Parámetros y Respuestas**

```java
@RestController
@RequestMapping("/api")
public class ApiController {

    @Operation(summary = "Obtener usuario por ID", responses = {
        @ApiResponse(responseCode = "200", description = "Operación exitosa", content = @Content(schema = @Schema(implementation = User.class))),
        @ApiResponse(responseCode = "400", ref = "#/components/responses/BadRequestResponse"),
        @ApiResponse(responseCode = "401", ref = "#/components/responses/UnauthorizedResponse")
    }, security = { @SecurityRequirement(name = "bearerAuth") })
    @GetMapping("/private/user/{id}")
    public ResponseEntity<User> getUserById(
            @Parameter(description = "ID del usuario", required = true)
            @PathVariable Long id) {
        // Lógica para obtener el usuario
        return ResponseEntity.ok(new User(id, "Nombre de Usuario"));
    }
}
```

#### **8.4. Personalización de Swagger UI**

```java
@Bean
public OpenAPI customOpenAPI() {
    return new OpenAPI()
            .info(new Info()
                    .title("API de Ejemplo")
                    .description("Documentación de la API de Ejemplo.")
                    .version("1.0.0")
                    .termsOfService("https://www.ejemplo.com/terminos")
                    .license(new License().name("MIT").url("https://opensource.org/licenses/MIT")))
            .components(new Components()
                    .addSecuritySchemes("bearerAuth",
                            new SecurityScheme()
                                    .type(SecurityScheme.Type.HTTP)
                                    .scheme("bearer")
                                    .bearerFormat("JWT"))
                    .addResponses("BadRequestResponse", new ApiResponse().description("Solicitud incorrecta"))
                    .addResponses("UnauthorizedResponse", new ApiResponse().description("No autorizado")))
            .addSecurityItem(new SecurityRequirement().addList("bearerAuth"));
}
```

Y en `application.properties`:

```properties
springdoc.swagger-ui.path=/docs
```

***

## **Conclusión**

Esta guía proporciona una visión completa de cómo integrar Springdoc OpenAPI en un proyecto con Spring Boot 3.x. Hemos cubierto desde la configuración básica hasta aspectos avanzados como seguridad con JWT y personalización de Swagger UI. Siguiendo estos pasos, podrás documentar eficazmente tus APIs y ofrecer una interfaz interactiva para los consumidores de tus servicios.

## **Recursos Adicionales**

* [Documentación oficial de Springdoc OpenAPI](https://springdoc.org/)
* [Guía de Spring Boot sobre Seguridad](https://spring.io/guides/gs/securing-web/)
