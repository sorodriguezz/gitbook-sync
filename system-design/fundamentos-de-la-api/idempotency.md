# Idempotency

Imagina que estás realizando una **compra**  en una tienda en línea.

Presiona **"pagar"** pero la pantalla se congela y no estás seguro de si el pago se realizó.

Entonces,  **actualiza** la página y **vuelve a intentarlo**.

Detrás de escena, ¿Cómo garantiza el sistema que no se **le cobrará dos veces** por accidente?

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.27.06.png" alt="" width="563"><figcaption></figcaption></figure>

Este escenario resalta un problema común en los sistemas distribuidos:  **gestionar operaciones repetidas con elegancia.**

La solución a este problema reside en el concepto de  **idempotencia.**

En este blog, exploraremos qué es la idempotencia, por qué es importante, cómo implementarla, los desafíos, las consideraciones y las mejores prácticas para garantizar sistemas sólidos y confiables.

## 1. ¿Qué es la idempotencia? <a href="#id-1-what-is-idempotency" id="id-1-what-is-idempotency"></a>

> En matemáticas, una operación es idempotente si aplicándola varias veces produce el mismo resultado que aplicándola una sola vez.

Por ejemplo, la función de valor absoluto es idempotente: `||-5|| = |-5| = 5.`

**La idempotencia**  es una propiedad de ciertas operaciones según la cual ejecutar la misma operación varias veces produce el mismo resultado que ejecutarla una sola vez.

Por ejemplo: si una solicitud para eliminar un elemento es idempotente, todas las solicitudes posteriores a la primera no tendrán impacto.

En programación, establecer un valor es idempotente, mientras que incrementar un valor no lo es.

`Idempotent: user.status = 'active'`

`Not Idempotent: user.login_count += 1`

Algunas operaciones son naturalmente idempotentes.

`UPDATE users SET status = 'active' WHERE id = 123;`

No importa cuantas veces ejecutes esto, el resultado sigue siendo el mismo.

### Por qué es importante la idempotencia <a href="#why-idempotency-matters" id="why-idempotency-matters"></a>

Los sistemas distribuidos suelen requerir  **tolerancia a fallos**  para garantizar una alta disponibilidad. Cuando un problema de red provoca un  **tiempo de espera**  o un  **error** , el cliente podría  **reintentar**  la solicitud.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 22.28.40.png" alt=""><figcaption></figcaption></figure>

Si el sistema maneja reintentos sin idempotencia, cada reintento podría cambiar el estado del sistema de manera impredecible.

Al diseñar operaciones para que sean idempotentes, los ingenieros crean un amortiguador contra comportamientos inesperados causados ​​por reintentos.

Esta “red de seguridad” evita que repetidos intentos distorsionen el resultado, garantizando estabilidad y fiabilidad.

## 2. Estrategias para implementar la idempotencia <a href="#id-2-strategies-to-implement-idempotency" id="id-2-strategies-to-implement-idempotency"></a>

### 1. Identificadores de solicitud únicos <a href="#id-1-unique-request-identifiers" id="id-1-unique-request-identifiers"></a>

Una de las técnicas más simples para lograr la idempotencia es adjuntar un  **identificador único** , a menudo llamado  **clave de idempotencia,**  a cada solicitud.

Cuando un cliente realiza una solicitud, genera un  **ID único**  que el servidor utiliza para rastrearla. Si el servidor recibe posteriormente una solicitud con el mismo ID, sabe que es un duplicado y la descarta.

> **Ejemplo** : Un servicio de pago podría exigir que cada solicitud de transacción incluya un ID único. Si el cliente vuelve a intentarlo con el mismo ID, el servidor omitirá el cargo, evitando así transacciones duplicadas.

**Ejemplo de código:**

