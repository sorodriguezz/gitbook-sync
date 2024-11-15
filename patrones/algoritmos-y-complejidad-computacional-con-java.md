# Algoritmos y Complejidad Computacional con Java

¡Bienvenido a esta clase completa sobre **Algoritmos y Complejidad Computacional** en Java! En esta guía exhaustiva, exploraremos los algoritmos más importantes, cómo implementarlos en Java y comprenderemos en profundidad la **notación Big O**, incluyendo cómo medir y representar gráficamente la complejidad de los algoritmos.

***

### Tabla de Contenidos

1. Introducción a los Algoritmos
   * 1.1. ¿Qué es un Algoritmo?
   * 1.2. Importancia de los Algoritmos en Programación
2. Notación Big O y Complejidad Computacional
   * 2.1. ¿Qué es la Notación Big O?
   * 2.2. Tipos Comunes de Complejidad
   * 2.3. Cómo Medir la Complejidad
   * 2.4. Representación Gráfica de la Complejidad
3. Algoritmos de Ordenamiento
   * 3.1. Bubble Sort (Ordenamiento de Burbuja)
   * 3.2. Selection Sort (Ordenamiento por Selección)
   * 3.3. Insertion Sort (Ordenamiento por Inserción)
   * 3.4. Merge Sort (Ordenamiento por Mezcla)
   * 3.5. Quick Sort (Ordenamiento Rápido)
4. Algoritmos de Búsqueda
   * 4.1. Búsqueda Lineal
   * 4.2. Búsqueda Binaria
5. Estructuras de Datos Básicas
   * 5.1. Pilas (Stacks)
   * 5.2. Colas (Queues)
   * 5.3. Listas Enlazadas
   * 5.4. Árboles Binarios
6. Algoritmos Avanzados
   * 6.1. Algoritmo de Dijkstra
   * 6.2. Programación Dinámica
7. Buenas Prácticas y Consejos
   * 7.1. Análisis de Algoritmos
   * 7.2. Optimización de Código
8. Conclusión
   * 8.1. Pasos Siguientes
   * 8.2. Recursos Adicionales

***

### 1. Introducción a los Algoritmos

#### 1.1. ¿Qué es un Algoritmo?

Un **algoritmo** es un conjunto bien definido de pasos o instrucciones para resolver un problema o realizar una tarea específica. En programación, los algoritmos son fundamentales para desarrollar soluciones eficientes y efectivas.

#### 1.2. Importancia de los Algoritmos en Programación

* **Eficiencia**: Permiten resolver problemas de manera óptima en términos de tiempo y recursos.
* **Reutilización**: Proporcionan soluciones generales que pueden aplicarse a múltiples situaciones.
* **Fundamento Teórico**: Ayudan a entender los principios subyacentes en la computación y el procesamiento de datos.

***

### 2. Notación Big O y Complejidad Computacional

#### 2.1. ¿Qué es la Notación Big O?

La **notación Big O** es una forma matemática de describir el rendimiento o la complejidad de un algoritmo, especialmente en términos de **tiempo de ejecución** o **uso de espacio** en función del tamaño de la entrada (n). Se centra en el comportamiento asintótico, es decir, cómo se comporta el algoritmo cuando n tiende a infinito.

#### 2.2. Tipos Comunes de Complejidad

1. **O(1) - Tiempo Constante**: El tiempo de ejecución no cambia con el tamaño de la entrada.
2. **O(log n) - Tiempo Logarítmico**: El tiempo de ejecución crece logarítmicamente con el tamaño de la entrada.
3. **O(n) - Tiempo Lineal**: El tiempo de ejecución crece linealmente con el tamaño de la entrada.
4. **O(n log n)**: Combinación de lineal y logarítmico; común en algoritmos de ordenamiento eficientes.
5. **O(n²) - Tiempo Cuadrático**: El tiempo de ejecución crece proporcional al cuadrado del tamaño de la entrada.
6. **O(2^n) - Tiempo Exponencial**: El tiempo de ejecución crece exponencialmente con el tamaño de la entrada.

#### 2.3. Cómo Medir la Complejidad

1. **Contar Operaciones Básicas**: Identificar las operaciones más significativas y cómo su número crece con n.
2. **Análisis de Bucles**:
   * Un bucle simple que itera n veces suele ser O(n).
   * Un bucle anidado (un bucle dentro de otro) que itera n veces cada uno suele ser O(n²).
3. **Recurrencias**: Analizar algoritmos recursivos mediante ecuaciones de recurrencia.

#### 2.4. Representación Gráfica de la Complejidad

Aunque no podemos mostrar gráficos aquí, podemos describir cómo diferentes complejidades se comportan gráficamente:

* **O(1)**: Línea horizontal; el tiempo de ejecución es constante.
* **O(log n)**: Curva que crece lentamente y se aplana con n grande.
* **O(n)**: Línea recta ascendente; el tiempo de ejecución aumenta proporcionalmente con n.
* **O(n log n)**: Crece más rápido que O(n) pero mucho más lento que O(n²).
* **O(n²)**: Curva parabólica; el tiempo de ejecución aumenta rápidamente con n.

***

### 3. Algoritmos de Ordenamiento

Los algoritmos de ordenamiento organizan elementos en una secuencia específica (por ejemplo, numérica o alfabética).

#### 3.1. Bubble Sort (Ordenamiento de Burbuja)

**Descripción**

Compara pares adyacentes de elementos y los intercambia si están en el orden incorrecto. Este proceso se repite hasta que la lista está ordenada.

**Implementación en Java**

```java
public class BubbleSort {
    public static void bubbleSort(int[] arr) {
        int n = arr.length;
        boolean intercambio;
        for (int i = 0; i < n - 1; i++) {
            intercambio = false;
            for (int j = 0; j < n - i - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    // Intercambiar arr[j] y arr[j+1]
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                    intercambio = true;
                }
            }
            // Si no hubo intercambios, la lista está ordenada
            if (!intercambio)
                break;
        }
    }
}
```

**Complejidad**

* **Peor caso**: O(n²)
* **Mejor caso (lista ya ordenada)**: O(n)

**Explicación**

En el peor caso, el algoritmo necesita hacer n² comparaciones. En el mejor caso, si la lista está ordenada, solo pasa una vez por el bucle.

***

#### 3.2. Selection Sort (Ordenamiento por Selección)

**Descripción**

Encuentra el elemento mínimo y lo coloca al inicio de la lista. Repite este proceso para cada posición.

**Implementación en Java**

```java
public class SelectionSort {
    public static void selectionSort(int[] arr) {
        int n = arr.length;
    
        for (int i = 0; i < n - 1; i++) {
            int indiceMin = i;
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[indiceMin]) {
                    indiceMin = j;
                }
            }
            // Intercambiar arr[i] y arr[indiceMin]
            int temp = arr[indiceMin];
            arr[indiceMin] = arr[i];
            arr[i] = temp;
        }
    }
}
```

**Complejidad**

* **Peor y Mejor caso**: O(n²)

**Explicación**

Siempre realiza n² comparaciones, independientemente del estado inicial de la lista.

***

#### 3.3. Insertion Sort (Ordenamiento por Inserción)

**Descripción**

Construye el ordenamiento final uno a uno, insertando cada elemento en su posición correcta.

**Implementación en Java**

```java
public class InsertionSort {
    public static void insertionSort(int[] arr) {
        int n = arr.length;
    
        for (int i = 1; i < n; ++i) {
            int clave = arr[i];
            int j = i - 1;
    
            // Mover los elementos mayores que la clave hacia adelante
            while (j >= 0 && arr[j] > clave) {
                arr[j + 1] = arr[j];
                j = j - 1;
            }
            arr[j + 1] = clave;
        }
    }
}
```

**Complejidad**

* **Peor caso**: O(n²)
* **Mejor caso (lista ya ordenada)**: O(n)

**Explicación**

En el mejor caso, solo realiza n comparaciones. En el peor caso, como una lista invertida, realiza n² comparaciones.

***

#### 3.4. Merge Sort (Ordenamiento por Mezcla)

**Descripción**

Divide la lista en sublistas hasta tener sublistas de un elemento y luego las mezcla ordenadamente.

**Implementación en Java**

```java
public class MergeSort {
    public static void mergeSort(int[] arr, int izquierda, int derecha) {
        if (izquierda < derecha) {
            int medio = (izquierda + derecha) / 2;

            // Ordenar primera y segunda mitad
            mergeSort(arr, izquierda, medio);
            mergeSort(arr, medio + 1, derecha);

            // Mezclar las mitades ordenadas
            merge(arr, izquierda, medio, derecha);
        }
    }

    public static void merge(int[] arr, int izquierda, int medio, int derecha) {
        // Tamaños de las sublistas
        int n1 = medio - izquierda + 1;
        int n2 = derecha - medio;

        // Arrays temporales
        int[] L = new int[n1];
        int[] R = new int[n2];

        // Copiar datos a arrays temporales
        for (int i = 0; i < n1; ++i)
            L[i] = arr[izquierda + i];
        for (int j = 0; j < n2; ++j)
            R[j] = arr[medio + 1 + j];

        // Mezclar los arrays temporales
        int i = 0, j = 0, k = izquierda;
        while (i < n1 && j < n2) {
            if (L[i] <= R[j]) {
                arr[k++] = L[i++];
            } else {
                arr[k++] = R[j++];
            }
        }

        // Copiar elementos restantes
        while (i < n1) {
            arr[k++] = L[i++];
        }
        while (j < n2) {
            arr[k++] = R[j++];
        }
    }
}
```

