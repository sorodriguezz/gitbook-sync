# Patrones de Diseño con Java

¡Bienvenido a esta clase completa sobre **Patrones de Diseño** en Java! En esta guía exhaustiva, exploraremos en profundidad los patrones de diseño más importantes, cómo aplicarlos en Java y cómo pueden mejorar la arquitectura y mantenibilidad de tus aplicaciones.

***

### Tabla de Contenidos

1. Introducción a los Patrones de Diseño
2. Patrones Creacionales
   * 2.1. Singleton
   * 2.2. Factory Method
   * 2.3. Abstract Factory
   * 2.4. Builder
   * 2.5. Prototype
3. Patrones Estructurales
   * 3.1. Adapter
   * 3.2. Bridge
   * 3.3. Composite
   * 3.4. Decorator
   * 3.5. Facade
   * 3.6. Flyweight
   * 3.7. Proxy
4. Patrones de Comportamiento
   * 4.1. Chain of Responsibility
   * 4.2. Command
   * 4.3. Iterator
   * 4.4. Mediator
   * 4.5. Memento
   * 4.6. Observer
   * 4.7. State
   * 4.8. Strategy
   * 4.9. Template Method
   * 4.10. Visitor
5. Buenas Prácticas en el Uso de Patrones de Diseño

***

### 1. Introducción a los Patrones de Diseño

#### ¿Qué son los Patrones de Diseño?

Los **Patrones de Diseño** son soluciones probadas y reutilizables a problemas comunes en el diseño de software. No son código específico, sino plantillas o guías que se pueden aplicar para resolver problemas en diferentes contextos.

#### Clasificación de los Patrones de Diseño

Según el libro _"Design Patterns: Elements of Reusable Object-Oriented Software"_ de los _Gang of Four_ (GoF), los patrones se clasifican en:

* **Patrones Creacionales**: Tratan con la creación de objetos.
* **Patrones Estructurales**: Se ocupan de la composición de clases y objetos.
* **Patrones de Comportamiento**: Se centran en las interacciones y responsabilidades entre objetos.

#### ¿Por qué son Importantes?

* **Reutilización de Soluciones**: Evitan reinventar la rueda.
* **Comunicación Efectiva**: Proporcionan un lenguaje común entre desarrolladores.
* **Mejora de la Arquitectura**: Promueven código más flexible y mantenible.

***

### 2. Patrones Creacionales

Los patrones creacionales se enfocan en la manera de crear objetos, ocultando la lógica de creación y haciendo el sistema independiente de cómo se crean, componen y representan los objetos.

#### 2.1. Singleton

**Definición**

El patrón **Singleton** asegura que una clase tenga una única instancia y proporciona un punto de acceso global a ella.

**Implementación en Java**

```java
public class Singleton {
    private static Singleton instancia;

    private Singleton() {
        // Constructor privado
    }

    public static Singleton getInstancia() {
        if (instancia == null) {
            instancia = new Singleton();
        }
        return instancia;
    }
}
```

**Ejemplo Práctico**

Supongamos que necesitamos una clase de `Logger` que se use en toda la aplicación:

```java
public class Logger {
    private static Logger instancia;

    private Logger() { }

    public static Logger getInstancia() {
        if (instancia == null) {
            instancia = new Logger();
        }
        return instancia;
    }

    public void log(String mensaje) {
        System.out.println("[LOG]: " + mensaje);
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        Logger logger = Logger.getInstancia();
        logger.log("Aplicación iniciada");
    }
}
```

**Consideraciones**

*   **Hilos Seguros (Thread-Safe):** En aplicaciones concurrentes, es necesario asegurar que solo una instancia sea creada incluso en entornos multi-hilo.

    ```java
    public class Singleton {
        private static Singleton instancia;

        private Singleton() { }

        public static synchronized Singleton getInstancia() {
            if (instancia == null) {
                instancia = new Singleton();
            }
            return instancia;
        }
    }
    ```
* **Inicialización Temprana vs. Perezosa:** La instancia puede ser creada al cargar la clase o cuando se solicita por primera vez.

**Ejercicio**

Implementa un Singleton hilos-seguro utilizando el patrón **Double-Checked Locking**.

**Solución**

```java
public class Singleton {
    private static volatile Singleton instancia;

    private Singleton() { }

    public static Singleton getInstancia() {
        if (instancia == null) {
            synchronized (Singleton.class) {
                if (instancia == null) {
                    instancia = new Singleton();
                }
            }
        }
        return instancia;
    }
}
```

***

#### 2.2. Factory Method

**Definición**

El patrón **Factory Method** define una interfaz para crear objetos, pero permite a las subclases decidir qué clase instanciar. Delega la instanciación a subclases.

**Estructura**

* **Producto:** Interfaz o clase abstracta que define el tipo de objetos que el factory method crea.
* **Creador:** Clase que declara el factory method y puede ser abstracta o concreta.
* **Productos Concretos:** Implementaciones concretas del producto.
* **Creadores Concretos:** Subclases del creador que implementan el factory method.

**Implementación en Java**

**Interfaz Producto:**

```java
public interface Producto {
    void operacion();
}
```

**Productos Concretos:**

```java
public class ProductoConcretoA implements Producto {
    public void operacion() {
        System.out.println("Operación de Producto A");
    }
}

public class ProductoConcretoB implements Producto {
    public void operacion() {
        System.out.println("Operación de Producto B");
    }
}
```

**Clase Creadora:**

```java
public abstract class Creador {
    public abstract Producto factoryMethod();

    public void operacion() {
        Producto producto = factoryMethod();
        producto.operacion();
    }
}
```

**Creadores Concretos:**

```java
public class CreadorConcretoA extends Creador {
    public Producto factoryMethod() {
        return new ProductoConcretoA();
    }
}

public class CreadorConcretoB extends Creador {
    public Producto factoryMethod() {
        return new ProductoConcretoB();
    }
}
```

**Ejemplo Práctico**

**Escenario:** Una aplicación que maneja diferentes tipos de notificaciones: Email y SMS.

**Interfaz Notificación:**

```java
public interface Notificacion {
    void enviar(String mensaje);
}
```

**Implementaciones Concretas:**

```java
public class NotificacionEmail implements Notificacion {
    public void enviar(String mensaje) {
        System.out.println("Enviando Email: " + mensaje);
    }
}

public class NotificacionSMS implements Notificacion {
    public void enviar(String mensaje) {
        System.out.println("Enviando SMS: " + mensaje);
    }
}
```

**Creador Abstracto:**

```java
public abstract class NotificacionFactory {
    public abstract Notificacion crearNotificacion();

    public void notificar(String mensaje) {
        Notificacion notificacion = crearNotificacion();
        notificacion.enviar(mensaje);
    }
}
```

**Factories Concretas:**

```java
public class EmailFactory extends NotificacionFactory {
    public Notificacion crearNotificacion() {
        return new NotificacionEmail();
    }
}

public class SMSFactory extends NotificacionFactory {
    public Notificacion crearNotificacion() {
        return new NotificacionSMS();
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        NotificacionFactory factory = new EmailFactory();
        factory.notificar("Hola por Email");

        factory = new SMSFactory();
        factory.notificar("Hola por SMS");
    }
}
```

**Ventajas**

