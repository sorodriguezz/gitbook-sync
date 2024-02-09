---
description: >-
  Proyecto que sirve para levantar Apache Kafka con un consumidor y productor en
  Spring Boot
---

# Uso de Kafka con Spring Boot

### Instalación de Kafka

Para comenzar necesitamos descargar Apache Kafka, lo descargaremos desde [aquí](https://kafka.apache.org/quickstart).

<figure><img src=".gitbook/assets/image.png" alt="" width="375"><figcaption></figcaption></figure>

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

```
// Decribir los detalles de un topic
.\bin\windows\kafka-topics.bat --describe --topic {topic-name} --bootstrap-server {host}:9092
```



### Iniciar consolas para ver y enviar mensajes en Kafka

Para esto volvemos abrir 2 consolas más donde estén en la ruta de Kafka. En el cual una consola será nuestro <mark style="color:purple;">consumidor</mark> y la otra nuestro <mark style="color:purple;">productor</mark>.

Para nuestra consola de <mark style="color:purple;">consumidor</mark> ejecutamos el siguiente comando para dejar en escucha:

```
// Inicia una consola para ver mensajes de un topic específico
.\bin\windows\kafka-console-consumer.bat --topic {nombreTopic} --bootstrap-server {host}:9092
```

Y para iniciar una consola <mark style="color:purple;">productor</mark> ejecutamos el siguiente comando:

```
// Inicia una consola para enviar mensajes a un topic específico
.\bin\windows\kafka-console-producer.bat --broker-list {host}:9092 --topic {topic-name}
```



### Creación de proyecto Spring Boot

Crear proyecto Spring, con las siguientes dependencias:

* Spring for Apacke Kafka
* Spring Web
* Spring Dev Tools