```python
from flask import Flask, request, jsonify
import sqlite3

app = Flask(__name__)

# Initialize SQLite connection
def get_db():
    conn = sqlite3.connect('database.db')
    return conn

@app.route('/process_payment', methods=['POST'])
def process_payment():
    request_id = request.headers.get("Request-ID")
    db = get_db()
    cursor = db.cursor()

    # Check if request_id already processed
    cursor.execute("SELECT 1 FROM processed_requests WHERE request_id = ?", (request_id,))
    if cursor.fetchone():
        return jsonify({"message": "Duplicate request ignored"}), 200

    # Process payment
    # Here you would include your payment processing logic
    print("Processing payment...")

    # Mark request as processed
    cursor.execute("INSERT INTO processed_requests (request_id) VALUES (?)", (request_id,))
    db.commit()
    return jsonify({"message": "Payment processed successfully"}), 200

if __name__ == '__main__':
    app.run(debug=True)
```

En este ejemplo, cada solicitud incluye un número único  `request_id` almacenado en la base de datos para rastrear las solicitudes procesadas y evitar duplicados.

### 2.  **Ajustes de diseño de la base de datos (operación Upsert)** <a href="#id-2-database-design-adjustments-upsert-operation" id="id-2-database-design-adjustments-upsert-operation"></a>

Algunas operaciones de base de datos, como insertar el mismo registro varias veces, pueden generar entradas duplicadas no deseadas.

Para lograr la idempotencia en estos casos a menudo es necesario rediseñar las operaciones de la base de datos para que sean inherentemente idempotentes.

Esto puede implicar el uso  `upsert` de operaciones (que actualizan un registro si existe o lo insertan en caso contrario) o la aplicación de  **restricciones únicas**  que impiden que se agreguen duplicados en primer lugar.

En este ejemplo, utilizamos SQL  `INSERT ... ON CONFLICT` para lograr una operación de inserción y actualización, garantizando que las entradas duplicadas no afecten el estado de la base de datos.

```sql
INSERT INTO inventory (item_id, stock)
VALUES (1, 10)
ON CONFLICT (item_id) DO UPDATE
SET stock = inventory.stock + EXCLUDED.stock;
```

Esta sentencia SQL inserta un nuevo artículo si no existe. Si existe (conflicto en  `item_id`), actualiza el stock añadiendo la nueva cantidad, lo que garantiza que la operación se mantenga idempotente.

### 3. Idempotencia en los sistemas de mensajería <a href="#id-3-idempotency-in-messaging-systems" id="id-3-idempotency-in-messaging-systems"></a>

En un sistema de mensajería, podemos imponer la idempotencia almacenando un registro de los identificadores de los mensajes procesados ​​y comparándolo con cada mensaje entrante.

```java
import java.util.HashSet;
import java.util.Set;

public class MessageConsumer {
    private Set<String> processedMessages = new HashSet<>();

    public void processMessage(String messageId, String messageContent) {
        if (processedMessages.contains(messageId)) {
            System.out.println("Duplicate message ignored: " + messageId);
            return;
        }

        // Process the message here
        System.out.println("Processing message: " + messageContent);

        // Add messageId to processed set
        processedMessages.add(messageId);
    }
}
```

Cada mensaje tiene un único  `messageId`. Antes de procesarlo, verificamos si  `messageId` ya está en  `processedMessages`. Si es así, se ignora; de lo contrario, se procesa y se añade al conjunto para evitar duplicados.

### **4. Idempotencia en métodos HTTP** <a href="#id-4-idempotency-in-http-methods" id="id-4-idempotency-in-http-methods"></a>

HTTP define varios métodos (verbos) para diferentes tipos de solicitudes.

Estos métodos se pueden clasificar según sean idempotentes o no idempotentes, lo que influye en cómo un sistema maneja los reintentos y previene efectos secundarios no deseados.

**Métodos idempotentes:**

**CONSEGUIR**

Recupera datos de un recurso. Las solicitudes GET son inherentemente idempotentes porque solo leen datos y no alteran el estado del servidor.

* **Ejemplo:**  Acceder a una entrada de blog mediante una solicitud GET  `/posts/123` simplemente recuperará esa entrada, sin modificar ningún dato del servidor. Tanto si la recuperas una como si la recuperas mil veces, la entrada permanece inalterada.

**PONER**

