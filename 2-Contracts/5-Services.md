# Services

In CMMV, the `@ContractService` decorator allows the definition of custom methods within contracts that will be implemented later. Within the contract context, it serves to inform transpilers (such as RESTful, GraphQL, and RPC) about the existence of these functionalities, enabling the automatic generation of corresponding calls in controllers and services. This approach is particularly useful when you want to implement functions beyond standard CRUD operations, providing specific returns based on the contract's context, as widely used in the authentication module.

The `@ContractService` acts as a bridge between the contract definition and its practical implementation in services, generating *boilerplate* (base code) to streamline development. For example:
- The RESTful transpiler will implement the function call in the controller with the specified route.
- GraphQL will generate the corresponding schemas.
- RPC will define methods for remote calls.
- A function *boilerplate* will be generated in the service within `/src/services` for the developer to implement.

This functionality enhances the application's flexibility and modularity, allowing developers to create custom methods that meet specific project requirements.

## Usage Example

Here’s an example of how to use `@ContractService` in a contract to define a custom login method:

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
    imports: ['crypto']
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
        validations: [{ type: 'IsString', message: 'Invalid password' }],
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
        auth: true,
        createBoilerplate: true,
    })
    Login: Function;
}
```

### What Happens?
1. **Contract Definition:** The `Login` method is declared with `@ContractService`, specifying its route (`path`), HTTP method (`method`), request (`request`) and response (`response`) structures, along with options like authentication (`auth`).
2. **Controller Generation:** The RESTful transpiler will create a `/login` route in the associated controller, using the `POST` method, expecting a `LoginRequest` as input and returning a `LoginResponse`.
3. **Service Generation:** A function *boilerplate* will be generated in `/src/services/auth.service.ts` (or similar), ready for the developer to implement.
4. **Multiplatform Support:** GraphQL and RPC will also recognize the method and generate their respective implementations.

## Properties of `@ContractService`

The `@ContractService` decorator supports a specific interface that defines its properties. Below is the `IContractService` interface with its available options:

```typescript
export interface IContractService {
    propertyKey: string;               // Internal property name in the contract
    name: string;                      // Name of the service/method
    path: string;                      // Route path (e.g., 'login')
    method: 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH' | 'OPTIONS'; // Supported HTTP method
    auth?: boolean;                    // (Optional) Defines if the method requires authentication
    rootOnly?: boolean;                // (Optional) Restricts access to root only
    cache?: CacheOptions;              // (Optional) Cache settings for the method
    functionName?: string;             // (Optional) Custom function name in the service
    request: string;                   // Name of the request message (e.g., 'LoginRequest')
    response: string;                  // Name of the response message (e.g., 'LoginResponse')
    createBoilerplate?: boolean;       // (Optional) Defines if boilerplate will be generated in the service
}
```

### Property Descriptions
- **`propertyKey`:** Internal identifier used by the system to reference the method in the contract.
- **`name`:** Name of the service or method, used for identification and code generation.
- **`path`:** Defines the route path associated with the method (e.g., `'login'` becomes `/login` in REST).
- **`method`:** Specifies the HTTP method to be used (GET, POST, etc.).
- **`auth`:** If `true`, requires authentication to access the method.
- **`rootOnly`:** If `true`, restricts access to users with root permissions only.
- **`cache`:** Cache settings, such as time-to-live (TTL) or cache key.
- **`functionName`:** Allows customization of the generated function name in the service (if not specified, uses `name`).
- **`request`:** Refers to the name of a request message defined with `@ContractMessage`.
- **`response`:** Refers to the name of a response message defined with `@ContractMessage`.
- **`createBoilerplate`:** If `true`, automatically generates a *boilerplate* in the service file for implementation.

## Generated Service Boilerplate

Based on the example above, the following *boilerplate* will be generated in `/src/services/auth.service.ts`:

```typescript
import { Telemetry } from '@cmmv/core';
import { LoginRequestDTO, LoginResponseDTO } from '@models/auth/auth.model';
import { Repository } from '@cmmv/repository';

export class AuthServiceGenerated {
    ...
    async Login(payload: LoginRequestDTO): Promise<LoginResponseDTO> {
        throw new Error("Function Login not implemented");
    }
}
```

### Notes
- The *boilerplate* includes the basic function structure with integrated telemetry and an example logic to be implemented.
- Developers can customize the implementation as needed, such as adding token generation or integration with authentication systems.

## Benefits

This approach is highly beneficial for:
- **Custom Functionalities:** Allows defining methods beyond CRUD, such as login, logout, or other specific operations.
- **Multiplatform Consistency:** Ensures REST, GraphQL, and RPC implement the same functionalities consistently.
- **Productivity:** The generated *boilerplate* reduces manual work, allowing developers to focus on business logic.
- **Scalability:** Facilitates adding new methods to the contract without altering the existing structure.

The `@ContractService` is a powerful tool for extending CMMV contracts, offering flexibility and automation for developing custom services.
