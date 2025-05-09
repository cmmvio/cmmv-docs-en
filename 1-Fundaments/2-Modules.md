# Modules

The CMMV module system shares similarities with NestJS, providing a modular approach to application development. However, it differs in one key aspect: no dependency context control. This means that services are not injected or managed by a centralized dependency injection container. Instead, any provider (service, utility, etc.) can be created independently and added to modules where needed. For shared services across the system, such as the Repository or Config classes, it's recommended to use singleton registries, making these services globally accessible without the need for repeated instantiation.

## Key Features

* **No Dependency Context Control:** Services are not automatically injected. Instead, they can be created and indexed within the module system.
* **Singleton Registries:** Shared services can be registered as singletons to avoid duplication and make them accessible across the entire system.
* **Module Composition:** Modules in CMMV are flexible, allowing you to define controllers, services (providers), transpilers, contracts, and submodules within a single module.
* **Automatic Module Generation:** The system generates the main module (e.g., ``app.module.ts``) based on contracts, controllers, and gateways defined within the application.

## Example

Here’s an example of a full-featured module with all possible properties in CMMV:

```typescript
import { Module } from '@cmmv/core';
import { DocsController } from './docs.controller';
import { DocsService } from './docs.service';
import { DocsTranspile } from './docs.transpile';
import { DocsContract } from './docs.contract';
import { SubModule } from './submodule';

export let DocsModule = new Module({
    controllers: [DocsController],
    providers: [DocsService],
    transpilers: [DocsTranspile],
    submodules: [SubModule],
    contracts: [DocsContract]
});
```

<br/>

* **controllers:** An array of controllers that handle HTTP requests and return responses. They manage the application's routes and are responsible for interacting with services.
* **providers:** A list of services or classes that contain the business logic for your module. These providers are manually instantiated and indexed when needed.
* **transpilers:** These are responsible for generating necessary files, such as database entities, Protobuf definitions, or other code artifacts based on contracts.
* **submodules:** Allows you to break your application into smaller, self-contained modules that can be nested within other modules.
* **contracts:** Defines the contracts associated with this module. Contracts define the data structure and behavior of entities, and transpilers generate the appropriate files (e.g., database models, API endpoints).

## Singleton Registries

Instead of injecting shared services across multiple modules, singleton registries can be used for services like ``Repository`` or ``Config``, which are intended to be accessible globally. This reduces the need for dependency injection and ensures that these services are instantiated only once and shared throughout the system.

## Application

Whenever contracts are defined, the system will automatically create a module at ``/src/app.module.ts``. This module will include all contracts, controllers, and gateways automatically generated by the application. Here is an example of how the file might be generated:

```typescript
// Generated automatically by CMMV

import { Module } from '@cmmv/core';
import { TaskController } from './controllers/task.controller';
import { TaskService } from './services/task.service';
import { TaskGateway } from './gateways/task.gateway';

export let ApplicationModule = new Module({
    controllers: [TaskController],
    providers: [TaskService, TaskGateway]
});
```

It is important to note that the file ``/src/app.module.ts`` is automatically generated by the system and should not be manually modified, as it will be recreated every time the application is started. Any changes made directly to this file will be overwritten. If you need to add or modify modules, services, or controllers, it is recommended to do so in separate files and register them properly within the application to ensure your modifications are preserved.

## Modular Design

CMMV follows a modular design, allowing you to break down your application into reusable and maintainable parts. Each module can focus on a specific domain or feature, which improves separation of concerns, maintainability, and scalability.

* **Feature-Oriented Structure:** Group controllers, services, and other components related to the same feature together in a module.
* **Reusability:** Modules can be reused across different parts of the application, and submodules can help break down complex functionality into simpler units.
* **Transpilers for Automation:** Transpilers automate repetitive tasks, like generating files based on contracts, minimizing manual coding and ensuring consistency.

## Best Practices

* **Use Singleton Registries:** For shared services (e.g., configuration, repository), use singleton registries to avoid repeated instantiation and simplify access.
* **Define Modules for Each Feature:** Organize your application by defining a separate module for each feature or domain, making it easier to manage and scale.
* **Automatic Contracts and Services:** Let the CMMV framework handle the generation of services, controllers, and gateways based on contracts to reduce manual boilerplate code.
* **Avoid Modifying Auto-Generated Files:** Files like ``app.module.ts`` are auto-generated and should not be manually edited. Always make your modifications in separate modules or services.

The CMMV module system provides a flexible and powerful way to structure your application, allowing for efficient handling of controllers, services, and transpilers. By leveraging features like singleton registries and automatic file generation through contracts, CMMV helps streamline the development process, reduce manual configuration, and ensure that the application remains scalable and maintainable.