Actualizar o reemplazar completamente un recurso existente. Las solicitudes PUT son idempotentes porque el estado final es el mismo independientemente de si se ejecutan una o varias veces.

* **Ejemplo:**  Actualizar la información del usuario mediante una solicitud PUT  `/users/45` con los datos actualizados sobrescribirá los datos del usuario con la nueva información proporcionada. Ejecutar la misma solicitud PUT repetidamente genera los mismos datos finales del usuario en el servidor.

**BORRAR**

Elimina un recurso del servidor. Las solicitudes DELETE son idempotentes, ya que eliminar un recurso ya eliminado no tiene ningún efecto.

* **Ejemplo:** Al eliminar un elemento mediante una solicitud DELETE,  `/items/678` se eliminará. Si se vuelve a intentar la solicitud DELETE, no tendrá efecto, ya que el elemento ya no existe.

**Métodos no idempotentes:**

**CORREO**

Crea un nuevo recurso en el servidor. Las solicitudes POST no son idempotentes, ya que cada una suele generar la creación de un nuevo recurso.

* **Ejemplo:** crear un nuevo pedido realizando una solicitud POST  `/orders` con los detalles del pedido generará un nuevo pedido cada vez que se realice la solicitud.

## 3. Desafíos y consideraciones <a href="#id-3-challenges-and-considerations" id="id-3-challenges-and-considerations"></a>

Si bien la idempotencia es poderosa, conlleva su propio conjunto de desafíos:

1. **Sobrecarga de rendimiento** : almacenar claves de idempotencia o verificar operaciones duplicadas puede agregar sobrecarga y aumentar la latencia general.
2. **Gestión del estado** : la idempotencia a menudo requiere mantener el estado, lo que puede resultar un desafío en arquitecturas sin estado.
3. **Sistemas distribuidos** : garantizar la idempotencia en sistemas distribuidos puede ser un desafío y puede requerir  **algoritmos de consenso**  o  **bloqueo distribuido** .
4. **Ventana temporal** : ¿Durante cuánto tiempo deben mantenerse las garantías de idempotencia? ¿Para siempre o por tiempo limitado?
5. **Restricciones de la base de datos** : no todas las operaciones son idempotentes de forma predeterminada; pueden ser necesarias restricciones únicas o lógica de inserción y actualización para evitar la duplicación.

## 4. Mejores prácticas <a href="#id-4-best-practices" id="id-4-best-practices"></a>

Al implementar la idempotencia en su sistema, tenga en cuenta estas prácticas recomendadas:

1. **Utilice identificadores únicos** : adjunte una identificación única (clave de idempotencia) a cada solicitud para rastrear y evitar el procesamiento duplicado.
2. **Diseño para la idempotencia desde el principio** : es mucho más fácil diseñar para la idempotencia desde el principio que agregarla más tarde.
3. **Implementar reintento con retroceso** : al reintentar operaciones idempotentes, utilice una estrategia de retroceso exponencial para evitar saturar el sistema.
4. **Utilice métodos HTTP idempotentes** : prefiera métodos idempotentes (GET, PUT, DELETE) para operaciones que puedan reintentarse; diseñe POST con identificadores únicos si se requiere idempotencia.
5. **Documentar operaciones idempotentes** : documente claramente qué operaciones son idempotentes en sus especificaciones de API.
6. **Pruebe exhaustivamente** : implemente pruebas que verifiquen la idempotencia de sus operaciones, incluidos casos extremos y escenarios de falla.
7. **Utilice bloqueos o control de versiones** : utilice bloqueos, control de concurrencia optimista o números de versión para administrar solicitudes simultáneas de forma segura.

La idempotencia es un concepto poderoso en los sistemas distribuidos que puede mejorar enormemente la confiabilidad y la tolerancia a fallas de sus sistemas.

Ya sea que esté diseñando una base de datos distribuida, un sistema de procesamiento de pagos o una API web simple, considerar la idempotencia en su diseño puede ahorrarle a usted (y a sus usuarios) muchos dolores de cabeza en el futuro.
