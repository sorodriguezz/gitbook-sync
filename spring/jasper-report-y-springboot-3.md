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

Ahora vamos a nuestro reporte en `JasperSoft` y copiamos todo el contenido del reporte y pegamos dentro de nuestro archivo `Report.jrxml` en Spring Boot.

Además creamos un directorio dentro de `src -> main -> resource -> static` llamado <mark style="color:purple;">images</mark>, donde copiaremos nuestras imágenes usadas en nuestro reporte.

Al igual agregar la siguiente linea en el listado de parametros del reporte:

```xml
<parameter name="imageDir" class="java.lang.String"/>
```

El cual iria de la siguiente manera:

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

### Configuración de JRXML y JasperConfig

Como usar nuestro reporte de JasperSoft necesitamos la siguiente dependencia para generar nuestro reporte, esto igual lo puedes tomar desde [aquí](https://mvnrepository.com/artifact/net.sf.jasperreports/jasperreports).

```xml
<dependency>
    <groupId>net.sf.jasperreports</groupId>
    <artifactId>jasperreports</artifactId>
    <version>6.21.0</version>
</dependency>
```

Ahora debemos corregir las variables que nos generó automáticamente el JasperSoft, debemos cambiar las rutas de las imagenes por lo siguiente:

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Y las variables deberian verse así:

```xml
<textFieldExpression><![CDATA[$P{voucher_id}]]></textFieldExpression>
```



Una vez modificado esto, agregamos el siguiente fragmento de codigo en nuestra clase Main de nuestro proyecto SpringBoot, esto hará que apenas levantemos nuestro proyecto se generé nuestro reporte en la ruta indicada:

```java
// SpringBootJasperApplication.java
import net.sf.jasperreports.engine.*;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import java.io.File;
import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.HashMap;
import java.util.Map;

@SpringBootApplication
public class SpringBootJasperApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootJasperApplication.class, args);
    }

    @Bean
    CommandLineRunner init() {
        return args -> {
            String destinationPath = "src" + File.separator +
                    "main" + File.separator +
                    "resources" + File.separator +
                    "static" + File.separator +
                    "ReportGenerated.pdf";

            String filePath = "src" + File.separator +
                    "main" + File.separator +
                    "resources" + File.separator +
                    "templates" + File.separator +
                    "report" + File.separator +
                    "Report.jrxml";

            LocalDateTime localDateTime = LocalDateTime.now();
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm:ss");

            Map<String, Object> parameters= new HashMap<>();
            parameters.put("voucher_id", "00000045634");
            parameters.put("current_date", formatter.format(localDateTime));
            parameters.put("amount_paid", new BigDecimal(30000));
            parameters.put("payment_method", "Cash");
            parameters.put("parent_name", "Santiago Perez");
            parameters.put("child_name", "Sofia Perez");
            parameters.put("imageDir", "classpath:/static/images/");

            JasperReport report = JasperCompileManager.compileReport(filePath);
            JasperPrint print = JasperFillManager.fillReport(report, parameters, new JREmptyDataSource());
            JasperExportManager.exportReportToPdfFile(print, destinationPath);
            System.out.println("Report Created Successfully");
        };
    }
}
```



{% hint style="info" %}
El `File.separator` nos ayuda para que esto pueda ejecutarse en todos los sistemas operativos y terminales.
{% endhint %}



### Enlace del repositorio

[https://github.com/sorodriguezz/springboot-jasper-report](https://github.com/sorodriguezz/springboot-jasper-report)
