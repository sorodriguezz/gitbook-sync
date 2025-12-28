# Hashing consistente

En un **sistema distribuido donde frecuentemente se agregan o eliminan** nodos (servidores) , enrutar solicitudes de manera eficiente se vuelve un desafío.

Un enfoque común es utilizar **un hash en la solicitud** y asignarla a un servidor usando `Hash(key) mod N`, donde N es el número de servidores.

Sin embargo, este método depende en gran medida de la cantidad de servidores y cualquier cambio en N puede generar un **rehashing significativo** , causando una redistribución importante de claves (solicitudes).

**El hash consistente** soluciona este problema al garantizar que solo sea necesario reasignar un pequeño subconjunto de claves cuando se agregan o eliminan nodos.

Popularizado por [**el artículo Dynamo de Amazon**](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf) , ahora se ha convertido en una técnica fundamental en bases de datos distribuidas como DynamoDB, Cassandra y ScyllaDB.

En este artículo, exploraremos qué es el hash consistente, por qué es necesario, cómo funciona y cómo implementarlo en el código.

## 1. El problema con el hash tradicional

Imagina que estás desarrollando una **aplicación web con mucho tráfico** que atiende a millones de usuarios a diario. Para gestionar la carga eficientemente, distribuyes las solicitudes entrantes entre varios servidores backend mediante un **balanceador de carga basado en hash** .

Su sistema consta de **5 servidores backend** ( `S0, S1, S2, S3, S4`), y las solicitudes se asignan mediante una función hash que asigna la dirección IP de cada usuario **a un servidor específico**.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.34.18.png" alt="" width="375"><figcaption></figcaption></figure>

El proceso funciona así:

1. El balanceador de carga toma la dirección IP de un usuario (o ID de sesión).
2. Una **función hash** asigna la IP a uno de los servidores backend tomando la **suma de bytes en la dirección IP** y calculando `mod 5`(ya que tenemos 5 servidores).
3. La solicitud se **enruta al servidor asignado** , lo que garantiza que el mismo usuario siempre sea dirigido al mismo servidor para mantener la coherencia de la sesión.

**Ejemplo:**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.35.08.png" alt="" width="375"><figcaption></figcaption></figure>

### **Todo funciona bien…hasta que escalas**

Este enfoque funciona **siempre que el número de servidores se mantenga constante** . Pero ¿qué sucede al **agregar o eliminar un servidor** ?

### **Escenario 1: Agregar un nuevo servidor (S5)**

A medida que aumenta el tráfico, decide **escalar** agregando un nuevo servidor backend ( `S5`). Ahora, debe modificar la función hash para usar `mod 6`en lugar de , `mod 5` ya que ahora tenemos 6 servidores.

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

### **Escenario 2: Eliminación de un servidor (S4)**

Ahora, supongamos que uno de los servidores ( `S4`) falla o se elimina. El número de servidores se reduce a 4, lo que obliga a la función hash a cambiar de `mod 5`a `mod 4`.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.39.19.png" alt="" width="375"><figcaption></figcaption></figure>

Aunque solo se eliminó **un servidor, la mayoría de los usuarios fueron reasignados a servidores diferentes** . Esto puede causar:

* **Pérdida de sesión:** los usuarios activos pueden cerrar sesión o desconectarse.
* **Invalidación de caché:** los datos almacenados en caché se vuelven irrelevantes, lo que aumenta la carga de la base de datos.
* **Degradación grave del rendimiento:** el sistema puede tener dificultades para funcionar de manera eficiente.

#### **La solución: Hashing consistente**

**El hash consistente** ofrece una solución más escalable y eficiente al garantizar que solo una **pequeña fracción de usuarios** se reasignen al aumentar o reducir la escala.

Funciona muy bien cuando se opera en entornos dinámicos, donde el sistema aumenta y reduce su escala con frecuencia.

## 2. Cómo funciona el hash consistente

El hash consistente es una **técnica de hash distribuido** que se utiliza para distribuir datos de manera eficiente entre múltiples nodos (servidores, cachés, etc.).

Utiliza un **espacio hash circular** (anillo hash) con un espacio hash grande y constante.

Tanto los nodos (servidores, cachés o bases de datos) como las claves (elementos de datos) se asignan a posiciones en este anillo hash mediante una **función hash** .

A diferencia del hash basado en módulo, donde los cambios en la cantidad de nodos provocan una reasignación a gran escala, el hash consistente garantiza que solo una pequeña fracción de claves se reasigna cuando se agrega o elimina un nodo, lo que lo hace altamente escalable y eficiente.

> En el hash consistente, cuando cambia el número de nodos, solo `k/n` es necesario reasignar las claves, donde `k`es el número total de claves y `n`es el número total de nodos.

### **2.1 Construcción del anillo hash**

En lugar de distribuir claves basadas en `Hash(key) mod N`, el hash consistente coloca tanto los servidores como las claves en un anillo hash circular.

**Definición del espacio hash**

* Utilizamos un espacio hash grande y fijo que va desde `0`hasta `2^32 - 1`(asumiendo una función hash de 32 bits).
* Esto crea una estructura circular, donde los valores se retoman después de alcanzar el límite máximo.

**Colocación de servidores en el anillo**

* A cada **servidor (nodo)** se le asigna una posición en el anillo hash mediante el cálculo `Hash(server_id)`.
* Utilizando el ejemplo anterior con **5 servidores (** `S0, S1, S2, S3, S4`**)** , la función hash los distribuye en diferentes posiciones alrededor del anillo.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

**Asignación de claves a servidores**

