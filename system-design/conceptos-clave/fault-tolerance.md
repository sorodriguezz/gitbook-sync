---
description: ¿Qué es la tolerancia a fallos y cómo construir sistemas tolerantes a fallos?
---

# Fault Tolerance

## ¿Qué es la tolerancia a fallos? <a href="#what-is-fault-tolerance" id="what-is-fault-tolerance"></a>

La tolerancia a fallos describe la capacidad de un sistema para gestionar errores e interrupciones sin perder funcionalidad. Es una capacidad crucial, especialmente en la computación en la nube, donde la fiabilidad y el tiempo de actividad son fundamentales.

Por ejemplo, a continuación se muestra una demostración sencilla de la tolerancia a fallos comparativa en la capa de base de datos. En el diagrama a continuación, la Aplicación 1 está conectada a una sola instancia de base de datos. La Aplicación 2 está conectada a dos instancias de base de datos: la base de datos principal y una réplica en espera.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.04.26.png" alt="" width="375"><figcaption></figcaption></figure>

En este escenario, la Aplicación 2 es más tolerante a fallos. Si su base de datos principal se desconecta, puede cambiar a la réplica en espera y seguir funcionando con normalidad.

La aplicación 1 no es tolerante a fallos. Si su base de datos se desconecta, todas las funciones de la aplicación que requieren acceso a ella dejarán de funcionar.

Por supuesto, este es solo un ejemplo sencillo. En realidad, la tolerancia a fallos debe considerarse en todas las capas de un sistema (no solo en la base de datos), y existen diferentes grados de tolerancia. Si bien la Aplicación 2 es más tolerante a fallos que la Aplicación 1, sigue siendo menos tolerante que muchas aplicaciones modernas. (Véase ejemplos de arquitectura de aplicaciones tolerantes a fallos).

¿Qué es la tolerancia a fallos en la computación en la nube? Implica el diseño de sistemas que se recuperan automáticamente de fallos, garantizando una interrupción mínima de los servicios. Esto es esencial para mantener la confianza del cliente y la continuidad del negocio.

La tolerancia a fallos también se puede lograr de diversas maneras. Estos son algunos de los enfoques más comunes para lograrla:

**Múltiples sistemas de hardware** capaces de realizar el mismo trabajo. Por ejemplo, la Aplicación 2 del diagrama anterior podría tener sus dos bases de datos ubicadas en dos servidores físicos diferentes, posiblemente en ubicaciones distintas. De esta manera, si el servidor de base de datos principal experimenta un error, una falla de hardware o un corte de energía, el otro servidor podría no verse afectado.

**Múltiples instancias de software** capaces de realizar el mismo trabajo. Por ejemplo, muchas aplicaciones modernas utilizan plataformas de contenedorización como Kubernetes para ejecutar múltiples instancias de servicios de software. Esto se debe, entre otras razones, a que, si una instancia detecta un error o se desconecta, el tráfico se puede enrutar a otras instancias para mantener la funcionalidad de la aplicación.

**Las fuentes de energía de respaldo**, como los generadores, se utilizan a menudo en sistemas locales para proteger la aplicación de interrupciones de suministro eléctrico si el suministro a los servidores se ve afectado, por ejemplo, por el clima. Este tipo de interrupción es más común de lo que se cree.

## Alta disponibilidad vs. tolerancia a fallos <a href="#high-availability-vs.-fault-tolerance" id="high-availability-vs.-fault-tolerance"></a>

¿Qué es la alta disponibilidad? **La alta disponibilidad** se refiere al tiempo total de funcionamiento de un sistema, y ​​lograrla es una de las principales razones por las que los arquitectos buscan construir sistemas tolerantes a fallos.

Técnicamente, al hablar de tolerancia a fallos y alta disponibilidad, no son exactamente lo mismo. Mantener una aplicación altamente disponible no se trata simplemente de hacerla tolerante a fallos. Una aplicación altamente tolerante a fallos podría no lograr alta disponibilidad si, por ejemplo, debe desconectarse regularmente para actualizar componentes de software, cambiar el esquema de la base de datos, etc.

En la práctica, sin embargo, ambos están a menudo estrechamente conectados y es difícil lograr una alta disponibilidad (ya sea para servidores o clústeres de alta disponibilidad) sin sistemas robustos y tolerantes a fallos.

## Objetivos de tolerancia a fallos

Desarrollar sistemas tolerantes a fallos es más complejo y, por lo general, también más costoso. Si recordamos nuestro ejemplo anterior, la Aplicación 2 es más tolerante a fallos, pero también debe pagar y mantener un servidor de base de datos adicional. Por lo tanto, es importante evaluar el nivel de tolerancia a fallos que requiere su aplicación y desarrollar el sistema en consecuencia.

