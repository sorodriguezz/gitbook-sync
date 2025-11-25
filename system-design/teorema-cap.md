# Teorema CAP

El **teorema CAP** , introducido por Eric Brewer en 2000, proporciona un marco fundamental para comprender las **compensaciones** que deben hacerse al diseñar sistemas distribuidos.

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.25.07.png" alt="" width="375"><figcaption></figcaption></figure>

CAP significa **Consistencia** , **Disponibilidad** y **Tolerancia de Partición** , y el teorema establece que:

> **Es imposible que un almacén de datos distribuido proporcione simultáneamente las tres garantías.**

* **Consistencia (C)** : cada lectura recibe la escritura más reciente o un error.
* **Disponibilidad (A)** : Cada solicitud (lectura o escritura) recibe una respuesta sin errores, sin garantía de que contenga la escritura más reciente.
* **Tolerancia de partición (P)** : el sistema continúa funcionando a pesar de que la red descarta (o retrasa) una cantidad arbitraria de mensajes entre nodos.

En este artículo, exploraremos los tres pilares del teorema CAP, las compensaciones y las estrategias de diseño prácticas para construir sistemas distribuidos resilientes y escalables.

## Los 3 pilares de la PAC

### **1. Consistencia**

La consistencia garantiza que **cada lectura reciba la escritura más reciente o un error** . Esto significa que todos los nodos activos en un sistema distribuido devolverán los mismos datos en cualquier momento.

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.26.54.png" alt="" width="375"><figcaption></figcaption></figure>

> En un sistema distribuido consistente, si escribe datos en el nodo A, una operación de lectura del nodo B reflejará inmediatamente la operación de escritura en el nodo A.

La coherencia es crucial para las aplicaciones en las que es fundamental disponer de los datos más actualizados, como en los sistemas financieros, donde una consulta de saldo debe reflejar el estado más actualizado de una cuenta.

### **2. Disponibilidad**

La disponibilidad garantiza que **cada solicitud (de lectura o escritura) reciba una respuesta** , sin necesidad de garantizar que contenga la escritura más reciente. Esto significa que el sistema permanece **operativo** y **con capacidad de respuesta** , incluso si la respuesta de algunos nodos no refleja los datos más actualizados.

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.27.49.png" alt="" width="375"><figcaption></figcaption></figure>

La disponibilidad es importante para las aplicaciones que necesitan permanecer operativas en todo momento, como los sistemas de venta minorista en línea.

### **3. Tolerancia de partición**

La tolerancia a particiones significa que el **sistema continúa funcionando a pesar de las particiones de red** donde los nodos no pueden comunicarse entre sí.

<figure><img src="../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.28.35.png" alt="" width="375"><figcaption></figcaption></figure>

> Una **partición de red** ocurre cuando una falla de red hace que un sistema distribuido se divida en dos o más grupos de nodos que no pueden comunicarse entre sí.

Cuando hay una partición de red, el sistema debe elegir entre **Consistencia** y **Disponibilidad** .

La tolerancia a particiones es esencial para los sistemas distribuidos, ya que las fallas de red pueden ocurrir, y de hecho ocurren. Un sistema que tolera particiones puede mantener las operaciones en diferentes segmentos de la red.

## El dilema de la PAC: elegir 2 de 3

El teorema CAP afirma que en presencia de una partición de red, un sistema distribuido debe elegir entre **Consistencia** y **Disponibilidad** .

Exploremos estos escenarios:

### **CP (Consistencia y tolerancia de partición):**

Estos sistemas priorizan la consistencia y pueden tolerar particiones de red, pero a costa de la disponibilidad. Durante una partición, el sistema puede rechazar algunas solicitudes para mantener la consistencia.

Las bases de datos relacionales tradicionales, como MySQL y PostgreSQL, cuando se configuran para una fuerte consistencia, priorizan la consistencia sobre la disponibilidad durante las particiones de red.

> **Los sistemas bancarios** generalmente priorizan **la consistencia sobre la disponibilidad,** ya que la precisión de los datos es más crítica que la disponibilidad durante problemas de red.
>
> Considere la red de cajeros automáticos de un banco. Al retirar dinero, el sistema debe garantizar que su saldo se actualice con precisión en todos los nodos (consistencia) para evitar sobregiros u otros errores.

