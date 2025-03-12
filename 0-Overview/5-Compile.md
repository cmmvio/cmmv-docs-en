# Compile

Starting from **version 8.5**, CMMV introduces the `compile` function, which allows processing all modules and generating derived files from transpilers that were previously only created when starting the application.

This feature is particularly useful in scenarios where you want to pre-compile all necessary files without actually running an HTTP or RPC application.

## When to Use

You should use `Application.compile` when:

- You need to **generate transpiled files** in advance without starting the application.
- You're preparing a deployment pipeline and want to pre-process contracts and related files.
- You want to ensure that all configurations and modules are correctly processed before running the application.

## How to Use

Instead of using `Application.create` to start the application, simply replace it with `Application.compile`. Below is an example usage:

```typescript
import { Application } from '@cmmv/core';
import { DefaultHTTPModule } from '@cmmv/http';
import { ProtobufModule } from '@cmmv/protobuf';
import { WSModule } from '@cmmv/ws';
import { RepositoryModule, Repository } from '@cmmv/repository';
import { AuthModule } from '@cmmv/auth';

// Contracts
import { TasksContract } from './contracts/tasks.contract';

Application.compile({
    modules: [
        DefaultHTTPModule,
        ProtobufModule,
        WSModule,
        RepositoryModule,
        AuthModule,
    ],
    services: [Repository],
    contracts: [TasksContract],
});
```

### Explanation

- **modules:** A list of application modules, such as HTTP, WebSockets, Protobuf, repository, caching, and authentication.
- **services:** The core services used within the application, such as `Repository`.
- **contracts:** Contracts that define the business logic of the application, such as `TasksContract`.

## Added Script

Starting from version **8.5**, a script has been added to simplify the compilation process by executing necessary cleanup steps and initializing the compilation process.

To compile without running an actual HTTP or RPC server, run the following command:

```bash
./tools/cleanupPackages.sh &&
    pnpm clean &&
    NODE_ENV=dev node -r @swc-node/register ./src/compile.ts
```

<br/>

1. **\`./tools/cleanupPackages.sh\`** – Cleans up unused or outdated package dependencies.
2. **\`pnpm clean\`** – Removes temporary files and builds directories.
4. **\`NODE_ENV=dev\`** – Sets the environment to development mode.
5. **\`node -r @swc-node/register ./src/compile.ts\`** – Compiles the application with SWC for faster execution.

## Running

To trigger the compilation process, you can run the following command:

```bash
$ pnpm run compile
```

This command ensures that all necessary files are prepared and ready for production or further manual verification.

