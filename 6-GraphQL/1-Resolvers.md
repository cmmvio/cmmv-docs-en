# Resolvers

The `@cmmv/graphql` module automatically generates GraphQL resolvers based on system contracts. This allows developers to focus on defining contracts while the system handles the generation of the necessary types and resolvers.

Additionally, `@cmmv/graphql` integrates seamlessly with `@cmmv/auth`, enabling authentication and authorization mechanisms within resolvers. However, developers can also define **custom resolvers** and manually add them to the module configuration.

## Automatic Resolver Generation

When a contract is created in CMMV, the system automatically generates a corresponding resolver. Below is an example of an **auto-generated** resolver for the `User` entity:

```typescript
/**
    **********************************************
    This script was generated automatically by CMMV.
    It is recommended not to modify this file manually,
    as it may be overwritten by the application.
    **********************************************
**/

import {
    Resolver, Query, Mutation,
    Authorized, Arg, Args,
    ID, Int, Float, Ctx,
    Field, ArgsType, ObjectType,
    PaginationArgs, GraphQLContext,
    PaginationResponse
} from "@cmmv/graphql";

import {
    IUser, User
} from "@models/auth/user.model";

import {
   UserService
} from "@services/auth/user.service";

@ArgsType()
class CreateUserInput {
    @Field(() => String, { nullable: false })
    username: String;

    @Field(() => String, { nullable: false })
    password: String;

    @Field(() => String, { nullable: true, defaultValue: "'{}'" })
    profile?: String;
}

@ObjectType()
class PaginationUserReturn {
    @Field(() => Int, { description: "Total records available.", nullable: false })
    count!: number;

    @Field(() => [User], { description: "List of user records.", nullable: false })
    data?: User[];

    @Field(() => PaginationResponse, { description: "Pagination metadata.", nullable: false })
    pagination!: PaginationResponse;
}

@Resolver(of => User)
export class UserResolverGenerated {
    private readonly userservice: UserService;

    constructor() {
        this.userservice = new UserService();
    }

    @Query(returns => PaginationUserReturn)
    @Authorized({ rootOnly: true })
    async userFind(@Args() queries: PaginationArgs, @Ctx() ctx: GraphQLContext) {
        return await this.userservice.getAll(queries, ctx.req);
    }

    @Query(returns => User)
    @Authorized({ rootOnly: true })
    async userById(@Arg("id") id: string) {
        return (await this.userservice.getById(id)).data;
    }

    @Mutation(returns => User)
    @Authorized({ rootOnly: true })
    async createUser(@Args() createUserData: CreateUserInput): Promise<User> {
        return (await this.userservice.insert(createUserData as Partial<User>)).data;
    }

    @Mutation(returns => Boolean)
    @Authorized({ rootOnly: true })
    async deleteUser(@Arg('id') id: string): Promise<boolean> {
        return (await this.userservice.delete(id)).success;
    }
}
```
<br/>

* **Automatic Contract-Based Resolver Creation:** When a contract is defined in CMMV, the system automatically generates resolvers based on its structure.
* **Pagination Support:** CMMV provides built-in pagination via PaginationArgs and PaginationResponse.
* **GraphQL Context Support:** Custom authentication and authorization logic can be applied by accessing the GraphQLContext.
* **Built-in Authorization:** Uses @Authorized({ rootOnly: true }) to restrict access to authenticated users.

## Custom Resolvers

Although CMMV generates most of the required resolvers automatically, developers can create custom resolvers when needed and add them to the system.

To register a custom resolver, modify the `Module` configuration:

```typescript
import { Module } from '@cmmv/core';

import { MyConfig } from './my.config';
import { MyService } from './my.service';
import { MyController } from './my.controller';
import { MyTranspile } from './my.transpiler';
import { MyResolver } from './my.resolver';

export const MyModule = new Module('mymodule', {
    configs: [MyConfig],
    controllers: [MyController],
    providers: [MyService],
    transpilers: [MyTranspile],
    resolvers: [MyResolver],
});
```

<br/>

* Developers can define their own query and mutation resolvers to extend existing functionality.
* Resolvers can be injected dynamically, allowing modular expansion.
* Authorization is fully customizable, integrating with @cmmv/auth.

Resolvers in `@cmmv/graphql` are automatically generated from contracts, ensuring a smooth GraphQL integration while enforcing a consistent schema.
For advanced use cases, developers can define custom resolvers and include them in the Modules configuration.

Future updates will introduce Apollo middleware integration with `@cmmv/http`, allowing seamless GraphQL and REST API coexistence.

## Embedded TypeGraphQL Decorators


The `@cmmv/graphql` module fully integrates with `type-graphql`, embedding its key decorators and functions.
This means **you don't need to manually install or import `type-graphql` in your project**—everything is available directly from `@cmmv/graphql`.

This approach ensures **simplified setup**, improved **developer experience**, and **better compatibility** within the CMMV ecosystem.

🔗 **Official TypeGraphQL Documentation**: [https://typegraphql.com/docs/introduction.html](https://typegraphql.com/docs/introduction.html)

The following table lists all embedded decorators and functions from `type-graphql`, along with their purpose:

| Decorator/Function  | Description |
|---------------------|-------------|
| `@ObjectType()`   | Defines a GraphQL object type, used for defining schemas. |
| `@Field()`        | Declares a field inside an object type, input type, or argument class. |
| `@Resolver()`     | Marks a class as a GraphQL resolver for a specific object type. |
| `@Query()`        | Defines a GraphQL query field that fetches data. |
| `@Mutation()`     | Defines a GraphQL mutation field to modify data. |
| `@Authorized()`   | Restricts access to a query/mutation based on authentication rules. |
| `@InputType()`    | Defines a GraphQL input type, primarily used for passing structured arguments. |
| `@Arg()`         | Declares an argument for a query or mutation. |
| `@Args()`        | Groups multiple arguments into a single object. |
| `@ArgsType()`    | Defines a class that encapsulates multiple arguments as an object. |
| `ArgOptions`     | Configuration options for the `@Arg()` decorator, such as nullable and default values. |
| `buildSchema()`  | Asynchronously generates a GraphQL schema from resolvers. |
| `buildSchemaSync()` | Synchronously generates a GraphQL schema from resolvers. |
| `@Ctx()`         | Provides access to the GraphQL execution context, useful for authentication. |
| `ID`             | Defines an `ID` type field in GraphQL schema, typically used for unique identifiers. |
| `Int`            | Declares an integer type in GraphQL schema. |
| `Float`          | Declares a floating-point type in GraphQL schema. |

### Usage in @cmmv/graphql

Since all necessary decorators and utilities are **pre-packaged** in `@cmmv/graphql`, you can **use them directly** in your resolvers without needing to import anything from `type-graphql`.

```typescript
import {
    Resolver, Query, ObjectType,
    Field
} from "@cmmv/graphql";

@ObjectType()
class User {
    @Field() id: string;
    @Field() username: string;
}

@Resolver()
class UserResolver {
    @Query(() => User)
    getUser(): User {
        return { id: "1", username: "cmmv-user" };
    }
}
```
<br/>

* No extra dependencies: `type-graphql` is embedded inside `@cmmv/graphql`, so you don't need to install it separately.
* Streamlined imports: Everything you need is available under `@cmmv/graphql`, reducing clutter.
* Fully compatible: Works seamlessly with `@cmmv/auth` and other modules.