---
description: >-
  Administración fluida de permisos y autorizaciones en su aplicación NestJS
  mediante CASL
---

# CASL con NestJS y JWT

En las aplicaciones web modernas , la gestión de permisos y autorizaciones es crucial. CASL (Biblioteca de Seguridad de Acceso al Código) ofrece una forma eficaz de gestionar estos permisos de forma declarativa. Esta guía le guiará en la integración de CASL con una aplicación NestJS, garantizando la protección eficiente de sus recursos.

## Introducción a CASL <a href="#heading-introduction-to-casl" id="heading-introduction-to-casl"></a>

CASL es una biblioteca altamente flexible y personalizable para gestionar la autorización en aplicaciones JavaScript. Al definir permisos de forma declarativa, los desarrolladores pueden controlar fácilmente el acceso a diversas partes de sus aplicaciones.

### **Configuración de la aplicación NestJS**

Para comenzar, se configurará una nueva aplicación NestJS . Esto se puede hacer usando la CLI de NestJS:

```bash
nest new nest-casl-app
```

### **Instalación de dependencias**

A continuación, se instalarán las dependencias necesarias:

```bash
npm i @casl/ability @nestjs/typeorm typeorm sqlite3 class-transformer \
    class-validator @nestjs/passport passport passport-jwt @nestjs/jwt \
    bcryptjs @nestjs/config
```

```bash
npm i -D @types/bcryptjs
```

## Estructura del proyecto

```
src
 ┣ auth
 ┃ ┣ dto
 ┃ ┃ ┗ login.dto.ts
 ┃ ┣ auth.controller.ts
 ┃ ┣ auth.middleware.ts
 ┃ ┣ auth.module.ts
 ┃ ┣ auth.service.ts
 ┃ ┣ jwt-auth.guard.ts
 ┃ ┗ jwt.strategy.ts
 ┣ casl
 ┃ ┗ casl-ability.factory.ts
 ┣ decorators
 ┃ ┗ check-policies.decorator.ts
 ┣ guards
 ┃ ┗ permissions.guard.ts
 ┣ rbac
 ┃ ┣ permission.entity.ts
 ┃ ┗ role.entity.ts
 ┣ seed
 ┃ ┗ seed.service.ts
 ┣ users
 ┃ ┣ dto
 ┃ ┃ ┗ create-user.dto.ts
 ┃ ┣ user.entity.ts
 ┃ ┣ users.controller.ts
 ┃ ┣ users.module.ts
 ┃ ┗ users.service.ts
 ┣ app.module.ts
 ┗ main.ts
```

## Paso 1: Modificar main.ts y crear .env

Para configurar los validation agregar la siguientes lineas al main.ts

```typescript
// src/main.ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ValidationPipe } from '@nestjs/common';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.useGlobalPipes(new ValidationPipe({ transform: true, whitelist: true }));

  await app.listen(process.env.PORT ?? 3000);
}
void bootstrap();
```

Crear .env con variables de entorno para datos sensibles y configuración del proyecto:

```
# .env
PORT=3000
JWT_SECRET=supersecretkey
DB_PATH=data/nest-casl.sqlite
```

## Paso 2: Crear entidades

Crear las siguientes entidades de Usuario, Rol y Permisos:

```typescript
// src/rbac/permission.entity.ts
import { Entity, PrimaryGeneratedColumn, Column, ManyToOne } from 'typeorm';
import { RoleEntity } from './role.entity';

@Entity('permissions')
export class PermissionEntity {
  @PrimaryGeneratedColumn()
  id!: number;

  @Column()
  action!: string; // 'manage' | 'read' | 'create' | 'update' | 'delete'

  @Column()
  subject!: string; // 'all' | 'User' | 'Post' | etc.

  @Column({ type: 'text', nullable: true })
  conditions?: string | null; // JSON string, e.g. {"id":"${user.id}"}

  @Column({ default: false })
  inverted!: boolean; // true => cannot

  @Column({ type: 'text', nullable: true })
  reason?: string | null;

  @ManyToOne(() => RoleEntity, (role) => role.permissions, {
    onDelete: 'CASCADE',
  })
  role!: RoleEntity;
}
```

