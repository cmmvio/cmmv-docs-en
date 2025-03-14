# Types

The **Core** (`@cmmv/core`) is responsible for generating **agnostic models** that can be used across different layers of the application, such as **controllers, gateways, and resolvers**.
When the **GraphQL module** (`@cmmv/graphql`) is included in the project, the system **automatically enhances models** to be compatible with GraphQL operations.

This transformation ensures that models remain **fully structured**, while enabling **GraphQL decorators, type enforcement, and validation rules**.

## How Models Are Modified for GraphQL

By default, CMMV models are designed for multi-environment use.
When GraphQL is enabled, the system **adds the following modifications**:

✅ **`@Field` decorator added**: Ensures compatibility with GraphQL type definitions.
✅ **Type configurations applied**: Fields are assigned explicit GraphQL types like `String`, `Int`, and `ID`.
✅ **Readonly and Required support**: Introduced in **v0.8.34**, models now enforce **readonly fields** and required fields (`!`).
✅ **Agnostic models remain intact**: The core structure is preserved for use in controllers, gateways, and other services.

## Generated Model

Below is an example of a **system-generated model** with GraphQL enhancements:

```typescript
/**
    **********************************************
    This script was generated automatically by CMMV.
    It is recommended not to modify this file manually,
    as it may be overwritten by the application.
    **********************************************
**/

import { fastJson, AbstractModel } from "@cmmv/core";

import {
    ApiSchema, ApiProperty,
    ApiPropertyOptional, ApiResponseProperty
} from "@cmmv/openapi";

import {
    ObjectType, Field, ID, Int, Float
} from "@cmmv/graphql";

import {
    Expose,
    instanceToPlain,
    plainToInstance
} from "@cmmv/core";

import {
    IsOptional,
    IsString,
    MinLength,
    MaxLength,
    IsNotEmpty
} from "@cmmv/core";

export interface IGroups {
    id?: any;
    name: string;
    roles?: string[];
}

// Model Definition
@ApiSchema({ name: "Groups" })
@ObjectType()
export class Groups extends AbstractModel implements IGroups {

    @ApiResponseProperty({ type: String }) // OpenAPI
    @Field(type => ID) // GraphQL
    @Expose({ toClassOnly: true })
    @IsOptional()
    readonly id!: string;

    @Expose()
    @IsNotEmpty()
    @IsString({ message: "Invalid name" })
    @MinLength(3, { message: "Invalid name" })
    @MaxLength(40, { message: "Invalid name" })
    @ApiProperty({
        type: String,
        required: true,
    })
    @Field(() => String, { nullable: false }) // GraphQL
    name: string;

    @Expose()
    @ApiPropertyOptional({
        type: [String],
        readOnly: true,
        default: []
    })
    @Field(() => [String], { nullable: true }) // GraphQL
    readonly roles?: string[] = [];

    constructor(partial: Partial<Groups>) {
        super();
        Object.assign(this, partial);
    }

    public static fromPartial(partial: Partial<Groups>): Groups {
        return plainToInstance(Groups, partial, {
            exposeUnsetFields: false,
            enableImplicitConversion: true,
            excludeExtraneousValues: true
        });
    }

    public static fromEntity(entity: any): any {
        return this.sanitizeEntity(Groups, entity);
    }

    public toString() {
        return GroupsFastSchema(this);
    }
}

// JSON Schema for Fast Processing
export const GroupsFastSchemaStructure = {
    title: "Groups Schema",
    type: "object",
    properties: {
        id: { type: "string", nullable: false },
        name: { type: "string", nullable: false },
        roles: {
            type: "array",
            nullable: true,
            items: { type: "string" }
        }
    },
    required: ["id", "name"]
};

export const GroupsFastSchema = fastJson(GroupsFastSchemaStructure);
```

### Enhancements for GraphQL Compatibility

| Feature | Description |
|---------|------------|
| **`@Field` decorator** | Every model property gets the GraphQL `@Field` decorator, making it accessible in GraphQL queries and mutations. |
| **Explicit GraphQL Types** | Fields are assigned GraphQL types such as `String`, `Int`, `Float`, and `ID` for type safety. |
| **Readonly Support** | Read-only fields are marked with `readonly` and properly handled in GraphQL responses. |
| **Required Fields** | Fields marked as required in the model now enforce GraphQL `!` (non-nullable) types. |
| **Seamless Integration** | Models remain fully compatible with `@cmmv/openapi`, `@cmmv/http`, and other modules. |

* **Consistency**: Ensures that all GraphQL models follow a structured and validated schema.
* **Less Boilerplate**: Developers don’t need to manually decorate each model with GraphQL decorators.
* **Multi-Purpose Models**: Models work seamlessly across **REST APIs, WebSockets, GraphQL, and other layers**.
* **Improved Type Safety**: GraphQL operations enforce strict typing, reducing errors at runtime.

By automatically enhancing models for GraphQL, **CMMV simplifies development** while maintaining **a unified model structure** across the entire application.
