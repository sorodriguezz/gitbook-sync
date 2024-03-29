---
description: >-
  Esta documentación tienen la guía de la explicación de configuración e
  implementación de Spring Security 6, compatible con Spring Boot 3.
---

# Spring Security 6

## Explicación de Spring Security

Para comenzar daremos la explicación del flujo/arquitectura de Spring Security, para esto nos guiaremos con el siguiente diagrama:

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Comenzamos por la petición del usuarios (en el diagrama está señalado como <mark style="color:purple;">`HTTP REQUEST`</mark>) donde le realiza la petición al Controller de nuestro servicio.

Primero que todo, la petición pasará por la <mark style="color:purple;">`SECURITY FILTER CHAIN`</mark> el cual es una serie de filtros, en el cual pasa de filtro a filtro, si pasa todos los filtros (son 12 filtros aproximadamente). Para el <mark style="color:purple;">`DelegatingFilterProxy`</mark> es donde crearemos filtros personalizados para Spring Security, donde tendremos nuestras propias validaciones.

&#x20;Para personalizar nuestra condiciones haremos uso del <mark style="color:purple;">`Authentication Manager`</mark> pero esto a su vez necesita un proveedor de autenticación el cual esta representado como <mark style="color:purple;">`Authentication Provider`</mark> A y B (pueden ser más) en el diagrama. Esto depende de que forma nos vamos a autenticar cambiará una forma o la otra (con usuario de base de datos, oauth2, etc).

Para autenticarnos con base de datos necesitamos 2 componentes, el <mark style="color:purple;">`PasswordEncoder`</mark> y el <mark style="color:purple;">`UserDetailsService`</mark>. Donde el <mark style="color:purple;">`PasswordEncoder`</mark> se encargará de codificar las contraseñas y validar que estén correctas y el <mark style="color:purple;">`UserDetailsService`</mark> se encarga de conectar a la base de datos y extraer los usuarios (Puede ser la base de datos a elección, para estos ejemplos usaremos PostgreSQL).

Luego, si todo sale correctamente, el <mark style="color:purple;">`Authentication Provider`</mark> se comunica con el <mark style="color:purple;">`Authentication Manager`</mark> donde le avisa que si se encuentra en base de datos y la contraseña es correcta. Y luego el Authentication Manager le avisa al DelegatingFilterProxy, donde este se encarga de registrar al usuario que esta autenticado en el <mark style="color:purple;">`SECURITY CONTEXT HOLDER`</mark> y cuando volvamos a realizar una petición ya no nos pedirá autenticación porque el usuario ya esta registrado. Pero si el usuario no esta en la base de datos ni tiene su contraseña correcta, el <mark style="color:purple;">`Authentication Manager`</mark> le avisa al <mark style="color:purple;">`DelegatingFilterProxy`</mark> y retorna un <mark style="color:purple;">`HTTP RESPONSE`</mark> de no autorización (El típico 401).

En el <mark style="color:purple;">`Principal`</mark> es donde se guarda la información general del usuario y el <mark style="color:purple;">`Authorities`</mark> es donde se guardan los permisos.

## Creación del proyecto

Para crear nuestro proyecto haremos uso de <mark style="color:purple;">Spring Initialzr</mark> (Puede ser tanto el web como el que viene en IntelliJ)

Para este ejemplo tendrá lo siguiente:

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

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
        .addFilterBefore(new JwtTokenValidator(jwtUtils), BasicAuthenticationFilter.class) // se ejecuta antes del filtro de autenticación
        .build();
}
```

Donde puede realizar muchas configuraciones para las rutas.























