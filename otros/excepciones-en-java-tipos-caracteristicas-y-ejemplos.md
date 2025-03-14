---
description: >-
  Situaciones anómalas o errores que ocurren durante la ejecución de un
  programa. Se dividen en dos grandes categorías: excepciones comprobadas
  (checked) y excepciones no comprobadas (unchecked).
---

# Excepciones en Java: Tipos, características y ejemplos

## Visión General: Checked vs. Unchecked

* **Checked (comprobadas):**
  * Se heredan de `Exception` (pero no de `RuntimeException`).
  * Son forzadas por el compilador, es decir, es obligatorio manejarlas (con `try-catch` o `throws` en la firma del método).
  * Representan condiciones que pueden ocurrir en situaciones normales de funcionamiento pero que se deben manejar (por ejemplo, problemas de E/S, archivos que no existen, etc.).
* **Unchecked (no comprobadas):**
  * Se heredan de `RuntimeException` o de `Error`.
  * El compilador **no** obliga a tratarlas explícitamente con `try-catch` o `throws`.
  * Suelen indicar errores de programación (por ejemplo, índice fuera de rango, `NullPointerException`) o errores graves del sistema (como `OutOfMemoryError`).

## Excepciones Comunes

### `Exception` (Checked)

* **Descripción:** Clase padre de todas las excepciones comprobadas.
* **Cuándo ocurre:** Se utiliza como la base para cualquier excepción que el compilador requiera que se maneje.
* **Ejemplo de uso:** Normalmente no se instancia directamente, sino que sirve como clase genérica o se extiende para crear excepciones personalizadas.
* **Comentario:** Si creas tus propias excepciones personalizadas y deseas que sean comprobadas, las harás extender `Exception`.

### `RuntimeException` (Unchecked)

* **Descripción:** Clase padre de todas las excepciones **no comprobadas** (aquellas que ocurren en tiempo de ejecución).
* **Cuándo ocurre:** Representa errores de lógica del programador o condiciones inesperadas que no es obligatorio capturar.
* **Ejemplo de uso:** Similar a `Exception`, sirve como clase genérica o para extender en tus propias excepciones runtime (p. ej. `IllegalArgumentException`).
* **Comentario:** Excepciones como `NullPointerException`, `ArithmeticException` o `IllegalStateException` heredan de `RuntimeException`.

### `IOException` (Checked)

* **Descripción:** Ocurre cuando se presentan problemas en **operaciones de entrada/salida**, por ejemplo lectura o escritura de archivos, problemas de red, etc.
* **Ejemplo más conocido:**
  * `FileNotFoundException` (ver más abajo), que indica que el archivo no existe o no puede abrirse.
* **Cuándo ocurre:** Al leer un archivo que no está disponible, al trabajar con flujos de red, etc.
*   **Ejemplo de código:**

    ```java
    javaCopiarEditartry {
        FileReader fr = new FileReader("archivo.txt");
        // ...
    } catch (IOException e) {
        // Manejo de error (el archivo no existe, problemas de E/S, etc.)
    }
    ```

### `SQLException` (Checked)

* **Descripción:** Aparece en **operaciones con bases de datos** que utilizan JDBC u otras APIs similares.
* **Cuándo ocurre:** Cuando hay un error en las consultas SQL, en la conexión a la base de datos o en la integridad de datos.
*   **Ejemplo de código:**

    ```java
    javaCopiarEditartry {
        Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/test", "user", "pass");
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery("SELECT * FROM tabla");
        // ...
    } catch (SQLException e) {
        // Manejo del error al ejecutar consultas SQL
    }
    ```

### `NullPointerException` (Unchecked)

* **Descripción:** Se produce al **acceder a un objeto que es nulo** (invocar métodos o acceder a campos).
* **Cuándo ocurre:** Cuando se hace `str.length()` y `str == null`, por ejemplo.
*   **Ejemplo de error:**

    ```java
    javaCopiarEditarString str = null;
    int length = str.length(); // Lanza NullPointerException
    ```
* **Buenas prácticas:** Siempre verificar la referencia con `null` o usar mecanismos como `Optional` (en Java 8+) para evitar estos errores.

### `ArrayIndexOutOfBoundsException` (Unchecked)

* **Descripción:** Se lanza al acceder a una **posición de array que no existe** (índice fuera de límites).
* **Cuándo ocurre:** `arr[10]` cuando `arr.length < 10`.
*   **Ejemplo de error:**

    ```java
    javaCopiarEditarint[] arr = new int[5];
    arr[10] = 3;  // Lanza ArrayIndexOutOfBoundsException
    ```
* **Buenas prácticas:** Verificar que el índice esté dentro de los límites del array antes de accederlo.

### `ArithmeticException` (Unchecked)

* **Descripción:** Se lanza cuando ocurre un **error aritmético**, como dividir entre cero.
*   **Ejemplo de error:**

    ```java
    javaCopiarEditarint x = 10 / 0; // Lanza ArithmeticException
    ```
* **Cuándo ocurre:** Operaciones matemáticas ilegales (division por cero en enteros, etc.).
* **Buenas prácticas:** Validar los valores antes de realizar la operación o manejar la posibilidad de valores ilegales.

### `ClassNotFoundException` (Checked)

