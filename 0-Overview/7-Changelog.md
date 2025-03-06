# Changelog

## Version 0.8.30
March 5, 2025

### Critical Fixes

- **Transpiler Includes Fix in `@cmmv/core`**
  Resolved an issue where included files in transpilers were not correctly handled, ensuring consistency in generated outputs.

- **Linked Parameter Type Fixes in `@cmmv/http`**
  Addressed issues where linked parameters had incorrect types, improving reliability in parameter handling within HTTP routes.

- **Fast-JSON-Stringify Schema Fix in `@cmmv/http`**
  Fixed an issue related to schema generation using `fast-json-stringify`, ensuring proper serialization and performance optimizations.

### Additions

- **New Application Hooks in `@cmmv/core`**
  Introduced lifecycle hooks (`awaitModule`, `awaitService`) to enable better control over service initialization.

- **Contract Relationships in `@cmmv/core`**
  Added support for defining relationships between contracts, improving modularity and reusability.

- **Custom Indexing in Contracts**
  Developers can now define **custom indexes** in contracts to optimize database queries.

- **Alias Support in Models**
  Added alias functionality to models, allowing for alternative identifiers within database queries.

- **Recursive Loading of Providers**
  Providers are now loaded recursively, ensuring dependencies are resolved correctly during initialization.

- **`execAsyncFn` for Asynchronous Processing**
  Added `execAsyncFn` to allow non-blocking execution of provider-specific functions.

- **New `Redirect` and `HTTPCode` Decorators in `@cmmv/http`**
  Developers can now define custom redirections and HTTP status codes directly in their controllers.

- **Extended Model Functions in `@cmmv/http`**
  - `fromPartial`, `fromEntity` functions for easier model instantiation.
  - `getByIdRaw` route generation for raw entity lookups.

- **Enhanced Authentication Features in `@cmmv/auth`**
  - **Role and Group Management** for advanced user permissions.
  - **JWT Fingerprint Verification** to prevent token hijacking.
  - **Two-Factor Authentication (T2F)** including QR code generation and validation.
  - **Session Logging** with device fingerprints and browsing data.
  - **ReCAPTCHA Validation** integrated for improved security.
  - **AES-256-GCM Encryption for JWT Tokens** ensuring secure token storage.
  - **Refresh Token Support** for seamless user sessions.

- **Pagination and Count Functions in `@cmmv/repository`**
  - Added `pagination`, `count`, and `insertIfNotExists` functions for better data retrieval and management.
  - Introduced `updateOne`, `exists`, and `count` methods for simplified repository queries.

- **New File Destination for Protobufs in `@cmmv/protobuf`**
  - Changed the location of generated `.proto` files and related outputs for better organization.

- **WebSocket Gateway and Model Organization in `@cmmv/ws`**
  - Moved WebSocket Gateways and Models to `.generated` while keeping placeholders in `src` for better code maintainability.

- **New Vault Module: `@cmmv/vault`**
  - Secure **key generation** and **payload encryption**.
  - Insert, retrieve, and delete encrypted data via **elliptic curve encryption (ECC) and AES-256-GCM**.

### Improvements

- **Config File Support for Multiple Formats**
  - `@cmmv/core` now supports `.js`, `.cjs`, and `.ts` configuration files.

- **Enhanced HTTP Exception Handling**
  - Custom HTTP exceptions can now be defined using `HttpException` for better error control.

- **Optimized Contract-Based Models**
  - Contracts, models, and entities are now loaded dynamically, reducing manual imports and improving flexibility.

- **Metadata `moduleContract` for Contract Modules**
  - Allows defining a contract as a module for easier separation of concerns.

- **Better Indexing and Entity Search in Repository**
  - The repository now supports searching for entities directly in `.generated` files.

### Recommendations

- **Leverage `@cmmv/vault` for Secure Data Storage**
  - If your application handles sensitive data, integrate the new vault module to encrypt and store secrets securely.

