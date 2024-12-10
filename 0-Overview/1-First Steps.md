# First steps

CMMV (Contract Model Model View) is a revolution in web application development, breaking paradigms and redefining how we create, maintain, and scale digital projects. Inspired by best practices and innovative concepts, CMMV integrates the power of contracts to automatically generate robust and secure structures, eliminating the complexity of manual code and providing an unprecedented development experience.

Imagine a platform where the definition of contracts in TypeScript becomes the heart of your application, automatically generating APIs, controllers, ORM entities, and even communication via binary RPC, all with optimized performance and seamless integration with the most modern technologies. With CMMV, you not only accelerate development, but also ensure the quality and consistency of your code, drastically reducing errors and rework.

In addition, CMMV offers a reactive and lightweight interface, based on Vue 3, but with the ability to support other frameworks such as React and Angular, always focusing on performance and SEO. With CMMV, the frontend is not just a presentation layer, but an integral and dynamic part of your application, synchronized in real time with the backend.

Whether you are an experienced developer or a programming newbie, CMMV empowers everyone to build powerful, scalable, modern systems by eliminating technical barriers and allowing creativity and innovation to be at the center of your development journey. It is more than a framework; it is a new way of thinking and building the future of web applications.

## Prerequisites

To run CMMV it will be necessary to have ``Node.js (version >= 18.0)`` installed in your operating system.

## Setup with CLI

CMMV now provides a CLI (Command Line Interface) to streamline the installation process and quickly set up your project with the desired configurations.

To initialize a new project, you can use the following command:

```bash
$ pnpm dlx @cmmv/cli@latest init <project-name>
```

This command will walk you through a guided setup process, asking about your preferred configurations, such as enabling Vite, RPC, caching, repository type, and view setup (e.g., Vue 3 or Reactivity). It will automatically create the necessary files and folders, set up dependencies, and configure the project.

## Legacy Setup (Manual)

If you prefer to set up the project manually, you can still install the necessary modules individually:

```bash
$ pnpm add @cmmv/core @cmmv/http @cmmv/view rxjs reflect-metadata class-validator class-transformer fast-json-stringify
```

## Structure

CMMV is a project that draws inspiration from some of the best practices and technologies in modern development, bringing solid references to create a powerful and flexible framework. At the heart of CMMV are contracts, which follow a model similar to TypeORM and Protobuf, but go further, offering a full range of configurations that allow the automatic generation of the entire base structure of the application. These contracts are the fundamental piece of the project, as from them it is possible to create everything from database entities to APIs and controllers.

Written in TypeScript, CMMV adopts a backend structure that resembles NestJS, but with adjustments that make dependency injection and module control more intuitive and adapted to modern development needs. The idea is to offer a modular system, where developers have full control over the application layers, allowing the architecture to adapt to the specific demands of each project.

On the frontend, CMMV incorporates a basic data binding controller inspired by Vue 3, but simplified and extremely lightweight. This reactive layer can be easily replaced by any other framework, such as React, Angular or even Vue itself, ensuring that the application maintains flexibility and performance, without sacrificing simplicity.

The project is strongly influenced by SOLID principles, applying well-defined concepts of responsibility, but without rigidly following all the rules. The goal is to keep the architecture clean and organized, but with the flexibility necessary to meet the practical realities of development. In addition, TDD (Test-Driven Development) concepts are applied to facilitate maintenance and ensure code quality, but the system is completely modular and optional, allowing each developer to choose what best suits their workflow.

In short, CMMV is more than a collection of tools and frameworks; it is an integrated approach to development, where contracts are at the core of everything. With a solid foundation, CMMV gives developers the freedom to build modern and scalable applications, without compromising the simplicity and flexibility that are essential for continuous innovation.

Every application flow starts in the `src/index.ts` file which is responsible for starting the HTTP server and its adapters.

