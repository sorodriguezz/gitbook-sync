# Load Balancing

El equilibrio de carga es el proceso de **distribuir el tráfico de red entrante** entre múltiples servidores para garantizar que ningún servidor se vea sobrecargado.

<figure><img src="../../.gitbook/assets/image (31).png" alt="" width="375"><figcaption></figcaption></figure>

Al distribuir uniformemente la carga de trabajo, el equilibrio de carga tiene como objetivo **evitar la sobrecarga** en un solo servidor, **mejorar el rendimiento** al reducir los tiempos de respuesta y **mejorar la disponibilidad** al redirigir el tráfico en caso de fallas del servidor.

Existen varios algoritmos para lograr el equilibrio de carga, cada uno con sus ventajas y desventajas.

En este artículo, profundizaremos en los algoritmos de equilibrio de carga más utilizados, cómo funcionan, cuándo usarlos, sus beneficios/desventajas y cómo implementarlos en el código.

## **Algoritmo 1: Round Robin**

<figure><img src="../../.gitbook/assets/image (32).png" alt="" width="375"><figcaption></figcaption></figure>

**Cómo funciona:**

1. Se envía una solicitud al primer servidor de la lista.
2. La siguiente solicitud se envía al segundo servidor, y así sucesivamente.
3. Después del último servidor de la lista, el algoritmo vuelve al primer servidor.

**Cuándo utilizar :**

* Cuando todos los servidores tienen capacidades de procesamiento similares y son igualmente capaces de manejar solicitudes.
* Cuando la simplicidad y la distribución uniforme de la carga son más críticas.

**Beneficios :**

* Fácil de implementar y entender.
* Garantiza una distribución uniforme del tráfico.

**Desventajas :**

* No tiene en cuenta la carga del servidor ni el tiempo de respuesta.
* Puede generar ineficiencias si los servidores tienen diferentes capacidades de procesamiento.

**Implementación:**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.09.22.png" alt=""><figcaption></figcaption></figure>

En esta implementación, la `RoundRobin`clase mantiene una **lista de servidores** y realiza un seguimiento del **índice actual** .

Actualiza `get_next_server()`el índice y devuelve el siguiente servidor del ciclo.

## Algoritmo 2: Round Robin ponderado

<figure><img src="../../.gitbook/assets/image (33).png" alt="" width="375"><figcaption></figcaption></figure>

**Cómo funciona:**

1. A cada servidor se le asigna un **peso** en función de su potencia de procesamiento o recursos disponibles.
2. Los servidores con pesos más altos reciben una parte proporcionalmente mayor de solicitudes entrantes.

**Cuándo utilizarlo:**

* Cuando los servidores tienen diferentes capacidades de procesamiento o recursos disponibles.
* Cuando desea distribuir la carga en función de la capacidad de cada servidor.

**Beneficios :**

* Equilibra la carga según la capacidad del servidor.
* Uso más eficiente de los recursos del servidor.

**Desventajas :**

* Un poco más complejo de implementar que el simple Round Robin.
* No tiene en cuenta la carga actual del servidor ni el tiempo de respuesta.

**Implementación:**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.10.54.png" alt=""><figcaption></figcaption></figure>

En esta implementación, la `WeightedRoundRobin`clase toma una lista de servidores y sus pesos correspondientes.

El `get_next_server()`método ejecuta un bucle infinito para encontrar un servidor adecuado en función de los pesos, lo que garantiza que los servidores con pesos más altos reciban más solicitudes.

El algoritmo realiza un seguimiento del peso actual y lo ajusta en cada iteración para mantener la relación de distribución deseada.

**Ejemplo:** si los pesos son `[5, 1, 1]`, el Servidor 1 se seleccionará 5 veces más a menudo que el Servidor 2 o el Servidor 3.

## **Algoritmo 3: Mínimas conexiones**

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

**Cómo funciona:**

1. Supervisar el **número de conexiones activas** en cada servidor.
2. Asigna las solicitudes entrantes al servidor con el menor número de conexiones activas.

**Cuándo utilizarlo:**

* Cuando desee distribuir la carga en función del número actual de conexiones activas.
* Cuando los servidores tienen capacidades de procesamiento similares pero pueden tener diferentes niveles de conexiones simultáneas.

**Beneficios :**

* Los saldos se cargan de forma más dinámica en función de la carga actual del servidor.
* Ayuda a evitar que cualquier servidor se sobrecargue con una gran cantidad de conexiones activas.

