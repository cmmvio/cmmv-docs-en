# Server

The **@cmmv/graphql** module includes a fully configured **Apollo Server**, which runs in **standalone mode** by default.

- **Automatic Resolver Generation** → Queries and Mutations are created from system contracts.
- **Integrated Authentication** → Works seamlessly with `@cmmv/auth` (if enabled).
- **Schema Auto-Generation** → A `.graphql` schema file is stored in `.generated/schema.graphql`.
- **Standalone Apollo Server** → Runs on **port 4000** by default.
- **GraphQL Playground** → Available in **development mode** for testing queries.

## How the Server Works

When `@cmmv/graphql` is included in a project, the following steps occur:

1️⃣ **Resolvers are auto-generated** → Contracts are transformed into GraphQL Queries/Mutations.
2️⃣ **Authentication integration** → If `@cmmv/auth` is enabled, authentication is handled automatically.
3️⃣ **Schema is generated** → The system saves a `.graphql` schema file in `.generated/`.
4️⃣ **Apollo Server starts** → A **GraphQL API** is launched on **port 4000** (default).
5️⃣ **GraphQL Playground is available** → In **development mode**, a built-in UI allows testing queries.

This ensures **a fully functional GraphQL API** **without requiring manual configuration**.

## Server Configuration

By default, **Apollo Server** runs in **standalone mode** on **port 4000**, but this can be customized.

### ✅ Example: Changing the Default Port

Modify your **`.cmmv.config.cjs`** file:

```javascript
module.exports = {
    server: {
        host: process.env.HOST || "0.0.0.0",
        port: process.env.PORT || 3000,
    },

    graphql: {
        port: process.env.GRAPHQL_PORT || 4000,
    },
};
```

| Option | Default | Description |
|--------|---------|-------------|
| **`graphql.port`** | **4000** | Defines the port where the GraphQL server runs. |

## Accessing the GraphQL API

Once the server is running, the GraphQL API is accessible at:

```
http://localhost:4000/graphql
```

During development, Apollo provides an **interactive GraphQL Playground**, where you can:

✅ **Run Queries & Mutations**
✅ **Test Authentication Headers**
✅ **View API Schema**

## Future Production

In **future updates**, Apollo Server will be **embedded as middleware** inside the default HTTP server.
This means that in **production mode**, the GraphQL API will be accessible at:

```
http://localhost:3000/graphql
```

This ensures **GraphQL seamlessly integrates** with the main **REST API**.

- **Apollo Server is built-in** and runs on **port 4000** by default.
- **Resolvers and Schema are generated automatically** from system contracts.
- **Authentication is fully integrated** if `@cmmv/auth` is enabled.
- **In development mode**, the GraphQL Playground is accessible for testing.
- **Future production updates** will embed Apollo inside the HTTP server at `/graphql`.
