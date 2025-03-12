# CLI

The CMMV CLI simplifies project initialization by providing an interactive way to create a new project with customizable configurations. Below is the updated documentation for using the CLI to generate a CMMV project.

<div style="
    background-color: #DBEAFE;
    border-left: 4px solid #2563EB;
    color: #1E3A8A;
    padding: 1rem;
    border-radius: 0.375rem;
    margin: 1.5rem 0;
    font-size: 12px;
">
    <p style="font-weight: bold; margin-bottom: 0.5rem;">Notice</p>
    <p>
        Recent updates to <strong>PNPM</strong> may require manual approval for modules with build scripts, affecting packages such as
        <strong>better-sqlite3-multiple-ciphers</strong>, <strong>esbuild</strong>, <strong>protobufjs</strong>, <strong>sqlite3</strong>, among others.
    </p>
    <p>
        A fix for this issue has been implemented starting from <strong>version 0.8.20</strong>. However, if your project is using an older version,
        you can manually resolve this by adding the following configuration to your <code>.npmrc</code> file:
    </p>
    <pre style="
        background-color: #F3F4F6;
        padding: 0.75rem;
        border-radius: 0.375rem;
        overflow-x: auto;
    ">
auto-install-peers=true
approve-builds=always</pre>
    <p>
        This will ensure that all necessary builds are automatically approved during installation, preventing issues related to manual approval prompts.
    </p>
</div>

## Getting Started

Install the CLI Globally: To use the CLI globally on your system, install it using ``pnpm``:

```bash
$ pnpm add -g @cmmv/cli@latest
```

Create a New Project: Run the ``cmmv create`` command to create a new project:

```bash
$ cmmv create project-name
```

This will start an interactive prompt asking for your project preferences, such as:

* Whether to enable Vite Middleware
* Use RPC (WebSocket + Protobuf)
* Enable the Cache module
* Select the repository type (SQLite, MongoDB, PostgreSQL, MySQL, MSSQL, Oracle)
* Select the cache type (Redis, Memcached, MongoDB, File System)
* Select the queue type (Redis, RabbitMQ, Kafka)
* Enable ESLint, Prettier, and Vitest

## Using

If you don't want to install the CLI globally, use ``pnpm dlx`` to execute it directly:

```bash
$ pnpm dlx @cmmv/cli@latest create project-name
```

This ensures you always use the latest version of the CLI without requiring a global installation.

## Changes in Version 5.9

The CMMV CLI has been refactored, introducing new commands and streamlining project workflows. Updating from previous versions is highly recommended.

<div style="
    background-color: #DBEAFE;
    border-left: 4px solid #3B82F6;
    color: #1E40AF;
    padding: 1rem;
    border-radius: 0.375rem;
    margin: 1.5rem 0;
    font-size: 12px;
">
    <p style="font-weight: bold; margin-bottom: 0.5rem;">Notice</p>
    <p>
        Starting from version <strong>v.0.5.9</strong>, the <strong>@cmmv/cli</strong> has been fully refactored, introducing new commands and streamlining project workflows.
        It is highly recommended to update to this version for enhanced performance and usability.
    </p>
    <p>
        The CLI now includes built-in support for <strong>ESLint</strong>, <strong>module releases</strong>, and an improved <strong>build system</strong> with both <strong>ESM</strong> and <strong>CJS</strong> support.
        Additionally, <strong>hot reload</strong> for development and a new <strong>run command</strong> for executing scripts are now available.
    </p>
</div>

### New Features

* Integrated ESLint: No need for separate dependencies—run `$ cmmv lint`.
* Module Release Automation: `$ cmmv release` now handles all dependencies automatically.
* Improved Build System: Supports ESM and CJS builds: `$ cmmv build`
* Enhanced Development Mode:
    * Built-in watch and debug without `nodemon`
    * Hot reload support using `$ cmmv dev`
    * Example configuration:

```json
"dev": {
    "watch": ["src", "docs"],
    "ignore": ["**/*.spec.ts", "src/app.module.ts", "docs/**/*.html"]
}
```
* Production Start Command: `$ cmmv start`
* Script Execution: `$ cmmv run ./src/<script>.ts`
* Performance Boost:
    * `@swc-node/register` replaces `ts-node` for dev and run.
    * `tsc` remains the default for production builds.

* ESLint Configuration:
    * Uses ESLint 9.20 with Prettier.
    * Configurable via `eslint.config.cjs` (or `eslint.config.ts` for ESM projects).

## Generated Project Structure

The CLI generates a structured project folder with necessary files and directories based on your preferences. Below is an example structure:

```
.
├── .generated/
├── src/
│   ├── app.controller.ts
│   ├── app.module.ts
│   ├── app.service.ts
│   └── main.ts
├── tests/
│   ├── app.controller.spec.ts
│   ├── app.module.spec.ts
│   └── app.service.spec.ts
├── tsconfig.json
├── eslint.config.cjs
├── .cmmv.config.cjs
├── package.json
├── .gitignore
└── ...
```

**Generated Configuration Files**
* ``.cmmv.config.cjs``: Central configuration for the CMMV application.
* ``package.json``: Includes necessary dependencies and scripts based on your selected options.
* ``tsconfig.json``: References for TypeScript configurations.
* ``.gitignore``, ``.npmignore``, ``.prettierignore``, ``.prettierrc``, ``.swcrc``: Pre-configured files for development standards.

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

Run ESLint:

```bash
$ pnpm lint
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
│   ├── main.ts
├── tests/
│   └── main.spec.ts
├── .gitignore
├── .npmignore
├── eslint.config.cjs
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
        "node": ">=20.0.0"
    },
    "scripts": {
        "build": "cmmv build",
        "lint": "cmmv lint",
        "release": "cmmv release",
        "test": "vitest",
        "prepare": "husky install",
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
$ cmmv contract contract-name
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
