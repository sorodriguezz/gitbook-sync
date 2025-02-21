---
description: >-
  Esta documentación tienen la guía de la explicación de configuración e
  implementación de Spring Security 6, compatible con Spring Boot 3.
---

# Spring Security 6

## Explicación de Spring Security

Para comenzar daremos la explicación del flujo/arquitectura de Spring Security, para esto nos guiaremos con el siguiente diagrama:

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Comenzamos por la petición del usuarios (en el diagrama está señalado como <mark style="color:purple;">`HTTP REQUEST`</mark>) donde le realiza la petición al Controller de nuestro servicio.

Primero que todo, la petición pasará por la <mark style="color:purple;">`SECURITY FILTER CHAIN`</mark> el cual es una serie de filtros, en el cual pasa de filtro a filtro, si pasa todos los filtros (son 12 filtros aproximadamente). Para el <mark style="color:purple;">`DelegatingFilterProxy`</mark> es donde crearemos filtros personalizados para Spring Security, donde tendremos nuestras propias validaciones.

&#x20;Para personalizar nuestra condiciones haremos uso del <mark style="color:purple;">`Authentication Manager`</mark> pero esto a su vez necesita un proveedor de autenticación el cual esta representado como <mark style="color:purple;">`Authentication Provider`</mark> A y B (pueden ser más) en el diagrama. Esto depende de que forma nos vamos a autenticar cambiará una forma o la otra (con usuario de base de datos, oauth2, etc).

Para autenticarnos con base de datos necesitamos 2 componentes, el <mark style="color:purple;">`PasswordEncoder`</mark> y el <mark style="color:purple;">`UserDetailsService`</mark>. Donde el <mark style="color:purple;">`PasswordEncoder`</mark> se encargará de codificar las contraseñas y validar que estén correctas y el <mark style="color:purple;">`UserDetailsService`</mark> se encarga de conectar a la base de datos y extraer los usuarios (Puede ser la base de datos a elección, para estos ejemplos usaremos PostgreSQL).

Luego, si todo sale correctamente, el <mark style="color:purple;">`Authentication Provider`</mark> se comunica con el <mark style="color:purple;">`Authentication Manager`</mark> donde le avisa que si se encuentra en base de datos y la contraseña es correcta. Y luego el Authentication Manager le avisa al DelegatingFilterProxy, donde este se encarga de registrar al usuario que esta autenticado en el <mark style="color:purple;">`SECURITY CONTEXT HOLDER`</mark> y cuando volvamos a realizar una petición ya no nos pedirá autenticación porque el usuario ya esta registrado. Pero si el usuario no esta en la base de datos ni tiene su contraseña correcta, el <mark style="color:purple;">`Authentication Manager`</mark> le avisa al <mark style="color:purple;">`DelegatingFilterProxy`</mark> y retorna un <mark style="color:purple;">`HTTP RESPONSE`</mark> de no autorización (El típico 401).

En el <mark style="color:purple;">`Principal`</mark> es donde se guarda la información general del usuario y el <mark style="color:purple;">`Authorities`</mark> es donde se guardan los permisos.

## Creación del proyecto

Para crear nuestro proyecto haremos uso de <mark style="color:purple;">Spring Initialzr</mark> (Puede ser tanto el web como el que viene en IntelliJ)

Para este ejemplo tendrá lo siguiente:

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

Una vez tengamos el proyecto lo abrimos en IntelliJ o Eclipse (Editor que mas te guste, en este caso será IntelliJ ya que tiene una versión community que es gratis, si lo creaste en el sitio web descomprimes el .zip y lo abres con el IDE)

## Configuración de base de datos

Para este ejemplos, mencioné que usaremos PostgreSQL, el cual debemos configurar para conectarnos a el. Para esto vamos al archivo <mark style="color:purple;">**application.properties**</mark>&#x20;

El cual deberíamos poner lo siguiente:

```properties
# Database configuration
spring.jpa.hibernate.ddl-auto=create-drop
spring.datasource.url=jdbc:postgresql://localhost:5432/securitydb
spring.datasource.username=user
spring.datasource.password=password
spring.datasource.driver-class-name=org.postgresql.Driver
spring.jpa.show-sql=true
```

Remplaza la conexión y credenciales según lo que tengas. En este caso usaremos <mark style="color:purple;">create-drop</mark> para que cada vez que se levante el servidor se creen las tablas y cuando se baje destruya todo lo creado.

## Creación autorización básica

### Basic Auth con credenciales estáticas

Para comenzar, crearemos un controlador de pruebas. El cual se llamará <mark style="color:purple;">**TestAuthController**</mark> para esto debemos crear un directorio llamado <mark style="color:purple;">`controllers`</mark>, de la siguiente manera:

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Dentro de este controller crearemos el siguiente contenido:

```java
@RestController
@RequestMapping("/auth")
public class TestAuthController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello World";
    }
    
    @GetMapping("/hello-secured")
    public String helloSecured() {
        return "Hello World Secured";
    }
}
```

