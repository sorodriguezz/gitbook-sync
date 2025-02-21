---
description: >-
  Esta guia muestra como desplegar un proyecto en Angular 19 en GitHub Page.
  Este sitio puede tener conexiones a APIs externas o servicios web externos.
---

# Deplegar Angular en GitHub Page

## Instalación y creación proyecto

Para comenzar necesitamos crear el proyecto Angular 19, para esto usamos el comando:

```bash
npm i -g @angular/cli@19
```

> **NOTA**: Para este caso será Angular 19, pero sirve para cualquier versión. Solo que en las nuevas versiones crea un directorio llamado Browser dentro del codigo transpilado (dist).

Una vez instalado, comprobamos que esté instalado, para esto usaremos el comando:

```bash
ng version
```

El cual deberia mostrar algo como esto:

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Para crear el proyecto usaremos el compando:

```bash
ng new name_proyect
```

Donde "name\_proyecto" lo reemplazamos por el nombre del proyecto. Luego, nos pedirá elegir las hojas de estilo, para esta parte es la que desees. En este caso, usaré Sass.

Como otra opción, nos preguntará si queremos SSR, el cual para este caso pondré no. Y comenzará a descargar los paquetes necesarios y crear los archivos principales del proyecto.

Ahora podemos abrir el proyecto y configurar como queramos. En este caso usaré bootstrap para crear una interfaz simple.

## Despliegue del proyecto

Para el despliegue del proyecto hay multiples formas, pero solo realizaré dos. El despliegue por rama y el despliegue por directorio `/docs`.&#x20;

### Despliegue por directorio docs

Para esto usaremos el comando:

Para Git Bash:

```bash
MSYS_NO_PATHCONV=1 ng build --configuration production --base-href "/nombre-repositorio/"
```

Para CMD o PowerShell:

```bash
ng build --configuration production --base-href "/nombre-repositorio/"
```

Esto nos generará un directorio /dist en la raiz de nuestro proyecto, con nuestro index.html apuntando a la ruta que expone GitHub Pages.&#x20;

Como mencioné, en las nuevas versiones crea todo este codigo JS en un directorio /browser. En las versiones 9 y 12 por ejemplo lo crea directorio sobre el directorio /dist. (Siempre dentro de un directorio con el mismo nombre del proyecto)

Para continuar, creamos un directorio llamado /docs dentro de nuestro proyecto y movemos todos los archivos de nuestro /browser a nuestro /docs. O usar el comando que sigue para realizar todos los pasos a la vez:

```bash
mkdir docs && mv dist/nombre_proyecto/browser/* docs && rm -rf dist
```

&#x20;Con este comando creamos el directorio docs, movemos todos los archivos de /browser dentro de docs y final mente eliminamos el directorio /dist. (Esto ultimo en caso de no querer este directorio)

Una vez realizado, subimos nuestro proyecto a GitHub donde deberia estar nuestro directorio /docs.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Ahora vamos a Settings de nuestro proyecto de GitHub y nos vamos a Pages.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

En Pages seleccionamos nuestra rama, que en este caso el proyecto esta en **master**  y como folder seleccionamos /docs.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Luego damos en guardar. Y luego de un rato al terminan el pipeline de GitHub Actions se visualizará nuestro sitio:

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Para ver el nombre de nuestra ruta, en el mismo apartado de GitHub Pages aparece la ruta y un botón "Visit site".

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>