```typescript
// src/rbac/role.entity.ts
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  ManyToMany,
  OneToMany,
} from 'typeorm';
import { PermissionEntity } from './permission.entity';
import { UserEntity } from 'src/users/user.entity';

@Entity('roles')
export class RoleEntity {
  @PrimaryGeneratedColumn()
  id!: number;

  @Column({ unique: true })
  name!: string; // 'admin', 'user', etc.

  @ManyToMany(() => UserEntity, (user) => user.roles)
  users!: UserEntity[];

  @OneToMany(() => PermissionEntity, (perm) => perm.role, {
    cascade: true,
    eager: true,
  })
  permissions!: PermissionEntity[];
}
```

```typescript
// src/users/user.entity.ts
import { RoleEntity } from 'src/rbac/role.entity';
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  ManyToMany,
  JoinTable,
} from 'typeorm';

@Entity('users')
export class UserEntity {
  @PrimaryGeneratedColumn()
  id!: number;

  @Column({ length: 100 })
  name!: string;

  @Column({ unique: true })
  email!: string;

  @Column()
  passwordHash!: string;

  @Column({ default: false })
  isAdmin!: boolean;

  @ManyToMany(() => RoleEntity, (role) => role.users, { eager: true })
  @JoinTable({ name: 'user_roles' })
  roles!: RoleEntity[];
}
```

## Paso 3: Crear y configurar servicio CASL

El siguiente código sirve para crear y configurar CASL para gestion de permisos:

```typescript
// src/casl/casl-ability.factory.ts
import {
  AbilityBuilder,
  ExtractSubjectType,
  InferSubjects,
  MongoAbility,
  createMongoAbility,
} from '@casl/ability';
import { Injectable } from '@nestjs/common';

export enum Actions {
  Manage = 'manage',
  Create = 'create',
  Read = 'read',
  Update = 'update',
  Delete = 'delete',
}

// Entidad de dominio "lógica" (no TypeORM)
export class User {
  id!: number;
  isAdmin!: boolean;
  roles!: string[];
}

export type Subjects = InferSubjects<typeof User> | 'all';
export type AppAbility = MongoAbility<[Actions, Subjects]>;

type BaseRule = {
  action: string;
  subject: string;
  conditions?: any;
  inverted?: boolean;
  reason: string;
};

@Injectable()
export class CaslAbilityFactory {
  createForUser(user: User, baseRules: BaseRule[] = []): AppAbility {
    const { can, cannot, build } = new AbilityBuilder<AppAbility>(
      createMongoAbility,
    );

    // 1) Reglas que vienen desde BD (roles → permisos)
    for (const r of baseRules) {
      const fn = r.inverted ? cannot : can;
      fn(r.action as any, r.subject as any, r.conditions ?? undefined)?.because(
        r.reason,
      );
    }

    // 2) Reglas por flags
    if (user.isAdmin) {
      can(Actions.Manage, 'all');
    } else {
      can(Actions.Read, 'all');
      cannot(Actions.Delete, User).because(
        'Solo administradores pueden eliminar usuarios',
      );
    }

    return build({
      detectSubjectType: (subj) =>
        subj.constructor as ExtractSubjectType<Subjects>,
    });
  }
}
```

## Paso 4: Crear @CheckPolicies

Crear decorador para setear metadatos para manejo de politicas y roles:

```typescript
// src/decorators/check-policies.decorator.ts
import { SetMetadata } from '@nestjs/common';
import { AppAbility } from '../casl/casl-ability.factory';

export const CHECK_POLICIES_KEY = 'checkPolicies';
export type PolicyHandler = (ability: AppAbility) => boolean;

export const CheckPolicies = (...handlers: PolicyHandler[]) =>
  SetMetadata(CHECK_POLICIES_KEY, handlers);
```

## Paso 5: Guard para manejo de permisos

Crear guard para manejo de permisos al realizar peticiones a las rutas:

```typescript
// src/guards/permissions.guard.ts
import {
  CanActivate,
  ExecutionContext,
  ForbiddenException,
  Injectable,
  UnauthorizedException,
} from '@nestjs/common';
import { Reflector } from '@nestjs/core';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import {
  CHECK_POLICIES_KEY,
  PolicyHandler,
} from '../decorators/check-policies.decorator';
import {
  CaslAbilityFactory,
  AppAbility,
  User,
} from '../casl/casl-ability.factory';
import { UserEntity } from '../users/user.entity';
import { PermissionEntity } from '../rbac/permission.entity';

@Injectable()
export class PermissionsGuard implements CanActivate {
  constructor(
    private readonly reflector: Reflector,
    private readonly caslFactory: CaslAbilityFactory,
    @InjectRepository(UserEntity)
    private readonly users: Repository<UserEntity>,
    @InjectRepository(PermissionEntity)
    private readonly perms: Repository<PermissionEntity>,
  ) {}

  async canActivate(ctx: ExecutionContext): Promise<boolean> {
    const handlers = this.reflector.get<PolicyHandler[]>(
      CHECK_POLICIES_KEY,
      ctx.getHandler(),
    );
    if (!handlers?.length) return true;

    const req = ctx.switchToHttp().getRequest();
    const jwtUser = req?.user as { id: number; email: string } | undefined;
    if (!jwtUser) throw new UnauthorizedException('No autenticado');

    // 1) Rehidratar user completo con roles
    const u = await this.users.findOne({ where: { id: jwtUser.id } });
    if (!u) throw new UnauthorizedException('Usuario no encontrado');

    // 2) Aplanar permisos desde roles del usuario
    const roleIds = (u.roles ?? []).map((r) => r.id);
    let baseRules: Array<{
      action: string;
      subject: string;
      conditions?: any;
      inverted?: boolean;
      reason: string;
    }> = [];

    if (roleIds.length) {
      const dbPerms = await this.perms
        .createQueryBuilder('p')
        .leftJoin('p.role', 'r')
        .where('r.id IN (:...roleIds)', { roleIds })
        .getMany();

      const replacePlaceholders = (s: string) =>
        s.replace(/\$\{user\.([a-zA-Z0-9_]+)\}/g, (_, k) =>
          String((u as any)[k] ?? ''),
        );

      baseRules = dbPerms.map((p) => ({
        action: p.action,
        subject: p.subject,
        conditions: p.conditions
          ? JSON.parse(replacePlaceholders(p.conditions))
          : undefined,
        inverted: p.inverted,
        reason: typeof p.reason === 'string' ? p.reason : '',
      }));
    }

    // 3) Construir Ability final (reglas de BD + globales del factory)
    const domainUser: User = {
      id: u.id,
      isAdmin: u.isAdmin,
      roles: u.roles?.map((r) => r.name) ?? [],
    };
    const ability: AppAbility = this.caslFactory.createForUser(
      domainUser,
      baseRules,
    );

    // 4) Evaluar políticas
    const ok = handlers.every((h) => h(ability));
    if (!ok)
      throw new ForbiddenException(
        'No tienes permiso para realizar esta acción',
      );

    return true;
  }
}
```

## Paso 6: Seed para llenado de datos

Crear seed para llenar datos base al levantar aplicación como usuarios, roles y permisos:

```typescript
// src/seed/seed.service.ts
import { Injectable, OnModuleInit } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import * as bcrypt from 'bcryptjs';
import { UserEntity } from '../users/user.entity';
import { RoleEntity } from '../rbac/role.entity';
import { PermissionEntity } from '../rbac/permission.entity';

@Injectable()
export class SeedService implements OnModuleInit {
  constructor(
    @InjectRepository(UserEntity)
    private readonly users: Repository<UserEntity>,
    @InjectRepository(RoleEntity)
    private readonly roles: Repository<RoleEntity>,
    @InjectRepository(PermissionEntity)
    private readonly perms: Repository<PermissionEntity>,
  ) {}

  async onModuleInit() {
    const countUsers = await this.users.count();
    if (countUsers > 0) return;

    const adminRole = this.roles.create({ name: 'admin' });
    const userRole = this.roles.create({ name: 'user' });
    await this.roles.save([adminRole, userRole]);

    // permisos:
    const p1 = this.perms.create({
      action: 'manage',
      subject: 'all',
      role: adminRole,
    });
    const p2 = this.perms.create({
      action: 'read',
      subject: 'all',
      role: userRole,
    });
    await this.perms.save([p1, p2]);

    // usuarios con contraseñas hash:
    const admin = this.users.create({
      name: 'Admin User',
      email: 'admin@example.com',
      passwordHash: await bcrypt.hash('admin123', 10),
      isAdmin: true,
      roles: [adminRole],
    });

    const regular = this.users.create({
      name: 'Regular User',
      email: 'user@example.com',
      passwordHash: await bcrypt.hash('user123', 10),
      isAdmin: false,
      roles: [userRole],
    });

    await this.users.save([admin, regular]);
  }
}
```