Donde un controller tendrá acceso publico y otro con seguridad. Tener en cuenta que desde que instalamos Spring Security en las dependencias de nuestro proyecto, ya comienza a evaluar los filtros por defecto del <mark style="color:purple;">**`SECURITY FILTER CHAIN`**</mark> . Por eso cada vez que tengamos que realizar una consulta, nos retornará 401 - Unauthorized.

Cuando levantemos el proyecto, en la consola de logs del IDE, nos mostrará una contraseña generada por defecto (esta contraseña va cambiando si se levanta o baja el servidor). Donde por defecto el usuario es **`user`**

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

Para probar la contraseña y usuario en **Postman**, debemos elegir <mark style="color:purple;">**`Basic Auth`**</mark> en la sección de <mark style="color:purple;">**`Authorization`**</mark>:

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

Para configurar estaticamente este usuario y contraseña podemos ir a nuestro archivo <mark style="color:purple;">**`application.properties`**</mark> nuevamente y agregar lo siguiente:

```properties
# Security config
spring.security.user.name=sorodriguezz
spring.security.user.password=1234
```

El cual serán nuestras credenciales personalizadas. Si intentamos nuevamente con estas contraseñas en **Postman**, funcionará la petición con los datos configurados.

Esta clase de autorización no es tan usada ya que solo permite el ingreso de un solo usuario, no permite múltiples usuarios ni separados por roles.

### Configurar Spring Security

Crearemos un directorio llamado <mark style="color:purple;">**`config`**</mark> en nuestro <mark style="color:purple;">**app**</mark>. Y dentro del directorio crearemos una clase llamada <mark style="color:purple;">**`SecurityConfig`**</mark>.

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

De acuerdo al diagrama mostrado con anterioridad, procedemos a configurar el `Security Filter Chain`, `Authentication Manager`, `Authentication Provider`, `PasswordEncoder` y `UserDetailsService`. Dentro de la clase de configuración creada, pondremos el siguiente código:

```java
@Configuration
@EnableWebSecurity // Activar seguridad web
@EnableMethodSecurity // Hacer configuraciones con notaciones
public class SecurityConfig {
    @Bean // Configuracion sin notaciones - Security Filter Chain
    public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
        return httpSecurity
                .csrf(csrf -> csrf.disable()) // sirve para vulnerabilidad para formulario, pero como no la usamos lo desactivamos
                .httpBasic(Customizer.withDefaults()) // solo cuando nos logueamos con user y pass
                .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)) //No se guardaranm sesiones en memoria, solo dependera de la expiracion del token.
                .authorizeHttpRequests(http -> {
                    // Configurar los endpoints publicos
                    http.requestMatchers(HttpMethod.GET, "/auth/get").permitAll();

                    // Configurar los endpoints privados
                    http.requestMatchers(HttpMethod.POST, "/auth/post").hasAnyRole("ADMIN", "DEVELOPER");
                    http.requestMatchers(HttpMethod.PATCH, "/auth/patch").hasAnyAuthority("REFACTOR");

                    // endp no especificados, no ingresan
                    http.anyRequest().denyAll();
                    // endp no especificados, se tienen que loguear
                    // http.anyRequest().authenticated();
                })
                .build(); // patrón builder
    }
    
    @Bean // Configuración Authentication Manager
    public AuthenticationManager authenticationManager(AuthenticationConfiguration authenticationConfiguration) throws Exception {
        return authenticationConfiguration.getAuthenticationManager();
    }
    
    @Bean // Authentication Provider
    public AuthenticationProvider authenticationProvider(){
        DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
        provider.setPasswordEncoder(passwordEncoder());
        provider.setUserDetailsService(userDetailService());
        return provider;
    }
    
    @Bean // Creación usuarios en memoria - Configuración UserDetailsService
    public UserDetailsService userDetailsService() {
        List<UserDetails> userDetailsList = new ArrayList<>();
        userDetailsList.add(User.withUsername("sorodriguezz")
            .password("1234")
            .roles("ADMIN")
            .authorities("READ", "CREATE")
            .build());
        userDetailsList.add(User.withUsername("sorodriguezz2")
            .password("12345")
            .roles("ADMIN")
            .authorities("READ")
            .build());
        return new InMemoryUserDetailsManager(userDetailsList);
    }
    
    @Bean // Configuración PassowordEncoder
    public PasswordEncoder passwordEncoder(){
        return NoOpPasswordEncoder.getInstance(); // solo para hacer pruebas, ya que no encripta las contraseñas
        //return new BCryptPasswordEncoder(); // encripta la contraseña, para producción
    }
}
```

Para este caso creamos los usuarios simulando la extracción desde base de datos. Ya posteriormente crearemos la conexión a base de datos para traer los usuarios.

Ahora podemos hacer esto mismo pero aprovechando el decorador <mark style="color:purple;">**@EnableMethodSecurity**</mark> que nos permite hacer las validaciones con decoradores en los controladores, como veremos en la siguiente sección. Esta parte es opcional para el final del proyecto.