- **Refactor Authentication Implementations**
  - Migrate existing authentication flows to use the new role-based access control, T2F, and reCAPTCHA validations.

- **Update Contracts for Custom Indexing**
  - Define indexes directly in your contracts to optimize database performance.

- **Migrate to New `@cmmv/core` Features**
  - Take advantage of `execAsyncFn`, recursive provider loading, and enhanced lifecycle hooks for better modularization.

### Updates

- **Documentation Updated**
  - Added comprehensive guides for new authentication features, contract relationships, custom indexing, and vault usage.

Updating to version `0.8.30` introduces **major improvements** in contract management, authentication, indexing, and secure storage, making the framework more robust and scalable for enterprise applications.

## Version 0.8.8
January 24, 2025

### Critical Fixes

- **Entity Generation Fixes in `@cmmv/repository`:**
  Resolved issues related to the generation of entities, specifically addressing cases involving serialized objects to ensure correct schema definitions and data integrity.

- **Transformation Handling with `toPlain`:**
  Introduced the `toPlain` parameter to revert transformations applied via `transform` when serializing objects for transmission via RPC or REST, ensuring accurate data representation.

### Additions

- **Custom Index Support in Contracts:**
  Added the ability to define custom indexes directly in the contract using the `index` parameter, allowing for complex multi-field indexes with advanced TypeORM options such as `unique`, `spatial`, `fulltext`, and more.

- **Extended Indexing in `@cmmv/repository`:**
  Updated the repository module to support and correctly apply custom index definitions from contracts, enhancing query performance and data organization.

- **New `@ContractMessage` Decorator:**
  Introduced the `@ContractMessage` decorator, enabling developers to define structured data messages within contracts, aligning with Protobuf concepts and standardizing DTOs for request and response handling.

- **New `@ContractService` Decorator:**
  Added the `@ContractService` decorator to facilitate the definition of custom functions within contracts, providing a structured approach to handle custom request/response flows.

- **Automatic DTO and Interface Generation:**
  Implemented automatic generation of DTOs and TypeScript interfaces based on contract messages, streamlining the development process and ensuring consistency across services.

- **Boilerplate Generation for Custom Functions:**
  The system now generates boilerplate code for implementing custom functions within controllers and gateways, simplifying integration and reducing manual effort.

### Improvements

- **Enhanced Data Handling in RPC and REST:**
  The introduction of `toPlain` improves data serialization, providing flexibility in handling transformations and ensuring compatibility with different transport layers.

- **Optimized Indexing Logic in Repository:**
  Improved the logic for applying custom indexes in the repository, enhancing performance and reducing potential database query overhead.

- **Standardized Request and Response Structures:**
  With the introduction of `@ContractMessage` and `@ContractService`, applications now have a consistent and maintainable way to define request and response structures.

### Recommendations

- **Update Contracts for Indexing:**
  Leverage the new indexing capabilities by defining custom indexes in contracts for optimized database operations.

- **Implement Structured Custom Functions:**
  Utilize the new decorators to standardize custom function implementations within contracts for better maintainability and scalability.

- **Review Serialization Strategies:**
  Ensure correct usage of the `toPlain` parameter to maintain data integrity during serialization for API responses.

### Updates

- **Documentation Overhaul:**
  The documentation has been updated to reflect the new features, including examples and guidelines for using `@ContractMessage`, `@ContractService`, and custom indexing options.

By updating to version `0.9`, developers can take advantage of powerful new features, improved indexing support, and a more efficient and structured approach to handling contract-based data processing.

## Version 0.8.7
January 22, 2025

### Critical Fixes

- **Route Identification Fixes in `@cmmv/server`**:
  Resolved issues related to incorrect route identification, ensuring accurate request routing and improved reliability.

- **Helmet Middleware Fix**:
  Fixed inconsistencies in the Helmet security middleware configuration, enhancing overall application security.

- **Lifecycle Handling in `@cmmv/http`**:
  Addressed issues in the request lifecycle to ensure proper execution flow and reduce potential request stalls.