* **Desacopla la creación de objetos de su uso.**
* **Facilita la extensión al agregar nuevos productos sin modificar el código existente.**

**Ejercicio**

Implementa una fábrica que cree diferentes tipos de documentos (`WordDocument`, `PDFDocument`) y una interfaz común `Documento`.

***

#### 2.3. Abstract Factory

**Definición**

El patrón **Abstract Factory** proporciona una interfaz para crear familias de objetos relacionados o dependientes sin especificar sus clases concretas.

**Estructura**

* **Abstract Factory:** Declara un conjunto de métodos para crear productos abstractos.
* **Concrete Factory:** Implementa los métodos para crear productos concretos.
* **Abstract Product:** Interfaz para un tipo de producto.
* **Concrete Product:** Implementaciones concretas del producto.

**Ejemplo Práctico**

**Escenario:** Aplicación que soporta temas (skins) diferentes (Claro y Oscuro).

**Productos Abstractos:**

```java
public interface Boton {
    void render();
}

public interface CheckBox {
    void render();
}
```

**Productos Concretos:**

```java
// Tema Claro
public class BotonClaro implements Boton {
    public void render() {
        System.out.println("Renderizando Botón Claro");
    }
}

public class CheckBoxClaro implements CheckBox {
    public void render() {
        System.out.println("Renderizando CheckBox Claro");
    }
}

// Tema Oscuro
public class BotonOscuro implements Boton {
    public void render() {
        System.out.println("Renderizando Botón Oscuro");
    }
}

public class CheckBoxOscuro implements CheckBox {
    public void render() {
        System.out.println("Renderizando CheckBox Oscuro");
    }
}
```

**Abstract Factory:**

```java
public interface GUIFactory {
    Boton crearBoton();
    CheckBox crearCheckBox();
}
```

**Factories Concretas:**

```java
public class TemaClaroFactory implements GUIFactory {
    public Boton crearBoton() {
        return new BotonClaro();
    }

    public CheckBox crearCheckBox() {
        return new CheckBoxClaro();
    }
}

public class TemaOscuroFactory implements GUIFactory {
    public Boton crearBoton() {
        return new BotonOscuro();
    }

    public CheckBox crearCheckBox() {
        return new CheckBoxOscuro();
    }
}
```

**Uso:**

```java
public class Aplicacion {
    private Boton boton;
    private CheckBox checkBox;

    public Aplicacion(GUIFactory factory) {
        boton = factory.crearBoton();
        checkBox = factory.crearCheckBox();
    }

    public void render() {
        boton.render();
        checkBox.render();
    }
}

public class Main {
    public static void main(String[] args) {
        GUIFactory factory;
        String tema = "oscuro"; // podría venir de configuración

        if (tema.equals("claro")) {
            factory = new TemaClaroFactory();
        } else {
            factory = new TemaOscuroFactory();
        }

        Aplicacion app = new Aplicacion(factory);
        app.render();
    }
}
```

**Ventajas**

* **Aísla las familias de productos concretos.**
* **Facilita el intercambio de familias completas de productos.**

**Ejercicio**

Implementa una `Abstract Factory` para crear diferentes tipos de bases de datos (`MySQL`, `Oracle`) con operaciones comunes.

***

#### 2.4. Builder

**Definición**

El patrón **Builder** separa la construcción de un objeto complejo de su representación, permitiendo crear diferentes representaciones.

**Estructura**

* **Builder:** Interfaz que define los pasos para construir el producto.
* **Concrete Builder:** Implementa los pasos para construir una representación concreta.
* **Director:** Construye un objeto usando el Builder.
* **Producto:** Objeto complejo que se construye.

**Ejemplo Práctico**

**Escenario:** Construcción de diferentes tipos de casas (`CasaModerna`, `CasaClasica`).

**Producto:**

```java
public class Casa {
    private String paredes;
    private String techo;
    private String piso;

    // Getters y Setters

    @Override
    public String toString() {
        return "Casa con " + paredes + ", " + techo + " y " + piso;
    }
}
```

**Builder:**

```java
public interface CasaBuilder {
    void construirParedes();
    void construirTecho();
    void construirPiso();
    Casa getCasa();
}
```

**Concrete Builders:**

```java
public class CasaModernaBuilder implements CasaBuilder {
    private Casa casa;

    public CasaModernaBuilder() {
        this.casa = new Casa();
    }

    public void construirParedes() {
        casa.setParedes("Paredes de vidrio");
    }

    public void construirTecho() {
        casa.setTecho("Techo plano");
    }

    public void construirPiso() {
        casa.setPiso("Piso de mármol");
    }

    public Casa getCasa() {
        return this.casa;
    }
}

public class CasaClasicaBuilder implements CasaBuilder {
    private Casa casa;

    public CasaClasicaBuilder() {
        this.casa = new Casa();
    }

    public void construirParedes() {
        casa.setParedes("Paredes de ladrillo");
    }

    public void construirTecho() {
        casa.setTecho("Techo a dos aguas");
    }

    public void construirPiso() {
        casa.setPiso("Piso de madera");
    }

    public Casa getCasa() {
        return this.casa;
    }
}
```

**Director:**

```java
public class Ingeniero {
    private CasaBuilder builder;

    public Ingeniero(CasaBuilder builder) {
        this.builder = builder;
    }

    public Casa construirCasa() {
        builder.construirParedes();
        builder.construirTecho();
        builder.construirPiso();
        return builder.getCasa();
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        CasaBuilder builder = new CasaModernaBuilder();
        Ingeniero ingeniero = new Ingeniero(builder);
        Casa casaModerna = ingeniero.construirCasa();
        System.out.println(casaModerna);

        builder = new CasaClasicaBuilder();
        ingeniero = new Ingeniero(builder);
        Casa casaClasica = ingeniero.construirCasa();
        System.out.println(casaClasica);
    }
}
```

**Ventajas**

* **Construcción paso a paso.**
* **Permite crear diferentes representaciones de un objeto.**
* **Más control sobre el proceso de construcción.**

**Ejercicio**

Implementa un `Builder` para crear una `Pizza` con diferentes ingredientes (masa, salsa, toppings).

***

#### 2.5. Prototype

**Definición**

El patrón **Prototype** permite crear nuevos objetos copiando una instancia existente, evitando la creación desde cero.

**Implementación en Java**

Utiliza el método `clone()` de la interfaz `Cloneable`.

```java
public class Prototipo implements Cloneable {
    private String atributo;

    public Prototipo(String atributo) {
        this.atributo = atributo;
    }

    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    // Getters y Setters
}
```

**Ejemplo Práctico**

**Escenario:** Crear múltiples instancias de soldados en un juego.

```java
public class Soldado implements Cloneable {
    private String tipoArma;
    private int nivel;

    public Soldado(String tipoArma, int nivel) {
        this.tipoArma = tipoArma;
        this.nivel = nivel;
    }

    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    // Getters y Setters

    @Override
    public String toString() {
        return "Soldado con arma " + tipoArma + " y nivel " + nivel;
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        try {
            Soldado soldadoOriginal = new Soldado("Espada", 5);
            Soldado soldadoClon = (Soldado) soldadoOriginal.clone();

            soldadoClon.setNivel(6);

            System.out.println(soldadoOriginal);
            System.out.println(soldadoClon);
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
    }
}
```

**Ventajas**