#### Accesos con @EnableMethodSecurity (Opcional)

Para hacer uso de este decorador debemos cambiar nuestro <mark style="color:purple;">**SecurityFilterChain**</mark> de la siguiente manera (No se pueden ocupar las dos maneras al mismo tiempo, es una o la otra):

```java
// ...
@Bean // Uso con notaciones
public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
    return httpSecurity
        .csrf(csrf -> csrf.disable())
        .httpBasic(Customizer.withDefaults())
        .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
        .build();
}
// ...
```

Una vez hayamos cambiado el <mark style="color:purple;">**SecurityFilterChain**</mark>, debemos realizar la configuración de acceso directamente en nuestro controller. Para esto necesitamos hacer las siguientes configuraciones:

```java
@RestController
@RequestMapping("/auth")
@PreAuthorize("denyAll()") // se debe tener el @EnableMethodSecurity en el SecurityConfig.class
// denyAll por defecto rechaza todas las peticiones
public class TestAuthController {
    @GetMapping("/hello")
    @PreAuthorize("hasAuthority('READ')") // solo pueden consultarlos usuarios con permiso "READ"
    public String hello() {
        return "Hello World";
    }
    
    @GetMapping("/hello-secured")
    @PreAuthorize("hasRole('ADMIN')") // Solo pueden acceder usuarios con role "ADMIN"
    public String helloSecured() {
        return "Hello World Secured";
    }
}
```

### Spring Security y Base de Datos

Para traer los usuarios de base de datos necesitamos crear nuevos archivos y modificar un poco lo que ya existía.

#### Creación de entidades

Para comenzar, creamos un directorio **`persistences`** donde al interior crearemos otro directorio llamado **`entities`**, ahora crearemos el archivo <mark style="color:purple;">**`UserEntity`**</mark> dentro del directorio **`entities`** y agregaremos el siguiente contenido:

```java
@Setter
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "users")
public class UserEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(unique = true)
    private String username;
    private String password;

    @Column(name = "is_enable")
    private boolean isEnable;

    @Column(name = "account_No_Expired")
    private boolean accountNoExpired;

    @Column(name = "account_No_Locked")
    private boolean accountNoLocked;

    @Column(name = "credential_No_Expired")
    private boolean credentialNoExpired;

    @ManyToMany(fetch = FetchType.EAGER, cascade = CascadeType.ALL)
    @JoinTable(name = "user_roles", joinColumns = @JoinColumn(name = "user_id"), inverseJoinColumns = @JoinColumn(name = "role_id"))
    private Set<RoleEntity> roleEntities = new HashSet<>();
}
```

Ahora crearemos la entidad de **roles**, la cual crearemos dentro del directorio **`entities`** y le pondremos <mark style="color:purple;">**`RoleEntity`**</mark>:

```java
@Setter
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "roles")
public class RoleEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "role_name")
    @Enumerated(EnumType.STRING)
    private RoleEnum roleEnum;

    @ManyToMany(fetch = FetchType.EAGER, cascade = CascadeType.ALL)
    @JoinTable(name = "role_permissions", joinColumns = @JoinColumn(name = "role_id"), inverseJoinColumns = @JoinColumn(name = "permission_id"))
    private Set<PermissionEntity> permissionEntities = new HashSet<>();
}
```

Para nuestro campo <mark style="color:purple;">**`RoleEnum`**</mark> crearemos un archivo llamado <mark style="color:purple;">**`RoleEnum`**</mark> dentro del mismo directorios **`entities`** para este caso y tendrá lo siguiente:

```java
public enum RoleEnum {
    ADMIN,
    USER,
    INVITED,
    DEVELOPER
}
```

Ahora necesitamos crear los permisos, para esto crearemos el archivo **`PermissionEntity`** dentro del directorio **`entities`** y su contenido tendrá lo siguiente:

```java
@Setter
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "permissions")
public class PermissionEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(unique = true, nullable = false, updatable = false)
    private String name;
}
```

También puedes ocupar el _**name**_ de _**permission**_ como un enum, pero para este caso será un String.

#### Creación de usuarios en BD

Tenemos 2 opciones para usar usuarios de BD para la autentificación. Uno de ellos es la creación manual en base de datos usando scripts y la otra manera es crearlos con el mismo código. Usualmente siempre van a estar antes los usuarios en base de datos. Pero para hacer pruebas los crearemos apenas levante nuestro servidor.

Para esto necesitamos crear un directorio llamado **`repositories`** en el directorio **`app`** y crear la interfaz <mark style="color:purple;">**`UserRepository`**</mark> con el siguiente contenido:

```java
public interface UserRepository extends CrudRepository<UserEntity, Long> {
    Optional<UserEntity> findUserEntityByUsername(String username);
}
```

Y necesitamos uno para roles, que se llamará <mark style="color:purple;">**`RoleRepository`**</mark> con el siguiente contenido:

