---
description: >-
  Esta guia explicará como recibir correos desde un formulario "Contact" usando
  EmailJS en Angular 19.
---

# Recibir correos con Angular

## Registro y configuración

Para comenzar necesitamos ir al sitio de [EmailJS](https://www.emailjs.com/), donde nos registraremos. Solo nos pedira un correo, contraseña y nombre de usuario. Luego una verificación del correo.

Una vez registrados y verificados, nos loggearemos al sitio. Donde tendremos un panel de control.

Para hacer un uso correcto, necesitamos crear un Service ID, Template ID y tener un API Key Publico.

### Crear Service ID

Para esto vamos al Menú e ingresamos a "Email Services". Damos clic en el botón "Add New Service" y elegimos el proveedor de correo que usamos, en mi caso mi correo es @gmail.com, entonces usaré Gmail.

Como "Nmae" puede ser el nombre que quieras, luego copiamos el Service ID y damos en "Connect Account", esto nos pedirá loggearnos a nuestro correo y dar los permisos correspondientes al servicio.

Una vez configurado el correo, daremos clic en "Create Service". Donde realizará la creación del servicio como tal.

### Crear Template ID

Para crear nuestra plantilla, necesitamos ir a Menú y hacer click en "Email Templates". Donde daremos clic en "Create New Template".

Esto nos dará las opciones del contenido del correo. Podemos poner \{{message\}} para crear el contenido del correo, \{{from\_name\}} para poner el nombre de quien nos está escribiendo. \{{to\_reply\}} para obtener el correo de quien nos escribe, etc.&#x20;

Además podemos editar para formato Mobile y Desktop. Estilizarlo con CSS y agregar más configuraciones.

Podemos dar clic en el botón "Test It" para que nos llegue un correo de prueba de como se verian los correos que envien los usuarios del  sitio.&#x20;

Ahora vamos a Settings dentro del mismo template y copiamos el Template ID.

### Obtener API Key Publica

Para este paso solo necesitamos ir a "Account" en el Menú lateral y copiar la clave que dice "Public Key" en la sección de API Keys.

## Usar EmailJS en Angular

Una vez ya obtenidos el Service ID, Template ID y API Public Key. Comenzamos a configurar e instlaar la libreria en Angular.

En el proyecto Angular instalamos la libreria con:

```bash
npm install emailjs-com --save
```

Donde se registrará en nuestro package.json y descargará las dependencias.

Una vez instalado necesitamos importar la libreria en nuestro componente .ts y usar send para enviar el correo con los parametros requeridos, de la siguiente manera:

<pre class="language-typescript"><code class="lang-typescript">import { Component } from '@angular/core';
import emailjs from 'emailjs-com';

@Component({
  selector: 'app-contact',
  templateUrl: './contact.component.html',
  styleUrls: ['./contact.component.scss']
})
export class ContactComponent {
  sendEmail(): void {
    const templateParams = {
      from_name: 'nombre-usuario-que-envia',
      to_name: 'nombre-usuario-recibe',
      message: 'mensaje-a-enviar-usuario-receptor',
      reply_to: 'correo-usuario-que-envia'
    };
    
<strong>    try {
</strong>      const respEmail = await emailjs.send(
        'YOUR_SERVICE_ID',      // Reemplaza con tu Service ID
        'YOUR_TEMPLATE_ID',     // Reemplaza con tu Template ID
        templateParams,
        'YOUR_USER_ID'          // Reemplaza con Public Key
      );
      console.log(respEmail);
    } catch (error) {
      console.error(error);
    }
  }
}
</code></pre>

Ahora solo falta usar formulario de template o reactivo para pasar los datos del templateParams, setear los ID de manera segura como en metodos readonly y crear el formulario en el template para que el usuario pueda interactuar con el.

Una vez realizado el send, recibiremos nuestro correo en nuestra bandeja. Tener en cuenta que en la capa gratuita el limite son 200 correos al mes que podemos recibir, pero al termina el mes se renuevan estos 200 correos.&#x20;
