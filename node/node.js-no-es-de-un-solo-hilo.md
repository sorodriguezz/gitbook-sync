# Node.js no es de un solo hilo

Node.js es conocido como una plataforma de servidor increíblemente rápida gracias a su revolucionaria arquitectura de un solo hilo, que utiliza los recursos del servidor de manera más eficiente. Pero, ¿realmente es posible lograr ese rendimiento sorprendente utilizando solo un hilo? La respuesta podría sorprenderte.

En este artículo revelaremos todos los secretos y la “magia” detrás de Node.js de una manera muy simple.

## Proceso vs Hilo ⚙️

Antes de comenzar, tenemos que entender qué es un proceso y qué es un hilo, y descubrir sus diferencias y similitudes.

Un proceso es una instancia de un programa que se está ejecutando actualmente. Cada proceso se ejecuta de forma independiente de los demás. Los procesos tienen varios recursos sustanciales:

* **Código de ejecución;**
* **Segmento de datos** — contiene variables globales y estáticas que necesitan ser accesibles desde cualquier parte del programa;
* **Heap (Montículo)** — asignación de memoria dinámica;
* **Pila (Stack)** — variables locales, argumentos de funciones y llamadas a funciones;
* **Registros** — ubicaciones de almacenamiento pequeñas y rápidas directamente dentro de la CPU que se utilizan para mantener datos temporalmente durante la ejecución de programas (como el puntero de programa y el puntero de pila).

Un **hilo** es una unidad única de ejecución dentro de un proceso. Puede haber múltiples hilos dentro de un proceso que realizan diferentes operaciones simultáneamente. El proceso comparte el código de ejecución, los datos y el heap con los hilos, pero la pila y los registros se asignan por separado para cada hilo.

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

## **JavaScript no es con hilos ⚠️**

Para evitar malentendidos con los términos, es importante tener en cuenta que JavaScript en sí mismo no es ni de un solo hilo ni multihilo. El lenguaje no tiene nada que ver con el manejo de hilos. Es simplemente un conjunto de instrucciones que la plataforma de ejecución debe manejar. La plataforma maneja estas instrucciones a su manera — ya sea de forma de un solo hilo o de forma multihilo.

## Operaciones de I/O 🧮

(O operaciones de Entrada / Salida) son generalmente consideradas más lentas en comparación con otras operaciones del computador. Aquí algunos ejemplos:

* escribir datos en el disco;
* leer datos desde el disco;
* esperar una entrada del usuario (como un clic del mouse);
* enviar una solicitud HTTP;
* realizar una operación en una base de datos.

## Las operaciones de I/O son lentas 🐢

Puede que te estés preguntando por qué leer datos desde el disco se considera lento. La respuesta está en la implementación física de los componentes de hardware.

Acceder a la memoria RAM se realiza en el orden de los nanosegundos, mientras que acceder a datos en el disco o a través de la red ocurre en el orden de los milisegundos.

Lo mismo se aplica al ancho de banda. La RAM tiene una tasa de transferencia que constantemente está en el orden de los GB/s, mientras que el disco o la red varía desde MB/s hasta, en el mejor de los casos, GB/s.

Además de eso, tenemos que considerar el factor humano. En muchas circunstancias, la entrada de una aplicación proviene de una persona real (por ejemplo, una pulsación de tecla). Así que la velocidad y frecuencia de las operaciones de I/O no dependen únicamente de aspectos técnicos.

## Las operaciones de I/O bloquean el hilo 🚧

Las operaciones de I/O pueden ralentizar significativamente un programa. El hilo permanece bloqueado, y no se ejecutará ninguna otra operación hasta que la operación de I/O haya finalizado.

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

## ¡Crea más hilos! 🤪

Bien, ¿por qué no simplemente generar más hilos dentro del programa y manejar cada solicitud por separado? Bueno, parece una buena idea. Ahora, cada solicitud de cliente tiene su propio hilo, y el servidor puede manejar múltiples solicitudes simultáneamente.

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

El programa necesita asignar memoria adicional y recursos de CPU para cada hilo. Esto suena razonable. Sin embargo, surge un problema significativo cuando los hilos realizan operaciones de I/O: se vuelven inactivos y pasan la mayor parte del tiempo usando un 0% de los recursos, esperando a que la operación se complete. Cuantos más hilos haya, más recursos se utilizan de manera ineficiente.

