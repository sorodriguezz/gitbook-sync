# Configurar GraphQL en NestJS

El proceso de configurar un servidor [GraphQL](https://graphql.org/) con el marco NestJS , establecer un esquema fuertemente tipado, implementar resolutores y explotar las ventajas de [TypeScript](https://code.visualstudio.com/docs/languages/typescript) para proporcionar una API más segura, eficiente y fácil de mantener.

## Paso 1: Ejecute la CLI para crear un nuevo proyecto <a href="#heading-step1nest-cli-to-create-a-new-project" id="heading-step1nest-cli-to-create-a-new-project"></a>

```bash
npx @nestjs/cli new common-project
# ó
nest new common-project
```

## Paso 2: Navegue hasta el directorio de su proyecto <a href="#heading-step-2-navigate-to-your-project-directory" id="heading-step-2-navigate-to-your-project-directory"></a>

```bash
cd common-project
```

## Paso 3: Instale las dependencias necesarias para GraphQL y TypeORM: <a href="#heading-step-3-install-the-required-dependencies-for-graphql-and-typeormhttpsorkhangitbookiotypeormd" id="heading-step-3-install-the-required-dependencies-for-graphql-and-typeormhttpsorkhangitbookiotypeormd"></a>

```bash
npm i @nestjs/graphql @nestjs/typeorm graphql typeorm @nestjs/apollo sqlite3
```

## Paso 4: Crear modelos de datos utilizando clases TypeScript <a href="#heading-step-4-create-data-models-using-typescript-classes" id="heading-step-4-create-data-models-using-typescript-classes"></a>

```typescript
// src/user/user.entity.ts
import { Entity, PrimaryGeneratedColumn, Column } from 'typeorm';
import { ObjectType, Field, ID } from '@nestjs/graphql';

@Entity()
@ObjectType()
export class User {
  @PrimaryGeneratedColumn()
  @Field(() => ID)
  id: number;

  @Column()
  @Field()
  name: string;

  @Column()
  @Field()
  email: string;
}
```

## Paso 5: Configure su conexión de base de datos en app.module.ts. <a href="#heading-step-5-configure-your-database-connection-in-the-appmodulets" id="heading-step-5-configure-your-database-connection-in-the-appmodulets"></a>

```typescript
// src/app.module.ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { User } from './user/user.entity';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'sqlite',
      database: 'data.db',
      entities: [User],
      synchronize: true,
    }),
  ],
})
export class AppModule {}
```

## Paso 6: Defina su esquema y sus resolves GraphQL <a href="#heading-step-6-define-your-graphql-schema-and-resolvers" id="heading-step-6-define-your-graphql-schema-and-resolvers"></a>

```typescript
// src/user/user.resolver.ts
import { Resolver, Query, Mutation, Args, Int } from '@nestjs/graphql';
import { User } from './user.entity';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';

@Resolver(() => User)
export class UserResolver {
  constructor(
    @InjectRepository(User)
    private readonly userRepository: Repository<User>,
  ) {}

  @Query(() => [User])
  async users(): Promise<User[]> {
    return this.userRepository.find();
  }

  @Query(() => User, { nullable: true })
  async user(
    @Args('id', { type: () => Int }) id: number,
  ): Promise<User | null> {
    return this.userRepository.findOneBy({ id });
  }

  @Mutation(() => User)
  async createUser(
    @Args('name') name: string,
    @Args('email') email: string,
  ): Promise<User> {
    const user = this.userRepository.create({ name, email });
    return this.userRepository.save(user);
  }
}
```

## Paso 7: Configurar el módulo GraphQL en app.module.ts <a href="#heading-step-7-set-up-the-graphql-module-in-appmodulets" id="heading-step-7-set-up-the-graphql-module-in-appmodulets"></a>

```typescript
// src/app.module.ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { User } from './user/user.entity';
import { GraphQLModule } from '@nestjs/graphql';
import { ApolloDriver, type ApolloDriverConfig } from '@nestjs/apollo';
import { UserResolver } from './user/user.resolve';
import { join } from 'path';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'sqlite',
      database: 'data/data.db',
      entities: [User],
      synchronize: true,
    }),
    TypeOrmModule.forFeature([User]),
    GraphQLModule.forRoot<ApolloDriverConfig>({
      driver: ApolloDriver,
      playground: true,
      autoSchemaFile: join(process.cwd(), 'src/schema.gql'),
    }),
  ],
  providers: [UserResolver],
})
export class AppModule {}
```

## Paso 8: Inicie su aplicación NestJS usando la CLI de Nest <a href="#heading-step-8-start-your-nestjshttpswwwturingcomblogwhat-is-nest-js-why-use-it-application-using-th" id="heading-step-8-start-your-nestjshttpswwwturingcomblogwhat-is-nest-js-why-use-it-application-using-th"></a>

```
npm run start:dev
```

## Paso 9: Abra su navegador y navegue por GraphQL Playground <a href="#heading-step-9-open-your-browser-and-navigate-graphql-playground" id="heading-step-9-open-your-browser-and-navigate-graphql-playground"></a>

Para acceder a GraphQL Playground , abra su navegador y vaya a [localhost:3000/graphql](http://localhost:3000/graphql). Ahora puede ejecutar consultas GraphQL e investigar su esquema.

## Paso 10: Ejecute consultas en Playground

Para crear usuarios:

```graphql
mutation {
  createUser(name: "Juan Pérez", email: "juan@example.com") {
    id
    name
    email
  }
}
```

Para obtener usuarios:

```graphql
query {
  users {
    id
    name
    email
  }
}
```

Para obtener un usuario por ID:

```graphql
query {
  user(id: 1) {
    id
    name
    email
  }
}
```
