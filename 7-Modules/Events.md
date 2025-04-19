# Events (Now part of `@cmmv/core` since v0.13)

> **Notice:** As of version `0.13`, the `@cmmv/events` module has been fully merged into `@cmmv/core`. You no longer need to install or import `@cmmv/events` separately — all functionality is available out-of-the-box via `@cmmv/core`.

## Migration

If your application was using `@cmmv/events` prior to v0.13, you can safely remove it from your dependencies and update your imports as follows:

### Before:
```ts
import { EventsModule, EventsService, OnEvent } from '@cmmv/events';
```

### After:
```ts
import { EventsService, OnEvent } from '@cmmv/core';
```

There is no need to include `EventsModule` in your module list anymore — event support is now globally available.

## Features

- **Event Binding:** Bind methods to events using the `@OnEvent` decorator.
- **Asynchronous Communication:** Facilitate decoupled inter-service workflows.
- **Flexible Payloads:** Supports any type of payload, with strong typing via TypeScript.
- **Seamless Integration:** Integrated directly into the core framework.
- **Error Handling:** Reliable event execution with detailed logging for debugging.

## Installation

> ❌ No installation needed. Events are now built-in to `@cmmv/core`.

## Usage

### Listening to Events

Use the `@OnEvent` decorator to respond to events:

```ts
import { Service, OnEvent } from '@cmmv/core';

@Service('listener')
export class Listener {
    @OnEvent('task.completed')
    public onTaskCompleted(payload: any) {
        console.log('Task completed:', payload);
    }
}
```

### Emitting Events

Use the `EventsService` to emit events from any service or controller:

```ts
import { EventsService, Service } from '@cmmv/core';

@Service('task-service')
export class TaskService {
    constructor(private readonly events: EventsService) {}

    public completeTask() {
        // ... task logic ...
        this.events.emit('task.completed', { id: 123, title: 'Write docs' });
    }
}
```

## Best Practices

- **Use namespaced events:** e.g., `user.created`, `task.failed`.
- **Define payload interfaces** for clarity and type safety.
- **Avoid redundant cron/event logic** in multi-instance environments unless controlled via config or infra setup.


## Compatibility

- Fully compatible with applications using `@cmmv/core >= 0.13`.
- Legacy applications using `@cmmv/events` can continue to work, but usage is **deprecated**.


## Advantages

- **No extra module setup**
- **Less boilerplate**
- **Better cohesion with the framework**
- **Ideal for modular or service-based design**
