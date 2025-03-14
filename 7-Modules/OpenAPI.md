# OpenAPI

This module requires **zero configuration** and **automatically maps all contracts and controllers**, generating a complete API specification, including:

✅ **Routes, request properties, and response types**
✅ **Pagination, authentication, and authorization**
✅ **Models and DTO schemas**
✅ **Bearer Token & OAuth2 authentication (if `@cmmv/auth` is enabled)**

Additionally, it generates **two schema files** inside `/public`, which can be accessed publicly:

📂 **openapi.json** → OpenAPI specification in JSON format
📂 **openapi.yml** → OpenAPI specification in YAML format

## Installation

To install `@cmmv/openapi`, run:

```bash
$ pnpm add @cmmv/openapi
```

## Configuration

To enable OpenAPI support in your CMMV application, add `OpenAPIModule` to your setup:

```typescript
import { Application } from "@cmmv/core";
import { DefaultAdapter } from "@cmmv/http";
import { OpenAPIModule } from "@cmmv/openapi";
import { AuthModule } from "@cmmv/auth";

Application.create({
    httpAdapter: DefaultAdapter,
    modules: [
        OpenAPIModule,
        AuthModule // Optional, for authentication docs
    ],
    contracts: [...],
});
```

This will automatically:

1️⃣ Generate OpenAPI documentation from contracts and controllers
2️⃣ Enable Swagger UI at `/docs`
3️⃣ Create `openapi.json` and openapi.yml files in `/public`

## OpenAPI Schema Files

After starting your application, the module will generate:

## Accessing Swagger UI

The module includes a built-in Swagger UI interface, accessible at:

```bash
http://localhost:3000/docs
```

* Interactive API documentation
* Try out requests directly from the browser
* Auto-updated as the system evolves

⚠️ Important:
Currently, Swagger UI is not customizable, but future updates will add customization options.

## Authentication & Security

If @cmmv/auth is enabled, OpenAPI will automatically include authentication definitions:

✅ Bearer Token Authentication → Uses Authorization: Bearer <token> header
✅ OAuth2 Support → If enabled, OAuth2 flows will be included in the documentation

Example OpenAPI authentication definition:

```json
"securitySchemes": {
    "bearerAuth": {
        "type": "http",
        "scheme": "bearer"
    },
    "OAuth2": {
        "type": "oauth2",
        "flows": {
            "authorizationCode": {
                "authorizationUrl": "/auth/login",
                "tokenUrl": "/auth/token",
                "scopes": {
                    "read": "Grants read access",
                    "write": "Grants write access"
                }
            }
        }
    }
}
```

This ensures your API is secure and well-documented without requiring manual setup.

## Automatic Schema Generation

The module automatically maps:

* Routes (based on `@cmmv/http` controllers)
* Request & Response Properties
* Query Parameters & DTOs
* Pagination Support
* Authentication Headers
* Error Handling & Response Types

Example of an auto-generated OpenAPI path:

```json
"/users": {
    "get": {
        "summary": "Get list of users",
        "parameters": [
            { "name": "limit", "in": "query", "schema": { "type": "integer", "default": 10 } },
            { "name": "offset", "in": "query", "schema": { "type": "integer", "default": 0 } }
        ],
        "responses": {
            "200": {
                "description": "Successful response",
                "content": {
                    "application/json": {
                        "schema": {
                            "$ref": "#/components/schemas/UserList"
                        }
                    }
                }
            }
        }
    }
}
```

## OpenAPI Decorators

The `@cmmv/openapi` module provides **two primary decorators** for adding **OpenAPI metadata** to models.
Although OpenAPI schemas are **automatically generated from contracts**, developers can also apply these decorators **manually** to **custom models**.

| Decorator | Description |
|-----------|-------------|
| **`@ApiSchema`** | Defines a schema for an OpenAPI model, making it available in the API documentation. |
| **`@ApiProperty`** | Marks a property as part of an OpenAPI schema, defining type, requirements, and additional metadata. |
| **`@ApiPropertyOptional`** | Similar to `@ApiProperty`, but marks the field as optional. |
| **`@ApiResponseProperty`** | Defines properties specifically for API responses. |
| **`@ApiHideProperty`** | Excludes a property from appearing in the OpenAPI documentation. |

The `@ApiSchema` decorator **declares a model** in OpenAPI documentation.
It must be applied to **class definitions** that should be included in the API schema.

```typescript
import { ApiSchema, ApiProperty } from "@cmmv/openapi";

@ApiSchema({ name: "User" })
export class User {
    @ApiProperty({ type: String })
    username!: string;

    @ApiProperty({ type: String, required: true })
    email!: string;
}
```