```java
@Repository
public interface RoleRepository extends CrudRepository<RoleEntity, Long> {
    List<RoleEntity> findRoleEntitiesByRoleEnumIn(List<String> roleName);
}
```

Ahora en nuestro archivo principal de Spring Boot llamado <mark style="color:purple;">**`SpringSecurityAppApplication`**</mark> pondremos el siguiente contenido:

```java
@SpringBootApplication
public class SpringSecurityAppApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringSecurityAppApplication.class, args);
    }

    @Bean
    CommandLineRunner init(UserRepository userRepository) {
        return args -> {
            /* CREATE PERMISSIONS */
            PermissionEntity createPermission = PermissionEntity.builder()
                    .name("CREATE")
                    .build();
            PermissionEntity readPermission = PermissionEntity.builder()
                    .name("READ")
                    .build();
            PermissionEntity updatePermission = PermissionEntity.builder()
                    .name("UPDATE")
                    .build();
            PermissionEntity deletePermission = PermissionEntity.builder()
                    .name("DELETE")
                    .build();
            PermissionEntity refactorPermission = PermissionEntity.builder()
                    .name("REFACTOR")
                    .build();
            /* CREATE ROLES */
            RoleEntity roleAdmin = RoleEntity
                    .builder()
                    .roleEnum(RoleEnum.ADMIN)
                    .permissionEntities(Set.of(createPermission, readPermission, updatePermission, deletePermission))
                    .build();
            RoleEntity roleUser = RoleEntity
                    .builder()
                    .roleEnum(RoleEnum.USER)
                    .permissionEntities(Set.of(createPermission, readPermission))
                    .build();
            RoleEntity roleInvited = RoleEntity
                    .builder()
                    .roleEnum(RoleEnum.INVITED)
                    .permissionEntities(Set.of(readPermission))
                    .build();
            RoleEntity roleDeveloper = RoleEntity
                    .builder()
                    .roleEnum(RoleEnum.DEVELOPER)
                    .permissionEntities(Set.of(createPermission, readPermission, updatePermission, deletePermission, refactorPermission))
                    .build();
            /* CREATE USERS*/
            UserEntity userSorodriguezz = UserEntity
                    .builder()
                    .username("sorodriguezz")
                    .password(new BCryptPasswordEncoder().encode("1234"))
                    .isEnable(true)
                    .accountNoExpired(true)
                    .accountNoLocked(true)
                    .credentialNoExpired(true)
                    .roleEntities(Set.of(roleAdmin))
                    .build();
            UserEntity userTest = UserEntity
                    .builder()
                    .username("usertest")
                    .password(new BCryptPasswordEncoder().encode("12345"))
                    .isEnable(true)
                    .accountNoExpired(true)
                    .accountNoLocked(true)
                    .credentialNoExpired(true)
                    .roleEntities(Set.of(roleUser))
                    .build();
            UserEntity userInvited = UserEntity
                    .builder()
                    .username("userinvited")
                    .password(new BCryptPasswordEncoder().encode("123"))
                    .isEnable(true)
                    .accountNoExpired(true)
                    .accountNoLocked(true)
                    .credentialNoExpired(true)
                    .roleEntities(Set.of(roleInvited))
                    .build();
            UserEntity userDev = UserEntity
                    .builder()
                    .username("userdev")
                    .password(new BCryptPasswordEncoder().encode("123456"))
                    .isEnable(true)
                    .accountNoExpired(true)
                    .accountNoLocked(true)
                    .credentialNoExpired(true)
                    .roleEntities(Set.of(roleDeveloper))
                    .build();

            userRepository.saveAll(List.of(userSorodriguezz, userDev, userInvited, userTest));
        };
    }
}
```

Donde esto creará los usuarios con sus respectivos roles y permisos en base de datos.

#### Configuración UserDetailsService para BD

Ahora según el diagrama, necesitamos hacer uso de nuestro <mark style="color:purple;">**`UserDetailsService`**</mark> desde base de datos, para esto necesitamos crear un servicio llamado <mark style="color:purple;">**`UserDetailServiceImpl`**</mark> y tendrá el siguiente contenido:

```java
@Service
public class UserDetailServiceImpl implements UserDetailsService {

    @Autowired
    private UserRepository userRepository;
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

        UserEntity userEntity = userRepository.findUserEntityByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("El usuario " + username + " no existe."));

        List<SimpleGrantedAuthority> authorityList = new ArrayList<>();

        // Spring Security diferencia los roles, deben empezar con la palabra "ROLE_"
        userEntity.getRoles()
                .forEach(role -> authorityList.add(new SimpleGrantedAuthority("ROLE_".concat(role.getRoleEnum().name()))));

        userEntity.getRoles().stream()
                .flatMap(role -> role.getPermissionList().stream())
                .forEach(permission -> authorityList.add(new SimpleGrantedAuthority(permission.getName())));

        return new User(userEntity.getUsername(), // retorna un usuario de Spring Security, por lo cual lo mapeamos
                userEntity.getPassword(),
                userEntity.isEnabled(),
                userEntity.isAccountNoExpired(),
                userEntity.isCredentialNoExpired(),
                userEntity.isAccountNoLocked(),
                authorityList);
    }
}
```