* Cuando se agrega una clave, su posición se determina mediante el cálculo `Hash(key)`.
  * Ejemplo: a la solicitud de un usuario se le asigna una posición en el anillo en función del hash de su dirección IP:`Hash(IP Address)`
* Luego nos movemos en el sentido de las agujas del reloj alrededor del anillo hasta que encontramos el siguiente servidor disponible.
* La clave (o solicitud) se asigna a este servidor para su almacenamiento o recuperación.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

> **Nota:** En caso de que el hash de una clave caiga directamente en la posición de un nodo, pertenece a ese nodo.

### **2.2 Agregar un nuevo servidor**

Supongamos que agregamos un **nuevo servidor (** `S5`**)** al sistema.

* La posición de `S5`las caídas entre `S1`y `S2`en el anillo hash.
* `S5`se hace cargo de todas las claves (solicitudes) que se encuentran entre `S1`y `S5`, que antes eran manejadas por `S2`.
  * **Ejemplo:** las solicitudes que originalmente fueron asignadas a ahora serán redirigidas a `User D’s S2,S5.`

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Esto demuestra cómo el hash consistente redistribuye eficientemente las claves con una interrupción mínima, garantizando que solo un pequeño subconjunto de claves se reasigne cuando se agregan nuevos servidores.

### **2.3 Eliminación de un nodo**

Cuando un servidor, como `S4`, falla o se elimina del sistema:

* Todas las claves asignadas previamente `S4`se reasignan al siguiente servidor disponible en el anillo ( `S3`).
* Solo es necesario mover las claves (solicitudes) que fueron asignadas , mientras que todas las demás claves permanecen inafectadas.`S4`

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Esto da como resultado un **movimiento mínimo de datos** , a diferencia del hash tradicional, donde eliminar un nodo requeriría reasignar la mayoría de las claves.

## 3. Nodos virtuales

En el **hash consistente básico** , a cada servidor se le asigna **una única posición** en el anillo hash. Sin embargo, esto puede provocar una **distribución desigual de los datos** , especialmente cuando:

* El número de servidores es pequeño.
* Algunos servidores se agrupan accidentalmente y crean **puntos calientes** .
* **Se elimina** un servidor , lo que provoca un cambio repentino de carga hacia su vecino inmediato.

**Los nodos virtuales (VNodes)** son una técnica utilizada en el hash consistente para mejorar **el equilibrio de carga** y **la tolerancia a fallas** al distribuir los datos de manera más uniforme entre los servidores.

**Cómo funcionan los nodos virtuales**

En lugar de asignar una posición por servidor, a cada servidor físico se le asignan **múltiples posiciones** (nodos virtuales) en el anillo hash.

* Cada servidor se envía varias veces a diferentes ubicaciones en el anillo.
* Cuando se aplica un hash a una solicitud (clave), se asigna al siguiente nodo virtual en el sentido de las agujas del reloj.
* Luego, la solicitud se envía al servidor real asociado con el nodo virtual.

**Ejemplo**

Supongamos que tenemos **tres servidores físicos (S1, S2, S3)** . Sin nodos virtuales, sus posiciones podrían ser:

```
S1 → Position 10
S2 → Position 50
S3 → Position 90
```

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Si **S1 falla** , todas sus claves deben reasignarse a **S2** , lo que puede crear una sobrecarga.

Con **nodos virtuales** , cada servidor se cifra varias veces:

```
S1-1 → Position 10
S1-2 → Position 70
S1-3 → Position 120
S2-1 → Position 50
S2-2 → Position 80
S2-3 → Position 160
S3-1 → Position 30
S3-2 → Position 90
S3-3 → Position 140
```

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Ahora, en lugar de un solo punto, `S1`se representa en **múltiples posiciones** , lo que hace que la distribución **sea más uniforme** .

Si **S1 falla** , sus claves se redistribuyen de manera más uniforme entre **S2 y S3** , en lugar de que todas vayan a **S2** .

## 4. Implementación de código (Python)

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.49.01.png" alt=""><figcaption></figcaption></figure>

**Explicación:**

1. **Componentes clave**
   * **Anillo hash (** `self.ring`**):** Almacena valores hash → asignaciones de servidores. Utiliza **nodos virtuales** (réplicas) para un mejor equilibrio de carga.
   * **Claves ordenadas (** `self.sorted_keys`**):** mantiene una **lista ordenada** de valores hash para búsquedas eficientes.
   * **Conjunto de servidores (** `self.servers`**):** realiza un seguimiento de los servidores físicos activos.
2. **Inicialización (** `__init__`**)**
   * Llama `add_server()`a cada servidor y le aplica un hash varias veces (según `num_replicas`) para garantizar una distribución uniforme.
3. **Función hash (** `_hash`**)**
   * Utiliza **el algoritmo hash MD5** para convertir cadenas en números enteros grandes para una ubicación consistente en el anillo hash.
4. **Agregar un servidor (** `add_server`**)**
   * Genera múltiples valores hash para cada servidor ( `server-0`, `server-1`, etc.).
   * Los almacena `self.ring`y mantiene un orden ordenado `self.sorted_keys`para una búsqueda rápida.
5. **Eliminar un servidor (** `remove_server`**)**
   * Elimina los valores hash del servidor y sus nodos virtuales de `self.ring`y `self.sorted_keys`.
6. **Obtener un servidor (** `get_server`**)**
   * Calcula la clave de entrada y encuentra el servidor más cercano **en sentido horario usando** . Regresa al primer nodo si es necesario.`bisect.bisect()`
