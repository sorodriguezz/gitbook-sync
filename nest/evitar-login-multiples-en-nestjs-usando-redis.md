---
description: >-
  Mejora de la seguridad y la gestión de sesiones al evitar inicios de sesión
  múltiples en NestJS mediante Redis.
---

# Evitar login múltiples en NestJS usando Redis

Evitar múltiples inicios de sesión para un mismo usuario puede ser esencial para la seguridad y para mantener una experiencia de usuario fluida, especialmente en aplicaciones donde la gestión de sesiones y la rendición de cuentas de los usuarios son cruciales. En este documento, exploraremos cómo evitar múltiples inicios de sesión en una aplicación NestJS invalidando sesiones anteriores al iniciar sesión.

## ¿Por qué evitar inicios de sesión múltiples? <a href="#heading-why-prevent-multiple-logins" id="heading-why-prevent-multiple-logins"></a>

Permitir múltiples inicios de sesión puede generar varios problemas:

* **Riesgos de seguridad** : un usuario puede compartir sus credenciales de inicio de sesión con otros o que se acceda a su cuenta desde múltiples ubicaciones sin su conocimiento.
* **Secuestro de sesión** : si se roba la sesión de un usuario, los atacantes pueden mantener el acceso sin que el usuario lo sepa.
* **Experiencia del usuario** : en algunas aplicaciones (por ejemplo, cuentas bancarias o sensibles), es beneficioso limitar el acceso a una sesión a la vez.

Al implementar un mecanismo para evitar múltiples inicios de sesión simultáneos, podemos mejorar la seguridad y la usabilidad de nuestras aplicaciones.

## Pasos para implementar la prevención de inicios de sesión múltiples en NestJS <a href="#heading-steps-to-implement-multiple-login-prevention-in-nestjs" id="heading-steps-to-implement-multiple-login-prevention-in-nestjs"></a>

Aquí hay una guía paso a paso para implementar una solución que evita inicios de sesión múltiples utilizando NestJS, JWT (JSON Web Token) y una estrategia de invalidación de sesión.

## Paso 1: Inicio y configuración proyecto NestJS

Para iniciar proyecto usar:

<pre class="language-bash"><code class="lang-bash"><strong>nest new nest-single-session
</strong></code></pre>

Entrar al directorio del proyecto:

```bash
cd nest-single-session
```

Instalar dependencias como:

```bash
npm i @nestjs/jwt @nestjs/passport passport passport-jwt bcrypt \
      class-validator class-transformer @nestjs/config uuid ioredis
```

```bash
npm i --save-dev @types/bcrypt 
```

Ejecutar el siguiente comando para levantar Redis en Docker:

```bash
docker run -d --name redis -p 6379:6379 redis:7-alpine
```

Crear un archivo `.env` con las siguientes configuraciones:

```
# JWT
JWT_SECRET=CREA_CREDENCIAL_SUPER_SECRETA
JWT_TTL=900s # 15 minutos (access token corto es buena práctica)

# Redis
REDIS_HOST=127.0.0.1
REDIS_PORT=6379
REDIS_PASSWORD=

# Demo user (para pruebas locales)
DEMO_EMAIL=user@example.com
# Hash de "Passw0rd!" (généralo con el snippet de abajo)
DEMO_PASSWORD=$2b$10$6qYvT8q2dN2qBKt2i0...completa_tu_hash...
```

Usar el siguiente comando para crear contraseña encriptada. Reemplazar "Passw0rd!" por la que desees encriptar:

```bash
node -e "const b=require('bcrypt');(async()=>{console.log(await b.hash('Passw0rd!',10))})()"
```

## Paso 2: Estructura de directorios: <a href="#heading-step-1-store-active-user-sessions" id="heading-step-1-store-active-user-sessions"></a>

Los directorios deberían tener la siguiente estructura para el código de los siguientes pasos.

```
src/
 ├─ app.module.ts
 ├─ main.ts
 ├─ redis/
 │   └─ redis.module.ts
 ├─ users/
 │   ├─ users.module.ts
 │   ├─ users.service.ts
 │   └─ dto/login.dto.ts
 └─ auth/
     ├─ auth.module.ts
     ├─ auth.service.ts
     ├─ auth.controller.ts
     ├─ session.service.ts
     └─ jwt.strategy.ts
```

## Paso 3: Módulo Redis <a href="#heading-step-1-store-active-user-sessions" id="heading-step-1-store-active-user-sessions"></a>