## Funcionamiento normal vs. degradación elegante <a href="#normal-functioning-vs.-graceful-degradation" id="normal-functioning-vs.-graceful-degradation"></a>

Al diseñar sistemas tolerantes a fallos, es posible que desee que la aplicación permanezca en línea y completamente funcional en todo momento. En este caso, el objetivo es **un funcionamiento normal** : desea que su aplicación, y por extensión la experiencia del usuario, se mantengan inalteradas incluso si un elemento de su sistema falla o se desconecta.

Otro enfoque busca la denominada **degradación elegante**, donde se permite que las interrupciones y los errores afecten la funcionalidad y degraden la experiencia del usuario, pero sin inutilizar la aplicación por completo. Por ejemplo, si una instancia de software detecta un error durante un período de tráfico intenso, la experiencia de la aplicación puede ralentizarse para otros usuarios y ciertas funciones podrían dejar de estar disponibles.

Desarrollar para un funcionamiento normal obviamente proporciona una experiencia de usuario superior, pero también suele ser más costoso. Por lo tanto, los objetivos de una aplicación específica podrían depender de su uso. Las aplicaciones y sistemas de misión crítica probablemente necesitarán mantener un funcionamiento normal en casi todos los desastres, salvo en los más graves, mientras que podría ser económicamente viable permitir que los sistemas menos esenciales se degraden sin problemas.

## Establecer objetivos de supervivencia

Lograr una tolerancia a fallas del 100% no es realmente posible, por lo que la pregunta que los arquitectos generalmente tienen que responder cuando diseñan sistemas tolerantes a fallas es cuánto quieren poder sobrevivir.

Los objetivos de supervivencia pueden variar, pero aquí hay algunos comunes para las aplicaciones que se ejecutan en una o más nubes públicas, en orden ascendente de resiliencia:

* **Supervivencia a fallos de nodo**. Ejecutar instancias de su software en varios nodos (a menudo, en diferentes servidores físicos) con la misma zona de disponibilidad (Zona de disponibilidad, o centro de datos) puede permitir que su aplicación sobreviva a fallos (como fallos de hardware o errores) en uno o más de esos nodos.
* **Supere las fallas de la zona de disponibilidad (AZ)**. Ejecutar instancias de su software en múltiples zonas de disponibilidad (centros de datos) dentro de una región de nube le permitirá sobrevivir a interrupciones de la AZ, como un corte de energía en un centro de datos específico durante una tormenta.
* **Superar fallos regionales**. Ejecutar instancias de su software en varias regiones de la nube puede permitirle sobrevivir a una interrupción que afecte a toda una región, como la interrupción de AWS US-east-1 mencionada al principio de esta publicación.
* **Supere las fallas de su proveedor de nube**. Ejecutar instancias de su software tanto en la nube como localmente, o con múltiples proveedores de nube, puede permitirle sobrevivir incluso a una interrupción total del servicio de su proveedor.

Estos no son los únicos objetivos de supervivencia posibles, por supuesto, y la tolerancia a fallos es solo un aspecto de la supervivencia ante interrupciones y otros desastres. Los arquitectos también deben considerar factores como el RTO y el RPO para minimizar el impacto negativo cuando se producen interrupciones. Sin embargo, considerar sus objetivos de tolerancia a fallos también es importante, ya que afectarán tanto la arquitectura de su aplicación como sus costos.

## El costo de la tolerancia a fallas

Al diseñar sistemas con tolerancia a fallos, otro factor importante es el coste. Este es un factor complejo y muy específico para cada caso, pero es importante recordar que, si bien la elección y el uso de arquitecturas y herramientas con mayor tolerancia a fallos conllevan costes inherentes, también existen costes significativos asociados a la _falta_ de un alto nivel de tolerancia a fallos.

Por ejemplo, operar varias instancias de su base de datos en varias regiones de la nube probablemente resulte más costoso para el balance general que operar una sola instancia en una sola región. Sin embargo, también debe considerar algunos aspectos:

* **¿Cuánto cuesta una interrupción en dólares?** Para sistemas críticos, incluso unos pocos minutos de inactividad pueden suponer una pérdida millonaria de ingresos.
* **¿Cuánto cuesta una interrupción en términos de daño a la reputación?** Los consumidores son exigentes, sobre todo en ciertos sectores comerciales. Una interrupción de una aplicación de tan solo unos minutos, por ejemplo, podría ser suficiente para ahuyentar a millones de clientes de un banco.
* **¿Cuánto cuesta una interrupción en horas de ingeniería?** El tiempo que su equipo dedica a recuperarse de una interrupción es tiempo que _no_ dedica a desarrollar nuevas funciones ni a realizar otras tareas importantes.
* **¿Cuánto afecta una interrupción del servicio a la moral del equipo, la retención y la contratación?** Además, las interrupciones suelen ocurrir en momentos inoportunos. La interrupción del servicio en US-east-1, por ejemplo, se produjo la víspera de Acción de Gracias, cuando la mayoría de los ingenieros estadounidenses estaban de vacaciones, lo que los obligó a acudir a la oficina en un día festivo o en plena noche para atender una emergencia. Los buenos ingenieros suelen tener muchas opciones en cuanto a su lugar de trabajo y evitarán trabajar en cualquier lugar donde este tipo de emergencias puedan interrumpir sus vidas.