The @ApiProperty decorator is used to annotate properties inside models, defining:

✅ Type → The data type (String, Number, Boolean, etc.).
✅ Required/Optional → Whether the field is mandatory.
✅ Default Values → The default value for the field.
✅ Read-Only Fields → Fields that cannot be modified.

```typescript
import { ApiSchema, ApiProperty } from "@cmmv/openapi";

@ApiSchema({ name: "Product" })
export class Product {
    @ApiProperty({ type: String, required: true })
    id!: string;

    @ApiProperty({ type: String, required: true })
    name!: string;

    @ApiProperty({ type: Number, required: true })
    price!: number;

    @ApiProperty({ type: Boolean, default: false })
    inStock!: boolean;
}
```

### Full Model with OpenAPI Decorators

Here’s an example of an automatically generated model with GraphQL & OpenAPI integration.

```typescript
import { fastJson, AbstractModel } from "@cmmv/core";
import { ObjectType, Field, ID } from "@cmmv/graphql";

import {
    ApiSchema, ApiProperty, ApiPropertyOptional,
    ApiResponseProperty, ApiHideProperty
} from "@cmmv/openapi";

@ApiSchema({ name: "Groups" })
@ObjectType()
export class Groups extends AbstractModel {
    @ApiResponseProperty({ type: String })
    @Field(type => ID)
    id!: string;

    @ApiProperty({ type: String, required: true })
    @Field(() => String)
    name!: string;

    @ApiPropertyOptional({ type: [String], readOnly: true, default: [] })
    @Field(() => [String], { nullable: true })
    roles?: string[] = [];

    @ApiHideProperty()
    secretCode!: string; // This will NOT appear in OpenAPI docs.

    constructor(partial: Partial<Groups>) {
        super();
        Object.assign(this, partial);
    }
}
```

## Controller-Based Documentation

Another way to document your API is by **adding metadata directly to controllers**.
This is especially useful for **agnostic modules** that **may not know** if the OpenAPI module will be included in the project.

🚀 **Advantages of Controller-Based Documentation**:
- ✅ Works **even if `@cmmv/openapi` is not installed**.
- ✅ Automatically **integrates with OpenAPI** if the module is present.
- ✅ Ensures **clear, structured API definitions** in controllers.

### How It Works

In this approach, controllers define:
- **Route contracts** → Linking API operations to system contracts.
- **Schema types** → Defining input/output models.
- **Summaries & descriptions** → Providing clear API documentation.
- **Authentication rules** → Using `@Auth()` decorators.

```typescript
import { Application } from "@cmmv/core";
import { Auth } from "@cmmv/auth";
import { ApiTags } from "@cmmv/openapi";

import {
   Controller, Get, Post, Put, Delete,
   Queries, Param, Body, Req, RouterSchema
} from "@cmmv/http";

import { Groups, GroupsFastSchema } from "@models/auth/groups.model";
import { GroupsService } from "@services/auth/groups.service";

@Controller('groups')
export class GroupsControllerGenerated {
    constructor(private readonly groupsservice: GroupsService) {}

    @Get({
        contract: Application.getContract("GroupsContract"),
        schema: RouterSchema.GetAll,
        summary: "Returns Groups records by filter",
        exposeFilters: true
    })
    @Auth({ rootOnly: true })
    async get(@Queries() queries: any, @Req() req) {
        return this.groupsservice.getAll(queries, req);
    }

    @Get(':id', {
        contract: Application.getContract("GroupsContract"),
        schema: RouterSchema.GetByID,
        summary: "Returns Groups record by ID"
    })
    @Auth({ rootOnly: true })
    async getById(@Param('id') id: string) {
        return this.groupsservice.getById(id);
    }

    @Post({
        contract: Application.getContract("GroupsContract"),
        schema: RouterSchema.Insert,
        summary: "Insert new Groups"
    })
    @Auth({ rootOnly: true })
    async insert(@Body() item: Groups, @Req() req) {
        return this.groupsservice.insert(item, req);
    }

    @Delete(':id', {
        contract: Application.getContract("GroupsContract"),
        schema: RouterSchema.Delete,
        summary: "Delete Groups by ID"
    })
    @Auth({ rootOnly: true })
    async delete(@Param('id') id: string) {
        return this.groupsservice.delete(id);
    }
}
```

| Component | Description |
|-----------|-------------|
| **`contract`** | Links the controller method to an **existing system contract**. |
| **`schema`** | Specifies a **schema type** for OpenAPI documentation (e.g., `RouterSchema.GetAll`). |
| **`summary`** | Provides a **short description** of what the endpoint does. |
| **`@Auth()`** | Defines authentication rules (e.g., `rootOnly: true`). |