```typescript
// src/redis/redis.module.ts
import { Module, Global } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import Redis from 'ioredis';

export const REDIS_CLIENT = 'REDIS_CLIENT';

@Global()
@Module({
  providers: [
    {
      provide: REDIS_CLIENT,
      inject: [ConfigService],
      useFactory: (cfg: ConfigService) =>
        new Redis({
          host: cfg.get<string>('REDIS_HOST'),
          port: Number(cfg.get<number>('REDIS_PORT')),
          password: cfg.get<string>('REDIS_PASSWORD') || undefined,
          // Puedes configurar retryStrategy, tls, etc. según tu entorno
        }),
    },
  ],
  exports: [REDIS_CLIENT],
})
export class RedisModule {}
```

ioredis crea la conexión al instanciar new Redis(...), y soporta opciones avanzadas (cluster, retry, tls).&#x20;

## Paso 4: Configurar AppModule y Bootstrap

```typescript
// src/app.module.ts
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';
import { RedisModule } from './redis/redis.module';
import { UsersModule } from './users/users.module';
import { AuthModule } from './auth/auth.module';

@Module({
  imports: [
    ConfigModule.forRoot({ isGlobal: true }),
    RedisModule,
    UsersModule,
    AuthModule,
  ],
})
export class AppModule {}
```

```typescript
// src/main.ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ValidationPipe } from '@nestjs/common';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.useGlobalPipes(
    new ValidationPipe({ whitelist: true, forbidNonWhitelisted: true }),
  );

  await app.listen(process.env.PORT ?? 3000);
}
void bootstrap();
```

## Paso 5: Crear DTO para Login

```typescript
// src/users/dto/login.dto.ts
import { IsEmail, IsString, MinLength } from 'class-validator';

export class LoginDto {
  @IsEmail() email: string;
  @IsString() @MinLength(6) password: string;
}
```

## Paso 6: Crear UserService

```typescript
// src/users/users.service.ts
import { Injectable } from '@nestjs/common';
import * as bcrypt from 'bcrypt';
import { ConfigService } from '@nestjs/config';

@Injectable()
export class UsersService {
  constructor(private cfg: ConfigService) {}

  async findByEmail(email: string) {
    const demoEmail = this.cfg.get<string>('DEMO_EMAIL');
    if (email !== demoEmail) return null;
    return {
      id: 'u_1',
      email: demoEmail,
      passwordHash: this.cfg.get<string>('DEMO_PASSWORD'),
    };
  }

  async validateUser(email: string, plain: string) {
    const user = await this.findByEmail(email);
    if (!user) return null;
    const ok = await bcrypt.compare(plain, user.passwordHash);
    return ok ? { id: user.id, email: user.email } : null;
  }
}
```

## Paso 7: Crear UserModule

```typescript
// src/users/users.module.ts
import { Module } from '@nestjs/common';
import { UsersService } from './users.service';

@Module({
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}
```

## Paso 8: Sesiones en Redis (Una sesión por usuario)

```typescript
// src/auth/session.service.ts
import { Injectable, Inject } from '@nestjs/common';
import { REDIS_CLIENT } from '../redis/redis.module';
import Redis from 'ioredis';

@Injectable()
export class SessionService {
  constructor(@Inject(REDIS_CLIENT) private readonly redis: Redis) {}

  private key(userId: string) {
    return `user:${userId}:sid`;
  }

  async setActiveSession(userId: string, sid: string, ttlSeconds: number) {
    await this.redis.set(this.key(userId), sid, 'EX', ttlSeconds);
  }

  async getActiveSession(userId: string) {
    return this.redis.get(this.key(userId));
  }

  async clearSession(userId: string) {
    await this.redis.del(this.key(userId));
  }
}
```

## Paso 9: Creación de AuthModule, Strategy y AuthController

```typescript
// src/auth/auth.module.ts
import { Module } from '@nestjs/common';
import { JwtModule } from '@nestjs/jwt';
import { PassportModule } from '@nestjs/passport';
import { ConfigService } from '@nestjs/config';
import { UsersModule } from '../users/users.module';
import { AuthService } from './auth.service';
import { AuthController } from './auth.controller';
import { JwtStrategy } from './jwt.strategy';
import { SessionService } from './session.service';

@Module({
  imports: [
    UsersModule,
    PassportModule,
    JwtModule.registerAsync({
      inject: [ConfigService],
      useFactory: (cfg: ConfigService) => ({
        secret: cfg.get<string>('JWT_SECRET'),
        signOptions: { expiresIn: cfg.get<string>('JWT_TTL') },
      }),
    }),
  ],
  controllers: [AuthController],
  providers: [AuthService, JwtStrategy, SessionService],
})
export class AuthModule {}
```