## Paso 7: Módulo Auth

Crear módulo para la gestión de seguridad de la aplicación:

```typescript
// src/auth/dto/login.dto.ts
import { IsEmail, IsString, MinLength } from 'class-validator';

export class LoginDto {
  @IsEmail()
  email!: string;

  @IsString()
  @MinLength(6)
  password!: string;
}
```

```typescript
// src/auth/jwt-strategy.ts
import { Injectable } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';

type JwtPayload = { sub: number; email: string; iat: number; exp: number };

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      secretOrKey: process.env.JWT_SECRET || 'dev_secret_change_me',
      ignoreExpiration: false,
    });
  }

  validate(payload: JwtPayload) {
    // Se mapea a req.user
    return { id: payload.sub, email: payload.email };
  }
}
```

```typescript
// src/auth/jwt-auth.guard.ts
import { Injectable } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}
```

```typescript
// src/auth/auth.middleware.ts
import { Injectable, NestMiddleware } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { UserEntity } from '../users/user.entity';
import { PermissionEntity } from '../rbac/permission.entity';

@Injectable()
export class AuthMiddleware implements NestMiddleware {
  constructor(
    @InjectRepository(UserEntity)
    private readonly usersRepo: Repository<UserEntity>,
    @InjectRepository(PermissionEntity)
    private readonly permsRepo: Repository<PermissionEntity>,
  ) {}

  async use(req: any, _res: any, next: () => void) {
    const id = Number(req.headers['x-user-id'] ?? 2);
    const user = await this.usersRepo.findOne({ where: { id } });
    if (!user) {
      // fallback: primer usuario
      const first = await this.usersRepo.find({ take: 1 });
      req.user = first[0] ?? null;
    } else {
      req.user = {
        id: user.id,
        isAdmin: user.isAdmin,
        roles: user.roles?.map((r) => r.name) ?? [],
      };
    }

    // Carga permisos agregados por los roles del usuario (flatten)
    const roleIds = (user?.roles ?? []).map((r) => r.id);
    if (roleIds.length > 0) {
      const perms = await this.permsRepo
        .createQueryBuilder('p')
        .leftJoin('p.role', 'r')
        .where('r.id IN (:...roleIds)', { roleIds })
        .getMany();

      req.abilityBaseRules = perms.map((p) => ({
        action: p.action,
        subject: p.subject,
        conditions: p.conditions ? JSON.parse(p.conditions) : undefined,
        inverted: p.inverted,
        reason: p.reason ?? undefined,
      }));
    } else {
      req.abilityBaseRules = [];
    }

    next();
  }
}
```

```typescript
// src/auth/auth.service.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { JwtService } from '@nestjs/jwt';
import { Repository } from 'typeorm';
import * as bcrypt from 'bcryptjs';
import { UserEntity } from '../users/user.entity';

@Injectable()
export class AuthService {
  constructor(
    @InjectRepository(UserEntity)
    private readonly users: Repository<UserEntity>,
    private readonly jwt: JwtService,
  ) {}

  async validateUser(email: string, password: string) {
    const user = await this.users.findOne({ where: { email } });
    if (!user) return null;
    const ok = await bcrypt.compare(password, user.passwordHash);
    return ok ? user : null;
  }

  async login(email: string, password: string) {
    const user = await this.validateUser(email, password);
    if (!user) throw new UnauthorizedException('Credenciales inválidas');

    // payload mínimo (rehidrataremos roles/permisos desde BD en cada request)
    const payload = { sub: user.id, email: user.email };
    const accessToken = await this.jwt.signAsync(payload);

    return {
      accessToken,
      user: { id: user.id, email: user.email, name: user.name },
    };
  }
}
```

