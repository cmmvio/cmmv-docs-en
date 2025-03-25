# DTOs

Beyond defining fields for database tables and CRUD operations, contracts also support the definition of **Data Transfer Objects (DTOs)**. DTOs are specialized data structures used for communication with services, such as API routes that require custom payloads not directly tied to the full contract entity.

- **Example**: The "Users" contract might define a full entity with fields like `id`, `name`, `email`, and `password`, but for routes like registration or login, you can define DTOs with a subset of fields (e.g., `email` and `password` for login) tailored to those specific operations.
- **Custom Methods**: DTOs represent the expected data structures for implementing custom methods in controllers, gateways, or resolvers.

### DTOs in Sandbox

In the Sandbox, DTOs enhance the configuration experience by:

- **Automatic Request/Response Setup**: The Sandbox understands these data structures and uses them to automatically configure request and response payloads for testing and validation.
- **Integration with Modules**: Modules like `@cmmv/openapi` leverage declared DTOs in controllers to generate accurate API documentation, ensuring consistency between your contract definitions and the exposed endpoints.

To define DTOs, you can extend your contract configuration within the Sandbox interface, specifying the structure and associating it with specific routes or methods.

## Usage in Sandbox

In the Sandbox interface:

1. **Left Menu**: Browse all contracts in your project and installed modules.
   - Editable contracts (project-specific) allow full configuration of fields, relationships, and DTOs.
   - Read-only contracts (from modules) display their structure and implementation details.
2. **Fields Configuration**:
   - Add, edit, or remove fields to define the contract's data structure.
   - Set up relationships by linking to other contracts.
   - Configure validations to ensure data consistency.
3. **DTO Configuration**:
   - Define custom DTOs for specific API routes or service methods.
   - Test request/response payloads based on these DTOs directly in the Sandbox.
4. **Preview and Validation**: Use the Sandbox to test how your contract's fields, relationships, and DTOs translate into REST APIs, GraphQL queries, and form layouts, with automatic documentation support when paired with OpenAPI.