* **Reduce el costo de creación de objetos.**
* **Facilita la clonación de objetos complejos.**

**Ejercicio**

Crea un `Prototype` de un `Documento` que pueda clonarse y modificar ciertas propiedades.

***

### 3. Patrones Estructurales

Los patrones estructurales se ocupan de la composición de clases y objetos para formar estructuras más grandes.

#### 3.1. Adapter

**Definición**

El patrón **Adapter** permite que clases con interfaces incompatibles trabajen juntas, convirtiendo la interfaz de una clase en otra esperada por el cliente.

**Estructura**

* **Target:** Interfaz esperada por el cliente.
* **Adapter:** Adapta la interfaz de Adaptee al Target.
* **Adaptee:** Clase existente con una interfaz incompatible.
* **Client:** Utiliza la interfaz Target.

**Ejemplo Práctico**

**Escenario:** Una aplicación que necesita reproducir audio en formatos antiguos y nuevos.

**Target Interface:**

```java
public interface ReproductorAudio {
    void reproducir(String tipoAudio, String nombreArchivo);
}
```

**Adaptee:**

```java
public class ReproductorMP3 {
    public void reproducirMP3(String nombreArchivo) {
        System.out.println("Reproduciendo MP3: " + nombreArchivo);
    }
}
```

**Adapter:**

```java
public class AdaptadorAudio implements ReproductorAudio {
    private ReproductorMP3 reproductorMP3;

    public AdaptadorAudio() {
        reproductorMP3 = new ReproductorMP3();
    }

    public void reproducir(String tipoAudio, String nombreArchivo) {
        if (tipoAudio.equalsIgnoreCase("mp3")) {
            reproductorMP3.reproducirMP3(nombreArchivo);
        } else {
            System.out.println("Formato no soportado: " + tipoAudio);
        }
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        ReproductorAudio reproductor = new AdaptadorAudio();
        reproductor.reproducir("mp3", "cancion.mp3");
        reproductor.reproducir("wav", "sonido.wav");
    }
}
```

**Ventajas**

* **Permite la reutilización de código existente.**
* **Desacopla el cliente de las clases concretas.**

**Ejercicio**

Implementa un `Adapter` que permita a una aplicación de dibujo utilizar librerías de gráficos antiguas y nuevas.

***

#### 3.2. Bridge

**Definición**

El patrón **Bridge** separa la abstracción de su implementación, permitiendo que ambas varíen independientemente.

**Estructura**

* **Abstraction:** Interfaz de alto nivel que contiene una referencia a Implementor.
* **Refined Abstraction:** Extiende la Abstraction.
* **Implementor:** Interfaz para implementaciones.
* **Concrete Implementor:** Implementaciones concretas de Implementor.

**Ejemplo Práctico**

**Escenario:** Dispositivos y sus controles remotos.

**Implementor:**

```java
public interface Dispositivo {
    void encender();
    void apagar();
    void ajustarVolumen(int nivel);
    boolean estaEncendido();
}
```

**Concrete Implementors:**

```java
public class TV implements Dispositivo {
    private boolean encendido = false;
    private int volumen = 30;

    public void encender() {
        encendido = true;
        System.out.println("TV encendida");
    }

    public void apagar() {
        encendido = false;
        System.out.println("TV apagada");
    }

    public void ajustarVolumen(int nivel) {
        volumen = nivel;
        System.out.println("Volumen de TV ajustado a " + volumen);
    }

    public boolean estaEncendido() {
        return encendido;
    }
}

public class Radio implements Dispositivo {
    private boolean encendido = false;
    private int volumen = 20;

    public void encender() {
        encendido = true;
        System.out.println("Radio encendida");
    }

    public void apagar() {
        encendido = false;
        System.out.println("Radio apagada");
    }

    public void ajustarVolumen(int nivel) {
        volumen = nivel;
        System.out.println("Volumen de Radio ajustado a " + volumen);
    }

    public boolean estaEncendido() {
        return encendido;
    }
}
```

**Abstraction:**

```java
public class ControlRemoto {
    protected Dispositivo dispositivo;

    public ControlRemoto(Dispositivo dispositivo) {
        this.dispositivo = dispositivo;
    }

    public void encender() {
        dispositivo.encender();
    }

    public void apagar() {
        dispositivo.apagar();
    }

    public void ajustarVolumen(int nivel) {
        dispositivo.ajustarVolumen(nivel);
    }
}
```

**Refined Abstraction:**

```java
public class ControlRemotoAvanzado extends ControlRemoto {
    public ControlRemotoAvanzado(Dispositivo dispositivo) {
        super(dispositivo);
    }

    public void silenciar() {
        dispositivo.ajustarVolumen(0);
        System.out.println("Dispositivo silenciado");
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        Dispositivo tv = new TV();
        ControlRemoto control = new ControlRemotoAvanzado(tv);

        control.encender();
        control.ajustarVolumen(50);
        ((ControlRemotoAvanzado) control).silenciar();
        control.apagar();
    }
}
```

**Ventajas**

* **Desacopla la abstracción de la implementación.**
* **Facilita la extensión de clases y la adición de nuevas implementaciones.**

**Ejercicio**

Crea un `Bridge` para figuras (`Forma`) y colores (`Color`), permitiendo combinar diferentes formas con diferentes colores.

***

#### 3.3. Composite

**Definición**

El patrón **Composite** permite tratar objetos individuales y composiciones de objetos de manera uniforme. Es ideal para representar jerarquías de objetos.

**Estructura**

* **Component:** Interfaz común para objetos en la composición.
* **Leaf:** Representa objetos individuales sin hijos.
* **Composite:** Tiene componentes hijos y define operaciones sobre ellos.

**Ejemplo Práctico**

**Escenario:** Estructura de archivos y carpetas.

**Component:**

```java
public abstract class Archivo {
    protected String nombre;

    public Archivo(String nombre) {
        this.nombre = nombre;
    }

    public abstract void mostrar();

    public String getNombre() {
        return nombre;
    }
}
```

**Leaf:**

```java
public class ArchivoSimple extends Archivo {

    public ArchivoSimple(String nombre) {
        super(nombre);
    }

    public void mostrar() {
        System.out.println("Archivo: " + nombre);
    }
}
```

**Composite:**

```java
import java.util.ArrayList;
import java.util.List;

public class Carpeta extends Archivo {
    private List<Archivo> archivos;

    public Carpeta(String nombre) {
        super(nombre);
        archivos = new ArrayList<>();
    }

    public void agregar(Archivo archivo) {
        archivos.add(archivo);
    }

    public void eliminar(Archivo archivo) {
        archivos.remove(archivo);
    }

    public void mostrar() {
        System.out.println("Carpeta: " + nombre);
        for (Archivo archivo : archivos) {
            archivo.mostrar();
        }
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        Archivo archivo1 = new ArchivoSimple("Documento1.txt");
        Archivo archivo2 = new ArchivoSimple("Imagen1.png");

        Carpeta carpeta1 = new Carpeta("Mis Documentos");
        carpeta1.agregar(archivo1);
        carpeta1.agregar(archivo2);

        Archivo archivo3 = new ArchivoSimple("Video1.mp4");

        Carpeta carpetaRaiz = new Carpeta("Raíz");
        carpetaRaiz.agregar(carpeta1);
        carpetaRaiz.agregar(archivo3);

        carpetaRaiz.mostrar();
    }
}
```