**Desventajas :**

* Puede no ser óptimo si los servidores tienen diferentes capacidades de procesamiento.
* Requiere seguimiento de las conexiones activas para cada servidor.

**Implementación:**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.12.09.png" alt="" width="375"><figcaption></figcaption></figure>

En este ejemplo, la `LeastConnections`clase mantiene un mapa de servidores y la cantidad de conexiones activas para cada servidor.

El `get_next_server()`método selecciona un servidor aleatorio con la menor cantidad de conexiones e incrementa el número de conexiones para ese servidor.

El `release_connection()`método se llama cuando se cierra una conexión, lo que disminuye el recuento de conexiones para el servidor correspondiente.

## **Algoritmo 4: Tiempo mínimo de respuesta**

<figure><img src="../../.gitbook/assets/image (35).png" alt="" width="375"><figcaption></figcaption></figure>

**Cómo funciona :**

* Monitorea el tiempo de respuesta de cada servidor
* Asigna las solicitudes entrantes al servidor con el tiempo de respuesta más rápido.

**Cuándo utilizar :**

* Cuando tienes servidores con tiempos de respuesta variables y deseas enrutar las solicitudes al servidor más rápido.

**Beneficios :**

* Minimiza la latencia general al seleccionar el servidor con el tiempo de respuesta más rápido.
* Puede adaptarse dinámicamente a los cambios en los tiempos de respuesta del servidor.
* Ayuda a mejorar la experiencia del usuario al proporcionar respuestas rápidas.

**Desventajas :**

* Requiere una medición precisa de los tiempos de respuesta del servidor, lo que puede resultar un desafío en sistemas distribuidos.
* Es posible que no tenga en cuenta otros factores como la carga del servidor o el número de conexiones.

**Implementación:**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.13.51.png" alt=""><figcaption></figcaption></figure>

En este ejemplo, la `LeastResponseTime`clase mantiene una lista de servidores y realiza un seguimiento del tiempo de respuesta de cada servidor.

El `get_next_server()`método selecciona el servidor con el menor tiempo de respuesta. `update_response_time()`Se llama después de cada solicitud para actualizar el tiempo de respuesta del servidor correspondiente.

Para simular el tiempo de respuesta, utilizamos una `simulate_response_time()`función que introduce un retraso aleatorio para imitar el tiempo de respuesta del servidor.

En un escenario del mundo real, medirías el tiempo de respuesta real de cada servidor.

## **Algoritmo 5: Hash de IP**

<figure><img src="../../.gitbook/assets/image (36).png" alt="" width="375"><figcaption></figcaption></figure>

**Cómo funciona :**

* Calcula un valor hash a partir de la dirección IP del cliente y lo utiliza para determinar el servidor que enrutará la solicitud.

**Cuándo utilizar :**

* Cuando se necesita persistencia de la sesión, ya que las solicitudes del mismo cliente siempre se dirigen al mismo servidor.

**Beneficios :**

* Fácil de implementar.
* Útil para aplicaciones que requieren sesiones persistentes.

**Desventajas :**

* Puede provocar una distribución desigual de la carga si ciertas direcciones IP generan más tráfico que otras.
* Carece de flexibilidad si un servidor deja de funcionar, ya que puede ser necesario reconfigurar el mapeo hash.

**Implementación:**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.15.08.png" alt=""><figcaption></figcaption></figure>

En esta implementación, la `IPHash`clase toma una lista de servidores.

El `get_next_server()`método calcula el hash MD5 de la dirección IP del cliente y utiliza el operador módulo para determinar el índice del servidor al que se debe enrutar la solicitud.

Esto garantiza que **las solicitudes de la misma dirección IP siempre se dirijan al mismo servidor.**

## **Resumen:**

* **Round Robin** : distribución simple y uniforme, mejor para servidores homogéneos.
* **Round Robin ponderado** : se distribuye en función de la capacidad del servidor, ideal para entornos heterogéneos.
* **Menos conexiones** : se equilibra dinámicamente en función de la carga, ideal para cargas de trabajo variables.
* **Tiempo de respuesta mínimo** : optimiza para una respuesta más rápida, ideal para entornos con rendimiento de servidor variable.
* **Hash de IP** : garantiza la persistencia de la sesión, útil para aplicaciones con estado.

La elección del algoritmo de equilibrio de carga adecuado depende de las necesidades y características específicas de su sistema, incluidas las capacidades del servidor, la distribución de la carga de trabajo y los requisitos de rendimiento.
