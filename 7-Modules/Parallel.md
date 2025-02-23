# Parallel

Repository: [https://github.com/cmmvio/cmmv-parallel](https://github.com/cmmvio/cmmv-parallel)


The `@cmmv/parallel` module introduces parallelism into the CMMV framework, enabling efficient data processing using threads based on `fast-thread`. This implementation leverages SharedArrayBuffer, Atomics, and fast-json-stringify (optional) for zero-copy data transfer between threads, making it significantly faster than the traditional `parentPort.postMessage` approach.

## Installation

To install the ``@cmmv/parallel`` module, run the following command:

```typescript
$ pnpm add @cmmv/parallel
```

## How It Works

Unlike traditional multi-threading in JavaScript, `@cmmv/parallel` creates an isolated execution context inside each worker thread. This allows complex computations to be executed without blocking the main thread, making it ideal for large-scale data processing.

| **Feature**           | **Traditional `worker_threads`**  | **`@cmmv/parallel`** |
|----------------------|--------------------------------|----------------------|
| **Data Transfer**    | JSON serialization (slow)     | SharedArrayBuffer (zero-copy) |
| **Context Loading**  | Requires manual imports       | Automatic context injection  |
| **Thread Management** | Manual worker creation       | Thread pool with dynamic scaling |
| **Communication**    | `parentPort.postMessage`      | Direct memory access via Atomics |

## Benchmarks

* [https://github.com/andrehrferreira/fast-thread/blob/main/benchmarks/index.js](https://github.com/andrehrferreira/fast-thread/blob/main/benchmarks/index.js)
* Machine: linux x64 | 32 vCPUs | 128.0GB Mem
* Node: v20.17.0

| Name                     | Messages | Messages Per Second | MB Per Second |
|--------------------------|----------|---------------------|--------------|
| **fast-thread**         | 617,483  | 61,748.30          | 65.34        |
| **JSON**                | 524,235  | 52,423.50          | 55.48        |
| **fast-json-stringify** | 500,024  | 50,002.40          | 52.10        |
| **BSON**                | 420,946  | 42,094.60          | 44.19        |
| **Protobuf.js**         | 296,340  | 29,634.00          | 29.75        |
| **msgpack-lite**        | 288,180  | 28,818.00          | 29.86        |
| **CBOR**                | 223,945  | 22,394.50          | 23.20        |

## Decorator

The `@cmmv/parallel` module introduces a set of decorators that simplify parallel execution by automating thread management, data transfer, and context initialization. These decorators provide an intuitive way to define parallel tasks without manually handling worker threads, serialization, and message passing.

## @Parallel

Marks a function to be executed in parallel using a worker thread pool.

* Automatically manages worker threads and synchronizes results.
* Uses a namespace to group related parallel tasks.
* Configurable thread count, allowing dynamic scaling.

```typescript
@Parallel({
    namespace: "parserLine",
    threads: 3
})
async parserLine(@Tread() thread: any, @ThreadData() payload: any) {
    return {
        data: await thread.jsonParser.parser(payload.value),
        index: payload.index
    }
}
```

## @ThreadContext

Defines a shared execution context for a parallel function.

* Loads dependencies and resources inside the worker thread.
* Ensures that all workers in a pool share the same context.
* Returns an object that is accessible via `@Tread()`.

```typescript
@TreadContext("parserLine")
async threadContext() {
    const { JSONParser, AbstractParserSchema, ToLowerCase, ToDate } = await import("@cmmv/normalizer");

    class CustomerSchema extends AbstractParserSchema {
        public field = {
            name: { to: 'name' },
            email: {
                to: 'email',
                transform: [ToLowerCase],
            },
            registrationDate: {
                to: 'createdAt',
                transform: [ToDate],
            },
        };
    }

    const jsonParser = new JSONParser({ schema: CustomerSchema });
    return { jsonParser };
}
```

## @ThreadData

Extracts the data payload that is sent to the worker thread.

* Makes the function signature clean and readable.
* Injects only the relevant data for processing.
* Works alongside `@Tread()` to access both input data and shared context.

```typescript
async parserLine(@Tread() thread: any, @ThreadData() payload: any) {
    return {
        data: await thread.jsonParser.parser(payload.value),
        index: payload.index
    }
}
```

## @Tread

Provides access to the thread’s shared context, as defined by `@TreadContext()`.

* Grants access to preloaded resources within the worker.
* Ensures efficient data processing without redundant initialization.
* Works together with `@ThreadData()` for seamless function execution.

```typescript
async parserLine(@Tread() thread: any, @ThreadData() payload: any) {
    return {
        data: await thread.jsonParser.parser(payload.value),
        index: payload.index
    }
}
```

By using these decorators, developers can eliminate boilerplate code, achieve zero-copy memory sharing, and efficiently process high-volume data in parallel. 🚀

## Integrating

This example demonstrates how `@cmmv/parallel` can efficiently parse large JSON files using multiple threads.

```typescript
import * as fs from 'node:fs';
import * as path from 'node:path';
import { parser } from 'stream-json';
import { streamArray } from 'stream-json/streamers/StreamArray';

import { Application, Hook, HooksType } from "@cmmv/core";

import {
    AbstractParallel, Parallel, Thread,
    ThreadData, ThreadPool, TreadContext
} from "@cmmv/parallel";

export class ReadBigFileWithParallel extends AbstractParallel {
    @Hook(HooksType.onInitialize)
    async start() {
        const finalData = new Array<any>();
        const poolNamespace = "parserLine";
        const pool = ThreadPool.getThreadPool(poolNamespace);
        const filename = path.resolve('./sample/large-customers.json');

        if (pool) {
            console.log('Parsing With Multi-Thread...');
            let start;
            const readStream = fs.createReadStream(filename);
            await pool.awaitStart();
            const jsonStream = readStream.pipe(parser()).pipe(streamArray());

            pool.on('message', async (response) => {
                finalData[response.index] = response.data;
            });

            pool.on('end', () => {
                const end = Date.now();
                console.log(`Parallel parser: ${finalData.length} | ${(end - start).toFixed(2)}s`);
            });

            jsonStream.on('data', async ({ value, key }) => {
                if (!start) start = Date.now();
                pool.send({ value, index: key });
            });

            jsonStream.on('end', () => pool.endData());
            jsonStream.on('error', error => console.error(error));

            await pool.awaitEnd();
        } else {
            throw new Error(`Thread pool '${poolNamespace}' not found`);
        }
    }

    @Parallel({
        namespace: "parserLine",
        threads: 3
    })
    async parserLine(@Thread() thread: any, @ThreadData() payload: any) {
        return {
            data: await thread.jsonParser.parser(payload.value),
            index: payload.index
        }
    }

    @TreadContext("parserLine")
    async threadContext() {
        const { JSONParser, AbstractParserSchema, ToLowerCase, ToDate } = await import("@cmmv/normalizer");

        class CustomerSchema extends AbstractParserSchema {
            public field = {
                id: { to: 'id' },
                name: { to: 'name' },
                email: {
                    to: 'email',
                    validation: /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/,
                    transform: [ToLowerCase],
                },
                registrationDate: {
                    to: 'createdAt',
                    transform: [ToDate],
                },
            };
        }

        const jsonParser = new JSONParser({ schema: CustomerSchema });
        return { jsonParser };
    }
}

Application.exec({
    modules: [ParallelModule],
    services: [ReadBigFileWithParallel]
});
```

<br/>

* Multi-threaded processing – Efficiently distribute tasks across multiple CPU cores.
* Zero-copy communication – Uses SharedArrayBuffer to prevent memory duplication.
* Context-aware threads – Load specific resources inside each thread.
* Fast serialization – Supports fast-json-stringify for performance optimization.
* Simplified API – No need to create separate files for worker threads.
