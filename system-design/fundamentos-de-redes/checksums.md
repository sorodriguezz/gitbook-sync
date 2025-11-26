# Checksums

Imagina que estás enviando una carta importante a tu amigo por  **correo** .

Antes de sellar el sobre, toma una  **fotografía**  de la carta.

Cuando tu amigo lo recibe, toma una foto de la carta y te la envía de vuelta.

Si las dos fotos  **coinciden** , sabrás que la carta no ha sido manipulada ni dañada durante el transporte.

**Si no coinciden** , es una clara señal de que algo salió mal en el camino: tal vez la carta fue  **alterada o parte de ella se perdió o dañó** .

En el mundo digital, las sumas de comprobación cumplen una función similar a la de esas fotos.

Así como tomar fotografías nos ayuda a responder la pregunta:  **"¿Se ha alterado o dañado la carta?"** , una suma de verificación responde a la pregunta:  **"¿Se han alterado estos datos de forma involuntaria o maliciosa desde que se crearon, almacenaron o transmitieron?".**

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.17.09.png" alt="" width="375"><figcaption></figcaption></figure>

En este artículo, exploraremos las sumas de comprobación, cómo funcionan, los diferentes tipos y sus aplicaciones en el mundo real.

## 1. ¿Qué es una suma de comprobación? <a href="#id-1-what-is-a-checksum" id="id-1-what-is-a-checksum"></a>

Una suma de comprobación es una  **huella digital única**  que se adjunta a los datos antes de su transmisión. Cuando los datos llegan al destinatario, se  **recalcula** la huella digital  para garantizar que coincida con la original.

Si la suma de comprobación de un dato coincide con el valor esperado, puede estar seguro de que los datos no han sido modificados ni dañados.

Las sumas de comprobación se calculan realizando una operación matemática en los datos, como sumar todos los bytes o ejecutarlos a través de una  **función hash criptográfica** .

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.17.56.png" alt=""><figcaption></figcaption></figure>

## 2. ¿Cómo funciona una suma de comprobación? <a href="#id-2-how-does-a-checksum-work" id="id-2-how-does-a-checksum-work"></a>

El proceso de utilizar una suma de comprobación para la detección de errores es sencillo:

1. **Cálculo:**  antes de enviar o almacenar datos, los datos originales se procesan a través de un algoritmo específico para producir un valor de suma de comprobación.
2. **Transmisión/Almacenamiento:**  La suma de comprobación se agrega a los datos y se envía a través de la red o se guarda en el almacenamiento.
3. **Verificación:**  Tras la recuperación o recepción, la suma de comprobación se recalcula utilizando el mismo algoritmo con los datos recibidos. Esta nueva suma de comprobación se compara con la suma de comprobación original.
4. **Detección de errores:**  Si los dos valores de suma de comprobación coinciden, los datos se consideran intactos. Si no coinciden, indica que los datos se han alterado o dañado durante la transmisión o el almacenamiento.

## 3. Tipos de sumas de comprobación <a href="#id-3-types-of-checksums" id="id-3-types-of-checksums"></a>

Existen varios tipos de sumas de comprobación, cada una con sus propias ventajas y desventajas. A continuación, se presentan algunas de las más comunes:

* **Bit de paridad:**  Un bit de paridad es un bit que se suma a un grupo de bits para que el número total de unos sea par (paridad par) o impar (paridad impar). Si bien puede detectar errores de un solo bit, falla si se invierte un número par de bits.
* **CRC (Comprobación de Redundancia Cíclica):**  Funciona tratando los datos como un número binario grande y dividiéndolos por un divisor predeterminado. El resto de esta división se convierte en la suma de comprobación. Las CRC están diseñadas para detectar errores comunes causados ​​por ruido en los canales de transmisión.
* **Funciones hash criptográficas:** Son funciones unidireccionales que generan un valor hash de tamaño fijo a partir de los datos. Algunos ejemplos populares son MD5, SHA-1 y SHA-256.

## **4. Aplicaciones reales de las sumas de comprobación** <a href="#id-4-real-world-applications-of-checksums" id="id-4-real-world-applications-of-checksums"></a>

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 22.18.44.png" alt="" width="375"><figcaption></figcaption></figure>

* **Descargas de archivos** : las sumas de comprobación verifican que los archivos descargados estén completos y no estén dañados.
* **Copias de seguridad de datos** : las sumas de comprobación garantizan que los datos respaldados sean precisos y confiables.
* **Comunicación de red** : Las sumas de comprobación garantizan que los paquetes de datos se transmitan correctamente, evitando errores y corrupción.

En resumen, las sumas de comprobación sirven como una línea de defensa vital, protegiendo contra errores y corrupción.

Desde las descargas de archivos y el almacenamiento de datos hasta las transmisiones de red y las instalaciones de software, las sumas de verificación trabajan incansablemente para detectar errores, prevenir la corrupción y darnos confianza en la precisión de nuestra información digital.
