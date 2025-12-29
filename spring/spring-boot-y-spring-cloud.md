# Spring Boot y Spring Cloud

<figure><img src="https://user-images.githubusercontent.com/33158051/103466606-760a4000-4d14-11eb-9941-2f3d00371471.png" alt=""><figcaption></figcaption></figure>

### Implementaciones

* Spring Boot
* MongoDB
* MySQL
* Discovery Server
* Config Server
* Secrets en Vault (HashiCrop Vault)
* Actuator @RefreshScope
* Spring Cloud Config
* Spring Cloud Bus
* RabbitMQ
* Spring Reactive Gateway
* Auth Server KeyCloak
* Circuit Breaker
* Resilence4j

### Descripción de microservicios

* **product-microservice**: Servicio que se conecta a MongoDB, inserta un producto y trae una lista de productos. Además tiene Eureka Client.
* **booking-microservice**: Servicio que se conecta a MySql. Además tiene Eureka Client y Circuit Breaker.
* **discovery-service**: Servicio que funciona con Spring Cloud Eureka, el cual es el servidor. Detalla los servicios disponibles.
* **config-server**: Se comunica con los archivos de `service-configuration` para traer propiedades de GitHub.
* **api-gateway**: Servicio que se encarga de orquestar las consultas, toma los endpoints desde Eureka Server y envia la petición donde corresponde.
* **stock-microservice**: Tiene la conexion a MySQL, con un endpoint para buscar un codigo.

### Uso de HashiCorp Vault

Para usar esta herramientas necesitamos descargar el software desde: [HashiCorp Vault](https://developer.hashicorp.com/vault/install?product_intent=vault)

* Posterior a esto, descomprimimos en una ruta que no debamos moverlo (En C: como recomendación).
* Abrir variables de entorno del sistema y en `Path` de variables del sistema agregar la ruta donde se encuentra nuestro `.exe`.
* Abrimos una consolta donde este el `.exe` del archivo descomprimido.
* Ejecutamos `vault`, si arroja la informacion de Vault esta perfecto.
* Luego vamos al sitio de [Spring Vault Config](https://spring.io/guides/gs/vault-config/).
* Dentro de la consola anteriormente abierta ejecutamos (Si no especificamos un token id asignará uno por defecto):

```=bash
$ vault server --dev --dev-root-token-id="00000000-0000-0000-0000-000000000000"
```

* Volvemos abrir las variables de entorno y agregamos una nueva variable que se llame `VAULT_ADDR` con el valor del servidor levantado por vault, por defecto es `http://127.0.0.1:8200`
* Luego visitamos el sitio `http://127.0.0.1:8200` y se nos abrira un login para vault, en este caso nuestro token id para entrar es `00000000-0000-0000-0000-000000000000` que es con el cual arracamos el servidor.
* Para agregar un nuevo archivo de secretos a nuestro vault agregamos con el comando:

```=bash
$ vault kv put secret/booking-microservice @booking-microservice.json
$ vault kv put secret/product-microservice @product-microservice.json
$ vault kv put secret/stock-microservice @stock-microservice.json
```

El `@booking-microservice.json` es nuestro archivo de configuraciones, posicionate dentro de la carpeta secrets.

### RabbitMQ con Docker

Para instalar [RabbitMQ](https://www.rabbitmq.com/download.html) en local, usaremos docker. Donde usamos el comando:

```=bash
$ docker run -it --rm -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.12-management
```

### Auth Server Keycloak con Docker y configuración

Para instalar el servidor de [keycloak](https://www.keycloak.org/getting-started/getting-started-docker) en local, usaremos docker con el siguiente comando:

```=bash
$ docker run -p 9090:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:23.0.3 start-dev
```

Para ir al dashboard visitaremos `http://localhost:9090/admin` Donde la contraseña es `admin` y el usuario es `admin`

Para crear un `realm` (tenant) vamos sobre donde dice "master" y le damos en **Create realm**. Esto permite crear un grupos aislados de aplicaciones y usuarios. (No se deberia usar el realm Master, ese es solo para Keycloak)

* Al crearlo le ponemos un nombre al realm y damos en crear.
* Para crear un cliente, vamos al menú lateral izquiedo donde dice **Clients**.
* Le damos un **Client ID**, en este caso le pondremos **spring-cloud-gateway-client**.
* En name pondremos **Api Gateway**.
* En **Capability config** pondremos check en **Authorization** y **Client authentication**.
* En **Login settings** para **Valid redirect URIs** pondremos `http://localhost:8080/login/oauth2/code/spring-cloud-gateway-client` para usar el gateway como autenticación con ouath2.
* Le damos **save** y en el apartado Credentials copiamos nuestro **Client Secret**.
* Luego crearemos un usuario en el apartado **Users** del mení lateral izquierdo. Completamos los datos y lo creamos.
* Una vez creado, vamos al apartado **Credentials** y seteamos una contraseña para el usuario creado.
* Luego para tomar las configuraciones para conectarnos al realme con SpringBoot, vamos a **Realm settings** en el manú y abrimos el link de **OpenID Endpoint Configuration**

### Motores de base de datos con Docker

Estos motores de base de datos los usaremos con docker, donde los levantaremos con los archivos `YML` de docker compose. Antes debes tener instalar [Docker](https://www.docker.com/products/docker-desktop/) en tu equipo.

```=bash
$ docker compose -f docker/docker-compose-mongodb.yml up -d
$ docker compose -f docker/docker-compose-mysql.yml up -d
```

### Link repositorio

[https://github.com/sorodriguezz/microservicios-springboot](https://github.com/sorodriguezz/microservicios-springboot)

### Diagrama de componentes

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Enlace de los videos seguidos: [Youtube](https://www.youtube.com/watch?v=-ksmE3KoX9U\&list=PL145AyWAbMDhwUbBL74s1D2ZV9EqBaQ1t)
