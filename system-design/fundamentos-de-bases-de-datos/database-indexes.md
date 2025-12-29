# Database Indexes

Consideremos un  **libro** grande  de 1000 páginas.

Supongamos que estás intentando encontrar la página que contiene información relacionada con una determinada  **palabra** .

Sin una página de índice, tendrías que recorrer cada página, lo que podría llevar horas o incluso días.

Pero con una página de índice, ¡sabes dónde buscar!

Una vez que encuentre el índice correcto, podrá saltar eficientemente a esa página.

El índice, al estar  **ordenado**  alfabéticamente y dar números de página para información específica, nos ahorra el tener que pasar demasiado tiempo hojeando cada página.

**Los índices de bases de datos**  funcionan de forma similar. Guían la base de datos a la ubicación exacta de los datos, lo que permite una recuperación más rápida y eficiente.

En este artículo, exploraremos:

* ¿Qué son los índices de bases de datos?
* ¿Cómo funcionan?
* Beneficios de usarlos.
* Diferentes tipos de índices.
* ¿Qué estructura de datos utilizan?
* ¿Cómo utilizarlos inteligentemente?

## **1. ¿Qué son los índices de bases de datos?** <a href="#id-1-what-are-database-indexes" id="id-1-what-are-database-indexes"></a>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.02.42.png" alt="" width="563"><figcaption></figcaption></figure>

Un índice de base de datos es una tabla de búsqueda súper eficiente que permite que una base de datos encuentre datos mucho más rápido.

Contiene los valores de las columnas indexadas junto con punteros a las filas correspondientes en la tabla.

Sin un índice, la base de datos podría tener que escanear cada fila de una tabla enorme para encontrar lo que busca: un proceso extremadamente lento.

Pero, con un índice, la base de datos puede localizar la ubicación exacta de los datos deseados utilizando los punteros del índice.

#### ¿Cómo crear índices? <a href="#how-to-create-indexes" id="how-to-create-indexes"></a>

A continuación se muestra un ejemplo de creación de un índice en una base de datos MySQL.

Digamos que tenemos una tabla llamada  `employees` con la siguiente estructura:

```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    department VARCHAR(50),
    salary DECIMAL(10, 2)
);
```

Ahora, creemos un índice en la  `last_name` columna para mejorar el rendimiento de las consultas que frecuentemente buscan u ordenan según el apellido.

```sql
-- Create index on last_name for faster lookups
CREATE INDEX idx_last_name ON employees (last_name);
```

En este ejemplo, usamos la  `CREATE INDEX` instrucción para crear un índice llamado  `idx_last_name` en la  `employees` tabla. El índice se crea en la  `last_name` columna.

Tras crear el índice,  `last_name` se optimizarán las consultas que impliquen condiciones u ordenación de la columna. Por ejemplo:

```sql
-- Query to find employees with last name 'Smith'
SELECT * FROM employees WHERE last_name = 'Smith';
```

Esta consulta utilizará el  `idx_last_name` índice para localizar rápidamente las filas donde  `last_name` está 'Smith', evitando un escaneo completo de la tabla.

También puede crear índices en varias columnas (índices compuestos) si sus consultas suelen incluir condiciones en varias columnas juntas. Por ejemplo:

```sql
-- Create composite index on first_name and last_name
CREATE INDEX idx_full_name ON employees (first_name, last_name);
```

Esto crea un índice compuesto en las  columnas `first_name` y  `last_name` , que puede ser útil para consultas que buscan u ordenan según ambas columnas.

## 2. ¿Cómo funcionan los índices de bases de datos? <a href="#id-2-how-do-database-indexes-work" id="id-2-how-do-database-indexes-work"></a>

A continuación se ofrece una explicación paso a paso de cómo funcionan los índices de bases de datos:

1. **Creación de índice** : el administrador de la base de datos crea un índice en una columna o conjunto de columnas específicas.
2. **Creación de índices** : el sistema de gestión de bases de datos crea el índice escaneando la tabla y almacenando los valores de las columnas indexadas junto con un puntero a los datos correspondientes.
3. **Ejecución de consulta** : cuando se ejecuta una consulta, el motor de base de datos verifica si existe un índice para las columnas solicitadas.
4. **Búsqueda de índice** : si existe un índice, la base de datos busca en el índice los datos solicitados, utilizando los punteros para localizar rápidamente los datos.
5. **Recuperación de datos** : la base de datos recupera los datos solicitados, utilizando los punteros del índice.

