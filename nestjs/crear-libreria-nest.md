---
description: Este repositorio es un ejemplo para crear una libreria en NestJS compartida.
---

# Crear librería Nest

<figure><img src="https://nestjs.com/img/logo-small.svg" alt="" width="375"><figcaption></figcaption></figure>

## Tecnologías

* Node
* NestJS 10.3.0
* Express

### Contenido

```
└── 📁src
    └── core.module.ts
    └── index.ts
    └── 📁logging
        └── logging.interceptor.ts
```

### Modo de uso

1. Usar el comando `npm run build` para crear el transpilado de la libreria.
2. Hacer `cd dist` para ingresar al directorio del compilado.
3. Usar `npm pack` para crear `.tgz`.
4. Para hacer uso en un servicio NestJS se debe tomar la ruta del `.tgs` y hacer un `npm install RUTA_TGZ`.

### Link del repositorio

[https://github.com/sorodriguezz/nest-lib-example](https://github.com/sorodriguezz/nest-lib-example)
