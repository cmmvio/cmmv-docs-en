# Sandbox

The `@cmmv/sandbox` module is a visual support system introduced in version 0.9.11 of CMMV. It is designed to simplify contract management and showcase the full potential of a *contract-first* approach in your application. With Sandbox, you can configure the system, create and edit contracts, validate REST and GraphQL implementations, create application backups, access logs, install CMMV modules, and more. Additionally, the module includes an initial version of a *form builder* that allows you to customize contract-derived form screens for use in your dashboards.

The Sandbox aims to streamline contract administration while demonstrating the comprehensive capabilities of CMMV through a practical and accessible interface. To use it, simply install it in your project and include it in your application setup.

<div class="relative overflow-hidden pt-2 mb-4">
    <div class="container mx-auto px-4">
        <div class="max-w-5xl mx-auto">
            <img src="/assets/publish-example-dark.png" alt="CMMV Framework Overview" class="rounded-xl shadow-2xl ring-1 ring-white/20 w-full">
            <div class="relative" aria-hidden="true">
                <div class="absolute -inset-x-20 bottom-0 bg-gradient-to-t from-neutral-900 pt-[7%]"></div>
            </div>
        </div>
    </div>
</div>

## Installation

To install `@cmmv/sandbox` and its required dependencies, run:

```bash
$ pnpm add @cmmv/sandbox @cmmv/repository @cmmv/http @cmmv/graphql @cmmv/auth
```

The Sandbox depends on the `@cmmv/repository`, `@cmmv/http`, `@cmmv/graphql`, and `@cmmv/auth` modules to function properly. Ensure all are included in your project.

## Configuration

To enable Sandbox support in your CMMV application, add the `SandboxModule` to your application setup:

```typescript
import { Application } from '@cmmv/core';
import { DefaultAdapter, DefaultHTTPModule } from '@cmmv/http';
import { RepositoryModule, Repository } from '@cmmv/repository';
import { AuthModule } from '@cmmv/auth';
import { GraphQLModule } from '@cmmv/graphql';
import { SandboxModule } from '@cmmv/sandbox';

Application.create({
    httpAdapter: DefaultAdapter,
    modules: [
        DefaultHTTPModule,
        RepositoryModule,
        AuthModule,
        GraphQLModule,
        SandboxModule,
        // Other modules, if needed
    ],
    providers: [Repository],
});
```

After configuration, the Sandbox will be available in your application, providing access to its features through an integrated visual interface.

## Features

The `@cmmv/sandbox` module offers the following key features:

- **System Configuration**: Adjust the application's general settings directly through the interface.
- **Contract Management**: Create, edit, and view contracts intuitively.
- **Implementation Validation**: Test and validate REST and GraphQL endpoints generated from contracts.
- **Backups**: Create full application backups for security and recovery.
- **Logs**: Access detailed system logs for monitoring and debugging.
- **Module Installation**: Install additional CMMV modules directly via the Sandbox.
- **Form Builder**: Customize contract-derived forms for use in dashboards (initial version).

The Sandbox integrates with the CMMV ecosystem, leveraging existing modules to provide a unified and efficient experience.

## Usage

After installation and configuration, start your CMMV application. Access the Sandbox route (default: `/sandbox`) in your browser to explore the visual interface. From there, you can:

1. Configure the system and view existing contracts.
2. Create or edit contracts and test their REST and GraphQL implementations.
3. Use the *form builder* to customize contract-based forms.
4. Manage backups and review logs directly in the interface.

The Sandbox is designed to be an *all-in-one* tool for developers using CMMV, offering a practical way to manage and extend their applications.