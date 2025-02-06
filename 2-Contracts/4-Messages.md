# Messages

Starting from version 0.9, the CMMV framework introduces the ``@ContractMessage`` decorator, which allows developers to define structured data messages within contracts. This feature aligns with the Protocol Buffers (Protobuf) approach, enabling the creation of DTOs (Data Transfer Objects) that define data structures used in both RPC and RESTful requests, ensuring that only the necessary data is transmitted without exposing the entire contract schema.

* **Encapsulation of Request and Response Structures:** Messages allow the explicit definition of data used for specific functions, avoiding unnecessary data exposure.

* **Auto-Generation of DTOs and Interfaces:** The system transpiles contract messages into TypeScript interfaces and DTOs, which can be used within generated controllers, gateways, or any other part of the application.

* **Improved Security and Maintainability:** By structuring requests and responses separately, applications can enforce strict data validation and transformation rules.

* **Integration with Existing CMMV Features:** Works seamlessly with existing contract features, such as transformations, validations, and services.

The ``@ContractMessage`` decorator is used within a contract class to define request and response structures explicitly. It takes the following parameters:

```typescript
import * as crypto from 'crypto';

import { 
    AbstractContract, 
    Contract, 
    ContractField, 
    ContractMessage, 
    ContractService 
} from '@cmmv/core';

@Contract({
    controllerName: 'User',
    protoPath: 'src/protos/auth.proto',
    protoPackage: 'auth',
    directMessage: true,
    generateController: false,
    imports: ['crypto'],
    index: [{ name: "idx_user_login", fields: ["username", "password"] }]
})
export class AuthContract extends AbstractContract {
    @ContractField({
        protoType: 'string',
        unique: true,
        validations: [{ type: 'IsString', message: 'Invalid username' }],
        transform: ({ value }) =>
            crypto.createHash('sha1').update(value).digest('hex'),
    })
    username: string;

    @ContractField({
        protoType: 'string',
        validations: [
            {
                type: 'IsString',
                message: 'Invalid password',
            },
        ],
        transform: ({ value }) =>
            crypto.createHash('sha256').update(value).digest('hex'),
    })
    password: string;

    @ContractMessage({
        name: 'LoginRequest',
        properties: {
            username: { type: 'string', required: true },
            password: { type: 'string', required: true },
        },
    })
    LoginRequest: {
        username: string;
        password: string;
    };

    @ContractMessage({
        name: 'LoginResponse',
        properties: {
            success: { type: 'bool', required: true },
            token: { type: 'string', required: false },
            message: { type: 'string', required: false },
        },
    })
    LoginResponse: {
        success: boolean;
        token?: string;
        message?: string;
    };

    @ContractService({
        name: 'Login',
        path: 'login',
        method: 'POST',
        request: 'LoginRequest',
        response: 'LoginResponse',
    })
    Login: Function;
}
```

## Generated DTOs

The transpiler will automatically generate DTO classes and interfaces based on the contract messages. For example, the above contract will result in the following DTOs:

```typescript
export interface LoginRequest {
    username: string;
    password: string;
}

export class LoginRequestDTO implements LoginRequest {
    username: string;
    password: string;

    constructor(partial: Partial<LoginRequestDTO>) {
        Object.assign(this, partial);
    }

    public serialize(){
        return instanceToPlain(this);
    }
}

export interface LoginResponse {
    success: boolean;
    token?: string;
    message?: string;
}

export class LoginResponseDTO implements LoginResponse {
    success: boolean;
    token?: string;
    message?: string;

    constructor(partial: Partial<LoginResponseDTO>) {
        Object.assign(this, partial);
    }

    public serialize(){
        return instanceToPlain(this);
    }
}
```

These DTOs will be used within the generated controllers and gateways, ensuring type safety and consistency across the application.

The introduction of the @ContractMessage feature in version 0.9 provides a structured way to define and utilize specific data structures within CMMV contracts. This enhancement improves data security, reduces payload size, and allows for efficient service communication using DTOs.

By leveraging this feature, developers can create more maintainable, secure, and scalable applications without unnecessary complexity.

## Protobuf Integration

If the ``@cmmv/protobuf`` module is present in the project, the transpiler will automatically generate the corresponding .proto file based on the defined contract messages. The generated .proto structure will adhere to the contract definitions, ensuring compatibility with gRPC and other Protobuf-based communication protocols.

For the given ``AuthContract`` example, the following Protobuf definitions will be generated:

```proto
message LoginRequest {
    string username = 1;
    string password = 2;
}

message LoginResponse {
    bool success = 1;
    string token = 2;
    string message = 3;
}
```

By leveraging the ``@cmmv/protobuf`` module, developers can extend their applications with high-performance serialization, ensuring consistency between contract messages and Protobuf definitions.