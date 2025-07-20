---
description: >-
  La serialización convierte objetos en un formato que permite transmitirlos o
  guardarlos para su posterior reconstrucción (deserialización).
---

# Tipos de serialización en Java

## Java Native Serialization

### **Uso**

* Basada en la **interfaz `Serializable`** para transformar objetos Java a flujos de bytes.
* Permite guardar el estado de los objetos y reconstruirlos en la misma JVM o en otra JVM compatible.

### **Rendimiento**

* **Lento** en comparación con otros métodos más optimizados.
* Incorpora metadatos detallados (versión de clase, etc.), lo que impacta en la velocidad.
* **Legibilidad humana:**
  * **No legible.** Es un formato binario propietario de Java.

### **Mejor para**

* **Aplicaciones exclusivamente Java**, donde no se requiera interoperar con otros lenguajes.
* Escenarios de bajo rendimiento o donde la simplicidad prime sobre la velocidad y compatibilidad.

### **Consideraciones**

* Existen riesgos de seguridad si se deserializan datos no confiables (posibles ataques de ejecución de código).
* Se recomienda usar mecanismos alternativos (por ejemplo, JSON o protocolos binarios más seguros) cuando se necesite comunicación fuera de entornos controlados.

## JSON Serialization

### **Uso**

* Convierte objetos en **formato JSON** (JavaScript Object Notation).
* Muy popular en **APIs REST**, servicios web y aplicaciones modernas de frontend-backend.

### **Rendimiento**

* **Moderado.** Más rápido que XML, pero generalmente más lento que binarios puros.
* Aun así, existen bibliotecas optimizadas (Jackson, Gson, JSON-B, etc.).
* **Legibilidad humana:**
  * **Sí**, formato de texto muy legible y ampliamente aceptado.

### **Mejor para**

* **Comunicaciones entre diferentes lenguajes** (Java, JavaScript, Python, etc.).
* Entornos **web** y servicios que requieran datos estructurados de fácil comprensión.

### **Consideraciones**

* Soporta estructuras anidadas y tipos básicos (números, booleanos, cadenas).
* Manejar adecuadamente problemas de codificación (UTF-8) y tipos especiales (fechas, binarios, etc.).

***

## XML Serialization

### **Uso**

* Convierte objetos al **formato XML**, tradicionalmente muy usado en sistemas empresariales o legados.
* Compatible con un gran ecosistema de herramientas y librerías (JAXB, por ejemplo, para Java).

### **Rendimiento**

* **Lento.** El parsing de XML es más complejo y verboso que JSON o binarios.

### **Legibilidad humana**

* **Sí**, aunque con mayor verbosidad (muchas etiquetas y metadatos).

### **Mejor para**

* **Aplicaciones legacy** donde XML ya está establecido como estándar de intercambio.
* Escenarios que requieran **validación estricta** mediante esquemas (XSD) y un formato muy estructurado.

### **Consideraciones**

* Dada su naturaleza detallada, puede aumentar el tamaño de los datos transmitidos o almacenados.
* A menudo se mantiene por razones de compatibilidad con sistemas antiguos.

***

## Binary Serialization

### **Uso**

* Serialización binaria **altamente eficiente** usando bibliotecas externas como **Kryo** o **Protocol Buffers** (Protobuf).
* Se centra en la compactación y rapidez.

### **Rendimiento**

* **Rápido.** Suele ser de las opciones más veloces y con menor tamaño de datos resultante.

### **Legibilidad humana**

* **No**, es un formato binario. Difícil de interpretar sin herramientas específicas.

### **Mejor para**

* **Aplicaciones de alto rendimiento** (Big Data, networking de baja latencia).
* Casos donde se prioriza el **tamaño reducido** y la velocidad de serialización por encima de la legibilidad.

### **Consideraciones**

* Se requiere un **esquema** o definición previa de estructuras (por ejemplo, en Protobuf o Avro) para compartir datos entre sistemas.
* Muy bueno para **aplicaciones distribuidas** o comunicación entre microservicios cuando se necesita optimizar recursos.

