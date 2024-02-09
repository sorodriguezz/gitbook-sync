# Usar librería .tgz en local

Para usar una librería de NestJS en local. Debemos usar el comando NPM de la siguiente manera:

{% hint style="warning" %}
Si la librería que usemos ya tiene creado los archivos y directorios `package-lock.json`, `dist` y `node_modules`. Debemos borrarlo, esto para evitar algún caching de las librerías.
{% endhint %}

Para comenzar hacer un `npm install` (igual se puede cambiar el `install` por `i`), esto generará nuevamente los 3 archivos y directorios pero con los archivos que modificamos.

Luego para crear el archivo `.tgz` que contiene el código transpilado de TypeScript a JavaScript. Vamos al directorio `dist` y dentro del ditectorio usamos el comando:

```
npm pack
```

Este comando creará el comprimido de la librería (este comprimido debería crearse dentro del mismo `dist`

Una vez tengamos el archivo `.tgz`, dejar en una la ruta que queramos.

Luego, para instalar la libreria dentro de un proyecto, se hace el comando:

```
npm i xxxx
```

Donde xxxx es la ruta completa donde se encuentra el archivo `.tgz`

Una vez el comando termine en el `package.json` quedará como “`nombre-libreria": "file: ../../../.tgz`"



{% hint style="info" %}
Si él `.tgz` se crea en la raíz del proyecto es porque **NO** realizó el comando `cd dist`
{% endhint %}