Una vez creado el servicio necesitamos inyectarlo en nuestro archivo <mark style="color:purple;">**`SecurityConfig`**</mark> para que obtenga los usuarios de BD. Para esto debemos modificar nuestro **`authenticationProvider`** de esta manera:

```java
// ...
@Bean
public AuthenticationProvider authenticationProvider(UserDetailServiceImpl userDetailService){
    DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
    provider.setPasswordEncoder(passwordEncoder());
    provider.setUserDetailsService(userDetailService);
    return provider;
}
// ...
```

Donde ya no le pasamos un método al set, sino que lo pasamos por parámetro.

Ahora debemos cambiar nuestro tipo de encriptación para la base de datos, para esto debemos modificar nuestro método <mark style="color:purple;">**`passwordEncoder()`**</mark> que esta en el **`SecurityConfig`**, debe quedar de esta manera:

<pre class="language-java"><code class="lang-java">// ...
<strong>public PasswordEncoder passwordEncoder() {
</strong>    // return NoOpPasswordEncoder.getInstance(); // solo para hacer pruebas, ya que no encripta las contraseñas
    return new BCryptPasswordEncoder(); // en producción
}
// ...
</code></pre>

Ahora modificaremos el controller que teniamos, para que tenga la siguiente estructura:

```java
@RestController
@RequestMapping("/method")
public class TestAuthController {

    @GetMapping("/hello")
    public String helloGet() {
        return "Hello World - GET";
    }

    @PostMapping("/hello")
    public String helloPost() {
        return "Hello World - POST";
    }

    @PutMapping("/hello")
    public String helloPut() {
        return "Hello World - PUT";
    }

    @DeleteMapping("/hello")
    public String helloDelete() {
        return "Hello World - DELETE";
    }

    @PatchMapping("/hello")
    public String helloPatch() {
        return "Hello World - PATCH";
    }
}
```

También necesitamos modificar el **`securityFilterChain`** que teníamos, donde lo dejaremos de la siguiente manera:

```java
@Bean // Configuracion sin notaciones - Security Filter Chain
public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
    return httpSecurity
        .csrf(csrf -> csrf.disable())
        .httpBasic(Customizer.withDefaults()) // solo cuando nos logueamos con user y pass
        .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
        .authorizeHttpRequests(http -> {
            http.requestMatchers(HttpMethod.POST, "/method/hello").hasAnyRole("ADMIN","DEVELOPER"); // endp privados
            http.requestMatchers(HttpMethod.PATCH, "/method/hello").hasAnyAuthority("REFACTOR"); // endp privados
            http.requestMatchers(HttpMethod.GET, "/method/hello").hasAnyRole("INVITED"); // endp privados
            
            // endp no especificados, no ingresan
            http.anyRequest().denyAll();
            // endp no especificados, se tienen que loguear
            // http.anyRequest().authenticated();
            })
        .build();
}
```

Donde puede realizar muchas configuraciones para las rutas.

## Agregando Token JWT

Para comenzar agregaremos al diagrama un nuevo filtro, el cual es <mark style="color:purple;">**`JwtTokenValidator`**</mark> , ahora al realizar una petición debe haber un filtro que valide el token para poder registrarlo en el <mark style="color:purple;">**`SECURITY CONTEXT HOLDER`**</mark> .

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

### Explicación JWT