**Salida:**

```
Carpeta: Raíz
Carpeta: Mis Documentos
Archivo: Documento1.txt
Archivo: Imagen1.png
Archivo: Video1.mp4
```

**Ventajas**

* **Facilita el trabajo con estructuras jerárquicas.**
* **Permite tratar objetos individuales y compuestos de la misma manera.**

**Ejercicio**

Implementa un sistema de menú utilizando el patrón Composite, donde los elementos pueden ser items individuales o submenús.

***

#### 3.4. Decorator

**Definición**

El patrón **Decorator** añade responsabilidades adicionales a un objeto de manera dinámica. Proporciona una alternativa flexible a la herencia para extender funcionalidades.

**Estructura**

* **Component:** Interfaz para los objetos que pueden tener responsabilidades añadidas.
* **Concrete Component:** Objeto real al que se le añaden responsabilidades.
* **Decorator:** Clase abstracta que implementa Component y tiene una referencia a un Component.
* **Concrete Decorator:** Añade funcionalidades al Component.

**Ejemplo Práctico**

**Escenario:** Sistema de notificaciones que puede enviar mensajes por email, SMS, y Facebook.

**Component:**

```java
public interface Notificador {
    void enviar(String mensaje);
}
```

**Concrete Component:**

```java
public class NotificadorBase implements Notificador {
    public void enviar(String mensaje) {
        System.out.println("Enviando notificación básica: " + mensaje);
    }
}
```

**Decorator:**

```java
public abstract class NotificadorDecorator implements Notificador {
    protected Notificador notificador;

    public NotificadorDecorator(Notificador notificador) {
        this.notificador = notificador;
    }

    public void enviar(String mensaje) {
        notificador.enviar(mensaje);
    }
}
```

**Concrete Decorators:**

```java
public class NotificadorEmail extends NotificadorDecorator {
    public NotificadorEmail(Notificador notificador) {
        super(notificador);
    }

    public void enviar(String mensaje) {
        super.enviar(mensaje);
        enviarEmail(mensaje);
    }

    private void enviarEmail(String mensaje) {
        System.out.println("Enviando Email: " + mensaje);
    }
}

public class NotificadorSMS extends NotificadorDecorator {
    public NotificadorSMS(Notificador notificador) {
        super(notificador);
    }

    public void enviar(String mensaje) {
        super.enviar(mensaje);
        enviarSMS(mensaje);
    }

    private void enviarSMS(String mensaje) {
        System.out.println("Enviando SMS: " + mensaje);
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        Notificador notificador = new NotificadorBase();

        notificador = new NotificadorEmail(notificador);
        notificador = new NotificadorSMS(notificador);

        notificador.enviar("Hola Mundo");
    }
}
```

**Salida:**

```
Enviando notificación básica: Hola Mundo
Enviando Email: Hola Mundo
Enviando SMS: Hola Mundo
```

**Ventajas**

* **Añade funcionalidades sin modificar las clases existentes.**
* **Permite combinar múltiples decoradores de forma flexible.**

**Ejercicio**

Crea un sistema de bebidas donde puedas añadir ingredientes (leche, azúcar, chocolate) utilizando el patrón Decorator.

***

#### 3.5. Facade

**Definición**

El patrón **Facade** proporciona una interfaz simplificada a un conjunto de interfaces en un subsistema, facilitando su uso.

**Estructura**

* **Facade:** Clase que proporciona métodos para interactuar con el subsistema.
* **Subsistema:** Conjunto de clases complejas que realizan el trabajo real.

**Ejemplo Práctico**

**Escenario:** Sistema de encendido de un coche con múltiples subsistemas.

**Subsistema:**

```java
public class Motor {
    public void encender() {
        System.out.println("Motor encendido");
    }
}

public class Bateria {
    public void proporcionarEnergia() {
        System.out.println("Batería proporcionando energía");
    }
}

public class SistemaElectrico {
    public void activar() {
        System.out.println("Sistema eléctrico activado");
    }
}
```

**Facade:**

```java
public class CocheFacade {
    private Motor motor;
    private Bateria bateria;
    private SistemaElectrico sistemaElectrico;

    public CocheFacade() {
        this.motor = new Motor();
        this.bateria = new Bateria();
        this.sistemaElectrico = new SistemaElectrico();
    }

    public void arrancarCoche() {
        bateria.proporcionarEnergia();
        sistemaElectrico.activar();
        motor.encender();
        System.out.println("Coche arrancado");
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        CocheFacade coche = new CocheFacade();
        coche.arrancarCoche();
    }
}
```

**Salida:**

```
Batería proporcionando energía
Sistema eléctrico activado
Motor encendido
Coche arrancado
```

**Ventajas**

* **Simplifica el uso de sistemas complejos.**
* **Desacopla el cliente del subsistema.**

**Ejercicio**

Implementa una fachada para un sistema de compra en línea que interactúa con inventario, pago y envío.

***

#### 3.6. Flyweight

**Definición**

El patrón **Flyweight** reduce el uso de memoria al compartir la mayor cantidad de datos posible con objetos similares; es ideal para grandes cantidades de objetos similares.

**Estructura**

* **Flyweight:** Interfaz que declara operaciones que pueden recibir estados externos.
* **Concrete Flyweight:** Implementa la interfaz Flyweight y almacena datos compartidos.
* **Flyweight Factory:** Crea y gestiona objetos Flyweight.

**Ejemplo Práctico**

**Escenario:** Renderizado de caracteres en un editor de texto.

**Flyweight:**

```java
public interface Caracter {
    void dibujar(int posicion);
}
```

**Concrete Flyweight:**

```java
public class CaracterConcreto implements Caracter {
    private char simbolo; // Estado intrínseco

    public CaracterConcreto(char simbolo) {
        this.simbolo = simbolo;
    }

    public void dibujar(int posicion) {
        System.out.println("Dibujando " + simbolo + " en posición " + posicion);
    }
}
```

**Flyweight Factory:**

```java
import java.util.HashMap;
import java.util.Map;

public class FlyweightFactory {
    private Map<Character, Caracter> caracteres = new HashMap<>();

    public Caracter getCaracter(char simbolo) {
        Caracter caracter = caracteres.get(simbolo);
        if (caracter == null) {
            caracter = new CaracterConcreto(simbolo);
            caracteres.put(simbolo, caracter);
        }
        return caracter;
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        String texto = "Hola Mundo";
        FlyweightFactory factory = new FlyweightFactory();

        int posicion = 0;
        for (char c : texto.toCharArray()) {
            Caracter caracter = factory.getCaracter(c);
            caracter.dibujar(posicion);
            posicion++;
        }
    }
}
```

**Salida:**

```
Dibujando H en posición 0
Dibujando o en posición 1
Dibujando l en posición 2
Dibujando a en posición 3
Dibujando   en posición 4
Dibujando M en posición 5
Dibujando u en posición 6
Dibujando n en posición 7
Dibujando d en posición 8
Dibujando o en posición 9
```

**Ventajas**

* **Ahorra memoria al compartir objetos.**
* **Ideal para aplicaciones donde se crean muchos objetos similares.**

**Ejercicio**

