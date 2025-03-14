---
description: >-
  Describe las principales anotaciones utilizadas en aplicaciones basadas en
  Spring. Para cada anotación, se detalla su propósito, uso recomendado y un
  ejemplo simplificado.
---

# Anotaciones esenciales Spring Framework

## `@Component`

### **Descripción**

* Indica que una clase es un _bean_ genérico de Spring.
* Permite a Spring detectar dicha clase durante el escaneo de componentes (component scan) y administrarla dentro de su contenedor de inyección de dependencias.

### **Uso / Funcionalidad**

* Se utiliza para cualquier clase que deba formar parte del _contexto de Spring_ y que no encaje específicamente en otros estereotipos (`@Service`, `@Repository`, `@Controller`).
* Al marcar una clase con `@Component`, esta se convierte en un candidato a ser inyectado en otras clases mediante anotaciones como `@Autowired`.

### **Ejemplo**

```java
javaCopiarEditar@Component
public class MyBean {
    public void doSomething() {
        System.out.println("Realizando alguna operación genérica...");
    }
}
```

### **Buenas Prácticas**

* Se recomienda usar `@Component` únicamente cuando la clase no pertenezca a capas muy definidas (servicios, repositorios, controladores).
* Para mayor claridad, en lugar de `@Component`, es mejor usar anotaciones más específicas (`@Service`, `@Repository`) si corresponde.

## `@Service`

### **Descripción**

* Indica que la clase forma parte de la **capa de servicio** dentro de la arquitectura de la aplicación.
* Suele contener **lógica de negocio** o acciones que requieren una secuencia de operaciones más compleja que simplemente acceder a datos.

### **Uso / Funcionalidad**

* Spring la registra en su contenedor igual que un `@Component`, pero semánticamente indica que es un bean de **capa de servicio**.
* Facilita la lectura y el mantenimiento del código, ya que deja claro cuál es el rol de la clase.

### **Ejemplo**

```java
javaCopiarEditar@Service
public class UserService {
    
    // Se suele inyectar un repository para acceder a la base de datos
    @Autowired
    private UserRepository userRepository;
    
    public void createUser(User user) {
        // Lógica de negocio para crear un usuario
        userRepository.save(user);
    }
}
```

### **Buenas Prácticas**

* Centralizar la lógica de negocio compleja en las clases marcadas con `@Service`.
* Mantener las responsabilidades claras: la capa de servicio no debería encargarse de detalles de persistencia (propios de `@Repository`) ni directamente del flujo HTTP (propio de `@Controller`).

## `@Repository`

### **Descripción**

* Marca la clase como perteneciente a la **capa de acceso a datos (DAO)**.
* Indica que la clase maneja la interacción con la base de datos u otras fuentes de persistencia.

### **Uso / Funcionalidad**

* Spring la trata como un componente especializado para la **gestión de datos**.
* Junto con frameworks como **Spring Data JPA**, se utilizan interfaces marcadas con `@Repository` para proveer métodos de acceso a tablas o colecciones de datos.
* Spring gestiona automáticamente las excepciones de persistencia transformándolas en `DataAccessException`, cuando se usan repositorios marcados con `@Repository`.

### **Ejemplo**

```java
javaCopiarEditar@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // Métodos de consulta personalizados (si es necesario)
    // Por ejemplo:
    User findByUsername(String username);
}
```

### **Buenas Prácticas**

* Mantener la lógica de acceso a datos en el repositorio.
* Asegurarse de no mezclar lógica de negocio compleja en el repositorio.
* Usar métodos claros y bien nombrados, aprovechando el soporte de Spring Data JPA (si se está usando).

## `@Controller`

### **Descripción**

* Indica que la clase es un **controlador MVC** dentro de Spring.
* Maneja solicitudes y respuestas HTTP, devolviendo típicamente vistas (plantillas) como respuesta.

### **Uso / Funcionalidad**

* Junto con anotaciones como `@GetMapping`, `@PostMapping`, etc., define métodos que se asocian a rutas específicas de la aplicación.
* Suele “enviar” datos al _Model_ y retornar el nombre de la vista que se debe renderizar.

### **Ejemplo**

```java
javaCopiarEditar@Controller
public class MyController {
    
    @GetMapping("/home")
    public String homePage(Model model) {
        model.addAttribute("message", "Bienvenido a la página principal");
        return "home"; // Se resuelve con un ViewResolver, por ejemplo home.html
    }
}
```

### **Buenas Prácticas**