```typescript
// src/auth/auth.controller.ts
import { Body, Controller, Post } from '@nestjs/common';
import { AuthService } from './auth.service';
import { LoginDto } from './dto/login.dto';

@Controller('auth')
export class AuthController {
  constructor(private readonly auth: AuthService) {}

  @Post('login')
  login(@Body() dto: LoginDto) {
    return this.auth.login(dto.email, dto.password);
  }
}
```

```typescript
// src/auth/auth.module.ts
import { Module } from '@nestjs/common';
import { PassportModule } from '@nestjs/passport';
import { JwtModule } from '@nestjs/jwt';
import { TypeOrmModule } from '@nestjs/typeorm';
import { UserEntity } from '../users/user.entity';
import { AuthService } from './auth.service';
import { AuthController } from './auth.controller';
import { JwtStrategy } from './jwt.strategy';
import { ConfigModule, ConfigService } from '@nestjs/config';

@Module({
  imports: [
    ConfigModule,
    PassportModule,
    JwtModule.registerAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: (config: ConfigService) => ({
        secret: config.get<string>('JWT_SECRET', 'dev_secret_change_me'),
        signOptions: { expiresIn: '15m' },
      }),
    }),
    TypeOrmModule.forFeature([UserEntity]),
  ],
  controllers: [AuthController],
  providers: [AuthService, JwtStrategy],
  exports: [JwtModule],
})
export class AuthModule {}
```

## Paso 8: Módulo Users

Crear módulo para gestionar usuarios

```typescript
// src/users/dto/create-user.dto.ts
import { IsString, Length, IsEmail } from 'class-validator';

export class CreateUserDto {
  @IsString()
  @Length(2, 60)
  name!: string;

  @IsEmail()
  email!: string;

  @IsString()
  @Length(6, 100)
  password!: string;
}
```

```typescript
// src/users/user.service.ts
import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import * as bcrypt from 'bcryptjs';
import { UserEntity } from './user.entity';
import { CreateUserDto } from './dto/create-user.dto';

@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(UserEntity) private readonly repo: Repository<UserEntity>,
  ) {}

  findAll() {
    return this.repo.find();
  }

  async create(dto: CreateUserDto) {
    const u = this.repo.create({
      name: dto.name,
      email: dto.email,
      passwordHash: await bcrypt.hash(dto.password, 10),
      isAdmin: false,
    });
    return this.repo.save(u);
  }

  async remove(id: number) {
    const u = await this.repo.findOne({ where: { id } });
    if (!u) return null;
    await this.repo.remove(u);
    return u;
  }
}
```

```typescript
// src/users/user.controller.ts
import {
  Body,
  Controller,
  Delete,
  Get,
  Param,
  Post,
  UseGuards,
} from '@nestjs/common';
import { UsersService } from './users.service';
import { CreateUserDto } from './dto/create-user.dto';
import { JwtAuthGuard } from '../auth/jwt-auth.guard';
import { PermissionsGuard } from '../guards/permissions.guard';
import { CheckPolicies } from '../decorators/check-policies.decorator';
import { Actions, User as DomainUser } from '../casl/casl-ability.factory';

@Controller('users')
@UseGuards(JwtAuthGuard, PermissionsGuard)
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Post()
  @CheckPolicies((ability) => ability.can(Actions.Create, DomainUser))
  create(@Body() dto: CreateUserDto) {
    return this.usersService.create(dto);
  }

  @Get()
  @CheckPolicies((ability) => ability.can(Actions.Read, DomainUser))
  findAll() {
    return this.usersService.findAll();
  }

  @Delete(':id')
  @CheckPolicies((ability) => ability.can(Actions.Delete, DomainUser))
  remove(@Param('id') id: string) {
    return this.usersService.remove(+id);
  }
}
```

```typescript
// src/users/user.module.ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { UserEntity } from './user.entity';
import { UsersService } from './users.service';
import { UsersController } from './users.controller';
import { PermissionEntity } from '../rbac/permission.entity';
import { CaslAbilityFactory } from '../casl/casl-ability.factory';
import { PermissionsGuard } from '../guards/permissions.guard';

@Module({
  imports: [TypeOrmModule.forFeature([UserEntity, PermissionEntity])],
  controllers: [UsersController],
  providers: [UsersService, CaslAbilityFactory, PermissionsGuard],
  exports: [TypeOrmModule],
})
export class UsersModule {}
```