Implementa un sistema de árboles en un bosque, donde los árboles comparten datos comunes como tipo y textura usando Flyweight.

***

#### 3.7. Proxy

**Definición**

El patrón **Proxy** proporciona un sustituto o placeholder para controlar el acceso a un objeto.

**Tipos de Proxy**

* **Remoto:** Controla el acceso a un objeto remoto.
* **Virtual:** Retrasa la creación de un objeto pesado hasta que sea necesario.
* **Protección:** Controla el acceso a un objeto basado en permisos.

**Estructura**

* **Subject:** Interfaz común para RealSubject y Proxy.
* **RealSubject:** Objeto real que el Proxy representa.
* **Proxy:** Controla el acceso a RealSubject.

**Ejemplo Práctico**

**Escenario:** Carga de imágenes pesadas en una aplicación.

**Subject:**

```java
public interface Imagen {
    void mostrar();
}
```

**RealSubject:**

```java
public class ImagenReal implements Imagen {
    private String nombreArchivo;

    public ImagenReal(String nombreArchivo) {
        this.nombreArchivo = nombreArchivo;
        cargarDesdeDisco(nombreArchivo);
    }

    private void cargarDesdeDisco(String nombreArchivo) {
        System.out.println("Cargando " + nombreArchivo);
    }

    public void mostrar() {
        System.out.println("Mostrando " + nombreArchivo);
    }
}
```

**Proxy:**

```java
public class ImagenProxy implements Imagen {
    private String nombreArchivo;
    private ImagenReal imagenReal;

    public ImagenProxy(String nombreArchivo) {
        this.nombreArchivo = nombreArchivo;
    }

    public void mostrar() {
        if (imagenReal == null) {
            imagenReal = new ImagenReal(nombreArchivo);
        }
        imagenReal.mostrar();
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        Imagen imagen = new ImagenProxy("foto.jpg");
        System.out.println("Aplicación continúa funcionando");
        imagen.mostrar();
        imagen.mostrar();
    }
}
```

**Salida:**

```
Aplicación continúa funcionando
Cargando foto.jpg
Mostrando foto.jpg
Mostrando foto.jpg
```

**Ventajas**

* **Controla el acceso a objetos pesados o sensibles.**
* **Puede agregar funcionalidades adicionales (como caching o logging).**

**Ejercicio**

Implementa un Proxy de seguridad que controle el acceso a un sistema de documentos basado en permisos de usuario.

***

### 4. Patrones de Comportamiento

Los patrones de comportamiento se centran en las interacciones y responsabilidades entre objetos.

#### 4.1. Chain of Responsibility

**Definición**

El patrón **Chain of Responsibility** permite que múltiples objetos tengan la oportunidad de manejar una solicitud, pasando esta a lo largo de una cadena.

**Estructura**

* **Handler:** Interfaz que declara el método para manejar solicitudes y mantener una referencia al siguiente manejador.
* **Concrete Handlers:** Implementan el manejo de solicitudes y deciden si pasan la solicitud al siguiente.

**Ejemplo Práctico**

**Escenario:** Sistema de soporte técnico con diferentes niveles de soporte.

**Handler:**

```java
public abstract class ManejadorSoporte {
    protected ManejadorSoporte siguiente;

    public void setSiguiente(ManejadorSoporte siguiente) {
        this.siguiente = siguiente;
    }

    public abstract void manejarSolicitud(String problema);
}
```

**Concrete Handlers:**

```java
public class SoporteBasico extends ManejadorSoporte {
    public void manejarSolicitud(String problema) {
        if (problema.equals("contraseña olvidada")) {
            System.out.println("Soporte Básico maneja el problema de: " + problema);
        } else {
            if (siguiente != null) {
                siguiente.manejarSolicitud(problema);
            }
        }
    }
}

public class SoporteIntermedio extends ManejadorSoporte {
    public void manejarSolicitud(String problema) {
        if (problema.equals("error de aplicación")) {
            System.out.println("Soporte Intermedio maneja el problema de: " + problema);
        } else {
            if (siguiente != null) {
                siguiente.manejarSolicitud(problema);
            }
        }
    }
}

public class SoporteAvanzado extends ManejadorSoporte {
    public void manejarSolicitud(String problema) {
        System.out.println("Soporte Avanzado maneja el problema de: " + problema);
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        ManejadorSoporte soporteBasico = new SoporteBasico();
        ManejadorSoporte soporteIntermedio = new SoporteIntermedio();
        ManejadorSoporte soporteAvanzado = new SoporteAvanzado();

        soporteBasico.setSiguiente(soporteIntermedio);
        soporteIntermedio.setSiguiente(soporteAvanzado);

        soporteBasico.manejarSolicitud("contraseña olvidada");
        soporteBasico.manejarSolicitud("error de aplicación");
        soporteBasico.manejarSolicitud("problema crítico");
    }
}
```

**Salida:**

```
Soporte Básico maneja el problema de: contraseña olvidada
Soporte Intermedio maneja el problema de: error de aplicación
Soporte Avanzado maneja el problema de: problema crítico
```

**Ventajas**

* **Desacopla el emisor de la solicitud de sus receptores.**
* **Permite agregar o modificar manejadores fácilmente.**

**Ejercicio**

Implementa una cadena de responsabilidad para un sistema de aprobación de gastos con diferentes niveles de autorización.

***

#### 4.2. Command

**Definición**

El patrón **Command** encapsula una solicitud como un objeto, permitiendo parametrizar clientes con diferentes solicitudes, encolar o registrar solicitudes, y soportar operaciones reversibles.

**Estructura**

* **Command:** Interfaz que declara el método `execute()`.
* **Concrete Command:** Implementa `Command` y define la relación entre el Receiver y una acción.
* **Receiver:** Objeto que realiza las acciones.
* **Invoker:** Invoca el comando.
* **Client:** Crea y configura los comandos.

**Ejemplo Práctico**

**Escenario:** Sistema de control remoto para dispositivos.

**Command:**

```java
public interface Comando {
    void ejecutar();
}
```

**Concrete Commands:**

```java
public class EncenderLuces implements Comando {
    private Luces luces;

    public EncenderLuces(Luces luces) {
        this.luces = luces;
    }

    public void ejecutar() {
        luces.encender();
    }
}

public class ApagarLuces implements Comando {
    private Luces luces;

    public ApagarLuces(Luces luces) {
        this.luces = luces;
    }

    public void ejecutar() {
        luces.apagar();
    }
}
```

**Receiver:**

```java
public class Luces {
    public void encender() {
        System.out.println("Luces encendidas");
    }

    public void apagar() {
        System.out.println("Luces apagadas");
    }
}
```

**Invoker:**

```java
public class ControlRemoto {
    private Comando comando;

    public void setComando(Comando comando) {
        this.comando = comando;
    }

    public void presionarBoton() {
        comando.ejecutar();
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        Luces luces = new Luces();

        Comando encender = new EncenderLuces(luces);
        Comando apagar = new ApagarLuces(luces);

        ControlRemoto control = new ControlRemoto();

        control.setComando(encender);
        control.presionarBoton();

        control.setComando(apagar);
        control.presionarBoton();
    }
}
```

**Salida:**

```
Luces encendidas
Luces apagadas
```

**Ventajas**