Además de eso, manejar hilos es una tarea compleja, lo que puede generar problemas potenciales como condiciones de carrera (_race conditions_), bloqueos mutuos (_deadlocks_) y bloqueos vivos (_livelocks_). El sistema operativo necesita alternar entre los hilos, lo cual puede añadir una sobrecarga y reducir las ganancias de eficiencia que se esperan del uso de múltiples hilos.

## ¿Cuál es la solución? 🤔

Afortunadamente, la humanidad ya ha inventado mecanismos inteligentes para realizar este tipo de operaciones de manera eficiente.

Demos la bienvenida al Event Demultiplexer. Este mecanismo implica un proceso llamado Multiplexación — un método mediante el cual varias señales se combinan en una sola señal sobre un recurso compartido. El objetivo es compartir un recurso escaso (en nuestro caso, la CPU y la RAM).

Por ejemplo, en telecomunicaciones, varias llamadas telefónicas pueden ser transmitidas utilizando un solo cable.

<figure><img src="../.gitbook/assets/image (27).png" alt="" width="375"><figcaption></figcaption></figure>

Las responsabilidades del Event Demultiplexer se dividen en los siguientes pasos:

1. **Identificar las fuentes de eventos.** Cada fuente puede generar eventos;
2. **Registrar las fuentes de eventos.** El registro implica especificar qué eventos se deben monitorear para cada fuente;
3. **Esperar eventos;**
4. **Enviar notificaciones de eventos.**

¡Importante! El Event Demultiplexer no es un componente o dispositivo que exista en el mundo real. Es más bien un modelo teórico utilizado para explicar cómo manejar numerosos eventos simultáneos de manera eficiente.

Para entender este proceso complejo, volvamos al pasado. Imagina una antigua central telefónica: identifica y registra fuentes de eventos (teléfonos) y espera nuevos eventos (llamadas). Una vez que ocurre un nuevo evento (una llamada telefónica), la central envía una notificación (enciende una bombilla). Entonces, el operador de la central reacciona a la notificación revisando el número de teléfono de destino y redirigiendo la llamada hacia su destino deseado.

Para las computadoras, el principio es el mismo. Sin embargo, el rol de las fuentes lo desempeñan elementos como descriptores de archivos, sockets de red, temporizadores o dispositivos de entrada del usuario. Cada fuente puede generar eventos como: datos disponibles para leer, espacio disponible para escribir o solicitudes de conexión.

Cada sistema operativo ya ha implementado su propio mecanismo de Event Demultiplexer:

* epoll (Linux),
* kqueue (macOS),
* event ports (Solaris),
* IOCP (Windows).

Pero Node.js es multiplataforma. Para gobernar todo este proceso mientras se admite I/O entre distintas plataformas, existe una capa de abstracción que encapsula estas complejidades interplataforma e intraplataforma, y expone una API generalizada para las capas superiores de Node.js.

## ¡Libuv el Rey! 🏆

Demos la bienvenida a libuv — una biblioteca multiplataforma (escrita en C) desarrollada originalmente para Node.js con el fin de proporcionar una interfaz consistente para operaciones de I/O no bloqueantes en varios sistemas operativos.

Libuv no solo se comunica con el Event Demultiplexer del sistema operativo, sino que también incorpora dos componentes importantes: la Event Queue (cola de eventos) y el Event Loop (bucle de eventos). Estos componentes trabajan juntos para manejar de forma eficiente recursos concurrentes y no bloqueantes.

* La Event Queue es una estructura de datos donde el Event Demultiplexer coloca todos los eventos, listos para ser encolados y procesados secuencialmente por el Event Loop hasta que la cola quede vacía.
* El Event Loop es un proceso que se ejecuta continuamente, espera mensajes en la Event Queue y luego los envía a los controladores (handlers) correspondientes.

## ¿Problema resuelto? 🥳

Esto es lo que sucede cuando llamamos a una operación de I/O:

1. Libuv inicializa el Event Demultiplexer apropiado según el sistema operativo;
2. El intérprete de Node.js analiza el código y coloca cada operación en la pila de llamadas (call stack);
3. Node.js ejecuta las operaciones de la pila de llamadas de forma secuencial. Sin embargo, para las operaciones de I/O, Node.js las envía al Event Demultiplexer de forma no bloqueante. Este enfoque garantiza que la operación de I/O no bloquee el hilo, permitiendo que otras operaciones se ejecuten de manera concurrente;
4. El Event Demultiplexer identifica la fuente de la operación de I/O y registra la operación utilizando las capacidades del sistema operativo;
5. El Event Demultiplexer monitorea continuamente la fuente (por ejemplo, un socket de red) buscando eventos (por ejemplo, cuando hay datos disponibles para leer);
6. Cuando ocurre el evento (como que los datos estén listos para leerse), el Event Demultiplexer envía una señal y agrega el evento junto con su _callback_ asociado a la Event Queue;
7. El Event Loop revisa continuamente la Event Queue y procesa el _callback_ del evento.

Lo que hace Node.js es que, mientras una solicitud está esperando, puede atender otra solicitud. Node.js no espera a que una solicitud se complete antes de procesar las demás. Por defecto, todas las solicitudes que realizas en Node.js son concurrentes — no esperan a que otras terminen antes de ejecutarse.

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

¡Hurra! Parece que el problema está resuelto. Node.js puede ejecutarse de manera eficiente en un solo hilo, ya que la mayoría de las complejidades relacionadas con las operaciones de I/O bloqueantes han sido resueltas por los desarrolladores de los sistemas operativos. ¡Gracias!

## El problema NO está resuelto 🫠

Pero si observamos más de cerca la estructura de libuv, encontramos un aspecto interesante:

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

¿Espera… un Thread Pool? ¿Qué? Sí, ahora hemos profundizado lo suficiente como para responder la pregunta principal: ¿Por qué Node.js no es (completamente) de un solo hilo?

## Revelando el secreto 🤫

Bien, tenemos una herramienta poderosa y utilidades del sistema operativo que nos permiten ejecutar código asíncrono en un solo hilo.

**Pero aquí aparece un problema** con el Event Demultiplexer. Dado que la implementación del Event Demultiplexer varía entre sistemas operativos, algunas partes de las operaciones de I/O no están completamente soportadas de manera asíncrona. Es difícil soportar todos los diferentes tipos de operaciones de I/O en todas las distintas plataformas de sistema operativo. Estos problemas están especialmente relacionados con las implementaciones de I/O en archivos. Esto también impacta algunas funciones DNS de Node.js.

Y no solo eso. Hay otros tipos de operaciones de I/O que no pueden completarse de manera asíncrona, como por ejemplo:

* **Operaciones DNS**, como dns.lookup, que pueden bloquear porque podrían necesitar consultar un servidor remoto;
* **Tareas intensivas de CPU**, como criptografía;
* **Compresión de archivos ZIP.**

Para este tipo de casos, se utiliza el Thread Pool para ejecutar las operaciones de I/O en hilos separados (típicamente hay 4 hilos por defecto). Así que, el diagrama completo de la arquitectura de Node.js se vería así:

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

Sí, Node.js en sí es de un solo hilo, pero las bibliotecas que utiliza internamente, como libuv con su thread pool para algunas operaciones de I/O, no lo son.

El **Thread Pool**, en conjunto con la Tasks Queue (cola de tareas), se utiliza para manejar operaciones de I/O que son bloqueantes. Por defecto, el Thread Pool incluye 4 hilos, pero este comportamiento puede modificarse proporcionando una variable de entorno adicional:

```bash
UV_THREADPOOL_SIZE=8 node my_script.js
```

Esto es lo que ocurre cuando una operación de I/O no puede realizarse de forma asíncrona, pero las diferencias clave son:

* Cuando el Event Demultiplexer identifica la fuente de la operación de I/O, registra la operación en la **Tasks Queue** (cola de tareas);
* El Thread Pool monitorea continuamente la Tasks Queue en busca de nuevas tareas;
* Cuando se coloca una nueva tarea en la cola, el Thread Pool reacciona manejándola con uno de los hilos predefinidos, de forma asíncrona;
* Al finalizar la operación, el Thread Pool envía una señal y agrega el evento con su _callback_ asociado a la Event Queue (cola de eventos).

Aquí no hay magia. La I/O no puede ser verdaderamente no bloqueante y no hay forma de lograr eso (al menos por ahora). Los datos no pueden transferirse más rápido de lo que lo permiten las limitaciones físicas. Nada es perfecto, así que hasta que encontremos maneras de aumentar la velocidad de transferencia de datos a nivel de hardware, utilizamos un conjunto de algoritmos optimizados para realizar operaciones asíncronas de la forma más eficiente posible.