* **Descripción:** Indica que una clase no se encontró en tiempo de ejecución.
* **Cuándo ocurre:** Al usar `Class.forName("NombreClase")` y la clase no está disponible en el classpath.
*   **Ejemplo de error:**

    ```java
    javaCopiarEditartry {
        Class.forName("UnknownClass"); // Si no existe, lanza ClassNotFoundException
    } catch (ClassNotFoundException e) {
        // Manejo del error
    }
    ```
* **Buenas prácticas:** Asegurarse de que las dependencias estén en el classpath o manejar adecuadamente estas excepciones.

### `FileNotFoundException` (Checked)

* **Descripción:** Tipo específico de `IOException` que ocurre cuando un archivo **no se encuentra** o no se puede abrir.
* **Cuándo ocurre:** Al intentar leer/escribir un archivo que no existe o no es accesible (permisos insuficientes).
*   **Ejemplo de error:**

    ```java
    javaCopiarEditartry {
        FileInputStream fis = new FileInputStream("abc.txt"); // Si no existe lanza FileNotFoundException
    } catch (FileNotFoundException e) {
        // Manejo de error
    }
    ```
* **Buenas prácticas:** Comprobar la existencia del archivo antes de abrirlo, manejar la excepción e informar al usuario.

### `NumberFormatException` (Unchecked)

* **Descripción:** Se lanza al **intentar convertir** una cadena a un número (int, double, etc.) cuando el formato no es válido.
* **Cuándo ocurre:** `Integer.parseInt("abc")` cuando “abc” no puede interpretarse como entero.
*   **Ejemplo de error:**

    ```java
    javaCopiarEditarint value = Integer.parseInt("abc"); // Lanza NumberFormatException
    ```
* **Buenas prácticas:** Verificar el contenido de la cadena antes de parsearla o manejar la excepción para informar un error de formato al usuario.

### `IllegalArgumentException` (Unchecked)

* **Descripción:** Ocurre cuando un **argumento inválido** se pasa a un método.
* **Cuándo ocurre:** Por ejemplo, si llamas `Thread.sleep(-1);`, ya que no se puede hacer dormir un hilo por un tiempo negativo.
*   **Ejemplo de error:**

    ```java
    javaCopiarEditarpublic void setAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("La edad no puede ser negativa");
        }
        this.age = age;
    }
    ```
* **Buenas prácticas:** Utilizarla para indicar que el método se está usando de forma incorrecta y guiar al desarrollador a corregir la llamada.

### `IllegalStateException` (Unchecked)

* **Descripción:** Indica que el **estado interno de un objeto** no es adecuado para la operación solicitada.
* **Cuándo ocurre:** Por ejemplo, al intentar llamar dos veces a `.start()` en un mismo hilo, o usar un recurso que no está listo.
*   **Ejemplo de error:**

    ```java
    javaCopiarEditarThread t = new Thread();
    t.start();
    t.start(); // Lanza IllegalStateException
    ```
* **Buenas prácticas:** Verificar la secuencia lógica o el estado del objeto antes de efectuar la llamada que podría ser inválida.

### `InterruptedException` (Checked)

* **Descripción:** Se lanza cuando un **hilo es interrumpido** mientras está en espera (sleep) o bloqueado.
* **Cuándo ocurre:** Al llamar `Thread.sleep(1000)` y otro hilo interrumpe el hilo en espera.
*   **Ejemplo de uso:**

    ```java
    javaCopiarEditartry {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        // Manejo de la interrupción
    }
    ```
* **Buenas prácticas:** Al manejar esta excepción, restaurar el estado de interrupción del hilo o parar la ejecución de forma segura.

### `StackOverflowError` (Unchecked, tipo Error)

* **Descripción:** **Error** (no excepción) que se produce cuando la **pila de llamadas** se desborda, usualmente por recursión infinita.
* **Cuándo ocurre:** En una recursión sin caso base que detenga la llamada, u otra situación que consuma toda la memoria de la pila.
*   **Ejemplo de error:**

    ```java
    javaCopiarEditarpublic void recurse() {
        recurse(); // Sin condición de salida => StackOverflowError
    }
    ```
* **Buenas prácticas:** Asegurarse de que las llamadas recursivas tengan condiciones de salida claras.

### `OutOfMemoryError` (Unchecked, tipo Error)

* **Descripción:** Otro **Error** que indica que la **JVM se quedó sin memoria** en el heap (u otros espacios de memoria).
* **Cuándo ocurre:** Al intentar crear muchos objetos sin liberar memoria o cuando hay fugas de memoria (`memory leaks`).
*   **Ejemplo de error:**

    ```java
    javaCopiarEditarList<int[]> bigList = new ArrayList<>();
    while(true) {
        bigList.add(new int[1000000]); // Eventualmente OutOfMemoryError
    }
    ```
* **Buenas prácticas:** Mantener un control adecuado del ciclo de vida de objetos, evitar colecciones excesivamente grandes sin necesidad y usar herramientas de análisis de memoria (profilers).

## Recomendaciones Generales

1. **Usar bloque `try-catch-finally` o `try-with-resources`** para manejar correctamente las excepciones checked y liberar recursos.
2. **Validar datos** antes de realizar operaciones (por ejemplo, comprobar que un objeto no sea `null`, que un índice esté dentro de los límites, etc.).
3. **Documentar** las excepciones que pueden ser lanzadas por tus métodos (usando `@throws` en la Javadoc).
4. **No abusar** de excepciones para el control de flujo normal. Lanzar excepciones debe ser la respuesta a condiciones excepcionales.
5. **Crear excepciones personalizadas** cuando sea necesario proporcionar un contexto claro del error.