* Mantener la lógica de negocio fuera del controlador, delegándola a servicios (clases marcadas con `@Service`).
* Usar DTOs o modelos para pasar datos entre la capa de presentación y la lógica interna.

## `@RestController`

### **Descripción**

* Combina la funcionalidad de `@Controller` y de `@ResponseBody`.
* Indica que todos los métodos en la clase devolverán directamente el resultado (generalmente JSON o XML) en lugar de una plantilla/vista.

### **Uso / Funcionalidad**

* Adecuado para **APIs REST** o servicios que expongan datos para ser consumidos por clientes (front-end SPA, móviles, etc.).
* Simplifica la escritura de controladores REST, al no requerir anotar cada método con `@ResponseBody`.

### **Ejemplo**

```java
javaCopiarEditar@RestController
public class ApiController {
    
    @GetMapping("/api/users")
    public List<User> getAllUsers() {
        // Devuelve JSON automáticamente (o XML si está configurado)
        return userService.findAll();
    }
}
```

### **Buenas Prácticas**

* Usar `@RestController` en aquellas clases cuyo propósito sea **exponer endpoints REST**.
* Manejar excepciones personalizadas con `@ControllerAdvice` y `@ExceptionHandler` para mejorar la experiencia del cliente.

## `@Autowired`

### **Descripción**

* Realiza inyección de dependencias de forma automática en Spring.
* Permite que el framework resuelva el bean adecuado y lo “inyecte” en el atributo o constructor donde se coloque esta anotación.

### **Uso / Funcionalidad**

* Puede utilizarse en campos, constructores y métodos _setter_.
* Cuando hay un único bean que coincide con el tipo requerido, Spring lo inyecta directamente.
* Si hay varios candidatos, se puede usar `@Qualifier` para especificar cuál bean inyectar.

### **Ejemplo (inyección por campo)**

```java
javaCopiarEditar@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<User> findAll() {
        return userRepository.findAll();
    }
}
```

### **Ejemplo (inyección por constructor)**

```java
javaCopiarEditar@Service
public class UserService {
    
    private final UserRepository userRepository;

    public UserService(@Autowired UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    // ...
}
```

### **Buenas Prácticas**

* Generalmente se prefiere la **inyección por constructor**, ya que facilita la prueba unitaria y la inmutabilidad.
* Evitar el uso excesivo de inyección por campo, aunque en proyectos pequeños puede ser más simple.

## `@Qualifier`

### **Descripción**

* Permite especificar el **nombre del bean** a inyectar cuando existen varios candidatos del mismo tipo.
* Se usa junto con `@Autowired` para eliminar ambigüedades en la inyección de dependencias.

### **Uso / Funcionalidad**

* Se puede colocar en el campo o en el constructor para indicar el bean específico.
* Requiere definir un **nombre** o **alias** al bean, ya sea mediante la anotación (por ejemplo, `@Component("specificBean")`) o configurando su `bean name`.

### **Ejemplo**

```java
javaCopiarEditar@Component("specificBean")
public class SpecificBean implements MyInterface {
    // ...
}

@Component("anotherBean")
public class AnotherBean implements MyInterface {
    // ...
}

@Service
public class MyService {

    @Autowired
    @Qualifier("specificBean")
    private MyInterface myInterfaceBean;
    
    // ...
}
```

### **Buenas Prácticas**

* Usar nombres descriptivos para los beans, evitando confusiones futuras.
* Preferir la diferenciación por jerarquía de clases o interfaces (si es viable) antes que usar un `@Qualifier` por cada caso, para mantener ordenado el diseño.

## `@Value`

### **Descripción**

* Inyecta valores en campos de la clase desde **archivos de propiedades** (por ejemplo, `application.properties`), variables de entorno u otros orígenes.

### **Uso / Funcionalidad**

* Permite la **parametrización** de la aplicación sin necesidad de “quemar” valores en el código.
* Se pueden usar expresiones `SpEL` (Spring Expression Language) para mayor flexibilidad.

### **Ejemplo**

```java
javaCopiarEditar@Component
public class ConfigBean {

    @Value("${app.name}")
    private String appName;

    @Value("${app.version:1.0}") // Valor por defecto si no está en properties
    private String appVersion;

    public void printConfig() {
        System.out.println("Nombre: " + appName + ", Versión: " + appVersion);
    }
}
```

### **Buenas Prácticas**

* Centralizar configuraciones clave en `application.properties` o `application.yml`.
* Usar nombres de propiedades claros y coherentes con el contexto de la aplicación.
* Evitar exponer información sensible en propiedades sin medidas de seguridad (por ejemplo, cifrado o vaults).
