# Methods

The Methods section in the `@cmmv/sandbox` allows you to manage and define the operations available for each contract in your CMMV project. Methods represent the functional capabilities of a contract, ranging from standard CRUD operations to custom logic tailored to your application's needs.

By default, if the **Generate Controller** option is enabled in a contract, basic CRUD functions (Create, Read, Update, Delete) are automatically generated based on the data structure defined in the **Fields** tab. The Methods interface builds on this by presenting these default methods and offering the ability to create custom methods to extend functionality.

## Default Methods

When **Generate Controller** is activated, the following standard CRUD methods are created for the contract:

- **Create**: Adds a new record based on the contract's fields.
- **Read**: Retrieves one or more records, with support for filtering and pagination.
- **Update**: Modifies an existing record.
- **Delete**: Removes a record (or marks it as deleted if soft delete is enabled).

These methods are automatically wired to the service layer, leveraging the structure defined in the contract's fields, and are accessible via REST and GraphQL endpoints.

## Custom Methods

In addition to default CRUD operations, you can define **custom methods** to implement specific business logic. While the declaration of these methods is done within the Sandbox, their actual logic requires manual implementation in the codebase.

### Method Declaration

When you add a custom method in the Sandbox:
- The method is registered in the contract's service with a placeholder implementation: `throw new Error('Not Implemented')`.
- A public service file is generated in the `/src` directory, which you can edit to override the default implementation.

### Implementation

To implement a custom method:
1. Locate the generated service file in `/src` (e.g., `UserService.ts` for a "Users" contract).
2. Override the method in the service class, which has access to all providers via dependency injection in the constructor (e.g., `Repository`, `DatabaseConnection`).
3. Focus solely on implementing the correlated logic, as the method's signature and dependencies are preconfigured.

For example:
```typescript
import { Service } from '@cmmv/core';
import { Repository } from '@cmmv/repository';

import {
    RegisterUserDto, UserResponseDto
} from "@models/user";

@Service()
export class UserService {
    constructor(private repository: Repository) {}

    // Override custom method
    async registerUser(payload: RegisterUserDto): Promise<UserResponseDto> {
        const user = await this.repository.create('User', payload);
        return { id: user.id, email: user.email };
    }
}
```

### DTOs for Custom Methods

Creating a custom method requires defining **Data Transfer Objects (DTOs)** for both the request payload and the response:
- **Request DTO**: Specifies the expected input structure (e.g., `RegisterUserDto` with `email` and `password`).
- **Response DTO**: Defines the output structure (e.g., `UserResponseDto` with `id` and `email`).

Once declared:
- These DTOs are automatically added to the project's models.
- The Sandbox recognizes them for testing purposes.
- They gain passive support for implementation across **REST**, **GraphQL**, and **RPC**.

## Usage in Sandbox

In the Sandbox interface, the Methods section provides:

1. **Method List**: Displays all default CRUD methods (if generated) and any custom methods you’ve defined.
2. **Custom Method Creation**:
   - Add a new method by specifying its name, request DTO, and response DTO.
   - The Sandbox generates the service stub with a "Not Implemented" error.
3. **Testing**:
   - Test default and custom methods directly via REST or GraphQL endpoints.
   - Use the Sandbox to simulate requests with the defined DTOs and inspect responses.
4. **Visibility**: All methods (default and custom) are visible and testable, with their DTOs reflected in the interface.

For example, after defining a `registerUser` method:
- Test it via REST at `POST /users/register` with the request DTO.
- Query it in GraphQL with the generated resolver.
- Verify the response matches the defined response DTO.

## Integration Benefits

- **Service Layer**: Custom methods are pre-stubbed in the service, reducing setup time and letting you focus on logic.
- **Dependency Injection**: Access all providers (e.g., database, authentication) without additional configuration.
- **Cross-Protocol Support**: Methods are automatically compatible with REST, GraphQL, and RPC once implemented.
- **Documentation**: Modules like `@cmmv/openapi` use method declarations and DTOs to generate accurate API docs.

<div style="
    background-color: #DBEAFE;
    border-left: 4px solid #3B82F6;
    color: #1E40AF;
    padding: 1rem;
    border-radius: 0.375rem;
    margin: 1.5rem 0;
    font-size: 12px;
">
    <p style="font-weight: bold; margin-bottom: 0.5rem;">Important Notice</p>
    <p>
        While method declarations and DTOs are supported in the Sandbox, the actual logic implementation still requires manual coding. Future updates aim to introduce visual logic builders or code generation to simplify this process. Current GraphQL resolver generation for custom methods is basic and may require manual adjustments for complex use cases.
    </p>
</div>

## Benefits

- **Efficiency**: Pre-generated CRUD methods and service stubs accelerate development.
- **Flexibility**: Custom methods allow tailored functionality with minimal boilerplate.
- **Testability**: Sandbox integration makes it easy to validate method behavior across protocols.

The Methods section in Sandbox enhances your ability to define and test contract operations, bridging the gap between contract design and practical implementation in your CMMV application.
