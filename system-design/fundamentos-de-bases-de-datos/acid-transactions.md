# ACID Transactions

Imagínese que está ejecutando una aplicación **de comercio electrónico** .

Un cliente realiza un pedido y su sistema debe deducir el artículo del inventario, cargar la tarjeta de crédito del cliente y registrar la venta en su sistema de contabilidad, todo a la vez.

¿Qué ocurre si el pago falla pero tu inventario ya se ha reducido? ¿O si tu aplicación falla a mitad del proceso?

Aquí es donde  entran en juego **las transacciones ACID**  , que garantizan que todos los pasos de estas operaciones críticas se realicen de forma fiable y consistente.

ACID es un acrónimo que se refiere al conjunto de 4 propiedades clave que definen una transacción:  **Atomicidad, Consistencia, Aislamiento**  y  **Durabilidad.**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-27 a la(s) 23.01.26.png" alt="" width="563"><figcaption></figcaption></figure>

En este artículo, analizaremos qué significa cada una de las propiedades ACID, por qué son importantes y cómo se implementan en las bases de datos.

## ¿Qué es una transacción de base de datos? <a href="#what-is-a-database-transaction" id="what-is-a-database-transaction"></a>

En el contexto de las bases de datos, una  **transacción**  es una secuencia de una o más operaciones (como insertar, actualizar o eliminar registros) que la base de datos trata como  **una sola acción** . Puede ser completamente exitosa o completamente fallida, sin estados intermedios.

**Ejemplo: Transferencia bancaria**

Cuando envías dinero a un amigo, suceden dos cosas:

1. Se deduce dinero de su cuenta.
2. Se añade dinero a su cuenta.

Estos dos pasos forman  **una sola transacción** . Si alguno falla, ambos se cancelan.

Sin transacciones, las bases de datos podrían terminar en estados inconsistentes.

Por ejemplo:

* **Actualizaciones parciales** : tu dinero se deduce, pero tu amigo nunca lo recibe.
* **Conflictos** : Dos personas reservan la última entrada de cine al mismo tiempo.

Las transacciones resuelven estos problemas al aplicar reglas como  **las propiedades ACID**  (atomicidad, consistencia, aislamiento, durabilidad).

Ahora, veamos cada una de las propiedades de ACID.

## 1. Atomicidad <a href="#id-1-atomicity" id="id-1-atomicity"></a>

La atomicidad garantiza que una transacción (que comprende múltiples operaciones) se ejecute como una  unidad de trabajo  **única e indivisible  : o bien tiene éxito total**  (se confirma) o bien  falla **totalmente**  (se revierte).

Si alguna parte de la transacción falla, se revierte toda la transacción y la base de datos se restaura exactamente a un estado como estaba antes de que comenzara la transacción.

> **Ejemplo:** En una transferencia de dinero, si falla el paso de crédito, no se puede permitir que el paso de débito se mantenga por sí solo. Esto evita situaciones incoherentes, como la desaparición de dinero de una cuenta sin aparecer en otra.

La atomicidad elimina la complejidad de deshacer manualmente los cambios si algo sale mal.

### Cómo las bases de datos implementan la atomicidad <a href="#how-databases-implement-atomicity" id="how-databases-implement-atomicity"></a>

Las bases de datos utilizan dos mecanismos clave para garantizar la atomicidad.

**1. Registros de transacciones (registros de escritura anticipada)**

* Cada operación se registra en un  **registro de escritura anticipada**  antes de aplicarse a la tabla de base de datos real.
* Si ocurre un error, la base de datos utiliza este registro para  **deshacer**  los cambios incompletos.

**Ejemplo:**

```
[TRANSACTION LOG ENTRY]

Transaction ID: 12345
Actions to perform:
1) UPDATE accounts
   SET balance = balance - 100
   WHERE account_id = 1

2) UPDATE accounts
   SET balance = balance + 100
   WHERE account_id = 2
```

Una vez que la entrada WAL está segura en el disco, la base de datos procede a modificar las páginas en memoria que contienen filas para  **la Cuenta A**  y  la Cuenta **B.**

Cuando las operaciones tienen éxito:

1. La base de datos marca  **el ID de transacción 12345**  como  **confirmado**  en el registro de transacciones.
2. Los saldos recientemente actualizados de A y B eventualmente se borrarán de la memoria a sus respectivos archivos de datos en el disco.

