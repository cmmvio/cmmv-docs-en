# Auth

When **`@cmmv/auth`** is present in the project, **`@cmmv/graphql`** seamlessly integrates authentication and access control features. This integration ensures **secure GraphQL operations** without requiring additional configuration.

## Automatic Authentication Features

Once both modules are included, GraphQL **automatically** supports all authentication functionalities available in `@cmmv/auth`, including:

**`@Authorized` Decorator**:
   - Built-in role and permission validation for queries and mutations.
   - Ensures users can only access authorized GraphQL endpoints.

**User Role Validation**:
   - Restricts access to GraphQL resolvers based on user roles.
   - Prevents unauthorized queries and mutations.

**Token & Session Management**:
   - **Token & Refresh Token Handling** is fully automated.
   - Users can **log in and refresh tokens** directly through GraphQL.

**Support for All Authentication Providers**:
   - OAuth2 (Google, Facebook, GitHub, etc.).
   - Multi-Factor Authentication (MFA).
   - reCAPTCHA validation for enhanced security.

**Built-in Auth Resolvers**:
   - `@cmmv/graphql` internally provides authentication resolvers for user login, token refresh, and session handling.

## Using the @Authorized Decorator

With `@cmmv/auth` enabled, all resolvers automatically support **authorization control**.
You can restrict access using the **`@Authorized` decorator**, specifying required roles.

```typescript
import {
    Resolver, Query, Mutation,
    Arg, Authorized
} from "@cmmv/graphql";

@Resolver()
class UserResolver {
    @Query(() => String)
    @Authorized() // Only authenticated users can access
    getSecretData(): string {
        return "This is a protected query!";
    }

    @Mutation(() => Boolean)
    @Authorized("admin") // Restricts to users with "admin" role
    deleteUser(@Arg("id") id: string): boolean {
        return true; // Simulated delete operation
    }
}
```

## Token Management

`@cmmv/graphql` **automatically manages token authentication**, allowing users to log in and refresh their token **directly through GraphQL**.

```graphql
mutation {
    login(username: "admin", password: "password123") {
        token
        refreshToken
    }
}
```

## Role-Based Access Control (RBAC)

By default, `@cmmv/auth` provides **role-based access control (RBAC)** within GraphQL.
Each **user's roles and permissions** are automatically **validated** when calling queries or mutations.

```typescript
import { Resolver, Query, Authorized, Ctx, GraphQLContext } from "@cmmv/graphql";

@Resolver()
class SecureResolver {
    @Query(() => String)
    @Authorized(["moderator", "admin"]) // Allows only specific roles
    getModeratorData(@Ctx() ctx: GraphQLContext): string {
        return `Hello, \${ctx.auth?.user?.username}! You have access.`;
    }
}
```

## OAuth2, M2F, and reCAPTCHA Support

All **authentication methods** from `@cmmv/auth` are **fully available** in GraphQL **without any additional setup**.
This means you can **log in using Google, GitHub, and other OAuth2 providers** or even **require MFA verification**.

| Feature       | Available in GraphQL? |
|--------------|---------------------|
| **OAuth2 Login (Google, GitHub, etc.)** | ✅ Yes |
| **Multi-Factor Authentication (MFA)** | ✅ Yes |
| **reCAPTCHA Validation** | ✅ Yes |
| **JWT & Refresh Token Handling** | ✅ Yes |
| **Role-Based Access Control (RBAC)** | ✅ Yes |

By integrating **`@cmmv/graphql`** with **`@cmmv/auth`**, GraphQL operations become **fully authenticated** with **zero additional setup**.
This ensures that:

- **All queries & mutations are secured** using `@Authorized()`.
- **Tokens are managed automatically**, allowing seamless authentication & refresh.
- **OAuth2, MFA, and role-based access control (RBAC) are built-in**.
- **GraphQL inherits all auth features** available in `@cmmv/auth`.
