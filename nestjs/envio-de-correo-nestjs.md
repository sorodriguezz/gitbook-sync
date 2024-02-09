---
description: >-
  Este documento explica el funcionamiento, instalación y configuración del
  envio de correos con nodemailer.
---

# Envio de correo NestJS

### Precondiciones

Para comenzar necesitamos tener instalado [NodeJS](https://nodejs.org/). Esto se puede usar con una instalación directa o usando NVM.

Una vez instalado NodeJS, usaremos el siguiente comando para instalar NestJS de manera global:

```bash
npm i -g @nestjs/cli
```

### Instalación de dependencias

Para comenzar el proyecto usaremos el siguiente comando para crear un proyecto nuevo de Nest (donde vamos a elegir para este caso <mark style="color:red;">npm</mark>):

```bash
nest new <name_project>
```

Una vez creado el proyecto y descargado las dependencias bases de Nest instalaremos las dependencias necesarias para el envio de correo:

```bash
npm i @nestjs-modules/mailer @nestjs/config hbs nodemailer
```

Y como dependecias de desarrollo instalaremos:

```bash
npm i -D @types/nodemailer
```

### Configuración inicial

Para comenzar, crearemos un archivo en la raíz del proyecto llamado `.env` con el siguiente contenido:

```properties
EMAIL_CREDENTIALS_SERVICE=gmail
EMAIL_CREDENTIALS_USER=correotest@gmail.com
# es la contraseña de una app nueva de gmail (Contraseñas de aplicaciones)
EMAIL_CREDENTIALS_PASSWORD=
EMAIL_DEFAULT_FROM=<no reply> correotest@gmail.com
EMAIL_PATH_TEMPLATES=../views
```

{% hint style="info" %}
Para tomar la contraseña de gmail debemos activar la doble factor para poder pedir una contraseña de aplicaciones
{% endhint %}

A continuación, crearemos un directorio llamado _<mark style="color:purple;">views</mark>_ en la raíz de nuestro proyecto. Y dentro crearemos un archivo llamado `mail.hbs` este archivo tendrá el siguiente contenido para este ejemplo:

```handlebars
<p>¡Hola {{ username }}!,</p>
<p>Este es un correo de prueba</p>
```

Luego en nuestro archivo `app.module.ts` en el arreglo de _<mark style="color:purple;">imports</mark>_ agregaremos el siguiente contenido:

```typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { MailerModule } from '@nestjs-modules/mailer';
import { ConfigModule, ConfigService } from '@nestjs/config';
import { join } from 'path';
import { HandlebarsAdapter } from '@nestjs-modules/mailer/dist/adapters/handlebars.adapter';

@Module({
  imports: [
    ConfigModule.forRoot(),
    MailerModule.forRootAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: async (configService: ConfigService) => ({
        transport: {
          service: configService.get('EMAIL_CREDENTIALS_SERVICE'),
          secure: false,
          auth: {
            user: configService.get('EMAIL_CREDENTIALS_USER'),
            pass: configService.get('EMAIL_CREDENTIALS_PASSWORD'),
          },
        },
        defaults: {
          from: configService.get('EMAIL_DEFAULT_FROM'),
        },
        template: {
          dir: join(__dirname, configService.get('EMAIL_PATH_TEMPLATES')),
          adapter: new HandlebarsAdapter(),
          options: {
            strict: true,
          },
        },
      }),
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

Donde agregamos el _<mark style="color:purple;">ConfigModule</mark>_ para que tome las variables desde el `.env` y _<mark style="color:purple;">MailerModule</mark>_ donde esta la configuración del modulo de envío de correos.

{% hint style="info" %}
En este caso usamos Handlebars, pero tambien se puede usar otro motor de plantillas como lo es <mark style="color:yellow;">**pug**</mark>. Para esto podemos instalarlo con&#x20;

```bash
npm i pug
```

Donde en el adapter de la configuración cambiaria a (donde inlineCssEnabled es para leer el contenido CSS descrito en el `.pug`):&#x20;

```
new PugAdapter({  inlineCssEnabled: true,})
```
{% endhint %}

### Construcción del modulo

Una vez configurado _nodemailer_, procedemos a crear un DTO para mapear el contenido que queremos recibir desde el body de la petición. El DTO para este ejemplo tendrá lo siguiente:

```
// /src/dto/data.dto.ts

export class DataDto {
  email: string;
  username: string;
}
```

Ahora dentro del archivo `app.service.ts` crearemos el siguiente método:

```typescript
import { MailerService } from '@nestjs-modules/mailer';
import { Injectable, InternalServerErrorException } from '@nestjs/common';
import { DataDto } from './dto/data.dto';

@Injectable()
export class AppService {
  constructor(private mailerService: MailerService) {}

  async sendEmail(data: DataDto): Promise<void> {
    try {
      await this.mailerService.sendMail({
        to: data.email,
        subject: 'Email de prueba',
        template: 'mail',
        context: {
          username: data.username,
        },
      });
    } catch (error) {
      throw new InternalServerErrorException(error);
    }
  }
}
```

Y en nuestro `app.controller.ts` creamos el siguiente método para recibir una meticion **POST:**

```typescript
import { Body, Controller, Post } from '@nestjs/common';
import { AppService } from './app.service';
import { DataDto } from './dto/data.dto';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Post('send-email')
  sendEmail(@Body() data: DataDto): void {
    this.appService.sendEmail(data);
  }
}
```

### Uso del servicio con Postman

Para realizar este paso necesitas tener instalado [Postman](https://www.postman.com/downloads/) en tu equipo, donde copiarás el siguiente cURL y lo pegarás en la barra de la URL del Postman:

```bash
curl --location 'localhost:3000/send-email' \
--header 'Content-Type: application/json' \
--data-raw '{
    "email": "<to_email>@gmail.com",
    "username": "<name_user>"
}'
```

Donde debes reemplazar los datos por el correo al que quieres que llegue el mail y un nombre de usuario.

### Link del repositorio

[https://github.com/sorodriguezz/nest-envio-correo](https://github.com/sorodriguezz/nest-envio-correo)