## **3. Beneficios de los índices de bases de datos** <a href="#id-3-benefits-of-database-indexes" id="id-3-benefits-of-database-indexes"></a>

Los índices de bases de datos ofrecen varios beneficios, entre ellos:

* **Rendimiento de consultas más rápido** : los índices pueden mejorar significativamente el rendimiento de las consultas, especialmente para conjuntos de datos grandes, al reducir la cantidad de datos que deben escanearse.
* **Uso reducido de CPU** : al reducir la cantidad de filas que deben escanearse, los índices pueden disminuir el uso de CPU y optimizar la utilización de recursos.
* **Recuperación rápida de datos** : los índices permiten la recuperación rápida de datos para consultas que involucran condiciones de igualdad o rango en las columnas indexadas.
* **Ordenación eficiente** : los índices también se pueden utilizar para ordenar datos de manera eficiente según las columnas indexadas, eliminando la necesidad de realizar costosas operaciones de ordenación.
* **Mejor organización de datos** : los índices pueden ayudar a mantener la organización y la estructura de los datos, lo que facilita la gestión y el mantenimiento de la base de datos.

## 4. Tipos de   índices **de bases de datos** <a href="#id-4-types-of-database-indexes" id="id-4-types-of-database-indexes"></a>

### Índices basados ​​en estructura y atributos clave: <a href="#indexes-based-on-structure-and-key-attributes" id="indexes-based-on-structure-and-key-attributes"></a>

* **Índice principal:**  Se crea automáticamente al definir una restricción de clave principal en una tabla. Garantiza la unicidad y facilita búsquedas ultrarrápidas con la clave principal.
* **Índice agrupado:**  Determina el orden en que se almacenan físicamente los datos en la tabla. Un índice agrupado es especialmente útil cuando se busca en un rango. Solo puede existir un índice agrupado por tabla.
* **Índice no agrupado o secundario:**  Este índice no almacena los datos en el orden establecido. En su lugar, proporciona una lista de punteros virtuales o referencias a la ubicación donde se almacenan los datos.

### Índices basados ​​en la cobertura de datos: <a href="#indexes-based-on-data-coverage" id="indexes-based-on-data-coverage"></a>

* **Índice denso:**  Contiene una entrada por cada valor de clave de búsqueda de la tabla. Ideal para situaciones donde los datos tienen pocos valores de clave de búsqueda distintos o cuando se requiere un acceso rápido a registros individuales.
* **Índice disperso:**  Contiene entradas solo para algunos valores de clave de búsqueda. Ideal para situaciones donde los datos contienen una gran cantidad de valores de clave de búsqueda distintos.

### Tipos de índices especializados: <a href="#specialized-index-types" id="specialized-index-types"></a>

* **Índice de mapa de bits:**  Excelente para columnas con baja cardinalidad (pocos valores distintos). Común en el almacenamiento de datos.
* **Índice hash:**  Un índice que utiliza una función hash para asignar valores a ubicaciones específicas. Ideal para consultas de coincidencia exacta.
* **Índice filtrado:**  Indexa un subconjunto de filas según una condición de filtro específica. Resulta útil para optimizar la velocidad de consulta en columnas con filtros comunes.
* **Índice de cobertura:**  incluye todas las columnas requeridas por una consulta en el propio índice, eliminando la necesidad de acceder a los datos de la tabla subyacente.
* **Índice basado en funciones:**  índices que se crean según el resultado de una función o expresión aplicada a una o más columnas de una tabla.
* **Índice de texto completo** : un índice diseñado para la búsqueda de texto completo, que permite una búsqueda eficiente de datos de texto.
* **Índice espacial:**  se utiliza para indexar tipos de datos geográficos.

## 5. ¿Qué estructura de datos utilizan los índices? <a href="#id-5-what-data-structure-do-indexes-use" id="id-5-what-data-structure-do-indexes-use"></a>

Las estructuras de datos más comúnmente utilizadas que potencian los índices son los árboles B, las tablas hash y los mapas de bits.

### Árbol B (árbol equilibrado) <a href="#b-tree-balanced-tree" id="b-tree-balanced-tree"></a>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.06.44.png" alt=""><figcaption></figcaption></figure>

La mayoría de los motores de bases de datos utilizan un árbol B o una variación de árboles B como los árboles B+.

Los árboles B tienen una estructura jerárquica con un nodo raíz, nodos internos (nodos de índice) y nodos hoja.

Cada nodo de un árbol B contiene una matriz ordenada de claves y punteros a nodos secundarios.