Si la base de datos falla  **después**  de escribir la entrada del registro pero  **antes de que**  los archivos de datos se actualicen por completo, el WAL proporciona una forma de recuperarse:

* Al reiniciar, la base de datos verifica el WAL.
* Se observa que  **la transacción 12345**  fue confirmada.
* Vuelve a aplicar las  operaciones **de ACTUALIZACIÓN**  para garantizar que los saldos finales sean correctos en los archivos de datos.

Si la transacción no se había confirmado (o estaba marcada como “en progreso”) en el momento del bloqueo, la base de datos revertiría  **esos**  cambios usando la información del registro, dejando la tabla como si la transacción nunca hubiera ocurrido.

**2. Protocolos de confirmación/reversión**

* Las bases de datos proporcionan comandos como  `BEGIN TRANSACTION`,  `COMMIT`, y `ROLLBACK`
* Cualquier cambio realizado entre  `BEGIN TRANSACTION` y  `COMMIT` se considera "en progreso" y no se aplicará de forma permanente a menos que la transacción se confirme correctamente.
* Si algún paso falla, o si emite explícitamente un  `ROLLBACK`, se deshacen todos los cambios desde el inicio de la transacción.

**Ejemplo:**

```sql
-- Begin a transaction
BEGIN TRANSACTION;

-- Step 1: Update the user's profile
UPDATE users
SET username = 'newUsername'
WHERE user_id = 123;

-- Step 2: Insert a log entry
INSERT INTO user_logs (user_id, action, timestamp)
VALUES (123, 'Updated username', NOW());

-- If everything went well, COMMIT the transaction
COMMIT;

-- If an error happens between BEGIN TRANSACTION and COMMIT,
-- we manually or automatically ROLLBACK to undo partial changes.
```

## 2. Consistencia <a href="#id-2-consistency" id="id-2-consistency"></a>

**La coherencia**  en el contexto de las transacciones ACID garantiza que cualquier transacción llevará la base de datos de un estado válido a otro estado válido, sin dejarla nunca en un estado roto o “inválido”.

Esto significa que todas las restricciones de integridad de datos, como  **las restricciones de clave principal**   (sin identificaciones duplicadas),  **las restricciones de clave externa**  (los registros relacionados deben existir en las tablas principales) y  **las restricciones de verificación** (la antigüedad no puede ser negativa), se satisfacen antes y después de la transacción.&#x20;

Si una transacción intenta violar estas reglas, no se confirmará y la base de datos volverá a su estado anterior.

#### Ejemplo: <a href="#example" id="example"></a>

Tienes dos tablas en una base de datos de comercio electrónico:

1. `products` (con columnas:  `product_id`,  `stock_quantity`, etc.)
2. `orders` (con columnas:  `order_id`,  `product_id`,  `quantity`, etc.)

**Restricción** : No se puede realizar un pedido de un producto si  `quantity` es mayor que el  `stock_quantity` de la  `products` tabla.

**Flujo de transacciones**

```sql
BEGIN TRANSACTION;

INSERT INTO orders (product_id, quantity)
VALUES (101, 10);

-- Next, try to decrement stock from the products table
UPDATE products
SET stock_quantity = stock_quantity - 10
WHERE product_id = 101;

-- Check constraint: If 'stock_quantity' goes below 0, this violates the rule.

COMMIT;
```

* Si el producto  `stock_quantity` fuera 8 (menos de lo que estamos intentando pedir), la base de datos ve que el nuevo valor sería ,  `-2` lo que rompe la regla de consistencia (no debería ser negativo).
* La transacción falla o desencadena una reversión, lo que evita que la base de datos finalice en un estado no válido.

### Cómo implementar la coherencia <a href="#how-to-implement-consistency" id="how-to-implement-consistency"></a>

**1. Restricciones del esquema de base de datos**

* **Las restricciones NOT NULL** ,  **UNIQUE** ,  **PRIMARY KEY** ,  **FOREIGN KEY** ,  **CHECK**  y otras definiciones de esquema garantizan que no se permitan entradas no válidas.

**2. Desencadenadores y procedimientos almacenados**

* Los activadores pueden verificar automáticamente reglas adicionales cada vez que se insertan, actualizan o eliminan filas.
* Los procedimientos almacenados pueden contener lógica para validar los datos antes de confirmarlos.