### Additions

- **Header Return Points in `@cmmv/server`**:
  Introduced additional return points for header processing, preventing unnecessary operations when responses have already been sent.

- **Function to Compile in `@cmmv/core`**:
  Added a new `compile` function, allowing developers to process modules and generate transpiled files without running the application.

### Improvements

- **Performance Enhancements in `@cmmv/server`**:
  Optimized various server components, resulting in faster response times and improved throughput.

- **Benchmark Updates**:
  Updated benchmarking results to reflect performance improvements and showcase optimizations across the server stack.

- **Request Handling Fixes in `@cmmv/http`**:
  Improved middleware import handling and request processing for better stability and extensibility.

- **`scriptsTimestamp` and Cache Control in `@cmmv/view`**:
  - Added `scriptsTimestamp` configuration to allow timestamp-based script handling.
  - Integrated script modification time (mtime) in query requests to bypass cache headers and ensure up-to-date resources.

### Removals

- **Vite Removal from `@cmmv/http`**:
  Completely removed Vite from the HTTP module to streamline dependencies and enhance flexibility.

- **Decoupling of `@cmmv/view` from `@cmmv/http`**:
  `@cmmv/view` is now fully decoupled, making it an optional module for server-side rendering, offering more modularity.

### Recommendations

- **Update to the Latest Server Version**:
  To benefit from performance improvements and fixes, update to `@cmmv/server` version `0.8.7`.

- **Refactor Vue Integrations**:
  With the decoupling of `@cmmv/view`, ensure proper adjustments in your project if Vue is being utilized.

### Updates

- **Documentation Refresh**:
  The documentation has been updated to reflect recent changes, providing clear guidelines on new features and improvements.

## Version 0.8.0
December 10, 2024

### Critical Fixes

- **Infinite Loop on Non-Existent Routes**:
  Resolved a critical issue where non-existent routes caused an infinite request loop, improving server stability.

### Additions

- **Groups and Root Access in Dev Mode**:
  - Added support for user groups, enabling more granular control over permissions and roles.
  - Introduced root access capabilities in development mode, simplifying debugging and testing scenarios.

- **Root Parameter**:
  Added a new `rootParam` feature to streamline configuration and enhance flexibility for root-level operations.

### Improvements

- **Server Update to Version 0.7.1**:
  Upgraded the server core to version `0.7.1`, bringing optimizations and improved compatibility with the latest modules.

- **Service Updates**:
  Enhanced the service layer with better error handling, improved request processing, and support for dynamic root-level configurations.

- **@cmmv/auth Module**:
  Fixed incorrect HTTP 401 response handling to ensure proper authentication flow and error messaging.

### Removals

- **Vue from `@cmmv/view`**:
  Vue has been completely removed from `@cmmv/view`. Developers should now use the dedicated `@cmmv/vue` module for all Vue-related integrations.

### Recommendations

- **Use `@cmmv/vue` for Vue Integrations**:
  Transition to the `@cmmv/vue` module for seamless Vue support, including RPC mixins and composables.

- **Update to Server 0.7.1**:
  Ensure your application is updated to the latest server version to leverage new fixes and features.

### Updates

- **Documentation**:
  Updated documentation to reflect the changes and provide guidance on using new features and best practices.


## Version 0.7.5
December 10, 2024

### Additions

- **`@cmmv/testing` Module**:
  Introduced a new module to simplify testing CMMV applications, providing utilities for mocking modules, services, and contracts.

- **Configuration Validator**:
  Added a validation system for application configurations, ensuring correctness and consistency across environments.

- **Schema for Configurations**:
  Introduced schema definitions for validating and structuring configurations in a standardized format.

### Improvements

- **Refactored Core Modules**:
  Simplified core modules for improved maintainability, reducing complexity while retaining all essential functionality.

- **Pagination, Sorting, and Ordering**:
  Added built-in support for pagination, ordering, and sorting to GET routes and gateways, streamlining data retrieval and API design.

