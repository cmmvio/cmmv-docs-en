# Scheduling (Now part of `@cmmv/core` since v0.13)

> **Notice:** As of version `0.13`, the `@cmmv/scheduling` module has been fully integrated into `@cmmv/core`. You no longer need to install or register it separately — all scheduling features are now available natively.

## Migration

If your application was previously using `@cmmv/scheduling`, you can remove it from your dependencies and update your imports:

### Before:
```ts
import { SchedulingModule, SchedulingService, Cron } from '@cmmv/scheduling';
```

### After:
```ts
import { Cron } from '@cmmv/core';
```

You no longer need to add `SchedulingModule` or register `SchedulingService` — scheduling is available globally by default.

## Features

- **Cron Scheduling:** Schedule tasks using cron expressions.
- **@Cron Decorator:** Easily attach cron jobs to class methods.
- **Built-in Integration:** No need for manual setup with core applications.
- **Logger Support:** Compatible with the `Logger` from `@cmmv/core`.

## Usage

Use the `@Cron` decorator to define tasks that run on a schedule:

```ts
import { Cron, Logger, Service } from '@cmmv/core';

@Service('task')
export class TaskService {
    private logger: Logger = new Logger('TaskService');

    @Cron('*/5 * * * * *')  // Runs every 5 seconds
    handleTask() {
        this.logger.log('Task executed every 5 seconds');
    }
}
```

## Cron Expression Format

```
*    *    *    *    *    *
┬    ┬    ┬    ┬    ┬    ┬
│    │    │    │    │    └ Day of the week (0 - 7) (Sunday=0 or 7)
│    │    │    │    └───── Month (1 - 12)
│    │    │    └────────── Day of the month (1 - 31)
│    │    └─────────────── Hour (0 - 23)
│    └──────────────────── Minute (0 - 59)
└───────────────────────── Second (0 - 59, optional)
```

## Examples

- `* * * * * *` — Runs every second
- `*/5 * * * * *` — Runs every 5 seconds
- `0 0 * * * *` — Runs every hour
- `0 0 12 * * *` — Runs every day at noon
- `0 0 1 1 *` — Runs once a year on January 1st

## Important Note on Multi-Instance Behavior

When running multiple instances of the same application, **each instance will execute the same cron task**. This can lead to **redundant executions**.

Until native instance-limiting is introduced, it is recommended to:

- Use `ENV`-based guards (`if (process.env.RUN_CRONS === 'true')`)
- Rely on external orchestration (e.g., designate a single runner pod/container in k8s)
- Avoid shared operations unless properly idempotent

## Advantages

- **Zero Config:** Scheduling runs with no additional registration
- **Clean Syntax:** Decorator-based setup
- **Type-safe:** Fully TypeScript-compatible
- **Future-Proof:** Planned support for distributed cron/task orchestration