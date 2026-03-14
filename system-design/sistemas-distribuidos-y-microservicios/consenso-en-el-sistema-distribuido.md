# Consenso en el sistema distribuido

**Sahil: ¡**&#x48;ola, Sourajit! Juguemos a algo.

**Sourajit:** Juguemos al fútbol.

**Sahil: ¡**&#x53;í, claro! A los dos nos encanta jugar a eso.

Tanto Sahil como Sourajit querían jugar un partido y Sourajit sugirió jugar al fútbol. Sahil estuvo de acuerdo con la propuesta de Sourajit. Entonces, acordaron un valor común sugerido por uno de ellos y tomaron medidas al rede. Este acuerdo sobre el valor común se conoce como **consenso**.

## ¿Qué es el consenso en el sistema distribuido? <a href="#id-9b75" id="id-9b75"></a>

En un sistema distribuido, múltiples ordenadores (conocidos como **nodos**) están mutuamente conectados entre sí y colaboran entre sí a través del paso de mensajes. Ahora, durante el cálculo, deben ponerse de acuerdo sobre un valor común para coordinar entre múltiples procesos. Este fenómeno se conoce como **Consenso Distribuido**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2026-03-14 a la(s) 11.36.29.png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2026-03-14 a la(s) 11.36.57.png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2026-03-14 a la(s) 11.37.09.png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2026-03-14 a la(s) 11.37.22.png" alt="" width="375"><figcaption></figcaption></figure>

## ¿Por qué se requiere consenso? <a href="#id-09a3" id="id-09a3"></a>

En un sistema distribuido, puede suceder que varios nodos estén procesando grandes cálculos distribuidos y necesiten conocer los resultados de cada nodo para mantenerlos actualizados sobre todo el sistema. En tal situación, los nodos deben ponerse de acuerdo sobre un valor común. Aquí es donde entra en escena el requisito de consenso.

### Cómo lograr el consenso en el sistema distribuido <a href="#id-6297" id="id-6297"></a>

Consideremos un sistema distribuido, donde _n_ nodos están conectados entre sí. Un nodo envía un mensaje a todos los demás nodos diciendo _"¡Chicos! Elegí valuev, ¿están todos de acuerdo?"._

Ahora, pueden surgar dos escenarios.

1. Todos los valores acordados _**v**_
2. Algunos de ellos no están de acuerdo

**Caso 1:** Todos los nodos pueden llevar a cabo su tarea manteniendo _**v**_ como el valor en su mente.

**Caso 2:** Supongamos que uno de los nodos sugiere _"Prefiero el valor **w".**_ En este tipo de situación, lograr un consenso es complicado.

Para lograr consenso, todos los nodos del sistema distribuido deben seguir el mismo protocolo al comunicarse. Hay tres condiciones básicas que deben ser satisfechas por el sistema para los algoritmos de consenso.

1. **Acuerdo:** Todos los nodos no defectuosos deben estar de acuerdo en el mismo valor.
2. **Validez:** Si un sistema ha decidido un valor _**v**_, entonces ese valor debe ser sugerido por uno de los nodos no defectuosos del sistema y todos los demás nodos no defectuosos deben decidir ese valor _**v**_.
3. **Terminación:** Cada nodo no defectuoso debe estar de acuerdo en algún valor. Si uno de los nodos no defectuosos está de acuerdo en el valor _**v,**_ no se puede lograr un consenso.

> Nodo no defectuoso significa, nodo que no está bloqueado, atacado o que no funciona mal.

### Desafíos en el consenso distribuido <a href="#c601" id="c601"></a>

Un sistema distribuido puede enfrentar principalmente dos tipos de fallas.

1. Fallo de choque
2. Fracaso bizantino

**El fallo de bloqueo** ocurre cuando un nodo no responde a otros nodos del sistema debido a algún fallo de hardware o software o red. Este es un problema muy común en los sistemas distribuidos y se puede manejar fácilmente simplemente ignorando la existencia del nodo.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2026-03-14 a la(s) 11.38.28.png" alt="" width="375"><figcaption></figcaption></figure>

**El fallo bizantino** es una situación en la que uno o más nodos no se bloquean, sino que se comportan de forma anormal y reenvían un mensaje diferente a diferentes pares, debido a un ataque interno o externo a ese nodo. Manejar este tipo de situación es complicado en el sistema distribuido.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2026-03-14 a la(s) 11.39.00.png" alt="" width="375"><figcaption></figcaption></figure>