He aquí por qué son tan adecuados:

* **Autoequilibrio:**  Los árboles B garantizan que la altura del árbol se mantenga equilibrada incluso al insertar o eliminar datos. Esto garantiza  `logarithmic time complexity` la inserción, la eliminación y la búsqueda.
* **Ordenado:**  los árboles B mantienen los datos ordenados, lo que hace que las consultas de rango ("encontrar todos los órdenes entre la fecha X e Y") y las comparaciones de desigualdad sean muy rápidas.
* **Compatible con discos:**  Los árboles B están diseñados para funcionar correctamente con el almacenamiento en disco. Un solo nodo de un árbol B suele corresponder a un bloque de disco, lo que minimiza las operaciones de acceso al disco.

Muchas bases de datos utilizan una variante del árbol B ligeramente modificada, denominada árbol B+.

En un árbol B+, todos los valores de datos se almacenan solo en los nodos hoja, lo que puede mejorar aún más el rendimiento para ciertos casos de uso, como las consultas de rango.

### Tablas hash <a href="#hash-tables" id="hash-tables"></a>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.07.31.png" alt=""><figcaption></figcaption></figure>

Las tablas hash se utilizan para índices hash, que se basan en una función hash.

Una tabla hash consta de una matriz de contenedores, y cada contenedor contiene las direcciones de las filas de los datos.

Los índices hash emplean una función hash para asignar claves a su contenedor correspondiente en la tabla hash, lo que permite operaciones de búsqueda en tiempo constante.

Los índices hash proporcionan búsquedas de igualdad rápidas, ya que la función hash determina la ubicación exacta de los datos en función de la clave.

Sin embargo, los índices hash no admiten consultas de rango ni ordenamiento de manera eficiente.

### Mapas de bits <a href="#bitmaps" id="bitmaps"></a>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-12-29 a la(s) 09.07.56.png" alt="" width="375"><figcaption></figcaption></figure>

Cada bit del mapa de bits corresponde a una fila y el valor del bit indica si el valor de la clave existe en esa fila.

Los índices de mapa de bits utilizan un mapa de bits (una matriz binaria) para representar la presencia o ausencia de un valor de clave específico en cada fila de una tabla.

Los índices de mapa de bits son adecuados para columnas con baja cardinalidad (una pequeña cantidad de valores distintos) y para realizar consultas complejas que involucran múltiples condiciones.

Las operaciones de mapa de bits como AND, OR y NOT se realizan de manera eficiente mediante operaciones bit a bit, lo que hace que los índices de mapa de bits sean adecuados para consultas analíticas que involucran múltiples columnas.

## **6. ¿Cómo utilizar los índices de bases de datos de forma inteligente?** <a href="#id-6-how-to-use-database-indexes-smartly" id="id-6-how-to-use-database-indexes-smartly"></a>

Para aprovechar al máximo los índices de bases de datos, tenga en cuenta estas prácticas recomendadas:

* **Identificar patrones de consulta** : analice las consultas más frecuentes y críticas ejecutadas en su base de datos para determinar qué columnas indexar y qué tipo de índice utilizar.
* **Índice de columnas utilizadas con frecuencia** : considere indexar las columnas que se utilizan con frecuencia en las cláusulas WHERE, JOIN y ORDER BY.
* **Columnas selectivas de índice:**  Los índices son más eficaces en columnas con una buena dispersión de valores de datos (alta cardinalidad). Indexar una  `gender` columna podría ser menos beneficioso que indexar una con un valor único  `customer_id`.
* **Utilice tipos de índice adecuados** : elija el tipo de índice adecuado para sus datos y consultas.
* **Considere índices compuestos** : Para consultas con varias columnas, considere crear índices compuestos que abarquen todas las columnas relevantes. Esto reduce la necesidad de varios índices de una sola columna y mejora el rendimiento de las consultas.
* **Supervisar el rendimiento del índice** : supervise periódicamente el rendimiento del índice, elimine los índices no utilizados y ajuste su estrategia de indexación a medida que evoluciona la carga de trabajo de la base de datos.
* **Evite la sobreindexación** : Evite crear demasiados índices, ya que esto puede aumentar los requisitos de almacenamiento y ralentizar el rendimiento de escritura. Los índices ocupan espacio adicional en disco, ya que son estructuras de datos adicionales que deben almacenarse junto con las tablas. Cada vez que inserta, actualiza o elimina datos en una tabla con un índice, este también debe actualizarse. Esto puede ralentizar ligeramente las operaciones de escritura.