**Complejidad**

* **Peor y Mejor caso**: O(n log n)

**Explicación**

Divide el array repetidamente y luego los combina, resultando en una complejidad de O(n log n).

***

#### 3.5. Quick Sort (Ordenamiento Rápido)

**Descripción**

Selecciona un **pivote** y divide el array en subarrays que son menores y mayores que el pivote, luego aplica el mismo proceso recursivamente.

**Implementación en Java**

```java
public class QuickSort {
    public static void quickSort(int[] arr, int inicio, int fin) {
        if (inicio < fin) {
            int indicePivote = particionar(arr, inicio, fin);
            quickSort(arr, inicio, indicePivote - 1);
            quickSort(arr, indicePivote + 1, fin);
        }
    }

    public static int particionar(int[] arr, int inicio, int fin) {
        int pivote = arr[fin];
        int i = (inicio - 1);
        for (int j = inicio; j < fin; j++) {
            if (arr[j] <= pivote) {
                i++;

                // Intercambiar arr[i] y arr[j]
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }

        // Intercambiar arr[i+1] y arr[fin] (pivote)
        int temp = arr[i + 1];
        arr[i + 1] = arr[fin];
        arr[fin] = temp;

        return i + 1;
    }
}
```

**Complejidad**

* **Peor caso**: O(n²) (cuando el pivote es el elemento más pequeño o más grande)
* **Mejor y Promedio caso**: O(n log n)

**Explicación**

En el mejor caso, divide el array en partes casi iguales, resultando en O(n log n). En el peor caso, se comporta como un ordenamiento por inserción.

***

### 4. Algoritmos de Búsqueda

#### 4.1. Búsqueda Lineal

**Descripción**

Recorre el array elemento por elemento hasta encontrar el elemento buscado.

**Implementación en Java**

```java
public class BusquedaLineal {
    public static int busquedaLineal(int[] arr, int elemento) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == elemento) {
                return i; // Retorna el índice del elemento
            }
        }
        return -1; // Elemento no encontrado
    }
}
```

**Complejidad**

* **Peor y Promedio caso**: O(n)

***

#### 4.2. Búsqueda Binaria

**Descripción**

Requiere un array ordenado. Divide repetidamente el espacio de búsqueda a la mitad comparando el elemento medio.

**Implementación en Java**

```java
public class BusquedaBinaria {
    public static int busquedaBinaria(int[] arr, int elemento) {
        int izquierda = 0;
        int derecha = arr.length - 1;
    
        while (izquierda <= derecha) {
            int medio = izquierda + (derecha - izquierda) / 2;
    
            if (arr[medio] == elemento)
                return medio;
            if (arr[medio] < elemento)
                izquierda = medio + 1;
            else
                derecha = medio - 1;
        }
        return -1; // Elemento no encontrado
    }
}
```

**Complejidad**

* **Peor y Promedio caso**: O(log n)

***

### 5. Estructuras de Datos Básicas

#### 5.1. Pilas (Stacks)

**Descripción**

Una **pila** es una estructura LIFO (Last In, First Out). Se puede añadir o quitar elementos solo desde el tope.

**Implementación en Java**

Usando `java.util.Stack`:

```java
import java.util.Stack;

public class PilaEjemplo {
    public static void main(String[] args) {
        Stack<Integer> pila = new Stack<>();
        pila.push(10); // Añadir elemento
        pila.push(20);
        System.out.println(pila.pop()); // Quitar y retornar elemento
    }
}
```

***

#### 5.2. Colas (Queues)

**Descripción**

Una **cola** es una estructura FIFO (First In, First Out). Los elementos se añaden al final y se quitan del inicio.

**Implementación en Java**

Usando `java.util.LinkedList` como `Queue`:

```java
import java.util.LinkedList;
import java.util.Queue;

public class ColaEjemplo {
    public static void main(String[] args) {
        Queue<Integer> cola = new LinkedList<>();
        cola.add(10); // Añadir elemento
        cola.add(20);
        System.out.println(cola.poll()); // Quitar y retornar elemento
    }
}
```

***

#### 5.3. Listas Enlazadas

**Descripción**

Consisten en nodos donde cada nodo contiene datos y una referencia al siguiente nodo.

**Implementación en Java**

```java
public class Nodo {
    int dato;
    Nodo siguiente;

    public Nodo(int dato) {
        this.dato = dato;
        this.siguiente = null;
    }
}

public class ListaEnlazada {
    Nodo cabeza;

    public void insertar(int dato) {
        Nodo nuevoNodo = new Nodo(dato);
        if (cabeza == null) {
            cabeza = nuevoNodo;
        } else {
            Nodo temp = cabeza;
            while (temp.siguiente != null) {
                temp = temp.siguiente;
            }
            temp.siguiente = nuevoNodo;
        }
    }
}
```

