---
description: >-
  Proyecto que sirve para levantar Apache Kafka con un consumidor y productor en
  Spring Boot
---

# Uso de Kafka con Spring Boot

### Instalación de Kafka

Para comenzar necesitamos descargar Apache Kafka, lo descargaremos desde [aquí](https://kafka.apache.org/quickstart).

<figure><img src=".gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Creamos un directorio en la ruta raíz (en este caso mi directorio será C:\kafka) y descomprimimos nuestro <mark style="color:red;">.tgz</mark> dentro del directorio creado.

{% hint style="warning" %}
Si estas realizando esto en **Windows**, debemos ir al directorio <mark style="color:red;">config</mark> y hacer los siguientes cambios:

* Abrir el archivo `server.properties` donde en la key `log.dirs=/tmp/kafka-logs` debemos cambiarlo por `log.dirs=c:/kafka/kafka-logs`
* Abrir el archivo `zookeeper.properties` y en la key `dataDir=` dejarlo como `dataDir=c:/kafka/zookeeper-data`
{% endhint %}



### Ejecutar Apache Kafka

Para ejecutar Apache Kafka, antes necesitamos iniciar Zookeeper el cual es un servicio necesario para que funcione Kafka.

Para esto, abrimos 2 consolas en modo administrador. Cada una de estas consolas debe estar en el directorio `C:\kafka` . Una vez con las dos consolas en la ruta ejecutamos el siguiente comando en una de ellas:

{% code fullWidth="false" %}
```sh
// Iniciar Zookeeper
.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
```
{% endcode %}

Una vez este servicio se haya levantado, ejecutamos el siguiente comando en la segunda consola:

```sh
// Iniciar Kafka
.\bin\windows\kafka-server-start.bat .\config\server.properties
```



### Crear Topic en Apache Kafka

Para esto abrimos una tercera consola, donde nos volvemos a situar en la ruta de Kafka (C:\kafka) y escribimos el siguiente comando:

```
// Crea un nuevo topic en el servidor de kafka
.\bin\windows\kafka-topics.bat --create --topic {topic-name} --bootstrap-server {host}:9092
```

{% hint style="info" %}
Donde <mark style="color:purple;">topic-name</mark> es el nombre del tópico y el <mark style="color:purple;">host</mark> es donde esta desplegado el servidor de Kafka (para este ejemplo será localhost)
{% endhint %}

Para listar los tópicos usar el comando:

```
// Listar todos los topics que existen dentro del broker
.\bin\windows\kafka-topics.bat --list --bootstrap-server {host}:9092
```

Y para ver el detalle de un tópico, esto para ver replicas, configuraciones, etc. Se usa el comando:

<pre><code>// Decribir los detalles de un topic
<strong>.\bin\windows\kafka-topics.bat --describe --topic {topic-name} --bootstrap-server {host}:9092
</strong></code></pre>



### Iniciar consolas para ver y enviar mensajes en Kafka

Para esto volvemos abrir 2 consolas más donde estén en la ruta de Kafka. En el cual una consola será nuestro <mark style="color:purple;">consumidor</mark> y la otra nuestro <mark style="color:purple;">productor</mark>.

Para nuestra consola de <mark style="color:purple;">consumidor</mark> ejecutamos el siguiente comando para dejar en escucha:

<pre><code>// Inicia una consola para ver mensajes de un topic específico
<strong>.\bin\windows\kafka-console-consumer.bat --topic {nombreTopic} --bootstrap-server {host}:9092
</strong></code></pre>

Y para iniciar una consola <mark style="color:purple;">productor</mark> ejecutamos el siguiente comando:

```
// Inicia una consola para enviar mensajes a un topic específico
.\bin\windows\kafka-console-producer.bat --broker-list {host}:9092 --topic {topic-name}
```



### Creación de proyectos Spring Boot

Crear proyecto SpringBoot 3.2.2 con Maven y JAVA 17, con las siguientes dependencias:

* Spring for Apacke Kafka
* Spring Web
* Spring Dev Tools

Una vez creado este proyecto vamos al `pom.xml` y agregaremos la siguiente linea debajo del `<description>`

```xml
<packaging>pom</packaging>
```

Luego borraremos la carpeta completa de _<mark style="color:purple;">src</mark>_ del proyecto padre recién creado. (Esto es porque tiraremos módulos como microservicios dentro del proyecto principal)

De igual manera quitamos nuestro `<`_`relativePath>`_ de nuestro `pom.xml`

Luego generamos dos proyectos en [https://start.spring.io/](https://start.spring.io/) donde lo haremos con Maven, JAVA 17 y SpringBoot 3.2.2 para este ejemplo, no deben tener dependencias agregadas ya que las heredaran del servicio padre.

Donde un proyecto se llamará _**SpringBootProvider**_ y el otro _**SpringBootConsumer**_

Una vez los generamos, ambos proyectos los cortamos y los pegamos en la raíz del proyecto padre. En donde descomprimiremos ambos en la raíz. (dando en extraer aquí)

### Creación de módulos

Una vez realizado el paso anterior, debemos ir al `pom.xml` del servicio padre (SpringBootForKafka) y debemos copiar lo siguiente:

```xml
<groupId>com.kafka</groupId>
<artifactId>SpringBootForKafka</artifactId>
<version>0.0.1-SNAPSHOT</version>
```

Y dejarlo dentro de nuestro `pom.xml` de nuestros servicios hijos, esto debe estar dentro de los `<parent>` reemplazando lo que ya estaba. Como en el siguiente ejemplo:

```xml
<parent>
	<groupId>com.kafka</groupId>
	<artifactId>SpringBootForKafka</artifactId>
	<version>0.0.1-SNAPSHOT</version>
</parent>
```

Una vez realizado estos pasos bajo el `<packaging>` del `pom.xml` de nuestro servicio padre agregaremos las etiquetas `<modules>` con el siguiente contenido:

```xml
<modules>
        <module>SpringBootProvider</module>
        <module>SpringBootConsumer</module>
</modules>
```

Este nombre se saca desde el `<artifactId>` que le pusimos a nuestros servicios hijo.



### Configuración básica de los módulos

Para darle un logging personalizado a cada microservicio usaremos la pagina [https://devops.datenkollektiv.de/banner.txt/index.html](https://devops.datenkollektiv.de/banner.txt/index.html)

Donde nos servirá para entregarnos el texto para loguear el nombre del proyecto

Una vez generado estos texto, vamos al directorio `resource` de cada microservicio y creamos un archivo llamado `banner.txt` donde pegaremos el contenido.

Un vez creado estos archivo banner, nos quedar darle un nombre y un puerto a nuestros módulos, los cuales en el directorio `resource` y archivo `application.properties` agregaremos lo siguiente para cada archivo del consumidor y productor.

```properties
# Consumer
spring.application.name=Spring-kafka-consumer
server.port=8081

# Provider
spring.application.name=Spring-kafka-provider
server.port=8080
```



### Configuración del módulo productor

Vamos a nuestro archivo application.properties y agregaremos las siguientes lineas al módulo:

```properties
# Configuración kafka
spring.kafka.bootstrap-servers=localhost:9092
```

Posterior a esto, debemos configurar el tópico. Donde crearemos un `package` en `provider` llamado `config`, en el cual crearemos una clase llamada `KafkaTopicConfig.java` en el cual tendrá el siguiente contenido:

```java
// KafkaTopicConfig.java
package com.kafka.provider.config;

import org.apache.kafka.clients.admin.NewTopic;
import org.apache.kafka.common.config.TopicConfig;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.config.TopicBuilder;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class KafkaTopicConfig {


    @Bean
    public NewTopic generateTopic() {
        // map para las configuraciones
        Map<String, String> configurations = new HashMap<>();

        // borra el mensaje (el compact mantiene el ultimo mensaje)
        configurations.put(TopicConfig.CLEANUP_POLICY_CONFIG, TopicConfig.CLEANUP_POLICY_DELETE);

        // cuanto será el tiempo que se van a retener los mensajes dentro del tipico (en ms, por defecto es -1 donde acumula todo)
        configurations.put(TopicConfig.RETENTION_MS_CONFIG, "86400000");

        // tamaño maximo de los segmentos dentro del topico (en bytes, por defecto es 1G)
        configurations.put(TopicConfig.SEGMENT_BYTES_CONFIG, "1073741824");

        // tamaño maximo de cada mensaje que voy a soportar (por defecto va en 1MB)
        configurations.put(TopicConfig.MAX_MESSAGE_BYTES_CONFIG, "1000012");

        // nombre de nuestro topico, particiones de nuestro topico y las replicas que debe tener el topico en caso de caer
        return TopicBuilder.name("messages-topic").partitions(2).replicas(2).configs(configurations).build();
    }

}
```

Ahora debemos configurar el cliente para nuestro provider a Kafka. Para esto creamos dentro del directorio `config` un archivo llamado `KafkaProviderConfig.java` donde tendrá el siguiente contenido:

```java
// KafkaProviderConfig.java
package com.kafka.provider.config;

import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class KafkaProviderConfig {
    // valor del application.properties
    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;

    // se recomienda siempre recibir un objeto en el mensaje
    public Map<String, Object> producerConfig() {
        Map<String, Object> properties = new HashMap<>();

        // le decimos donde esta nuestro servidor de kafka
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);

        // cual es el objeto que se encarga de serializar la llave del mensaje a una secuencia de bytes
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);

        // indicar quien serializará el objeto del mensaje
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);

        return properties;
    }

    @Bean
    public ProducerFactory<String, String> providerFactory() {
        // usar el patron factory para el cliente kafka
        return new DefaultKafkaProducerFactory<>(producerConfig());
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate(ProducerFactory<String, String> producerFactory) {
        // al ser un bean spring se encargará de inyectar el factory, esto se ocupa por la inyeccion de dependencia
        return new KafkaTemplate<>(producerFactory);
    }

}
```



### Prueba del productor

Para probar la configuración realizada, entraremos al archivo `SpringBootProviderApplication.java` donde agreegaremos la siguiente contenido:

```java
// SpringBootProviderApplication.java
package com.kafka.provider;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.kafka.core.KafkaTemplate;

@SpringBootApplication
public class SpringBootProviderApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBootProviderApplication.class, args);
	}

	@Bean
	CommandLineRunner init(KafkaTemplate<String, String> kafkaTemplate) {
		// ejecutar este codigo apenas levante la app
		return args -> {
			// envio de mensaje al topico
			kafkaTemplate.send("messages-topic", "Hola desde springboot");
		};
	}

}

```

{% hint style="info" %}
Para probar esto debemos tener nuestra consola de consumidor arriba para ver los mensajes.
{% endhint %}

{% hint style="warning" %}
Una vez levante la aplicación por primera vez creará el tópico y dará un error, entonces apenas lo cree, debemos bajar la aplicación nuevamente y listar los tópicos en Kafka, una vez listados debemos ver el tópico _**messages-topic.**_ Si esta creado volvemos a levantar la aplicación y veremos el mensaje en la consola de la escucha de tópicos.&#x20;

<img src=".gitbook/assets/image (1) (1) (1) (1) (1).png" alt="" data-size="original">
{% endhint %}



### Configuración del módulo consumidor

Al igual que en el modulo de productor, pondremos en nuestro `application.properties` la siguiente linea de codigo:

```properties
# Configuración kafka
spring.kafka.bootstrap-servers=localhost:9092
```

Luego crearemos nuestro directorio `config` y dentro el archivo llamado `KafkaConsumerConfig.java` en el cual tendrá el siguiente contenido:

```java
// KafkaConsumerConfig.java
package com.kafka.consumer.config;

import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.config.KafkaListenerContainerFactory;
import org.springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
import org.springframework.kafka.listener.ConcurrentMessageListenerContainer;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class KafkaConsumerConfig {

    // valor del application.properties
    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;

    // se recomienda siempre recibir un objeto en el mensaje
    public Map<String, Object> consumerConfig() {
        Map<String, Object> properties = new HashMap<>();

        // le decimos donde esta nuestro servidor de kafka
        properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);

        // clase que tiene que deserializar la key
        properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringSerializer.class);

        // indicar quien deserializará el objeto del mensaje
        properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringSerializer.class);

        return properties;
    }

    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        // definir consumer factory con la configuracion
        return new DefaultKafkaConsumerFactory<>(consumerConfig());
    }

    // metodo para leer los mensajes
    @Bean
    public KafkaListenerContainerFactory<ConcurrentMessageListenerContainer<String, String>> consumer() {
        ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();

        factory.setConsumerFactory(consumerFactory());

        return factory;
    }
}

```

Una vez creada la configuración de Kafka consumidor, debemos crear un directorio `listeners`, donde estarán la configuración de los consumidores de escucha, dentro de este directorio crearemos la clase `KafkaConsumerListener.java` el cual tendrá el siguiente contenido:

```java
// KafkaConsumerListener.java
package com.kafka.consumer.listeners;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.KafkaListener;

@Configuration
public class KafkaConsumerListener {
    private Logger LOGGER = LoggerFactory.getLogger(KafkaConsumerListener.class);

    // etiqueta para escuchar mensajes que se envian, el groupId es para formar grupos de consumidores
    @KafkaListener(topics = {"messages-topic"}, groupId = "mi-id-grupo")
    public void listener(String message) {
        LOGGER.info("Mensaje recibido, el mensaje es: " + message);
    }
}

```



### Prueba del consumidor

Una vez realizada la configuración del consumidor, levantamos el módulo de <mark style="color:purple;">consumer</mark> y posteriormente levantamos el módulo de <mark style="color:purple;">producer</mark>. Esto hará que nuestro consumidor escuche el mensaje que enviará el productor apenas levante el servicio:

<figure><img src=".gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

### Link repositorio

[https://github.com/sorodriguezz/springboot-kafka-example](https://github.com/sorodriguezz/springboot-kafka-example)