Un algoritmo de consenso, si puede manejar el fallo bizantino, puede manejar cualquier tipo de problema de consenso en un sistema distribuido.

### Algoritmos de consenso <a href="#e9bd" id="e9bd"></a>

#### Algoritmos de consenso basados en la votación <a href="#id-1221" id="id-1221"></a>

Algunas de las primeras implementaciones de algoritmos de consenso comenzaron a emplear varias técnicas basadas en la votación. Estos tienen suficiente tolerancia a fallas y suficiente prueba matemática para garantizar la seguridad y la estabilidad. Sin embargo, debido a su carácter democrático, estos algoritmos son extremadamente lentos e ineficientes, especialmente a medida que la red se hace más grande.

1. **Tolerancia práctica a fallas bizantinas**

Entendamos la tolerancia práctica a la falla bizantina (pBFT) a través de un ejemplo.



_Imagina que varias divisiones del ejército bizantino están acampadas fuera de una ciudad enemiga, cada división comandada por su propio general._

_Los generales solo pueden comunicarse entre sí por mensajería. Después de observar al enemigo, deben decidir sobre un plan de acción común._

_Sin embargo, algunos de los generales pueden ser traidores, tratando de evitar que los generales leales lleguen a un acuerdo. Los generales deben decidir cuándo atacar la ciudad, pero necesitan una gran mayoría de su ejército para atacar al mismo tiempo._

_Los generales deben tener un algoritmo para garantizar que (a) todos los generales leales decidan sobre el mismo plan de acción, y (b) un pequeño número de traidores no pueden hacer que los generales leales adopten un mal plan._

_Todos los generales leales harán lo que el algoritmo dice que deben hacer, pero los traidores pueden hacer lo que quieran. El algoritmo debe garantizar la condición (a) independientemente de lo que hagan los traidores. Los generales leales no solo deben llegar a un acuerdo, sino que deben acordar un plan razonable._

El padre de los Sistemas Distribuido&#x73;_, Leslie Lamport,_ demostró que...

> Si más de **dos tercios** de todos los nodos en un sistema son honestos, entonces se puede llegar a un consenso.

Este algoritmo funciona según el principio mencionado anteriormente. El sistema distribuido se divide en tres fases _(pre-preparación, preparación, confirmación)_ y los nodos se ordenan secuencialmente con un nodo siendo el **nodo primario (o nodo líder)** y otros como el **nodo secundario (o nodo de respaldo).** El objetivo es que todos los nodos defectuosos ayuden a lograr un consenso con respecto al estado del sistema utilizando la regla mayoritaria.

Las rondas de consenso de pBFT son -

* El cliente envía una solicitud al nodo principal.
* Los nodos primarios transmiten la solicitud a todos los nodos secundarios.
* Todos los nodos realizan el servicio que se solicita y lo envían al cliente como respuesta.
* La solicitud se sirve con éxito cuando el cliente recibió un mensaje similar de al menos dos tercios del total de nodos.

El nodo primario se reemplaza en cada ronda consecutiva utilizando el _protocolo de cambio de vista_ si pasa una cantidad predeterminada de tiempo sin que el nodo líder transmita una solicitud a los nodos de respaldo (secundario).

2\. **Otros algoritmos notables**

Hay otros algoritmos de consenso basados en la votación como -

* [Cosas calientes](https://arxiv.org/abs/1803.05069v6)
* [Paxos](https://en.wikipedia.org/wiki/Paxos_\(computer_science\))
* [Balsa](https://en.wikipedia.org/wiki/Raft_\(algorithm\)), etc...

#### Algoritmos de consenso basados en pruebas <a href="#id-8ffa" id="id-8ffa"></a>

Con el desarrollo de la tecnología blockchain y los libros mayores distribuidos, las redes se volvieron considerablemente más amplias y sin permiso. Para estas circunstancias, una técnica de consenso basada en la prueba parecía preferible. En este caso, un participante debe mostrar pruebas adecuadas de algo para contribuir a la toma de decisiones.

Hay varios algoritmos de consenso basados en pruebas -

* [Prueba de trabajo (PoW)](https://en.wikipedia.org/wiki/Proof_of_work)
* [Prueba de participación (PoS), ](https://en.wikipedia.org/wiki/Proof_of_stake)etc...

### Aplicación del consenso distribuido <a href="#id-43ec" id="id-43ec"></a>

Los algoritmos de consenso se utilizan en muchas aplicaciones del mundo real en redes distribuidas o descentralizadas -

✅ Blockchain y criptomonedas

✅ rango de página de Google

✅ Equilibrio de carga, etc...
