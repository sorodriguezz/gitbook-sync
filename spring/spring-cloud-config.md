---
description: Documento que explica la creación de un config server y un config client.
---

# Spring Cloud Config

## Introducción

En el mundo de microservicios, manejas configuraciones en diferentes entornos y servicios puede llegar a ser una tarea compleja. Donde la configuración centralizada es una método útil para mantener todas de manera compartida en un solo lugar.

## Creación Repositorios

Para estos ejemplos usaremos GitHub como gestor de repositorios (Igual se puede hacer uso de BitBucket o GitLab).

En GitHub podemos crear un repositorio llamado `service-configuration`  donde en su interior agregaremos archivos terminamos en `.properties` o `.yml` (para este caso serán archivos .properties).

Y podemos crear otro repositorio que donde tenga otros archivos de configuración. Estos archivos de configuración deben tener la siguiente estructura:

Según la convención de nombres estándar en Spring Cloud Config es`{application}-{profile}.properties`, donde:

* `{application}` se refiere al nombre de la aplicación o microservicio, que debe coincidir con la propiedad `spring.application.name` en el cliente.
* `{profile}` se refiere al perfil activo, como `dev`, `prod`, etc., que se especifica en el cliente mediante la propiedad `spring.profiles.active`.  Para agregar mas perfiles se deben separar con coma.

Para este caso usaré los archivos y repositorios para este ejemplo (estos archivos y repositorios pueden variar según tus necesidades):



Repositorio: `service-configuracion-sb`

Archivos: `product-microservice-dev.properties` y `product-microservice-prod.properties`



Repositorio: `test`

Archivos: `ms-productos-dev.properties`

## Creación Config Server

Para comenzar, con la ayuda de [Spring Initializr](https://start.spring.io/) para crea nuestro proyecto llamado "spring-config-server". Los requerimientos para este proyecto serán:

* Maven v4.0.0
* JAVA v22
* Spring Boot 3.2.5

Una vez tengamos todo esto seleccionado en el primer apartado.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Seleccionaremos de Spring Cloud Config, la dependencia Config Server. Como se muestra en la siguiente imagen:

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Una vez listo este paso, le damos en crear (o generar en el caso del Initializr)

### Configuración Config Server

Debemos ir a la clase principal de la aplicación y agregar el siguiente decorador para habilitar la funcionalidad de "servidor de configuración" para la aplicación.

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

En el directorio `src/main/resource` debemos crear el archivo `application.yml` que contenga lo siguiente:

```yaml
server:
  port: 8888
spring:
  cloud:
    config:
      server:
        git:
          uri: RUTA_DIRECTORIO_PROPIEDADES
          default-label: RAMA_A_REVISAR
          clone-on-start: true # Indicar que el repositorio debe clonarse al inicio (no a pedido). Generalmente conduce a un inicio más lento pero a una primera consulta más rápida.
          repos:
            products:
              pattern: PATRON_A_BUSCAR
              uri: RUTA_DIRECTORIO_PROPIEDADES
              default-label: RAMA_A_REVISAR
              clone-on-start: true
```

El archivo de ejemplo sería:

```yaml
server:
  port: 8888
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/sorodriguezz/service-configuration-sb
          default-label: master
          clone-on-start: true
          repos: # Define configuraciones de repositorios adicionales para aplicaciones específicas basadas en patrones de nombres.
            products: # Es un identificador para este grupo de configuraciones; este nombre es sólo para uso interno y no afecta a la funcionalidad.
              pattern: ms-* # Define un patrón de coincidencia para los nombres de las aplicaciones. Cualquier aplicación cuyo nombre comience con ms- utilizará este repositorio en lugar del definido en la raíz del uri.
              uri: https://github.com/sorodriguezz/test
              default-label: main
              clone-on-start: true
```

Donde como repositorio por defecto sería el `git.uri` y para extender con más repositorios de propiedades el uso de `repos`.&#x20;

## Pruebas con Postman

Para probar nuestro Config Server, debemos iniciarlo y con la ayuda del navegador o Postman realizamos la petición a las siguientes rutas:



Peticiones al repositorio `service-configuracion-sb`

http://localhost:8888/product-microservice-dev.properties\
http://localhost:8888/product-microservice-prod.properties



Peticiones al repositorio `test`

http://localhost:8888/ms-productos-dev.properties



Para estos ejemplos obtendremos el contenido de los archivos, pero igual podemos consultar como (donde dev y prod son los perfiles):

http://localhost:8888/product-microservice/dev

http://localhost:8888/product-microservice/prod

Este ultimo trae la información mas procesada y estructurada en formato JSON.

## Conexión cliente a config server



## Repositorio

[https://github.com/sorodriguezz/spring-config-server.git](https://github.com/sorodriguezz/spring-config-server.git)

## Referencias

[https://docs.spring.io/spring-cloud-config/docs/current/reference/html/](https://docs.spring.io/spring-cloud-config/docs/current/reference/html/)

[https://github.com/sorodriguezz/spring-config-server.git](https://github.com/sorodriguezz/spring-config-server.git)

