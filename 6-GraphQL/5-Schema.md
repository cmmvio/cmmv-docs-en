# Schema

The **@cmmv/graphql** module automatically generates a **GraphQL schema** in the standard `.graphql` format.
This schema is saved in the **`.generated/` directory** and is **recreated every time the system restarts**.

⚠️ **Important:**
This file is **automatically regenerated**—any manual modifications will be **overwritten** on the next reload.
Because of this, **Schema-First development is not possible**, and all custom types or resolvers must be defined using **Code-First** or via **contracts**.

## How the Schema is Generated

During system initialization, **@cmmv/graphql** performs the following steps:

1️⃣ Reads all **system contracts** and **custom resolvers**.
2️⃣ Generates a **GraphQL schema** in `.graphql` format.
3️⃣ Saves the schema file in the **`.generated/` directory**.
4️⃣ Every time the system **restarts**, the schema is **updated automatically**.

This ensures **consistency** across services, allowing external tools or microservices to **consume the schema** when needed.

## Schema Location

The generated schema file is located at:

```
.generated/schema.graphql
```

## Example

Here’s an example of what the **GraphQL schema** might look like:

```graphql
type User {
    id: ID!
    username: String!
    email: String!
    roles: [String!]!
}

type Query {
    userFind(limit: Int = 10, offset: Int = 0, sortBy: String = "id", sort: String = "asc", search: String, searchField: String, filters: JSON): PaginationUserReturn
    userById(id: ID!): User
}

type Mutation {
    createUser(username: String!, password: String!, profile: JSON): User
    updateUser(id: ID!, username: String!, password: String!, profile: JSON): Boolean
    deleteUser(id: ID!): Boolean
}

scalar JSON

type PaginationUserReturn {
    count: Int!
    data: [User!]!
    pagination: PaginationResponse!
}

type PaginationResponse {
    limit: Int!
    offset: Int!
    sortBy: String!
    sort: String!
    search: String
    searchField: String
    filters: JSON
}
```

### Schema-First Development is Not Supported

Since the schema is generated **automatically**, **Schema-First GraphQL development is not feasible** in CMMV.
Instead, developers must use **Code-First** or **Contract-Based Development**.

### ❌ Schema-First (Not Supported)
Manually defining schemas in `.graphql` files **is not possible** because the system **overwrites** the schema on every restart.

### ✅ Code-First (Supported)
Using decorators and TypeScript, developers can create **custom resolvers and types** dynamically.

```typescript
import { Resolver, Query, ObjectType, Field } from "@cmmv/graphql";

@ObjectType()
class CustomUser {
    @Field() id: string;
    @Field() username: string;
}

@Resolver()
class CustomResolver {
    @Query(() => CustomUser)
    getUser(): CustomUser {
        return { id: "1", username: "cmmv-user" };
    }
}
```

## Contract-Based (Recommended)

The **preferred approach** in **CMMV** is to **define contracts**, which are automatically converted into **GraphQL types, REST endpoints, and WebSocket events**.

Example contract:

```typescript
import { Contract, ContractField } from "@cmmv/core";

@Contract({ controller: "User" })
export class UserContract {
    @ContractField(() => String)
    username!: string;

    @ContractField(() => String, { nullable: true })
    email?: string;
}
```

This contract will be **automatically included in the generated schema**.

Even though **Schema-First development is not possible**, the generated `schema.graphql` file can still be useful:

✅ **API Documentation:** External services can read the schema for **GraphQL introspection**.
✅ **Microservice Integration:** Other applications can use the schema as a reference.
✅ **GraphQL Clients:** Frontend applications can **fetch the schema dynamically** for client-side queries.

To explore the schema, you can use **GraphQL introspection tools** like:

- **Apollo Sandbox** ([sandbox.apollo.dev](https://sandbox.apollo.dev))
- **GraphiQL** ([GraphQL Playground](https://www.graphql.com/graphiql))
- **Postman GraphQL Client**

## Conclusion

- **`@cmmv/graphql` automatically generates a schema** at `.generated/schema.graphql`.
- **Manual modifications are not allowed**, as the file is overwritten on every restart.
- **Schema-First development is not supported**—developers must use **Code-First** or **Contract-Based approaches**.
- The generated schema can be used for **documentation, introspection, and integration**.
