# Principios de ingeniera de Software

## DRY (Don’t Repeat Yourself)

### **Explicación**

Evitar la duplicación de código mediante el uso de componentes, funciones o módulos reutilizables.

### **Beneficio**

* Reduce el **esfuerzo de mantenimiento** al corregir o actualizar la lógica en un único lugar.
* Hace que el **código sea más modular y reutilizable**, al centralizar la funcionalidad común.

### **Ejemplo**

En lugar de copiar y pegar la misma validación en varias partes de la aplicación, se crea una función o clase compartida que encapsula esa lógica.

***

## KISS (Keep It Simple, Stupid)

### **Explicación**

Mantener los diseños y las implementaciones lo más simples posible para **evitar la complejidad excesiva**.

### **Beneficio**

* Facilita la **lectura**, **depuración** y **extensión** del código.
* Reduce la probabilidad de bugs relacionados con lógica enrevesada.

### **Ejemplo**

Usar estructuras de control y diseños de clases sencillos en vez de elaborar soluciones demasiado sofisticadas sin necesidad.

***

## YAGNI (You Ain’t Gonna Need It)

### **Explicación**

No implementar funcionalidades hasta que realmente se necesiten, para **evitar el sobre-diseño** (over-engineering).

### **Beneficio**

* **Evita** agregar complejidades que pueden no llegar a usarse.
* **Ahorra** tiempo y recursos de desarrollo, promoviendo la entrega rápida de lo que sí es requerido.

### **Ejemplo**

No construir un complejo sistema de plugins o extensiones para un módulo si no existe la necesidad inmediata o plan cercana de utilizarlo.

***

## TDA (Tell, Don’t Ask)

### **Explicación**

En lugar de solicitar datos a los objetos y tomar decisiones externamente, “decirles” directamente lo que deben hacer. Esto promueve el **encapsulamiento**.

### **Beneficio**

* **Reduce el acoplamiento** al no exponer detalles internos de las clases.
* Fomenta la **orientación a objetos** más limpia, delegando comportamientos a quien corresponde.

### **Ejemplo**

En vez de pedir varios getters para calcular algo en otra clase, se llama a un método del objeto que realice esa lógica internamente.

***

## SLAP (Single Level of Abstraction Principle)

### **Explicación**

Cada función, clase o módulo debe operar en **un solo nivel de abstracción**. Evita mezclar lógica muy detallada con lógica de alto nivel en el mismo bloque de código.

### **Beneficio**

* Mantiene el **código organizado** y favorece la **legibilidad** y el **mantenimiento**.
* Permite crear capas lógicas (por ejemplo, capa de servicio vs. capa de repositorio) sin confundir las responsabilidades.

### **Ejemplo**

En un método de alto nivel (por ejemplo, un controlador), evitar implementar detalles de acceso a la base de datos. Dichos detalles se delegan a un repositorio o capa de persistencia.

***

## LOD (Law of Demeter)

### **Explicación**

Una clase debería **interactuar únicamente con sus colaboradoras directas** y no con las colaboradoras de sus colaboradoras (evitando “hablar” con objetos demasiado lejanos en la jerarquía).

### **Beneficio**

* **Previene cadenas de dependencias** profundas, reduciendo la fragilidad del código.
* Facilita la refactorización y la cohesión, ya que se evitan las “travesías de objetos” excesivas.

### **Ejemplo**

En vez de `obj.getOther().getAnother().doSomething()`, se delega la acción a `obj` para que internamente llame a sus dependencias (si corresponde).

***

## POLA (Principle of Least Astonishment)

### **Explicación**

El **software** debe comportarse de manera **predecible**, minimizando sorpresas para los usuarios y desarrolladores.

### **Beneficio**

* Mejora la **usabilidad** y la **experiencia de desarrollo** al tener comportamientos consistentes e intuitivos.
* Facilita el aprendizaje y reduces errores producidos por suposiciones erróneas.

### **Ejemplo**

Evitar que un botón de “Guardar” realice operaciones adicionales o inesperadas (ej. actualizar la configuración sin avisar).

***

## Law of Three (Ley de las Tres Veces)

### **Explicación**

* La primera vez que se hace algo, se hace manualmente.
* La segunda vez, se documenta.
* La tercera vez, se automatiza (porque a partir de ahí se reconoce una repetición).

### **Beneficio**

* Fomenta la **abstracción consciente** y evita crear automatizaciones prematuras que tal vez no se volverán a usar.
* Permite identificar patrones de repetición reales antes de invertir en automatización o refactorización.

### **Ejemplo**

Si solo se va a ejecutar un script una vez, no conviene crear una infraestructura elaborada. Si se repite frecuentemente, conviene documentar y automatizar.

***

## Occam’s Razor (La Navaja de Occam)

### **Explicación**

La explicación o solución **más simple** suele ser la mejor. En diseño, significa no sobre-ingeniar (over-engineer) un sistema cuando un diseño más sencillo es suficiente.

### **Beneficio**

* **Evita complejidad innecesaria** que puede dificultar el mantenimiento o la escalabilidad a largo plazo.
* Reduce el riesgo de errores al mantener la solución lo más clara posible.

### **Ejemplo**

No recurrir a microservicios y colas de mensajería avanzadas si una aplicación monolítica sencilla resuelve el problema.

***

### Stable Abstractions Principle

### **Explicación**

Mientras más abstracto sea un módulo, más estable debe ser para **evitar romper** a los módulos dependientes.

### **Beneficio**

* Garantiza que las **abstracciones clave** permanezcan estables a lo largo del tiempo.
* Previene cambios bruscos en la API o en las interfaces centrales que puedan impactar a múltiples consumidores.

### **Ejemplo:**

Diseñar APIs públicas con interfaces que no cambien frecuentemente, de modo que los clientes no tengan que actualizar su código sin cesar.

## Resumen

* **Reducir la complejidad** (KISS, Occam’s Razor).
* **Evitar repeticiones** innecesarias (DRY, Law of Three).
* **Enfocarse en la funcionalidad real** (YAGNI).
* **Mejorar la encapsulación y modularidad** (TDA, LOD, SLAP).
* **Ofrecer una experiencia coherente y estable** para usuarios y desarrolladores (POLA, Stable Abstractions Principle).