- **Dependency Cleanup**:
  Removed unused dependencies and legacy functions, reducing the framework's footprint and improving overall efficiency.

- **Modular HTTP Adapters**:
  The Express and Fastify modules, previously integrated into `@cmmv/http`, have been decoupled into standalone modules:
  - `@cmmv/express`
  - `@cmmv/fastify`
  This change reduces the dependency footprint of the core, allowing developers to choose and install only the HTTP adapter they need.

### Removals

- **Vue and TailwindCSS Integration**:
  Vue and TailwindCSS were removed from the core module. Use Vite for frontend asset management and the `@cmmv/vue` module for Vue-specific integrations.

### Recommendations

- **Vite Integration**:
  Leverage Vite for modern frontend development workflows, including Vue and TailwindCSS.

- **`@cmmv/vue` Module**:
  Utilize the `@cmmv/vue` module to generate RPC mixins and composables for seamless Vue integration.

### Updates

- **Documentation**:
  Revised and expanded documentation to reflect the new features, removals, and recommended practices.

## Version 0.6.0
November 26, 2024

### New Features

- **Inspector Module**:
  Added the `@cmmv/inspector` module for debugging and monitoring CMMV applications. The module provides runtime insights, performance profiling, and analysis of critical bottlenecks.

### Improvements

- **Performance Optimization**:
  Using insights from the Inspector module's profiling, critical server-side functions were optimized. This resulted in a performance increase from an average of 71.1k to 79k requests per second in benchmark tests, an improvement of approximately 8-10% in response times.
- **Documentation Update**:
  Updated documentation to include the new Inspector module and its usage in the ecosystem.
- **Benchmark Revamp**:
  Re-performed and documented benchmarks with the optimized server, reflecting the improved performance metrics.

### Changes

- **Removed Fast-JSON-Stringify from Samples**:
  Removed `fast-json-stringify` from samples as the schema needs to be processed during application startup. This feature will be revisited and potentially optimized in future releases.

### Notes for Upcoming Versions

- Plan to integrate schema processing during the application's startup to improve the usability of `fast-json-stringify` without performance degradation.

## Version 0.5.5
November 16, 2024

### New Features

- **Vite Middleware Integration**:
  Added Vite as a middleware to serve static files during development, improving Vue 3 integration and the development experience.
- **RPC Mixins for Vue 3 and Nuxt**:
  Introduced RPC mixins generated from CMMV contracts for seamless Vue 3 and Nuxt integration.
- **Complete RPC Implementation**:
  Implemented full RPC support for Vue and Nuxt, enabling dynamic contract-based client-server communication.
- **Vue 3 + TailwindCSS Import Support**:
  Enhanced the `view` module to support Vue 3 and TailwindCSS imports for modern frontend development workflows.

### Improvements

- **Fast-JSON-Stringify**:
  Replaced standard JSON serialization with `fast-json-stringify`, significantly improving serialization performance.
- **Switch to Vitest**:
  Migrated from Mocha to Vitest for faster, more modern testing and improved TypeScript integration.
- **Sample To-Do List Application**:
  Added a fully functional To-Do List example showcasing Vue 3, Vite, and RPC mixins generated by CMMV.

### Bug Fixes

- **MongoDB Repository Fixes**:
  Resolved critical bugs in the `repository` module for MongoDB, ensuring stable database interactions.
- **Deprecated Dependency Updates**:
  Updated deprecated dependencies across the framework, improving compatibility and removing security warnings.
- **View Module Enhancements**:
  Fixed issues with static imports in the `view` module to better handle Vue 3 and TailwindCSS assets.

### Dependency Updates
- Updated dependencies across the framework, ensuring compatibility with the latest ecosystem versions and tools.


## Version 0.5.0
November 5, 2024