**3. Medidas de seguridad a nivel de aplicación**

* Si bien la base de datos impone restricciones en un nivel inferior, las aplicaciones a menudo agregan controles adicionales, como garantizar que se cumplan las reglas comerciales o que los datos se validen incluso antes de que lleguen a la capa de base de datos.

## 3. Aislamiento <a href="#id-3-isolation" id="id-3-isolation"></a>

**El aislamiento**  garantiza que las transacciones que se ejecutan simultáneamente no interfieran con los estados intermedios de las demás.

Básicamente, mientras una transacción está en progreso, sus actualizaciones (o datos intermedios) permanecen invisibles para otras transacciones en curso, dando la ilusión de que cada transacción se ejecuta secuencialmente, una a la vez.

Sin aislamiento, dos o más transacciones podrían leer y escribir datos parciales o no confirmados entre sí, lo que provocaría resultados incorrectos o inconsistentes.

Con el aislamiento, los desarrolladores pueden razonar de manera más confiable sobre cómo aparecerán los cambios de datos en otras transacciones.

### Anomalías de concurrencia <a href="#concurrency-anomalies" id="concurrency-anomalies"></a>

Para comprender cómo funciona el aislamiento, es útil ver qué puede fallar sin un aislamiento adecuado. Las anomalías de concurrencia comunes incluyen:

**1. Lectura sucia**

* La transacción A lee datos que la transacción B ha modificado pero aún no ha confirmado.
* Si luego la transacción B se revierte, la transacción A termina conteniendo un valor no válido o “sucio” que en realidad nunca existió en el estado confirmado.

**2. Lectura no repetible**

* La transacción A lee las mismas filas varias veces durante su ejecución, pero ve datos diferentes porque otra transacción actualizó o eliminó esas filas entre las lecturas de A.

**3. Lectura fantasma**

* La transacción A realiza una consulta que devuelve un conjunto de filas. Otra transacción inserta, actualiza o elimina filas que coinciden con las condiciones de la consulta de A.
* Si A vuelve a ejecutar la misma consulta, verá un conjunto diferente de filas (“fantasmas”).

### **Niveles de aislamiento** <a href="#isolation-levels" id="isolation-levels"></a>

Las bases de datos generalmente permiten elegir un  **nivel de aislamiento** que equilibra la exactitud de los datos con el rendimiento.

Los niveles de aislamiento más altos proporcionan una mayor consistencia de los datos, pero pueden reducir el rendimiento del sistema al aumentar los tiempos de espera para las transacciones.

Exploremos los cuatro niveles de aislamiento comunes:

**1. Leer sin compromiso**

* Permite lecturas sucias; las transacciones pueden ver cambios no confirmados.
* Se utiliza raramente ya que puede provocar anomalías graves.

**Ejemplo:**

```
Transaction A updates balance 10000 -> 5000 but doesn’t commit.
Transaction B reads the balance and sees 5000.
Transaction A then rolls back, reverting the balance to 10000.
→ Transaction B has read uncommitted data (a “dirty read”).
```

**2. Lectura comprometida**

* Una transacción sólo ve los datos que se han confirmado en el momento de la lectura.
* Evita lecturas sucias, pero aún pueden ocurrir lecturas no repetibles y lecturas fantasmas.

**Ejemplo:**

```sql
Transaction A reads balance: 10000
Transaction B deducts 5000 and commits
Transaction A reads again: 5000
```

**3. Lectura repetible**

* Asegura que si lees las mismas filas varias veces dentro de una transacción, obtendrás los mismos valores (a menos que los modifiques explícitamente).
* Evita lecturas sucias y no repetibles, pero aún pueden ocurrir lecturas fantasmas (dependiendo del motor de base de datos).

**Ejemplo:**

```sql
Transaction A reads 5 available concert tickets
Transaction B adds 3 more tickets and commits
Transaction A reads again: 8 tickets appear
```

**4. Serializable**

* El nivel más alto de aislamiento, actuando como si todas las transacciones ocurrieran secuencialmente, una a la vez.
* Evita lecturas sucias, lecturas no repetibles y lecturas fantasmas.
* Es el más costoso en términos de rendimiento y concurrencia porque puede requerir más bloqueos o más controles de conflictos.

**Ejemplo:**