```typescript
// src/auth/auth.service.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { UsersService } from '../users/users.service';
import { v4 as uuid } from 'uuid';
import { SessionService } from './session.service';
import { ConfigService } from '@nestjs/config';

@Injectable()
export class AuthService {
  constructor(
    private users: UsersService,
    private jwt: JwtService,
    private sessions: SessionService,
    private cfg: ConfigService,
  ) {}

  async login(email: string, password: string) {
    const user = await this.users.validateUser(email, password);
    if (!user) throw new UnauthorizedException('Credenciales inválidas');

    const sid = uuid();
    const ttl = this.parseTtl(this.cfg.get<string>('JWT_TTL') || '900s');

    const payload = { sub: user.id, email: user.email, sid };
    const accessToken = await this.jwt.signAsync(payload);

    await this.sessions.setActiveSession(user.id, sid, ttl); // invalida tokens previos

    return { accessToken };
  }

  async logout(userId: string) {
    await this.sessions.clearSession(userId);
    return { ok: true };
  }

  private parseTtl(ttl: string) {
    const m = ttl.match(/^(\d+)([smhd])$/);
    if (!m) return 900;
    const n = +m[1];
    return { s: n, m: n*60, h: n*3600, d: n*86400 }[m[2] as 's'|'m'|'h'|'d'];
  }
}
```

```typescript
// src/auth/jwt.strategy.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';
import { ConfigService } from '@nestjs/config';
import { SessionService } from './session.service';

type JwtPayload = { sub: string; email: string; sid: string };

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(cfg: ConfigService, private sessions: SessionService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: cfg.get<string>('JWT_SECRET'),
    });
  }

  async validate(payload: JwtPayload) {
    const activeSid = await this.sessions.getActiveSession(payload.sub);
    if (!activeSid || activeSid !== payload.sid) {
      throw new UnauthorizedException('Sesión invalidada. Inicia sesión de nuevo.');
    }
    return { userId: payload.sub, email: payload.email };
  }
}
```

```typescript
// src/auth/auth.controller.ts
import { Body, Controller, Get, HttpCode, Post, UseGuards, Req } from '@nestjs/common';
import { AuthService } from './auth.service';
import { LoginDto } from '../users/dto/login.dto';
import { AuthGuard } from '@nestjs/passport';

@Controller('auth')
export class AuthController {
  constructor(private auth: AuthService) {}

  @Post('login')
  @HttpCode(200)
  async login(@Body() dto: LoginDto) {
    return this.auth.login(dto.email, dto.password);
  }

  @UseGuards(AuthGuard('jwt'))
  @Post('logout')
  @HttpCode(200)
  async logout(@Req() req: any) {
    return this.auth.logout(req.user.userId);
  }

  @UseGuards(AuthGuard('jwt'))
  @Get('me')
  me(@Req() req: any) {
    return req.user;
  }
}
```

Esta estructura sigue la guía oficial de autenticación + JWT + Passport de Nest; sólo sustituyes el wrapper de Redis por ioredis directo.&#x20;

## Paso 10: Probar que funcione

### 1. Arranca Redis (si no lo hiciste):

```bash
docker start redis
```

### 2. Levanta Nest:

```bash
npm run start:dev
```

### 3. Login (crea sid y emite JWT):

```
curl -s -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"Passw0rd!"}'
# => {"accessToken":"<JWT1>"}
```

### 4. Ruta protegida con JWT1:

Debe reemplazar "$TOKEN1" con el token generado en el endpoint anterior

```
curl -i http://localhost:3000/auth/me -H "Authorization: Bearer $TOKEN1"
# => 200 OK (retorna { userId, email })
```

### 5. Segundo login (simula otro dispositivo):

Esto generará un segundo token

```
curl -s -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"Passw0rd!"}'
# => {"accessToken":"<JWT1>"}
```

### 6. El token viejo (JWT1) ya NO sirve:

```
curl -i http://localhost:3000/auth/me -H "Authorization: Bearer $TOKEN1"
# => 401 Unauthorized (Sesión invalidada)
```

### 7. El token nuevo (JWT2) sí sirve:

```
curl -i http://localhost:3000/auth/me -H "Authorization: Bearer $TOKEN2"
# => 200 OK
```

### 8. Logout invalida la sesión actual:

```
curl -i -X POST http://localhost:3000/auth/logout -H "Authorization: Bearer $TOKEN2"
curl -i http://localhost:3000/auth/me -H "Authorization: Bearer $TOKEN2"
# => 401 Unauthorized
```

## Notas de producción y mejoras

* Tokens cortos + Refresh Tokens: Mantén access breve (5–15 min) y usa refresh con rotación/blacklist para sesiones largas. La guía de JWT en Nest y Passport sigue siendo la referencia base.&#x20;
* Caché oficial (Nest 11): si luego usas CacheModule, en v11 se apoya en Keyv; si almacenas en Redis, usa @keyv/redis (distinto del cliente ioredis que usamos aquí para sesiones).
* Seguridad: fuerza alg (HS256/RS256), HTTPS, rate limiting, logging de login/logout, etc. (lineamientos generales de JWT/Passport).