***

#### 5.4. Árboles Binarios

**Descripción**

Estructura jerárquica donde cada nodo tiene a lo sumo dos hijos: izquierdo y derecho.

**Implementación en Java**

```java
public class NodoArbol {
    int valor;
    NodoArbol izquierdo, derecho;

    public NodoArbol(int valor) {
        this.valor = valor;
        izquierdo = derecho = null;
    }
}

public class ArbolBinario {
    NodoArbol raiz;

    public void insertar(int valor) {
        raiz = insertarRecursivo(raiz, valor);
    }

    private NodoArbol insertarRecursivo(NodoArbol raiz, int valor) {
        if (raiz == null) {
            raiz = new NodoArbol(valor);
            return raiz;
        }
        if (valor < raiz.valor) {
            raiz.izquierdo = insertarRecursivo(raiz.izquierdo, valor);
        } else if (valor > raiz.valor) {
            raiz.derecho = insertarRecursivo(raiz.derecho, valor);
        }
        return raiz;
    }
}
```

***

### 6. Algoritmos Avanzados

#### 6.1. Algoritmo de Dijkstra

**Descripción**

Encuentra el camino más corto desde un nodo origen a todos los demás nodos en un grafo con pesos no negativos.

**Implementación en Java**

Debido a la complejidad, se proporciona una implementación simplificada usando una matriz de adyacencia.

```java
import java.util.Arrays;

public class Dijkstra {
    public static void dijkstra(int[][] grafo, int origen) {
        int n = grafo.length;
        int[] dist = new int[n];
        boolean[] visitado = new boolean[n];

        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[origen] = 0;

        for (int i = 0; i < n - 1; i++) {
            int u = distanciaMinima(dist, visitado);
            visitado[u] = true;

            for (int v = 0; v < n; v++) {
                if (!visitado[v] && grafo[u][v] != 0 &&
                    dist[u] != Integer.MAX_VALUE &&
                    dist[u] + grafo[u][v] < dist[v]) {
                    dist[v] = dist[u] + grafo[u][v];
                }
            }
        }

        // Mostrar distancias
        for (int i = 0; i < n; i++) {
            System.out.println("Distancia del nodo " + origen + " al nodo " + i + " es " + dist[i]);
        }
    }

    public static int distanciaMinima(int[] dist, boolean[] visitado) {
        int min = Integer.MAX_VALUE, indiceMin = -1;

        for (int v = 0; v < dist.length; v++) {
            if (!visitado[v] && dist[v] <= min) {
                min = dist[v];
                indiceMin = v;
            }
        }
        return indiceMin;
    }
}
```

***

#### 6.2. Programación Dinámica

**Descripción**

Es una técnica para resolver problemas complejos dividiéndolos en subproblemas más simples y almacenando los resultados de estos subproblemas para evitar cálculos repetidos.

**Ejemplo: Problema de la Mochila (Knapsack Problem)**

Debido a la extensión, se recomienda investigar este algoritmo por separado.

***

### 7. Buenas Prácticas y Consejos

#### 7.1. Análisis de Algoritmos

* **Comprender el problema**: Antes de elegir un algoritmo, entiende completamente el problema.
* **Elegir el algoritmo adecuado**: Considera la complejidad y el contexto.
* **Probar con diferentes entradas**: Especialmente casos extremos y grandes n.

#### 7.2. Optimización de Código

* **Evitar bucles innecesarios**: Reduce la complejidad al mínimo necesario.
* **Usar estructuras de datos adecuadas**: Pueden mejorar significativamente el rendimiento.
* **Profiling**: Utiliza herramientas para medir el rendimiento y encontrar cuellos de botella.

***

### 8. Conclusión

#### 8.1. Pasos Siguientes

* **Practicar**: Implementa los algoritmos y analiza su complejidad.
* **Estudiar Algoritmos Avanzados**: Como árboles balanceados, grafos, algoritmos de búsqueda avanzada.
* **Participar en Competencias**: Ayuda a mejorar habilidades y aprender nuevos algoritmos.

#### 8.2. Recursos Adicionales

* **Libros**:
  * _"Introduction to Algorithms"_ por Cormen, Leiserson, Rivest y Stein.
  * _"Algorithms"_ por Robert Sedgewick y Kevin Wayne.

***

**Ejercicio Práctico**

1. **Implementar y comparar** al menos tres algoritmos de ordenamiento con arrays de diferentes tamaños y medir su tiempo de ejecución.
2. **Analizar la complejidad** de un algoritmo recursivo, como el cálculo de Fibonacci, y optimizarlo usando programación dinámica.
3. **Crear una aplicación** que utilice un árbol binario para almacenar y buscar datos, y analizar su eficiencia.
