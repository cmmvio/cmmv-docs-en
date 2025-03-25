# Contracts

The Contracts section in the `@cmmv/sandbox` provides a powerful interface for managing and inspecting contracts within your CMMV project. Contracts are central to the *contract-first* philosophy of CMMV, defining the structure, behavior, and relationships of your application's data and APIs.

In the Sandbox's left-hand menu, you'll find a list of all contracts existing in your project, as well as contracts from installed modules. Project-specific contracts are fully editable, while module-provided contracts are available in **read-only** mode. This allows you to verify their implementation without modifying their behavior.

## Contract Configuration

Contracts in CMMV offer a wide range of configuration options, enabling fine-tuned control over their behavior and integration into the system. These include:

- **Controller Route**: Define the base route for REST endpoints generated from the contract.
- **RPC Options**: Configure Remote Procedure Call settings for contract methods.
- **Subdirectories and Namespaces**: Organize contracts into logical subdirectories or namespaces for better structure.
- **Database-Specific Data**: Specify database-related details such as:
  - Table or collection name.
  - Soft delete support (e.g., marking records as deleted without removing them).
  - Other database-specific settings.

These configurations allow contracts to adapt to various use cases, from API generation to database interactions.

## Fields Tab

The first tab available in the contract interface is **Fields**, where you can define the core structure of the contract. This includes:

- **Field Definitions**: Set the basic structure of supported fields (e.g., name, type, constraints).
- **Relationships**: Establish connections between contracts (e.g., one-to-many, many-to-one).
- **Validations**: Apply rules using the `class-validator` library to enforce data integrity.

The fields defined here serve as the foundation for generating generic interfaces and models across the system. These are automatically equipped with validations from `class-validator`, and future updates will integrate data transformations via `class-transformer`.

### Relationships in Contracts

A key feature of CMMV contracts is the abstraction of relationships, which is handled independently of the underlying database. This abstraction comes before any direct database relationship mapping, making it particularly useful for databases like MongoDB, where native relationships are not predefined.

- **Relationship Abstraction**: Define how contracts relate to one another (e.g., a "User" contract linked to a "Post" contract).
- **Data Loading**: CMMV manages these relationships and ensures dependent data is correctly loaded in:
  - **API Responses**: REST endpoints return related data as specified.
  - **GraphQL Resolvers**: Automatically generated resolvers fetch and resolve related data.
  - **Form Builder**: Forms reflect relationships for seamless editing and visualization.

This means you can create a full relationship abstraction even when using MongoDB, with CMMV handling the logic to fetch and present dependent data accurately.

## Usage in Sandbox

In the Sandbox interface:

1. **Left Menu**: Browse all contracts in your project and installed modules.
   - Editable contracts (project-specific) allow full configuration.
   - Read-only contracts (from modules) display their structure and implementation details.
2. **Fields Configuration**:
   - Add, edit, or remove fields to define the contract's data structure.
   - Set up relationships by linking to other contracts.
   - Configure validations to ensure data consistency.
3. **Preview and Validation**: Use the Sandbox to test how your contract's fields and relationships translate into REST APIs, GraphQL queries, and form layouts.

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
        The relationship abstraction currently supports basic configurations (e.g., one-to-many, many-to-one). Advanced features like cascading deletes or complex joins are planned for future releases. Similarly, <strong>class-transformer</strong> integration for data transformation is not yet available but will enhance field processing in upcoming versions.
    </p>
</div>

## Benefits

- **Flexibility**: Define contracts tailored to your application's needs, regardless of the database backend.
- **Consistency**: Ensure uniform data structures and validations across REST, GraphQL, and forms.
- **Scalability**: Use namespaces and subdirectories to organize contracts as your project grows.

The Contracts section in Sandbox empowers you to fully leverage CMMV's *contract-first* capabilities, providing a visual and interactive way to design, manage, and validate your application's core components.