### **Added**
- **Vue 3 Support**: Full implementation of Vue 3, configurable via `.cmmv.config.cjs`, enabling usage of Vue 3 syntax. Integrated SSR configurations for optimized performance.
- **Dynamic Layout and View Loading**: Added support for dynamic loading of layouts and views, including integration with Vite to efficiently serve `.vue` files.
- **Data Binding and Setup Configuration**: Introduced `s-setup` for configuring scripts and reactive data in views, with support for lifecycle hooks such as `mounted` and `created`.
- **New Lifecycle Hooks**: Added `mounted` and `created` hooks in view setup scripts, providing flexibility for executing code when the view is loaded.
- **Include Directive**: A new directive for modularly importing and reusing view components across different pages.
- **Telemetry Service**: Added a `Telemetry` class to monitor performance and capture runtime metrics across the system.
- **Method Injection**: RPC functions are now automatically injected as methods in Vue components, simplifying remote function calls in the frontend.

### **Changed**
- **Improved View Rendering**: Optimized the view rendering system with HTML minification and inline script extraction to enhance performance.
- **Enhanced Error Handling**: Improved error handling to ensure proper logging of failures with more informative error messages.

## Version 0.4.0
October 15, 2024

### **Added**
- **RPC Support with Protobuf**: Introduced integration for Protobuf to enable binary communication through WebSocket, enhancing real-time capabilities.
- **WebSocket Adapter**: Added WebSocket support with a dedicated adapter for real-time communication, including contract-based RPC generation.
- **Caching Module**: Integrated the caching module (`@cmmv/cache`) for high-performance data storage, supporting Redis and in-memory options.
- **Repository Module**: Added support for repository-based database management with integration for TypeORM and MongoDB.
- **Contract-Based Auto-Generation**: Controllers, entities, and services are now automatically generated from TypeScript contracts, reducing boilerplate code.
- **Dynamic Configuration**: Added support for dynamic configuration using `.cmmv.config.cjs`, allowing runtime adjustments without modifying source code.

### **Changed**
- **Improved CLI**: Enhanced the CLI to simplify project initialization, with customizable settings for RPC, caching, and repositories.
- **Streamlined Directory Structure**: Revised the default project structure for better organization, separating public assets, views, and templates.
- **Improved Module Integration**: Refined integration of modules like `@cmmv/repository` and `@cmmv/view` to ensure seamless compatibility with auto-generated components.

### **Fixed**
- Resolved issues with contract validation during application startup.
- Fixed database synchronization bugs for SQLite and MongoDB configurations.

## Version 0.3.5
September 28, 2024

### **Features**
- **Contract Generator**: Automates the creation of contracts between server and client, ensuring consistent API interfaces and reducing manual coding efforts.
- **Enhanced WebSocket Support**: Implemented WebSocket integration for real-time RPC communications using Protobuf, optimizing data transmission efficiency and reliability.

### **Improvements**
- **Performance Optimization**: Significant improvements in processing time for contract generation and execution, reducing latency in high-load scenarios.
- **Modular Design**: Restructured the core framework to support better module isolation, enabling easier extension or modification without impacting the entire system.
- **Scalability Enhancements**: Fine-tuned the core system to handle increased traffic and larger data payloads with minimal performance impact.

### **Bug Fixes**
- **Lint Corrections**: Addressed all linting issues across the codebase to ensure clean and maintainable code.
- **Test Fixes**: Resolved various issues to improve the reliability of unit and integration tests.
- **WebSocket Import Fix**: Corrected import issues in the WebSocket module to ensure proper initialization and functionality.
- **CircleCI Configuration**: Integrated and fixed issues with CircleCI for continuous integration and deployment pipelines.
- **Miscellaneous Fixes**: Various minor bug fixes and code improvements throughout the framework.

### **Documentation and Examples**
- **Advanced Contract Setup**: Updated documentation for advanced contract configuration and API generation, providing clearer and more detailed examples for developers.

### **Testing and Quality**
- **Expanded Test Coverage**: Added unit tests for critical modules to improve the overall stability and reliability of the framework.
- **Stress Testing**: Simulated high-traffic environments to validate the robustness of real-time communication and ensure scalability under load.