```typescript
import { Application } from "@cmmv/core";
import { DefaultAdapter, DefaultHTTPModule } from "@cmmv/http";
import { ProtobufModule } from "@cmmv/protobuf";
import { WSModule, WSAdapter } from "@cmmv/ws";
import { ViewModule } from "@cmmv/view";
import { RepositoryModule, Repository } from "@cmmv/repository";
import { ApplicationModule } from "./app.module";

Application.create({
    httpAdapter: DefaultAdapter,    
    wsAdapter: WSAdapter,
    modules: [
        DefaultHTTPModule,
        ProtobufModule,
        WSModule,
        ViewModule,
        RepositoryModule
    ],
    services: [Repository],
    contracts: [...]
});
```

It is possible to perform configurations through the ``.cmmv.config.js`` file without having to change the application source code, even in the future we will provide other HTTP server modules.

The default directories that come with the project are the following:
```
.
└── public/
    ├── assets/
    ├── templates/
    └── views/
└── src/
    ├── contracts/
    ├── controllers/
    ├── entities/
    ├── models/
    ├── services/
    ├── app.module.ts
    └── index.ts
```

## Structure Explanation

- **public/**: This directory contains all the static resources of the application, such as CSS, JavaScript and images. Within `public`, we have:
- **assets/**: Stores static files such as styles, scripts, images and other resources needed for the user interface.
- **templates/**: Contains HTML template files that can be used as base layouts for views.
- **views/**: Contains the pages or UI components that will be rendered on the frontend.

- **src/**: The `src` directory is the core of the application, where the source code is organized in a modular way to facilitate development and maintenance. In it, we find:
- **contracts/**: This is the most important directory, as it is where the application contracts are defined. When starting the application, the system checks the contracts present in this directory and, from them, automatically generates the controllers, entities, models and services. These contracts serve as the basis of the entire application structure.

- **controllers/**: After verifying the contracts, controllers are automatically generated in this directory. They are responsible for handling HTTP requests, processing data, and returning appropriate responses.

- **entities/**: If the `repository` module is present, this directory will house the entities generated in TypeORM format, which represent the database tables and serve as object-relational mapping (ORM).

- **models/**: Also generated from contracts, models are used to define the structure of the data that will be manipulated by the application, including data validation and transformation.

- **services/**: Services encapsulate the business logic and are responsible for manipulating entities and interacting with other application modules. They are automatically generated based on the contracts and installed modules.

- **app.module.ts**: This file is the root module of the application, configuring the main services and modules necessary for the application to function.

- **index.ts**: The application entry point, where the server is initialized and configured to start receiving requests.

## Automatic Generate

CMMV's greatest strength lies in its ability to **automatically generate** code based on contracts. When starting the application, the system checks the `contracts` directory and, depending on the installed modules, automatically generates the following parts:

- **Controllers, Entities, Models and Services**: Based on the defined contracts, the controllers, entities, models and services required for the application are created. If the `repository` module is present, the entities and models will be generated in TypeORM format and the connection to the database will be configured according to the information in the `.cmmv.config.js` file.

- **RPC support with Protobuf**: If the `protobuf` module is present, `.proto` contracts will be automatically generated for RPC (Remote Procedure Call) communication. This communication is done through a WebSocket adapter, integrating the HTTP and WebSocket server on the same port, facilitating real-time communication.

- **HTTP Server Modularity**: Currently, the HTTP module supports Express and Fastify, allowing developers to choose the framework that best suits their needs. In the future, new modules may be added to provide additional support for other frameworks.

This modular approach ensures that CMMV easily adapts to the needs of the project, maintaining flexibility and scalability without compromising simplicity. The **core** of the application resides in the contracts, allowing the entire system to be configured and expanded in an efficient and automated way.

## Static files

The recommendation regarding static files is to always use the CDN integration features available in the editor, as whenever the application is built all static files will be automatically uploaded and updated on the CDN, however if you want to maintain the availability of the files being served by the application, the system is configured to map a directory /public, if this directory exists in the export and deployment of the application, its files will be copied automatically, becoming available for public access without access control.

## Dev mode

To start the application in development mode you can use the following command. 

```bash
$ pnpm dev
```

In this way, the initial application watches in parallel with the nodemon that checks for changes in the files and automatically reloads in case of changes, the server that provides the API will be loaded following the pure Typescript settings, the editor will be compiled for Javascript.