Para comenzar tenemos la siguiente estructura de un JWT generico para esto usaremos la pagina [www.jwt.io ](https://jwt.io/)donde tenemos como muestra el siguiente token:

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Toda la cadena de la izquierda se genera con un algoritmo de encriptación, el cual en este caso usa **`HS256.`** El token se divide en 3 secciones, el cual en el _**header**_ estará la metada e información del token.

El **Payload** es el cuerpo del token donde guardamos la información, aquí guardamos la información que nosotros queramos, como permisos, nombres, roles, etc. Para **`JWT`** cada valor del **Payload** se llaman **`Claim`** .

Y finalmente tenemos la firma del token, el cual tiene las encriptaciones y una clave para validar si la clave del token es correcta.

### Integrando JwtTokenValidator

Para comenzar iremos a nuestro archivo <mark style="color:purple;">**`application.properties`**</mark> donde crearemos 2 propiedades, una para la key privada y otra para un usuario generador, esto deberia quedar de la siguiente manera:

```properties
spring.application.name=SpringSecurityApp

# Database configuration
spring.jpa.hibernate.ddl-auto=create-drop
spring.datasource.url=jdbc:postgresql://192.168.100.49:5432/securitydb
spring.datasource.username=user
spring.datasource.password=password
spring.datasource.driver-class-name=org.postgresql.Driver
spring.jpa.show-sql=true

# Security config
#spring.security.user.name=sorodriguezz
#spring.security.user.password=1234

# Nuevas propiedades
security.jwt.key.private=03ac674216f3e15c761ee1a5e255f067953623c8b388b4459e13f978d7c846f4
security.jwt.user.generator=AUTH0JWT-BACKEND
```

Para que un usuario pueda ingresar, debe pasar todos los filtros de **`Scurity Filter Chain`**, cada vez que un usuario realiza la petición va a un método llamado **`doFilter()`** el cual contiene lo siguiente:

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

Este es el listado de cadena de filtros que tiene cada vez que realizamos una petición. Ahora nosotros debemos agregar nuestro filtro haciendo uso de JWT.

Para esto debemos agregar las dependencias de JWT, para esto podemos ir a la pagina [https://jwt.io/libraries](https://jwt.io/libraries) para ver que librerías encontramos para JWT (Seleccionar en el filtro JAVA). Si hacemos clic en el repositorio (en este caso usaremos **`java-jwt`**) de la siguiente manera:

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

Ingresando al repositorio nos mostrará un README que nos mostrará como se integra y como se llama la dependencia.&#x20;

Debemos ir a nuestro **`pom.xml`** donde agregamos la dependencia e instalando la dependencia agregada, para este caso será:

```xml
<dependency>
  <groupId>com.auth0</groupId>
  <artifactId>java-jwt</artifactId>
  <version>4.4.0</version>
</dependency>
```

Ahora agregaremos un directorio llamado **util** en el directorio **app** y agregamos la clase **`JwtUtils`**, el cual tendrá el siguiente contenido:

```java
@Component
public class JwtUtils {
    @Value("${security.jwt.key.private}") // llama desde las .properties
    private String privateKey;

    @Value("${security.jwt.user.generator}") // llama desde las .properties
    private String userGenerator;

    public String createToken(Authentication authentication) {
        // encripta la key con un algoritmo criptografico
        Algorithm algorithm = Algorithm.HMAC256(this.privateKey);

        String username = authentication.getPrincipal().toString();
        // permisos a declarar, debe ir como "CREATE,UPDATE,DELETE"
        String authorities = authentication.getAuthorities()
                .stream()
                .map(GrantedAuthority::getAuthority) // .map(grantAuthoritie -> granAuthoritie.getAuthority())
                .collect(Collectors.joining(",")); // "CREATE,UPDATE,DELETE"
        String jwtToken = JWT.create() // crea jwt
                .withIssuer(this.userGenerator) // usuario que genera el token
                .withSubject(username) // usuario que se autentica
                .withClaim("authorities", authorities) // permisos en claims de jwt
                .withIssuedAt(new Date()) // fecha en que se genera el token
                .withExpiresAt(new Date(System.currentTimeMillis() + 1_800_000)) // tiempo de duracion del token en milisegundos, en este caso es media hora
                .withJWTId(UUID.randomUUID().toString()) // identificador unico del token
                .withNotBefore(new Date(System.currentTimeMillis())) // desde que momento se considera token valido en MS
                .sign(algorithm); // firma con algoritmo de encriptacion
        return jwtToken;
    }

    // Valida token según variables backend
    public DecodedJWT validateToken(String token) {
        try {
            Algorithm algorithm = Algorithm.HMAC256(this.privateKey);

            JWTVerifier verifier = JWT.require(algorithm)
                    .withIssuer(this.userGenerator) // usuario que genero token
                    .build();
            DecodedJWT decodedJWT = verifier.verify(token);
            return decodedJWT;
        } catch (JWTVerificationException exception) { // si el token es invalido cae en el catch
            throw new JWTVerificationException("Token invalid, not Authorized");
        }
    }

    // extrae usuario que viene dentro del token
    public String extractUsername(DecodedJWT decodedJWT) {
        return decodedJWT.getSubject().toString();
    }

    // sirve para extraer un claim especifico
    public Claim getSpecificClaim(DecodedJWT decodedJWT, String claimName) {
        return decodedJWT.getClaim(claimName);
    }

    // extrae todos los claims del token
    public Map<String, Claim> returnAllClaims(DecodedJWT decodedJWT) {
        return decodedJWT.getClaims();
    }
}
```

Ahora vamos a nuestro directorio **config** y creamos un directorio llamado **filter** donde en su interior crearemos nuestra clase **`JwtTokenValidator`** que tendrá el siguiente contenido:

```java
public class JwtTokenValidator extends OncePerRequestFilter { // ejecuta filter por cada request

    private final JwtUtils jwtUtils; // inyección por constructor de util

    public JwtTokenValidator(JwtUtils jwtUtils) {
        this.jwtUtils = jwtUtils;
    }

    @Override
    protected void doFilterInternal(
            @NotNull HttpServletRequest request,
            @NotNull HttpServletResponse response,
            @NotNull FilterChain filterChain) throws ServletException, IOException {
        String jwtToken = request.getHeader(HttpHeaders.AUTHORIZATION);

        if (jwtToken != null) { // si viene token
            jwtToken = jwtToken.substring(7); // elimita el Bearer
            DecodedJWT decodedJWT = jwtUtils.validateToken(jwtToken); // valida token

            String username = jwtUtils.extractUsername(decodedJWT); // extraer user
            String stringAuthorities = jwtUtils.getSpecificClaim(decodedJWT, "authorities").asString(); // extrae permisos como "READ,WRITE"

            Collection<? extends GrantedAuthority> authorities = AuthorityUtils.commaSeparatedStringToAuthorityList(stringAuthorities); // separa los permisos como coleccion

            SecurityContext context = SecurityContextHolder.getContext();
            Authentication authentication = new UsernamePasswordAuthenticationToken(username,null,authorities);

            context.setAuthentication(authentication); 
            SecurityContextHolder.setContext(context); // lo inserta en el SECURITY CONTEXT HOLDER
        }

        filterChain.doFilter(request, response); // continuar con el siguiente filtro
    }
}
```

Ahora vamos a nuestra clase **`SecurityConfig`** donde inyectamos nuestro jwtUtils de la siguiente manera:

<pre class="language-java"><code class="lang-java">@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {

    @Autowired
    private JwtUtils jwtUtils;

<strong>    // ...
</strong>}
</code></pre>

Ahora en la parte de **`securityFilterChain`** agregamos el nuevo filtro para que lo reconozca, esto de la siguiente manera:

```java
// ...
@Bean // Configuracion sin notaciones - Security Filter Chain
public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
    return httpSecurity
        .csrf(csrf -> csrf.disable())
        .httpBasic(Customizer.withDefaults()) // solo cuando nos logueamos con user y pass
        .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
        .authorizeHttpRequests(http -> {
            //                    http.requestMatchers(HttpMethod.GET, "/method/hello").permitAll(); // endp publicos
            http.requestMatchers(HttpMethod.POST, "/auth/**").permitAll(); // endp publicos
            
            http.requestMatchers(HttpMethod.POST, "/method/hello").hasAnyRole("ADMIN","DEVELOPER"); // endp privados
            http.requestMatchers(HttpMethod.PATCH, "/method/hello").hasAnyAuthority("REFACTOR"); // endp privados
            http.requestMatchers(HttpMethod.GET, "/method/hello").hasAnyRole("INVITED"); // endp privados
            
            // endp no especificados, no ingresan
            http.anyRequest().denyAll();
            // endp no especificados, se tienen que loguear
            // http.anyRequest().authenticated();
            })
        .addFilterBefore(new JwtTokenValidator(jwtUtils), BasicAuthenticationFilter.class) // se ejecuta antes del filtro de basic autenticación
        .build();
}
// ...
```

Ahora para hacer que el usuario se pueda loguear, crearemos un nuevo controlador, para esto en el directorio **controller** crearemos el **`AuthenticationController`**, el cual tendrá el siguiente contenido:

```java
@RestController
@RequestMapping("auth")
public class AuthenticationController {

    @Autowired
    private UserDetailServiceImpl userDetailService;

    @PostMapping("sign-up")
    public ResponseEntity<AuthResponse> register(@RequestBody @Valid AuthCreateUser authCreateUser) {
        return new ResponseEntity<>(this.userDetailService.createUser(authCreateUser), HttpStatus.CREATED);
    }

    @PostMapping("/log-in")
    public ResponseEntity<AuthResponse> login(@RequestBody @Valid AuthLoginRequest userRequest) {
        return new ResponseEntity<AuthResponse>(this.userDetailService.loginUser(userRequest), HttpStatus.OK);
    }
}
```

En nuestro directorio **controller** crearemos un directorio llamado **dto**, que tenga 4 records, los cuales serán los siguientes con sus respectivos contenidos:

```java
// AuthResponse.class
@JsonPropertyOrder({"username","message","jwt","status"}) // para dar orden a la respuesta
public record AuthResponse(String username, String message, String jwt, boolean status) {
}
```

```java
// AuthCreateRoleRequest.class
@Validated
public record AuthCreateRoleRequest(
        @Size(max = 3, message = "The user cannot have more than 3 roles") List<String> roleListName) {
}
```

```java
// AuthCreateUser.class
public record AuthCreateUser(
        @NotBlank String username,
        @NotBlank String password,
        @Valid AuthCreateRoleRequest roleRequest) {
}
```

```java
// AuthLoginRequest.class
public record AuthLoginRequest(@NotBlank String username, String password) {
}
```

La estructura de nuestros directorios y archivos se debería ver así:

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

Ahora nuestro servicio de **UserDetailServiceImpl** debe modificarse para tener el siguiente contenido:

```java
@Service
public class UserDetailServiceImpl implements UserDetailsService {

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Autowired
    private JwtUtils jwtUtils;

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private RoleRepository roleRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        UserEntity userEntity = userRepository.findUserEntityByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("El usuario " + username + " no existe"));

        /* Mapeo para Spring Security */
        List<SimpleGrantedAuthority> authorityList = new ArrayList<>();

        userEntity.getRoleEntities()
                .forEach(role -> authorityList.add(new SimpleGrantedAuthority("ROLE_".concat(role.getRoleEnum().name()))));

        userEntity.getRoleEntities().stream()
                .flatMap(role -> role.getPermissionEntities().stream())
                .forEach(permission -> authorityList.add(new SimpleGrantedAuthority(permission.getName())));

        return new User(
                userEntity.getUsername(),
                userEntity.getPassword(),
                userEntity.isEnable(),
                userEntity.isAccountNoExpired(),
                userEntity.isCredentialNoExpired(),
                userEntity.isAccountNoLocked(),
                authorityList
        );
    }

    public AuthResponse loginUser(AuthLoginRequest authLoginRequest) {
        String username = authLoginRequest.username();
        String password = authLoginRequest.password();

        Authentication authentication = this.authenticate(username, password);
        SecurityContextHolder.getContext().setAuthentication(authentication);

        String accessToken = jwtUtils.createToken(authentication);

        AuthResponse authResponse = new AuthResponse(username, "User loged successfuly", accessToken, true);

        return authResponse;
    }

    public Authentication authenticate(String username, String password) {
        UserDetails userDetails = this.loadUserByUsername(username);

        if (userDetails == null) {
            throw new BadCredentialsException("Invalid username or password");
        }

        if (!passwordEncoder.matches(password, userDetails.getPassword())) {
            throw new BadCredentialsException("Invalid password");
        }

        return new UsernamePasswordAuthenticationToken(username, userDetails.getPassword(), userDetails.getAuthorities());
    }

    public AuthResponse createUser(AuthCreateUser authCreateRoleRequest) {
        String username = authCreateRoleRequest.username();
        String password = authCreateRoleRequest.password();
        List<String> roleRequest = authCreateRoleRequest.roleRequest().roleListName();

        Set<RoleEntity> roleEntitySet = new HashSet<>(roleRepository.findRoleEntitiesByRoleEnumIn(roleRequest));

        if (roleEntitySet.isEmpty()) {
            throw new IllegalArgumentException("The roles specified does not exist.");
        }

        UserEntity userEntity = UserEntity.builder().username(username).password(passwordEncoder.encode(password)).roleEntities(roleEntitySet).isEnable(true).accountNoLocked(true).accountNoExpired(true).credentialNoExpired(true).build();

        UserEntity userCreated = userRepository.save(userEntity);

        ArrayList<SimpleGrantedAuthority> authorityList = new ArrayList<>();

        userCreated.getRoleEntities()
                .forEach(role -> authorityList.add(new SimpleGrantedAuthority("ROLE_".concat(role.getRoleEnum().name()))));

        userCreated.getRoleEntities()
                .stream()
                .flatMap(role -> role.getPermissionEntities().stream())
                .forEach(permission -> authorityList.add(new SimpleGrantedAuthority(permission.getName())));

        Authentication authentication = new UsernamePasswordAuthenticationToken(
                userCreated.getUsername(),
                userCreated.getPassword(),
                authorityList);

        String accessToken = jwtUtils.createToken(authentication);

        AuthResponse authResponse = new AuthResponse(
                userCreated.getUsername(),
                "User created successfuly",
                accessToken,
                true);

        return authResponse;
    }
}
```

Ahora cada vez que realicemos una petición a otra ruta protegida, debemos generar un token con un usuario registrado de la siguiente manera:

<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

Si intentamos hacer una petición a una ruta protegida nos arrojará 401 como el siguiente ejemplo:

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Pero si le pasamos el token generado anteriormente como Bearer, nos retornará lo siguiente exitosamente:

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Ahora como creamos nuestros servicio para registrar un usuario y un controller para esto lo probamos de la siguiente manera:

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Ahora si pegamos el token generado al momento de registrar el usuario podemos revisar el token en [https://jwt.io/](https://jwt.io/) y ver lo siguiente:

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Donde dice que se creo con el rol de ADMIN, en base de dato igual podemos verlo de la siguiente manera:

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Donde lo vemoa creado y con sus respectivos permisos y rol.

### cURL Postman

```sh
# log-in
curl --location 'localhost:8080/auth/log-in' \
--header 'Content-Type: application/json' \
--data '{
    "username": "sorodriguezz",
    "password": "1234"
}'
```

```sh
# Sign-up
curl --location 'localhost:8080/auth/sign-up' \
--header 'Content-Type: application/json' \
--data '{
    "username": "test3",
    "password": "111",
    "roleRequest": {
        "roleListName": [
            "ADMIN"
        ]
    }
}'
```



## Enlace del repositorio

[https://github.com/sorodriguezz/spring-security6-test](https://github.com/sorodriguezz/spring-security6-test)
