# CLI

The CMMV CLI simplifies project initialization by providing an interactive way to create a new project with customizable configurations. Below is the updated documentation for using the CLI to generate a CMMV project.

## Getting Started

Install the CLI Globally: To use the CLI globally on your system, install it using ``pnpm``:

```bash 
$ pnpm add -g @cmmv/cli
```

Create a New Project: Run the ``cmmv create`` command to create a new project:

```bash
$ cmmv create <project-name>
```

This will start an interactive prompt asking for your project preferences, such as:

* Whether to enable Vite Middleware
* Use RPC (WebSocket)
* Enable the Cache module
* Select the repository type (SQLite, MongoDB, PostgreSQL, MySQL)
* Choose the View configuration (Reactivity, Vue3, or Vue3 + TailwindCSS)
* Enable ESLint, Prettier, and Vitest

## Using ``pnpm dlx``

If you don't want to install the CLI globally, use ``pnpm dlx`` to execute it directly:

```bash
$ pnpm dlx @cmmv/cli@latest create <project-name>
```

This ensures you always use the latest version of the CLI without requiring a global installation.

## Generated Project Structure

The CLI generates a structured project folder with necessary files and directories based on your preferences. Below is an example structure:

```
.
├── public/
│   ├── assets/
│   │   └── protobuf.min.js (if RPC is enabled)
│   ├── core/
│   ├── templates/
│   └── views/
├── src/
│   ├── contracts/
│   ├── controllers/
│   ├── entities/
│   ├── models/
│   ├── modules/
│   ├── services/
│   ├── locale/
│   ├── app.module.ts
│   ├── server.ts
│   └── client.ts (if Vite is enabled)
├── node_modules/
├── index.html (if Vite is enabled)
├── tailwind.config.js (if TailwindCSS is enabled)
├── tsconfig.json
├── tsconfig.client.json (if Vite is enabled)
├── tsconfig.vue.json (if Vue3 or Vue3 + TailwindCSS is enabled)
├── vite.config.js (if Vite is enabled)
├── vitest.config.ts (if Vitest is enabled)
├── .cmmv.config.js
├── package.json
├── .gitignore
└── ...
```

**Generated Configuration Files**
* ``.cmmv.config.js``: Central configuration for the CMMV application.
* ``package.json``: Includes necessary dependencies and scripts based on your selected options.
* ``tsconfig.json``: References for TypeScript configurations.
* ``.gitignore``, ``.npmignore``, ``.prettierignore``, ``.prettierrc``, ``.swcrc``: Pre-configured files for development standards.
* ``vite.config.js``: Vite configuration (if enabled).
* ``tailwind.config.js`` and ``src/tailwind.css``: TailwindCSS configuration (if enabled).

## Available Scripts

Development Mode:

```bash
$ pnpm dev
```

Build for Production:

```bash
$ pnpm build
```

Start Production Server:

```bash
$ pnpm start
```

Run Tests (if Vitest is enabled):

```bash
$ pnpm test
```

## Module 

The CMMV CLI now includes a ``module`` command to simplify the creation of new modules within an existing CMMV project. Modules help organize your application into reusable and feature-specific units. Below is the documentation for the ``module`` command.

```bash
$ cmmv module <module-name>
```

## Generated Module Structure

```bash
module/
├── src/
│   ├── index.ts                # Main entry point for the module
├── scripts/
│   └── release.js (if release is enabled)
├── tests/
│   └── index.test.ts (if vitest is enabled)
├── .gitignore
├── .npmignore
├── .swcrc
├── tsconfig.json
├── tsconfig.cjs.json
├── tsconfig.esm.json
├── package.json
└── ...
```

The generated package.json includes essential metadata and scripts for the module:

```json
{
    "name": "module",
    "version": "0.0.1",
    "description": "",
    "keywords": [],
    "author": "",
    "publishConfig": {
        "access": "public"
    },
    "engines": {
        "node": ">=18.18.0 || >=20.0.0"
    },
    "scripts": {
        "build:cjs": "tsc --project tsconfig.cjs.json",
        "build:esm": "tsc --project tsconfig.esm.json",
        "build": "npm run build:cjs && npm run build:esm",
        "test": "vitest",
        "prepare": "husky install",
        "lint": "pnpm run lint:spec",
        "lint:fix": "pnpm run lint:spec -- --fix",
        "release": "node scripts/release.js",
        "changelog": "conventional-changelog -p angular -i CHANGELOG.md -s"
    },
    "devDependencies": {
        ...
    },
    "dependencies": {
        "@cmmv/core": "^1.0.0"
    }
}
```

## Contract

The `cmmv contract` command allows you to create contracts in the CMMV framework. Contracts define the structure, validation, and metadata for your application's entities and controllers. Below is the documentation for using the `contract` command.

### Usage

To create a contract, use the following command:

```bash
$ cmmv contract <contract-name>
```

This will launch an interactive prompt to configure your contract. You can define the contract's name, metadata, fields, and validation rules.

### Interactive Prompts

The CLI will prompt you for the following details:

* **Contract Metadata:** Configurations such as controller name, proto path, proto package, caching, and imports.
* **Fields:** Add fields to the contract with properties like proto type, default value, validations, and more.

### Contract Structure

Once the contract is created, it will be added to the src/contracts directory with the following structure:

```bash
src/contracts/
├── <contract-name>.contract.ts
```

### Example

```typescript
import { AbstractContract, Contract, ContractField } from '@cmmv/core';

@Contract({
    controllerName: 'Task',
    protoPath: 'src/protos/task.proto',
    protoPackage: 'task',
    imports: ['crypto'],
    cache: {
        key: 'task:',
        ttl: 300,
        compress: true,
    },
})
export class TasksContract extends AbstractContract {
    @ContractField({
        protoType: 'string',
        unique: true,
        validations: [
            {
                type: 'IsString',
                message: 'Invalid label',
            },
            {
                type: 'IsNotEmpty',
                message: 'Invalid label',
            },
        ],
    })
    label: string;

    @ContractField({
        protoType: 'bool',
        defaultValue: false,
        validations: [
            {
                type: 'IsBoolean',
                message: 'Invalid checked type',
            },
        ],
    })
    checked: boolean;

    @ContractField({
        protoType: 'bool',
        defaultValue: false,
        validations: [
            {
                type: 'IsBoolean',
                message: 'Invalid removed type',
            },
        ],
    })
    removed: boolean;

    @ContractField({ protoType: 'date' })
    createdAt?: Date;
}
```

### Integration

To integrate a contract into your application, you need to register it in the `Application` setup within your `index.ts` or `server.ts` or entry point file. This ensures the contract is available for use in your application.

Include the contract in the contracts property of the Application.create configuration. Here's an example:

```typescript
// Imports

import { TasksContract } from './contracts/tasks.contract'; // Register your import here

// Create the application
Application.create({
    httpAdapter: DefaultAdapter,
    wsAdapter: WSAdapter,
    modules: [
        ...
    ],
    services: [...],
    transpilers: [...],
    contracts: [TasksContract], // Register your contract here
});
```