### **AP (Disponibilidad y tolerancia de partición):**

Estos sistemas garantizan la disponibilidad y pueden tolerar particiones de red, pero a costa de la consistencia. Durante una partición, distintos nodos pueden devolver valores distintos para los mismos datos.

Las bases de datos NoSQL como Cassandra y DynamoDB están diseñadas para ser altamente disponibles y tolerantes a particiones, potencialmente a costa de una fuerte consistencia.

> **El sistema de carrito de compras de Amazon** está diseñado para aceptar siempre artículos, priorizando la disponibilidad.
>
> Cuando agregas artículos a tu carrito de Amazon, la acción casi nunca falla, incluso durante períodos de mucho tráfico como el Viernes Negro.

### **CA (Consistencia y Disponibilidad):**

En ausencia de particiones, un sistema puede ser consistente y estar disponible. Sin embargo, las particiones de red son inevitables en sistemas distribuidos, lo que hace que esta combinación sea poco práctica.

**Sistemas de ejemplo:** Las bases de datos de un solo nodo pueden proporcionar consistencia y disponibilidad, pero no son tolerantes a particiones. En un entorno distribuido, esta combinación es teóricamente imposible.

## Estrategias prácticas de diseño

El diseño de sistemas distribuidos requiere equilibrar cuidadosamente estas compensaciones en función de los requisitos de la aplicación.

A continuación se presentan algunas estrategias prácticas:

### **1. Consistencia eventual**

Para muchos sistemas, la consistencia estricta no siempre es necesaria.

La consistencia eventual puede proporcionar un buen equilibrio donde las actualizaciones se propagan a todos los nodos eventualmente, pero no inmediatamente.

> **Ejemplo:** Sistemas donde la consistencia inmediata no es crítica, como DNS y redes de distribución de contenido (CDN).

### **2. Fuerte consistencia**

Un modelo que garantiza que una vez que se confirma una escritura, cualquier lectura posterior devolverá ese valor.

> **Ejemplo:** Sistemas que requieren alta precisión de datos, como aplicaciones financieras y de gestión de inventario.

### **3. Consistencia ajustable**

La consistencia ajustable permite que los sistemas ajusten sus niveles de consistencia según necesidades específicas, proporcionando un equilibrio entre una consistencia fuerte y una consistencia eventual.

Sistemas como Cassandra permiten configurar el nivel de consistencia para cada consulta, lo que proporciona flexibilidad.

> **Ejemplo:** Aplicaciones que necesitan diferentes niveles de consistencia para diferentes operaciones, como plataformas de comercio electrónico donde el procesamiento de pedidos requiere una fuerte consistencia pero las recomendaciones de productos pueden tolerar una consistencia eventual.

### **4. Enfoques basados ​​en el quórum:**

Los enfoques basados ​​en quórum utilizan la votación entre un grupo de nodos para garantizar un cierto nivel de consistencia y tolerancia a fallas.

> **Ejemplo:** Sistemas que necesitan un equilibrio entre consistencia y disponibilidad, a menudo utilizados en algoritmos de consenso como Paxos y Raft.

## Más allá de CAP: PACELC

Si bien el CAP es fundamental, no cubre todos los escenarios.

Daniel Abadi propuso el teorema [**PACELC**](https://en.wikipedia.org/wiki/PACELC_theorem) como una extensión al introducir **la latencia** y **la consistencia** como atributos adicionales de los sistemas distribuidos.

* Si hay una partición (P), el equilibrio es entre disponibilidad y consistencia (A y C).
* De lo contrario (E), el equilibrio es entre **latencia (L)** y **consistencia (C)** .

Este teorema reconoce que incluso cuando el sistema funciona normalmente, existe un equilibrio entre latencia y consistencia.

En conclusión, el Teorema CAP es una herramienta poderosa para comprender las compensaciones inherentes al diseño de sistemas distribuidos. No se trata de elegir la mejor propiedad, sino de tomar decisiones informadas según las necesidades específicas de la aplicación.

Al evaluar cuidadosamente las ventajas y desventajas del CAP, puede diseñar sistemas robustos y resilientes que brinden el equilibrio adecuado entre consistencia, disponibilidad y tolerancia a particiones.
