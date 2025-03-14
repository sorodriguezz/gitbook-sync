---
description: >-
  Documentación detallada basada en el video sobre cómo funciona la gestión de
  memoria en Java, con foco en Stack y Heap:
---

# Funcionamiento memoria  en Java

## **Gestión de Memoria en Java: Stack y Heap**

Java, al igual que otras plataformas, utiliza un modelo de memoria basado en dos regiones principales: **Stack** (pila) y **Heap**.

### **Stack**

* Es una región de memoria donde se almacenan los **identificadores** (nombres de variables) y sus **valores**.
* Cuando se crean variables primitivas (ej., `int a = 500`), su valor se almacena directamente en el stack.
* En el caso de objetos, el stack almacena una **referencia** (dirección de memoria) hacia el objeto en el Heap.
* Cuando se comparan variables primitivas con el operador `==`, se compara el valor directamente en el stack.

### **Heap**

* Es una región de memoria donde se crean dinámicamente los **objetos** durante la ejecución del programa.
* Dentro del Heap, hay áreas reservadas para objetivos específicos (ej: Integer cache, String pool).
* Cuando se comparan objetos con el operador `==`, se comparan las direcciones de memoria (referencias).

## **Comportamiento con Objetos**

Cuando se trabaja con objetos, el comportamiento de stack y heap es diferente:

* Los identificadores de los objetos se almacenan en el stack, pero en lugar del valor, se guarda una referencia al objeto en el heap.
* Al comparar dos objetos con `==`, se comparan las referencias, no el contenido de los objetos. Por lo tanto, dos objetos con el mismo contenido pueden no ser iguales si están en diferentes ubicaciones de memoria.
* Para comparar el contenido de los objetos, se utiliza el método `equals()`. Este método compara el valor interno de los objetos en lugar de su dirección de memoria.

### **Integer Cache**

Existe una excepción al comportamiento regular de los objetos:

* Cuando se utilizan valores pequeños (entre -127 y 128) para objetos de tipo `Integer`, Java utiliza un "Integer Cache".
* En este rango, en lugar de crear nuevos objetos en el heap, las referencias apuntan a objetos existentes en el Integer Cache, por razones de eficiencia.
* Para forzar la creación de un nuevo objeto `Integer` fuera del caché, se debe utilizar la palabra clave `new`.

### **Strings**

Los strings tienen un comportamiento especial en Java:

* Cuando se crea un string, se almacena en una región especial del heap llamada "String Pool".
* Si se crea un string con el mismo valor que uno existente en el String Pool, la nueva referencia apuntará al mismo objeto en el pool. Esto optimiza el uso de memoria.
* Para forzar la creación de un nuevo objeto String fuera del String Pool, se debe utilizar la palabra clave `new`.
* Los strings en Java son inmutables. Esto significa que cada vez que se modifica un string, se crea un nuevo objeto en el String Pool.

### **Objetos Creados por el Usuario**

Cuando se crean objetos a partir de clases definidas por el usuario:

* Cada instancia creada con la palabra clave `new` genera un nuevo objeto en el heap.
* Si se asigna un objeto a otro, ambas referencias apuntarán al mismo objeto en el heap.
* Los cambios realizados a través de una referencia se verán reflejados en la otra.
* Por defecto, el método `equals()` compara las direcciones de memoria de los objetos.
* Para comparar el contenido de los objetos, se debe **sobreescribir** (override) el método `equals()` en la clase.

## **Consideraciones Finales**

* Es crucial conocer las reglas de gestión de memoria en Java para evitar comportamientos inesperados.
* Al comparar objetos, se debe tener en cuenta si se está comparando la referencia o el contenido.
* El uso de `equals()` es fundamental para comparar el contenido de los objetos.
* Se debe tener cuidado con las regiones especiales como el String Pool y el Integer Cache.
* En el Stack se registran los identificadores y si son valores primitivos se guardan directamente, sino lo que se almacena son direcciones de memoria hacia los objetos que "viven" en el Heap.
* Siempre se debe tener cuidado con las regiones especiales del Heap como el "String Pool" y en este caso "Integer Cache".
