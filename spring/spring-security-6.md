---
description: >-
  Esta documentación tienen la guía de la explicación de configuración e
  implementación de Spring Security 6, compatible con Spring Boot 3.
---

# Spring Security 6

## Explicación de Spring Security

Para comenzar daremos la explicación del flujo/arquitectura de Spring Security, para esto nos guiaremos con el siguiente diagrama:

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Comenzamos por la petición del usuarios (en el diagrama está señalado como <mark style="color:purple;">`HTTP REQUEST`</mark>) donde le realiza la petición al Controller de nuestro servicio.

Primero que todo, la petición pasará por la <mark style="color:purple;">`SECURITY FILTER CHAIN`</mark> el cual es una serie de filtros, en el cual pasa de filtro a filtro, si pasa todos los filtros (son 12 filtros aproximadamente). Para el <mark style="color:purple;">`DelegatingFilterProxy`</mark> es donde crearemos filtros personalizados para Spring Security, donde tendremos nuestras propias validaciones.

&#x20;Para personalizar nuestra condiciones haremos uso del <mark style="color:purple;">`Authentication Manager`</mark> pero esto a su vez necesita un proveedor de autenticación el cual esta representado como <mark style="color:purple;">`Authentication Provider`</mark> A y B (pueden ser más) en el diagrama. Esto depende de que forma nos vamos a autenticar cambiará una forma o la otra (con usuario de base de datos, oauth2, etc).

Para autenticarnos con base de datos necesitamos 2 componentes, el <mark style="color:purple;">`PasswordEncoder`</mark> y el <mark style="color:purple;">`UserDetailsService`</mark>. Donde el <mark style="color:purple;">`PasswordEncoder`</mark> se encargará de codificar las contraseñas y validar que estén correctas y el <mark style="color:purple;">`UserDetailsService`</mark> se encarga de conectar a la base de datos y extraer los usuarios (Puede ser la base de datos a elección, para estos ejemplos usaremos PostgreSQL).

Luego, si todo sale correctamente, el <mark style="color:purple;">`Authentication Provider`</mark> se comunica con el <mark style="color:purple;">`Authentication Manager`</mark> donde le avisa que si se encuentra en base de datos y la contraseña es correcta. Y luego el Authentication Manager le avisa al DelegatingFilterProxy, donde este se encarga de registrar al usuario que esta autenticado en el <mark style="color:purple;">`SECURITY CONTEXT HOLDER`</mark> y cuando volvamos a realizar una petición ya no nos pedirá autenticación porque el usuario ya esta registrado. Pero si el usuario no esta en la base de datos ni tiene su contraseña correcta, el <mark style="color:purple;">`Authentication Manager`</mark> le avisa al <mark style="color:purple;">`DelegatingFilterProxy`</mark> y retorna un <mark style="color:purple;">`HTTP RESPONSE`</mark> de no autorización (El típico 401).

En el <mark style="color:purple;">`Principal`</mark> es donde se guarda la información general del usuario y el <mark style="color:purple;">`Authorities`</mark> es donde se guardan los permisos.

## Creación del proyecto

Para crear nuestro proyecto haremos uso de <mark style="color:purple;">Spring Initialzr</mark> (Puede ser tanto el web como el que viene en IntelliJ)