If the **OpenAPI module is installed**, the **controller metadata is automatically included** in the generated API documentation.

However, if OpenAPI is **not installed**, the controller will still work **without any changes**.

This makes it an ideal approach for **modular applications** where documentation should be **optional but available**.

## Custom Request/Response Schemas

In addition to automatic schema generation, `@cmmv/openapi` allows developers to **define custom request and response schemas** for API endpoints.

By default, OpenAPI generates request and response schemas **automatically** from system contracts.
However, developers can **override these defaults** by specifying **custom schemas** inside controllers.

```typescript
import { Controller, Post, Body, Request, Response, Session } from "@cmmv/http";
import { Config } from "@cmmv/core";
import { LoginPayloadSchema, LoginReturnSchema } from "./schemas/auth.schemas";

@Controller("auth")
export class AuthController {
    @Post("login", {
        summary: "Route to login using username and password",
        externalDocs: "https://cmmv.io/docs/modules/authentication#login",
        docs: {
            body: LoginPayloadSchema,
            return: LoginReturnSchema,
        },
    })
    async handlerLogin(
        @Body() payload,
        @Request() req,
        @Response() res,
        @Session() session
    ) {
        const localLogin = Config.get("auth.localLogin", false);

        if (localLogin) {
            const { result } = await this.authorizationService.login(
                payload,
                req,
                res,
                session
            );
            return result;
        }

        return false;
    }
}
```

| Property       | Description |
|---------------|-------------|
| **`docs.body`** | Defines the **request schema** (payload format). |
| **`docs.return`** | Defines the **response schema**, including headers and metadata. |
| **`externalDocs`** | Adds a **link to external documentation** for more details. |

A **custom request schema** allows developers to **define specific properties**, validation rules, and example payloads.

```typescript
export const LoginPayloadSchema = {
    content: {
        "application/json": {
            schema: {
                type: "object",
                properties: {
                    username: { type: "string", required: true },
                    password: { type: "string", required: true },
                    token: { type: "string", required: false },
                    opt: { type: "string", required: false },
                },
            },
            examples: {
                default: {
                    value: {
                        username: "user@example.com",
                        password: "mypassword",
                    },
                },
                reCAPTCHA: {
                    value: {
                        username: "user@example.com",
                        password: "mypassword",
                        token: "recaptcha_token",
                    },
                },
                F2A: {
                    value: {
                        username: "user@example.com",
                        password: "mypassword",
                        token: "2fa_token",
                        opt: "123456",
                    },
                },
            },
        },
    },
};
```
<br/>

- **Custom validation rules** (e.g., required fields).
- **Flexible input formats** (e.g., different authentication methods).
- **Multiple request examples** for better documentation.

Custom response schemas **define what the API returns**, including:

```typescript
export const LoginReturnSchema = {
    description: "Login response schema",
    headers: {
        "set-cookie": {
            schema: {
                type: "string",
                description:
                    "Session cookie for authentication if application is enabled",
            },
        },
    },
    content: {
        "application/json": {
            schema: {
                type: "object",
                properties: {
                    status: { type: "number" },
                    processingTime: { type: "number" },
                    result: {
                        type: "object",
                        properties: {
                            token: { type: "string" },
                            refreshToken: { type: "string" },
                        },
                    },
                },
            },
        },
    },
};
```

For **complex APIs**, developers may want to link **external documentation**.
The **`externalDocs`** property allows adding a **URL** for extended API references.

```typescript
@Post("register", {
    summary: "Route to register a new public user",
    externalDocs: "https://cmmv.io/docs/modules/authentication#user-registration",
    docs: {
        body: RegistryPayloadSchema,
        return: RegistryReturnSchema,
    },
})
async handlerRegister(@Body() payload) {
    return this.authorizationService.register(payload);
}
```

## Conclusion

The `@cmmv/openapi` module provides zero-configuration OpenAPI support, ensuring your API is well-documented, secure, and ready for integration.

By automatically mapping contracts, controllers, and authentication, it simplifies API documentation while maintaining full compatibility with the CMMV ecosystem.

✅ Auto-generated OpenAPI & Swagger
✅ Live API documentation at /docs
✅ No manual setup required
✅ Works seamlessly with `@cmmv/http` & `@cmmv/auth`
✅ **Fine-tuned control** → Customize input & output schemas for endpoints.
✅ **Better API documentation** → Provide detailed structure, headers, and metadata.
✅ **Enhanced security & validation** → Enforce request/response rules explicitly.
✅ **External documentation support** → Link to extended API details.

This allows developers to focus on building APIs instead of writing documentation manually.