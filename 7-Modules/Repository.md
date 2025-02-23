# Repository

Repository: [https://github.com/cmmvio/cmmv/tree/main/packages/repository](https://github.com/cmmvio/cmmv/tree/main/packages/repository)

The ``@cmmv/repository`` module is designed to work alongside the ``@cmmv/core`` framework and is built using [TypeORM](https://typeorm.io/). This module automatically generates entities based on defined contracts and extends the service layer for handling CRUD operations with the configured database. Below, we provide detailed information on installation, usage, and features of this module.

**Installation**

To use the ``@cmmv/repository`` module in your project, you can install it via npm:

```bash
$ pnpm add @cmmv/repository
```

In addition to installing the ``@cmmv/repository`` module, depending on the database type you are using, you will need to install the respective database driver.

**Supported Databases and Drivers**

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

TypeORM supports multiple database systems, and each system requires the appropriate npm package to function correctly. Here is a list of supported databases along with the corresponding driver to install:

**SQLite:** [NPM](https://www.npmjs.com/package/sqlite3)

```bash
$ pnpm add sqlite3
```

**MySQL / MariaDB:** [NPM](https://www.npmjs.com/package/mysql2)

```bash
$ pnpm add mysql2
```

**PostgreSQL:** [NPM](https://www.npmjs.com/package/pg)

```bash
$ pnpm add pg
```

**Microsoft SQL Server:** [NPM](https://www.npmjs.com/package/mssql)

```bash
$ pnpm add mssql
```

**Oracle:** [NPM](https://www.npmjs.com/package/oracledb)

```bash
$ pnpm add oracledb
```

**MongoDB:** [NPM](https://www.npmjs.com/package/mongodb)

```bash
$ pnpm add mongodb
```

Once you install the module and the corresponding database driver, you can configure your database connection in the ``.cmmv.config.cjs`` file. TypeORM will automatically use the appropriate driver based on the configuration.

## Purpose

The ``@cmmv/repository`` module simplifies the process of integrating database entities and services for CRUD operations by leveraging contracts defined using ``@cmmv/core``. It automatically transpiles contracts into TypeORM entities and provides a standardized way to manage interactions with the database.

* **Automatic Entity Generation:** Based on the contracts defined in your project, the module transpiles the contract definitions into TypeORM entities.
* **CRUD Service:** The module generates CRUD operations for the entities without needing to manually implement services, allowing easy data management.
* **TypeORM Integration:** Full integration with TypeORM, making it easier to work with a variety of databases, such as PostgreSQL, MySQL, SQLite, etc.
* **Contract-Based:** Using contracts defined with ``@cmmv/core``, the module automatically generates both the database schema and the CRUD service layer.

``/src/contract/task.contract.ts``
```typescript
import { AbstractContract, Contract, ContractField } from '@cmmv/core';

@Contract({
    controllerName: 'Task',
    protoPath: 'src/protos/task.proto',
    protoPackage: 'task',
})
export class TasksContract extends AbstractContract {
    @ContractField({
        protoType: 'string',
        unique: true,
    })
    label: string;

    @ContractField({
        protoType: 'bool',
        defaultValue: false,
    })
    checked: boolean;

    @ContractField({
        protoType: 'bool',
        defaultValue: false,
    })
    removed: boolean;
}
```

This contract defines the fields and structure for the Task entity, which will be transpiled into a database entity by the ``@cmmv/repository`` module.

Once the contract is defined, the ``@cmmv/repository`` module transpiles the contract into a TypeORM entity. Below is an example of an automatically generated entity (``task.entity.ts``):

```typescript
// Generated automatically by CMMV

import { Entity, PrimaryGeneratedColumn, Column, Index } from 'typeorm';
import { Task } from '../models/task.model';

@Entity('task')
@Index("idx_task_label", ["label"], { unique: true })
export class TaskEntity implements Task {
    @PrimaryGeneratedColumn('uuid')
    id: string;

    @Column({ type: 'varchar' })
    label: string;

    @Column({ type: 'boolean', default: false })
    checked: boolean;

    @Column({ type: 'boolean', default: false })
    removed: boolean;
}
```

The ``@cmmv/repository`` module also generates a CRUD service that automatically handles interactions with the database. This service will use the entity generated from the contract to provide standard CRUD methods (create, read, update, delete).

```typescript
// Generated automatically by CMMV

import { Telemetry } from "@cmmv/core";
import { AbstractService, Service } from '@cmmv/http';
import { Repository } from '@cmmv/repository';
import { TaskEntity } from '../entities/task.entity';

@Service("task")
export class TaskService extends AbstractService {
    public override name = "task";

    async getAll(queries?: any, req?: any): Promise<TaskEntity[]> {
        const instance = Repository.getInstance();
        const repository = instance.dataSource.getRepository(TaskEntity);
        Telemetry.start('TaskService::GetAll', req?.requestId);
        let result = await repository.find();
        Telemetry.end('TaskService::GetAll', req?.requestId);
        return result;
    }

    async getById(id: string, req?: any): Promise<TaskEntity> {
        const instance = Repository.getInstance();
        const repository = instance.dataSource.getRepository(TaskEntity);
        Telemetry.start('TaskService::GetById', req?.requestId);
        const item = await repository.findOneBy({ id });
        Telemetry.end('TaskService::GetById', req?.requestId);

        if (!item)
            throw new Error('Item not found');

        return item;
    }

    async add(item: Partial<TaskEntity>, req?: any): Promise<TaskEntity> {
        const instance = Repository.getInstance();
        const repository = instance.dataSource.getRepository(TaskEntity);
        Telemetry.start('TaskService::Add', req?.requestId);
        const result = await repository.save(item);
        Telemetry.end('TaskService::Add', req?.requestId);
        return result;
    }

    async update(
        id: string, item: Partial<TaskEntity>, req?: any
    ): Promise<TaskEntity> {
        const instance = Repository.getInstance();
        const repository = instance.dataSource.getRepository(TaskEntity);
        Telemetry.start('TaskService::Update', req?.requestId);
        await repository.update(id, item);
        let result = await repository.findOneBy({ id });
        Telemetry.end('TaskService::Update', req?.requestId);
        return result;
    }

    async delete(
        id: string, req?: any
    ): Promise<{ success: boolean, affected: number }> {
        const instance = Repository.getInstance();
        const repository = instance.dataSource.getRepository(TaskEntity);
        Telemetry.start('TaskService::Delete', req?.requestId);
        const result = await repository.delete(id);
        Telemetry.end('TaskService::Delete', req?.requestId);
        return { success: result.affected > 0, affected: result.affected };
    }
}
```

The ``@cmmv/repository`` module significantly simplifies database interaction by automatically generating the necessary entities and services based on predefined contracts. With built-in TypeORM integration, this module streamlines CRUD operations, enabling faster development and easier database management.

## Settings

To ensure that the ``@cmmv/repository`` module works correctly with your project, you need to define the database configurations in a ``.cmmv.config.cjs`` file. This file serves as the central configuration for your project, including the repository settings for database interaction.

```javascript
module.exports = {
    // Other project configurations...

    repository: {
        type: "sqlite",
        database: "./database.sqlite",
        synchronize: true,
        logging: false,
    },
};
```

In this example, the module is configured to use SQLite as the database with automatic synchronization enabled. This means that any changes in your entities will automatically be reflected in the database schema without requiring migrations.

For a more detailed list of all available configurations, please visit the [TypeORM Data Source Options documentation](https://typeorm.io/data-source). There, you will find additional options such as:

* **entities:** An array of paths or classes to specify which entities TypeORM should manage.
* **migrations:** A path to your migration files.
* **cache:** Enable caching of queries.
* **username and password:** Credentials for connecting to the database (for database types that require authentication).
* **host and port:** For specifying the host and port for databases like PostgreSQL, MySQL, etc.

To properly configure the ``@cmmv/repository`` module, you need to specify your database connection settings in the ``.cmmv.config.cjs`` file. The example provided shows a typical setup using SQLite, but you can adjust the configuration for other databases like PostgreSQL or MySQL. For further details on possible configurations, refer to the [TypeORM documentation](https://typeorm.io).

## Custom Index

Starting from version 0.9, the ``@cmmv/repository`` module introduces support for custom index declarations within contracts. This feature allows developers to define more complex indexes directly in the contract using the index parameter in the ``@Contract`` decorator. It supports multi-field indexes and includes all available indexing options provided by TypeORM.

```typescript
@Contract({
    controllerName: 'User',
    protoPath: 'src/protos/auth.proto',
    protoPackage: 'auth',
    generateController: true,
    auth: true,
    imports: ['crypto'],
    index: [
        {
            name: 'idx_user_login',
            fields: ['username', 'password'],
            options: {
                where: "password IS NOT NULL",
                expireAfterSeconds: 3600,
            },
        },
    ],
})
export class UserContract extends AbstractContract {
    @ContractField({ protoType: 'string', unique: true })
    username: string;

    @ContractField({ protoType: 'string' })
    password: string;

    @ContractField({ protoType: 'string', nullable: true })
    googleId: string;

    @ContractField({ protoType: 'string', defaultValue: '"[]"' })
    groups: string;

    @ContractField({ protoType: 'string', defaultValue: '"[]"' })
    roles: string;

    @ContractField({ protoType: 'bool', defaultValue: false })
    root: boolean;
}
```

### Index Options

The ``index`` parameter accepts an array of index definitions, each containing the following options:

```typescript
export interface ContractIndexOptions {
    unique?: boolean;
    spatial?: boolean;
    fulltext?: boolean;
    nullFiltered?: boolean;
    parser?: string;
    where?: string;
    sparse?: boolean;
    background?: boolean;
    concurrent?: boolean;
    expireAfterSeconds?: number;
}
```

### Generated Entity

The ``@cmmv/repository`` module will generate the corresponding TypeORM entity with the defined indexes:

```typescript
@Entity('user')
@Index("idx_user_username", ["username"], { unique: true })
@Index("idx_user_googleId", ["googleId"])
@Index("idx_user_login", ["username", "password"], {
    where: "password IS NOT NULL",
    expireAfterSeconds: 3600
})
export class UserEntity implements IUser {
    @ObjectIdColumn()
    _id: ObjectId;

    @Column({ type: 'varchar' })
    username: string;

    @Column({ type: 'varchar' })
    password: string;

    @Column({ type: 'varchar', nullable: true })
    googleId: string;

    @Column({ type: 'varchar', default: "[]" })
    groups: string;

    @Column({ type: 'varchar', default: "[]" })
    roles: string;

    @Column({ type: 'boolean', default: false })
    root: boolean;
}
```
