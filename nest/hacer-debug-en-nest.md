# Hacer debug en Nest

Para hacer debug en VSCode, necesitamos crear una carpeta en la raiz del proyecto que se llame “**.vscode**“. Si posee la extensión de “**material icon**” de vscode se mostrará de la siguiente manera:

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Dentro de esta carpeta crear un archivo llamado “**launch.json**“, el cual debería quedar como:

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Dentro de este archivo deben ir las siguientes líneas de código:

```json
{
  "version": "0.0.1",
  "configurations": [
      {
          "type": "node",
          "request": "launch",
          "name": "Nest",
          "args": [
              "${workspaceFolder}/src/main.ts"
          ],
          "runtimeArgs": [
              "--nolazy",
              "-r",
              "ts-node/register",
              "-r",
              "tsconfig-paths/register"
          ],
          "sourceMaps": true,
          "envFile": "${workspaceFolder}/.env",
          "cwd": "${workspaceRoot}",
          "console": "integratedTerminal"
      }
  ]
}
```

Luego ir ir a la sección **“Ejecución y Depuración“ el cual está en:**

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al abrir la sección mostrará en la parte superior “**Nest**“, como se muestra en la siguiente imagen:

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Para ejecutar presionar el botón play en verde y se abrirá la consola integrada de VSCode con la ejecución del código. Para usar es agregar puntos de interrupción y abrirá una pequeña ventana con las opciones:

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Al usar git en el proyecto asegúrese de que la carpeta **.vscode** este declarado en el **.gitignore** para que no queden archivo de configuración personal en el repositorio.
{% endhint %}