## Paso 9: Configurar AppModule

```typescript
// src/app.module.ts
import { Module } from '@nestjs/common';
import { ConfigModule, ConfigService } from '@nestjs/config';
import { TypeOrmModule } from '@nestjs/typeorm';
import { PermissionEntity } from './rbac/permission.entity';
import { SeedService } from './seed/seed.service';
import { UserEntity } from './users/user.entity';
import { UsersModule } from './users/users.module';
import { AuthModule } from './auth/auth.module';
import { RoleEntity } from './rbac/role.entity';

@Module({
  imports: [
    ConfigModule.forRoot({ isGlobal: true }),
    TypeOrmModule.forRootAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: (config: ConfigService) => ({
        type: 'sqlite',
        database: config.get<string>('DB_PATH', 'nest-casl.sqlite'),
        entities: [UserEntity, RoleEntity, PermissionEntity],
        synchronize: true,
        logging: false,
      }),
    }),
    TypeOrmModule.forFeature([UserEntity, RoleEntity, PermissionEntity]),
    UsersModule,
    AuthModule,
  ],
  providers: [SeedService],
})
export class AppModule {}
```

## Pruebas

### 1. Login (Admin)

```bash
curl -s -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"admin123"}'
```

### 2. Listar usuarios (admin; permitido por manage) <a href="#id-2-listar-usuarios-admin-permitido-por-manage" id="id-2-listar-usuarios-admin-permitido-por-manage"></a>

```bash
curl -H "Authorization: Bearer <TOKEN>" http://localhost:3000/users
```

### 3. Crear usuario como admin <a href="#id-3-crear-usuario-como-admin" id="id-3-crear-usuario-como-admin"></a>

```bash
curl -X POST http://localhost:3000/users \
  -H "Authorization: Bearer <TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"name":"Pepe","email":"pepe@example.com","password":"pepe123"}'
```

### 4. Login (usuario regular) <a href="#id-4-login-usuario-regular" id="id-4-login-usuario-regular"></a>

```bash
curl -s -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"user123"}'
```

### 5. Crear usuario con regular (debería fallar) <a href="#id-5-crear-usuario-con-regular-deber-c3-ada-fallar" id="id-5-crear-usuario-con-regular-deber-c3-ada-fallar"></a>

```bash
curl -i -X POST http://localhost:3000/users \
  -H "Authorization: Bearer <TOKEN_USER>" \
  -H "Content-Type: application/json" \
  -d '{"name":"Juan","email":"juan@example.com","password":"juan123"}'
```

### 6. Listar usuarios con regular (permitido por read) <a href="#id-6-listar-usuarios-con-regular-permitido-por-read" id="id-6-listar-usuarios-con-regular-permitido-por-read"></a>

```bash
curl -H "Authorization: Bearer <TOKEN_USER>" http://localhost:3000/users
```

### 7. Eliminar usuario con regular (debería fallar) <a href="#id-7-eliminar-usuario-con-regular-deber-c3-ada-fallar" id="id-7-eliminar-usuario-con-regular-deber-c3-ada-fallar"></a>

```bash
curl -i -X DELETE -H "Authorization: Bearer <TOKEN_USER>" \
  http://localhost:3000/users/1
```

## Notas de seguridad y extensibilidad

* El payload del JWT es mínimo (sub, email). Los roles y permisos se consultan en BD en cada request, reflejando cambios de inmediato.
* CASL permite condiciones dinámicas (por ejemplo, permitir update solo sobre su propio usuario).
* Puedes ampliar el sistema agregando más roles, permisos y condiciones en el seed o desde la base de datos.
* Para producción, cambia el secreto JWT y desactiva `synchronize: true` en TypeORM.
* El guard de permisos puede cachear reglas por usuario para optimizar lecturas a BD.
* Si necesitas refresh tokens, logout, o alias de acciones (manage ⇒ CRUD), puedes extender fácilmente la arquitectura.

Enlace demo del proyecto\
[https://github.com/sorodriguezz/poc-casl-jwt-nest](https://github.com/sorodriguezz/poc-casl-jwt-nest)
------------------------------------------------------------------------------------------------------