Éstos son sólo algunos de los costos asociados con _no_ lograr un alto nivel de tolerancia a fallas.

La _forma_ de lograr la tolerancia a fallos también puede tener un impacto significativo en los costos. Consideremos el caso real de una importante empresa de electrónica que necesitaba desarrollar una versión más escalable y tolerante a fallos de su base de datos MySQL .

La empresa podría haber mejorado la tolerancia a fallos de la base de datos MySQL fragmentándola manualmente, pero este enfoque es técnicamente complejo y requiere mucho trabajo de ejecución y mantenimiento. En su lugar, la empresa optó por migrar a CockroachDB dedicado, una base de datos como servicio administrada, distribuida por naturaleza y con tolerancia a fallos.

Si bien CockroachDB en sí es más costoso que MySQL (que es gratuito), migrar a CockroachDB le permitió a la empresa ahorrar millones en costos laborales porque automatiza el laborioso proceso de fragmentación manual y resuelve muchas de las complejidades técnicas que introduciría la fragmentación manual.

Finalmente, la empresa logró una base de datos que es tan o _más_ tolerante a fallos que MySQL fragmentado manualmente, y al mismo tiempo gastó millones de dólares menos de lo que en última instancia les habría costado una MySQL fragmentada manualmente.

Esto no significa que CockroachDB ni _ninguna_ herramienta o plataforma específica sean la opción más asequible para todos los casos de uso. Sin embargo, es importante reconocer que los métodos que elija para lograr sus objetivos de tolerancia a fallos pueden tener un impacto significativo en sus costos, tanto a corto como a largo plazo.

## Ejemplos de arquitectura tolerante a fallos <a href="#fault-tolerant-architecture-examples" id="fault-tolerant-architecture-examples"></a>

Hay muchas formas de lograr tolerancia a fallas, pero veamos un enfoque muy común para las aplicaciones modernas: adoptar una arquitectura multirregional basada en la nube construida alrededor de servicios de contenedorización como Kubernetes.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 21.10.44.png" alt=""><figcaption></figcaption></figure>

_Ejemplo de una arquitectura multirregional con tolerancia a fallos. Haga clic para ampliar._

Esta aplicación podría sobrevivir a un fallo en un nodo, una zona de disponibilidad (AZ) o incluso una región que afecte a su capa de aplicación, a su capa de base de datos o a ambas. Analicemos con más detalle cómo es posible.

## Lograr tolerancia a fallos en la capa de aplicación <a href="#achieving-fault-tolerance-in-the-application-layer" id="achieving-fault-tolerance-in-the-application-layer"></a>

En el diagrama anterior, la aplicación está distribuida en múltiples regiones y cada una de ellas tiene su propio clúster de Kubernetes.

Dentro de cada región, la aplicación se crea con microservicios que ejecutan tareas específicas, y estos microservicios suelen operar dentro de pods de Kubernetes. Esto permite una mayor tolerancia a fallos, ya que se puede iniciar un nuevo pod con una nueva instancia cada vez que un pod existente detecta un error. Este enfoque también facilita la escalabilidad horizontal de la aplicación: a medida que aumenta la carga de un servicio específico, se pueden agregar instancias adicionales de ese servicio en tiempo real para gestionar la carga y luego eliminarlas cuando la carga disminuye y ya no son necesarias.

## Lograr tolerancia a fallos en la capa de persistencia (base de datos) <a href="#achieving-fault-tolerance-in-the-persistence-database-layer" id="achieving-fault-tolerance-in-the-persistence-database-layer"></a>

La aplicación del diagrama anterior adopta un enfoque similar en la capa de base de datos. En este caso, se elige CockroachDB porque su naturaleza distribuida, basada en nodos, proporciona naturalmente un alto nivel de tolerancia a fallos y la misma flexibilidad al escalar horizontalmente. Al ser una base de datos SQL distribuida, también permite sólidas garantías de consistencia, lo cual es importante para la mayoría de las cargas de trabajo transaccionales.

CockroachDB también tiene sentido para esta arquitectura porque, si bien es una base de datos distribuida, la aplicación puede tratarla como una base de datos Postgres de instancia única: casi toda la complejidad de distribuir los datos para cumplir con los objetivos de disponibilidad y supervivencia de la aplicación ocurre de manera interna.