***

## YAML Serialization

### **Uso**

* Emplea el formato **YAML**, considerado más legible para humanos y con soporte de estructuras complejas (similares a JSON).
* Muy usado en **archivos de configuración** y en algunos casos de intercambio de datos.

### **Rendimiento**

* **Moderado.** Puede ser menos eficiente que JSON, ya que su parser es un poco más complejo.
* Aun así, la diferencia no suele ser muy grande para la mayoría de casos.

### **Legibilidad humana**

* **Sí**, con sintaxis más tersa que XML y más flexible que JSON en ciertos aspectos (permite comentarios, por ejemplo).

### **Mejor para**

* **Archivos de configuración**, scripts de despliegue (ej.: Kubernetes), y APIs donde la **legibilidad** sea prioritaria.
* Entornos donde se requiera una sintaxis amigable y que admita comentarios.

### **Consideraciones**

* Hay que tener cuidado con la **indentación** y los tipos implícitos en YAML (espacios, caracteres especiales).
* Al igual que JSON, no es tan eficiente como un formato binario para el transporte masivo de datos.

***

## Avro Serialization

### **Uso**

* **Apache Avro** es común en ecosistemas de **Big Data** como **Apache Kafka** o **Hadoop**.
* Ofrece serialización binaria **eficiente** y una forma de **evolucionar el esquema** sin grandes problemas de compatibilidad.

### **Rendimiento**

* **Rápido**, similar a otros formatos binarios (Protobuf, Thrift, etc.).

### **Legibilidad humana**

* **No** en su versión binaria nativa. Sin embargo, existe un formato JSON para Avro que se utiliza principalmente con fines de depuración.

### **Mejor para**

* **Aplicaciones de Big Data**, procesamiento de grandes volúmenes de información, streaming de eventos.
* Entornos donde los esquemas evolucionan con el tiempo y se requiere que distintos productores y consumidores se mantengan compatibles.

### **Consideraciones**

* Gestiona de manera robusta el **esquema** (versiones, campos opcionales, etc.), lo que facilita la interoperabilidad en sistemas distribuidos.
* Requiere la definición previa del esquema Avro (`.avsc` files).

***

## Resumen

| **Tipo**                      | **Rendimiento** | **Legible por Humanos** | **Mejor para**                                                                     |
| ----------------------------- | --------------- | ----------------------- | ---------------------------------------------------------------------------------- |
| **Java Native Serialization** | Lento           | No                      | Apps sólo en Java, no orientado a compatibilidad cross-language                    |
| **JSON Serialization**        | Moderado        | Sí                      | REST APIs, servicios web, integración multi-lenguaje                               |
| **XML Serialization**         | Lento           | Sí                      | Sistemas empresariales antiguos, validaciones con XSD                              |
| **Binary Serialization**      | Rápido          | No                      | Alto rendimiento (Kryo, Protobuf), mensajería y microservicios optimizados         |
| **YAML Serialization**        | Moderado        | Sí                      | Configuraciones, despliegues (ej. Kubernetes), APIs donde la legibilidad sea clave |
| **Avro Serialization**        | Rápido          | No (binario)            | Big Data, ecosistemas Kafka/Hadoop, evolución de esquemas en tiempo de ejecución   |

* **Elegir el tipo de serialización** depende en gran medida de los requisitos de **rendimiento**, **legibilidad** y **compatibilidad** del proyecto.
* **Java Native Serialization** es sencillo pero poco eficiente y poco seguro para entornos externos.
* **JSON** y **YAML** destacan por su legibilidad, ideales para **APIs** y **configuraciones**.
* **XML** es más verboso, se mantiene en muchos proyectos **legacy**, con buenos mecanismos de validación.
* **Binary formats** (Kryo, Protobuf, Avro) son superiores en **rendimiento** y tamaño de datos, usados en aplicaciones de **alto rendimiento** o **Big Data**.
* **Avro** aporta esquemas evolutivos y es muy popular en plataformas de streaming como **Kafka**.