* **Desacopla el objeto que invoca la operación del que la realiza.**
* **Permite implementar operaciones deshacer/rehacer.**
* **Facilita la implementación de transacciones.**

**Ejercicio**

Crea un sistema de órdenes para un restaurante donde los meseros toman órdenes (comandos) y la cocina las ejecuta.

***

#### 4.3. Iterator

**Definición**

El patrón **Iterator** proporciona una forma de acceder secuencialmente a los elementos de un objeto agregado sin exponer su representación interna.

**Estructura**

* **Iterator:** Interfaz que declara métodos para navegar por los elementos.
* **Concrete Iterator:** Implementa la interfaz Iterator.
* **Aggregate:** Interfaz para crear un Iterator.
* **Concrete Aggregate:** Implementa la interfaz Aggregate.

**Ejemplo Práctico**

**Escenario:** Colección personalizada de nombres.

**Aggregate:**

```java
public interface Coleccion {
    Iterador crearIterador();
}
```

**Concrete Aggregate:**

```java
public class ColeccionNombres implements Coleccion {
    private String[] nombres = {"Ana", "Juan", "Pedro", "María"};

    public Iterador crearIterador() {
        return new IteradorNombres(nombres);
    }
}
```

**Iterator:**

```java
public interface Iterador {
    boolean tieneSiguiente();
    Object siguiente();
}
```

**Concrete Iterator:**

```java
public class IteradorNombres implements Iterador {
    private String[] nombres;
    private int indice;

    public IteradorNombres(String[] nombres) {
        this.nombres = nombres;
    }

    public boolean tieneSiguiente() {
        return indice < nombres.length;
    }

    public Object siguiente() {
        if (tieneSiguiente()) {
            return nombres[indice++];
        }
        return null;
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        Coleccion coleccion = new ColeccionNombres();
        Iterador iterador = coleccion.crearIterador();

        while (iterador.tieneSiguiente()) {
            System.out.println(iterador.siguiente());
        }
    }
}
```

**Salida:**

```
Ana
Juan
Pedro
María
```

**Ventajas**

* **Facilita la iteración sobre diferentes estructuras de datos.**
* **Encapsula la lógica de recorrido.**

**Ejercicio**

Implementa un iterador para una lista enlazada personalizada.

***

#### 4.4. Mediator

**Definición**

El patrón **Mediator** define un objeto que encapsula cómo un conjunto de objetos interactúa, promoviendo el acoplamiento débil entre ellos.

**Estructura**

* **Mediator:** Interfaz para comunicar componentes.
* **Concrete Mediator:** Implementa la interfaz Mediator y coordina la comunicación.
* **Colleague:** Objetos que interactúan a través del Mediator.

**Ejemplo Práctico**

**Escenario:** Chatroom donde los usuarios se comunican a través de un mediador.

**Mediator:**

```java
public interface ChatMediator {
    void enviarMensaje(String mensaje, Usuario usuario);
    void agregarUsuario(Usuario usuario);
}
```

**Concrete Mediator:**

```java
import java.util.ArrayList;
import java.util.List;

public class ChatRoom implements ChatMediator {
    private List<Usuario> usuarios = new ArrayList<>();

    public void enviarMensaje(String mensaje, Usuario usuario) {
        for (Usuario u : usuarios) {
            if (u != usuario) {
                u.recibir(mensaje);
            }
        }
    }

    public void agregarUsuario(Usuario usuario) {
        usuarios.add(usuario);
    }
}
```

**Colleague:**

```java
public abstract class Usuario {
    protected ChatMediator mediator;
    protected String nombre;

    public Usuario(ChatMediator mediator, String nombre) {
        this.mediator = mediator;
        this.nombre = nombre;
    }

    public abstract void enviar(String mensaje);
    public abstract void recibir(String mensaje);
}
```

**Concrete Colleague:**

```java
public class UsuarioConcreto extends Usuario {
    public UsuarioConcreto(ChatMediator mediator, String nombre) {
        super(mediator, nombre);
    }

    public void enviar(String mensaje) {
        System.out.println(nombre + " envía: " + mensaje);
        mediator.enviarMensaje(mensaje, this);
    }

    public void recibir(String mensaje) {
        System.out.println(nombre + " recibe: " + mensaje);
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        ChatMediator chat = new ChatRoom();

        Usuario usuario1 = new UsuarioConcreto(chat, "Juan");
        Usuario usuario2 = new UsuarioConcreto(chat, "María");
        Usuario usuario3 = new UsuarioConcreto(chat, "Pedro");

        chat.agregarUsuario(usuario1);
        chat.agregarUsuario(usuario2);
        chat.agregarUsuario(usuario3);

        usuario1.enviar("Hola a todos");
    }
}
```

**Salida:**

```
Juan envía: Hola a todos
María recibe: Hola a todos
Pedro recibe: Hola a todos
```

**Ventajas**

* **Reduce las dependencias entre objetos.**
* **Facilita la comunicación y coordinación.**

**Ejercicio**

Implementa un Mediator para controlar el aterrizaje y despegue de aviones en un aeropuerto.

***

#### 4.5. Memento

**Definición**

El patrón **Memento** captura y externaliza el estado interno de un objeto sin violar la encapsulación, para que el objeto pueda ser restaurado a este estado más tarde.

**Estructura**

* **Memento:** Almacena el estado interno del Originator.
* **Originator:** Crea un Memento con su estado actual y puede restaurar su estado desde un Memento.
* **Caretaker:** Guarda y restaura Mementos.

**Ejemplo Práctico**

**Escenario:** Editor de texto con funcionalidad de deshacer.

**Memento:**

```java
public class TextoMemento {
    private String estado;

    public TextoMemento(String estado) {
        this.estado = estado;
    }

    public String getEstado() {
        return estado;
    }
}
```

**Originator:**

```java
public class EditorTexto {
    private String texto = "";

    public void escribir(String palabras) {
        texto += palabras;
    }

    public String getTexto() {
        return texto;
    }

    public TextoMemento guardar() {
        return new TextoMemento(texto);
    }

    public void restaurar(TextoMemento memento) {
        texto = memento.getEstado();
    }
}
```

**Caretaker:**

```java
import java.util.Stack;

public class AdministradorVersiones {
    private Stack<TextoMemento> historial = new Stack<>();

    public void guardar(EditorTexto editor) {
        historial.push(editor.guardar());
    }

    public void deshacer(EditorTexto editor) {
        if (!historial.isEmpty()) {
            editor.restaurar(historial.pop());
        } else {
            System.out.println("No hay estados previos");
        }
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        EditorTexto editor = new EditorTexto();
        AdministradorVersiones admin = new AdministradorVersiones();

        editor.escribir("Hola ");
        admin.guardar(editor);

        editor.escribir("Mundo ");
        admin.guardar(editor);

        editor.escribir("!");
        System.out.println("Texto actual: " + editor.getTexto());

        admin.deshacer(editor);
        System.out.println("Después de deshacer: " + editor.getTexto());

        admin.deshacer(editor);
        System.out.println("Después de deshacer: " + editor.getTexto());
    }
}
```

**Salida:**

```
Texto actual: Hola Mundo !
Después de deshacer: Hola Mundo 
Después de deshacer: Hola 
```

**Ventajas**

* **Permite restaurar estados previos sin violar la encapsulación.**
* **Útil para implementar funciones de deshacer/rehacer.**

