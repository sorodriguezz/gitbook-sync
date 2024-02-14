---
description: >-
  Documento que describe como crear reportes con JasperSoft Community y
  SpringBoot 3
---

# Jasper Report y SpringBoot 3

### Instalación JasperSoft

Para comenzar debemos descargar el software de Jasper, el cual lo encontrarás en el siguiente [enlace](https://community.jaspersoft.com/).

Este debe ser la versión community de JasperSoft.

### Uso de JasperSoft

Una vez instalado, lo abrimos y habrá creado un directorio llamado _<mark style="color:purple;">myReports.</mark>_

En el cual podemos cambiar en <mark style="color:purple;">switch workspace</mark>

Luego damos clic en `New` sobre <mark style="color:purple;">myReports</mark> y le damos en `Jasper Report.`

Esto abrirá un ventana flotante con los templates disponibles, en este caso crearemos uno desde cero, le damos en `Blank A4`. Y le ponemos un nombre a nuestro reporte manteniendo la extensión `.jrxml` y luego en `Finish`

### Diseñar reporte

Para empezar a diseñar el reporte podemos agregar `Static Text` para agregar texto que no modificaremos, hacer uso de `Text Field` para agregar texto por parámetro y para agregar imágenes usamos usamos el elemento `Image`.

Como en la siguiente imagen:

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

* `Static Text`: Este elemento basta con arrastrarlo y soltarlo, agregar el texto que uno quiera y en la pestaña `Properties` cambiar sus propiedades, como agrandar texto, poner color, tipo de texto, etc.
* `Text Field`: Este componente se arrastra y se pone donde nosotros queramos, ahora antes de usar tenemos que ir a la parte inferior izquierda de la pantalla, en `Outline` e ir a la parte de `Parameters`, luego hacemos clic derecho y presionamos `Create Parameter` y en la parte inferior derecha vamos a las `Properties` y cambiamos el nombre del parametro y su tipo de dato. Una vez configurado el tipo de parámetro, hacemos doble clic sobre el `Text Field` que creamos y vamos a `Parameters` nuevamente y seleccionamos el parámetro que creamos (haciendo doble clic), nos aseguramos que en la parte superior se vea como `$P{amount_paid}`
* `Image`: Para agregar una imagen a nuestro reporte solo debemos descargar una imagen y ponerla a la altura de nuestro `reporte.jrxml` donde en la parte inferior derecha pondremos el nombre y extension de la imagen:

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### Creación de proyecto Spring Boot

Ahora crearemos nuestro proyecto Spring Boot 3 con java 17 y no agregamos depedencias para este ejemplo.

Una vez creado, vamos a `src -> main -> resource -> template` y creamos un directorio que se llame `report` y dentro del directorio crearemos un archivo llamado `Report.jrxml`

Ahora vamos a nuestro reporte en `JasperSoft` y copiamos todo el contenido del reporte y pegamos dentro de nuestro archivo en Spring Boot.







### Configuración de JRXML y JasperConfig