```sql
Transaction A queries tickets and sees 5 available.
Transaction B adds 3 more tickets and commits.
Transaction A tries to query again—under Serializable:
  - Sees the original 5 (as if B happened “after” A)
  - OR hits a serialization error and is forced to retry
```

### Cómo las bases de datos refuerzan el aislamiento <a href="#how-databases-enforce-isolation" id="how-databases-enforce-isolation"></a>

**1. Bloqueo**

**Control de concurrencia pesimista**

* Las filas o tablas se bloquean para que ninguna otra transacción pueda leerlas o escribirlas hasta que se libere el bloqueo.
* Puede provocar bloqueos o interbloqueos si varias transacciones compiten por los mismos bloqueos.

**2. MVCC (Control de concurrencia de múltiples versiones)**

**Control de concurrencia optimista**

* En lugar de bloquear las lecturas, la base de datos mantiene múltiples versiones de una fila.
* Los lectores ven una instantánea consistente de los datos (como una vista de un punto en el tiempo), mientras que los escritores crean una nueva versión de la fila al actualizar.
* Este enfoque reduce la contención de bloqueos, pero requiere administrar cuidadosamente las versiones de filas y la limpieza (aspirar en PostgreSQL, por ejemplo).

**3. Aislamiento de instantáneas**

* Una forma de MVCC donde cada transacción ve los datos tal como estaban al inicio (o en un punto consistente) de la transacción.
* Evita lecturas no repetibles y lecturas sucias. Las lecturas fantasmas aún pueden ocurrir a menos que el nivel de aislamiento sea completamente serializable.

## 4. Durabilidad <a href="#id-4-durability" id="id-4-durability"></a>

**La durabilidad**  garantiza que una vez que se haya confirmado una transacción, los cambios realizados sobrevivirán, incluso ante cortes de energía, caídas u otros eventos catastróficos.

En otras palabras, una vez que una transacción dice “terminada”, los datos se registran de forma permanente y no pueden simplemente desaparecer.

### Cómo las bases de datos garantizan la durabilidad <a href="#how-databases-ensure-durability" id="how-databases-ensure-durability"></a>

**1. Registros de transacciones (registro de escritura anticipada)**

La mayoría de las bases de datos relacionales se basan en un  **registro de escritura anticipada (WAL)**  para preservar los cambios antes de que se escriban en los archivos de datos principales:

1. **Escribir cambios en el WAL** : las operaciones previstas (actualizaciones, inserciones, eliminaciones) se registran en el WAL en un almacenamiento duradero (disco).
2. **Confirmar la transacción** : una vez que la entrada WAL se conserva de forma segura, la base de datos puede marcar la transacción como confirmada.
3. **Aplicar cambios a los archivos de datos principales** : los datos actualizados eventualmente se escriben en los archivos principales, posiblemente primero en la memoria y luego se vacían en el disco.

Si la base de datos falla, utiliza el WAL durante  **la recuperación** :

* **Rehacer** : se vuelven a aplicar todas las transacciones comprometidas que aún no se reflejan en los archivos principales.
* **Deshacer** : cualquier transacción incompleta (no confirmada) se revierte para mantener la base de datos consistente.

**2. Replicación/Redundancia**

Además de WAL, muchos sistemas utilizan replicación para garantizar que los datos permanezcan duraderos incluso si falla el hardware o un centro de datos completo.

* **Replicación síncrona** : Las escrituras se copian inmediatamente a múltiples nodos o centros de datos. Una transacción se marca como confirmada solo si el nodo principal y al menos una réplica confirman que está almacenada de forma segura.
* **Replicación asincrónica** : los cambios eventualmente se sincronizan con otros nodos, pero hay una ventana (pequeña) en la que puede ocurrir pérdida de datos si el nodo principal falla antes de que se actualice la réplica.

**3. Copias de seguridad**

**Las copias de seguridad** periódicas   ofrecen una red de seguridad que va más allá de los registros y la replicación. En caso de corrupción grave, error humano o fallo catastrófico:

* **Copias de seguridad completas** : captura toda la base de datos en un momento determinado.
* **Copias de seguridad incrementales/diferenciales** : almacene los cambios desde la última copia de seguridad para realizar copias de seguridad más rápidas y frecuentes.
* **Almacenamiento externo** : garantiza que las copias de seguridad permanezcan seguras ante desastres localizados, lo que le permite restaurar datos incluso si el hardware está dañado.