**Ejercicio**

Implementa un sistema de juego donde puedas guardar y restaurar el estado del jugador usando Memento.

***

#### 4.6. Observer

**Definición**

El patrón **Observer** define una dependencia uno a muchos entre objetos, de manera que cuando uno cambia de estado, notifica a los demás y éstos actualizan su estado automáticamente.

**Estructura**

* **Subject:** Mantiene una lista de Observers y proporciona métodos para agregarlos o eliminarlos.
* **Observer:** Interfaz que define el método `update()`.
* **Concrete Subject:** Notifica a los Observers cuando su estado cambia.
* **Concrete Observer:** Implementa el método `update()` para mantener su estado consistente con el Subject.

**Ejemplo Práctico**

**Escenario:** Sistema de noticias donde los suscriptores reciben actualizaciones.

**Subject:**

```java
import java.util.ArrayList;
import java.util.List;

public class AgenciaNoticias {
    private List<Observador> observadores = new ArrayList<>();
    private String noticia;

    public void agregarObservador(Observador observador) {
        observadores.add(observador);
    }

    public void eliminarObservador(Observador observador) {
        observadores.remove(observador);
    }

    public void notificarObservadores() {
        for (Observador o : observadores) {
            o.actualizar(noticia);
        }
    }

    public void nuevaNoticia(String noticia) {
        this.noticia = noticia;
        notificarObservadores();
    }
}
```

**Observer:**

```java
public interface Observador {
    void actualizar(String noticia);
}
```

**Concrete Observer:**

```java
public class Suscriptor implements Observador {
    private String nombre;

    public Suscriptor(String nombre) {
        this.nombre = nombre;
    }

    public void actualizar(String noticia) {
        System.out.println(nombre + " ha recibido la noticia: " + noticia);
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        AgenciaNoticias agencia = new AgenciaNoticias();

        Observador suscriptor1 = new Suscriptor("Juan");
        Observador suscriptor2 = new Suscriptor("María");

        agencia.agregarObservador(suscriptor1);
        agencia.agregarObservador(suscriptor2);

        agencia.nuevaNoticia("Nuevo producto lanzado!");
    }
}
```

**Salida:**

```
Juan ha recibido la noticia: Nuevo producto lanzado!
María ha recibido la noticia: Nuevo producto lanzado!
```

**Ventajas**

* **Promueve un bajo acoplamiento entre Subject y Observers.**
* **Permite agregar y eliminar Observers dinámicamente.**

**Ejercicio**

Implementa un sistema de alerta meteorológica donde los dispositivos reciben actualizaciones de clima.

***

#### 4.7. State

**Definición**

El patrón **State** permite que un objeto altere su comportamiento cuando su estado interno cambia, pareciendo que cambia su clase.

**Estructura**

* **Context:** Mantiene una instancia de un objeto State que define el estado actual.
* **State:** Interfaz para los diferentes estados.
* **Concrete States:** Implementan el comportamiento asociado con un estado.

**Ejemplo Práctico**

**Escenario:** Máquina expendedora con diferentes estados.

**State:**

```java
public interface Estado {
    void insertarMoneda();
    void expulsarMoneda();
    void presionarBoton();
    void dispensar();
}
```

**Concrete States:**

```java
public class SinMoneda implements Estado {
    private MaquinaExpendedora maquina;

    public SinMoneda(MaquinaExpendedora maquina) {
        this.maquina = maquina;
    }

    public void insertarMoneda() {
        System.out.println("Moneda insertada");
        maquina.setEstado(maquina.getEstadoConMoneda());
    }

    public void expulsarMoneda() {
        System.out.println("No hay moneda que expulsar");
    }

    public void presionarBoton() {
        System.out.println("Inserte moneda primero");
    }

    public void dispensar() {
        System.out.println("Pague primero");
    }
}

public class ConMoneda implements Estado {
    private MaquinaExpendedora maquina;

    public ConMoneda(MaquinaExpendedora maquina) {
        this.maquina = maquina;
    }

    public void insertarMoneda() {
        System.out.println("Ya hay una moneda insertada");
    }

    public void expulsarMoneda() {
        System.out.println("Moneda expulsada");
        maquina.setEstado(maquina.getEstadoSinMoneda());
    }

    public void presionarBoton() {
        System.out.println("Botón presionado");
        maquina.setEstado(maquina.getEstadoVendiendo());
    }

    public void dispensar() {
        System.out.println("No se puede dispensar sin presionar el botón");
    }
}

public class Vendiendo implements Estado {
    private MaquinaExpendedora maquina;

    public Vendiendo(MaquinaExpendedora maquina) {
        this.maquina = maquina;
    }

    public void insertarMoneda() {
        System.out.println("Espere, estamos dispensando su producto");
    }

    public void expulsarMoneda() {
        System.out.println("Demasiado tarde para expulsar moneda");
    }

    public void presionarBoton() {
        System.out.println("Ya hemos procesado su solicitud");
    }

    public void dispensar() {
        System.out.println("Dispensando producto");
        maquina.setEstado(maquina.getEstadoSinMoneda());
    }
}
```

**Context:**

```java
public class MaquinaExpendedora {
    private Estado estadoSinMoneda;
    private Estado estadoConMoneda;
    private Estado estadoVendiendo;

    private Estado estadoActual;

    public MaquinaExpendedora() {
        estadoSinMoneda = new SinMoneda(this);
        estadoConMoneda = new ConMoneda(this);
        estadoVendiendo = new Vendiendo(this);

        estadoActual = estadoSinMoneda;
    }

    public void setEstado(Estado estado) {
        estadoActual = estado;
    }

    public void insertarMoneda() {
        estadoActual.insertarMoneda();
    }

    public void expulsarMoneda() {
        estadoActual.expulsarMoneda();
    }

    public void presionarBoton() {
        estadoActual.presionarBoton();
        estadoActual.dispensar();
    }

    public Estado getEstadoSinMoneda() {
        return estadoSinMoneda;
    }

    public Estado getEstadoConMoneda() {
        return estadoConMoneda;
    }

    public Estado getEstadoVendiendo() {
        return estadoVendiendo;
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        MaquinaExpendedora maquina = new MaquinaExpendedora();

        maquina.insertarMoneda();
        maquina.presionarBoton();

        maquina.insertarMoneda();
        maquina.expulsarMoneda();

        maquina.presionarBoton();
    }
}
```

**Salida:**

```
Moneda insertada
Botón presionado
Dispensando producto
Moneda insertada
Moneda expulsada
Inserte moneda primero
```

**Ventajas**

* **Organiza el código relacionado con estados específicos.**
* **Facilita el mantenimiento y extensión de estados.**

**Ejercicio**

Implementa una puerta con estados `Cerrado`, `Abierto` y `Bloqueado` utilizando el patrón State.

***

#### 4.8. Strategy

**Definición**

El patrón **Strategy** define una familia de algoritmos, encapsula cada uno y los hace intercambiables, permitiendo variar el algoritmo independientemente de los clientes que lo usan.

**Estructura**

* **Strategy:** Interfaz común para las estrategias.
* **Concrete Strategies:** Implementan diferentes algoritmos.
* **Context:** Usa una Strategy.

**Ejemplo Práctico**

**Escenario:** Sistema de compresión con diferentes algoritmos.

**Strategy:**

```java
public interface CompresionStrategy {
    void comprimir(String archivo);
}
```

**Concrete Strategies:**

```java
public class CompresionZIP implements CompresionStrategy {
    public void comprimir(String archivo) {
        System.out.println("Comprimiendo " + archivo + " en formato ZIP");
    }
}

public class CompresionRAR implements CompresionStrategy {
    public void comprimir(String archivo) {
        System.out.println("Comprimiendo " + archivo + " en formato RAR");
    }
}
```

**Context:**

```java
public class ContextoCompresion {
    private CompresionStrategy strategy;

    public void setStrategy(CompresionStrategy strategy) {
        this.strategy = strategy;
    }

    public void comprimirArchivo(String archivo) {
        strategy.comprimir(archivo);
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        ContextoCompresion contexto = new ContextoCompresion();

        contexto.setStrategy(new CompresionZIP());
        contexto.comprimirArchivo("archivo.txt");

        contexto.setStrategy(new CompresionRAR());
        contexto.comprimirArchivo("archivo.txt");
    }
}
```

**Salida:**

```
Comprimiendo archivo.txt en formato ZIP
Comprimiendo archivo.txt en formato RAR
```

**Ventajas**

* **Permite cambiar algoritmos en tiempo de ejecución.**
* **Evita el uso de múltiples condicionales para seleccionar algoritmos.**

**Ejercicio**

Implementa un sistema de pago que soporte diferentes métodos (tarjeta, PayPal) utilizando el patrón Strategy.

***

#### 4.9. Template Method

**Definición**

El patrón **Template Method** define el esqueleto de un algoritmo en una operación, diferiendo algunos pasos a las subclases. Permite redefinir ciertos pasos de un algoritmo sin cambiar su estructura.

**Estructura**

* **Abstract Class:** Define el método template y los pasos del algoritmo.
* **Concrete Class:** Implementa los pasos específicos.

**Ejemplo Práctico**

**Escenario:** Proceso de preparación de bebidas.

**Abstract Class:**

```java
public abstract class Bebida {
    public final void prepararReceta() {
        hervirAgua();
        agregarIngredientes();
        servirEnTaza();
        agregarExtras();
    }

    public void hervirAgua() {
        System.out.println("Hirviendo agua");
    }

    public abstract void agregarIngredientes();

    public void servirEnTaza() {
        System.out.println("Sirviendo en taza");
    }

    public abstract void agregarExtras();
}
```

**Concrete Classes:**

```java
public class Te extends Bebida {
    public void agregarIngredientes() {
        System.out.println("Agregando bolsa de té");
    }

    public void agregarExtras() {
        System.out.println("Agregando limón");
    }
}

public class Cafe extends Bebida {
    public void agregarIngredientes() {
        System.out.println("Agregando café molido");
    }

    public void agregarExtras() {
        System.out.println("Agregando azúcar y leche");
    }
}
```

**Uso:**

```java
public class Main {
    public static void main(String[] args) {
        Bebida te = new Te();
        te.prepararReceta();

        System.out.println();

        Bebida cafe = new Cafe();
        cafe.prepararReceta();
    }
}
```

**Salida:**

```
Hirviendo agua
Agregando bolsa de té
Sirviendo en taza
Agregando limón

Hirviendo agua
Agregando café molido
Sirviendo en taza
Agregando azúcar y leche
```

**Ventajas**

* **Fomenta el uso de métodos reutilizables.**
* **Facilita la extensión y personalización de algoritmos.**

**Ejercicio**

Crea un proceso de fabricación de vehículos donde diferentes tipos de vehículos siguen pasos similares pero con implementaciones específicas.

***

#### 4.10. Visitor

**Definición**

El patrón **Visitor** permite definir nuevas operaciones sobre una estructura de objetos sin cambiar las clases de los elementos sobre los que opera.

**Estructura**

* **Visitor:** Interfaz que declara métodos para cada tipo de elemento a visitar.
* **Concrete Visitor:** Implementa las operaciones específicas.
* **Element:** Interfaz que acepta un Visitor.
* **Concrete Element:** Implementa el método accept.

**Ejemplo Práctico**

**Escenario:** Sistema de procesamiento de figuras geométricas.

**Visitor:**

```java
public interface FiguraVisitor {
    void visitar(Circulo circulo);
    void visitar(Rectangulo rectangulo);
}
```

**Concrete Visitor:**

```java
public class DibujarVisitor implements FiguraVisitor {
    public void visitar(Circulo circulo) {
        System.out.println("Dibujando círculo de radio " + circulo.getRadio());
    }

    public void visitar(Rectangulo rectangulo) {
        System.out.println("Dibujando rectángulo de ancho " + rectangulo.getAncho() + " y alto " + rectangulo.getAlto());
    }
}
```

**Element:**

```java
public interface Figura {
    void aceptar(FiguraVisitor visitor);
}
```

**Concrete Elements:**

```java
public class Circulo implements Figura {
    private double radio;

    public Circulo(double radio) {
        this.radio = radio;
    }

    public double getRadio() {
        return radio;
    }

    public void aceptar(FiguraVisitor visitor) {
        visitor.visitar(this);
    }
}

public class Rectangulo implements Figura {
    private double ancho;
    private double alto;

    public Rectangulo(double ancho, double alto) {
        this.ancho = ancho;
        this.alto = alto;
    }

    public double getAncho() {
        return ancho;
    }

    public double getAlto() {
        return alto;
    }

    public void aceptar(FiguraVisitor visitor) {
        visitor.visitar(this);
    }
}
```

**Uso:**

```java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Figura> figuras = new ArrayList<>();
        figuras.add(new Circulo(5));
        figuras.add(new Rectangulo(3, 4));

        FiguraVisitor dibujarVisitor = new DibujarVisitor();

        for (Figura figura : figuras) {
            figura.aceptar(dibujarVisitor);
        }
    }
}
```

**Salida:**

```
Dibujando círculo de radio 5.0
Dibujando rectángulo de ancho 3.0 y alto 4.0
```

**Ventajas**

* **Añade nuevas operaciones sin modificar las clases existentes.**
* **Separa algoritmos de la estructura de datos.**

**Ejercicio**

Implementa un Visitor que calcule el área y perímetro de diferentes figuras geométricas.

***

### 5. Buenas Prácticas en el Uso de Patrones de Diseño

* **No Forzar el Uso de Patrones:** Utiliza patrones cuando aporten valor real al diseño, no simplemente por usarlos.
* **Comprender el Problema:** Asegúrate de entender completamente el problema antes de aplicar un patrón.
* **Mantener la Simplicidad:** No complicar el diseño innecesariamente.
* **Documentar:** Indica claramente dónde y por qué se ha aplicado un patrón.
* **Combinar Patrones con Cuidado:** Algunos patrones pueden complementarse, pero evita crear diseños demasiado complejos.

***



**Pasos Siguientes:**

* **Practicar:** Implementa los patrones en proyectos personales.
* **Analizar Código Existente:** Identifica patrones en bibliotecas y frameworks.
* **Estudiar Patrones Avanzados:** Profundiza en patrones más complejos y en cómo se combinan.

**Recuerda:** Los patrones de diseño son herramientas, no reglas rígidas. Úsalos cuando aporten valor y evita su sobreuso.
