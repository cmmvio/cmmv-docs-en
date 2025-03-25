# Throttler

Repository: [https://github.com/cmmvio/cmmv/tree/main/packages/throttler](https://github.com/cmmvio/cmmv/tree/main/packages/throttler)

The `@cmmv/throttler` module is a powerful and flexible rate-limiting solution designed to work seamlessly within the CMMV framework. It provides rate-limiting capabilities for REST, GraphQL, and RPC endpoints, ensuring that your application remains protected from abuse, excessive requests, or denial-of-service attacks. Built with performance and integration in mind, this module leverages the CMMV ecosystem to offer a consistent and easy-to-use experience for developers.

The throttler module integrates natively with `@cmmv/server` and other CMMV components, allowing you to define rate-limiting rules globally or per-endpoint. It is highly configurable, lightweight, and optimized for high-traffic applications.

## Installation

To add the `@cmmv/throttler` module to your CMMV project, use the following command:

```bash
$ pnpm add @cmmv/throttler
```

## Usage

To enable rate-limiting in your CMMV application, import the `ThrottlerModule` and include it in your application's module list. Below is an example of how to set it up:

```typescript
import { Application } from '@cmmv/core';
import { DefaultAdapter, DefaultHTTPModule } from '@cmmv/http';
import { ThrottlerModule } from '@cmmv/throttler';

Application.create({
    httpAdapter: DefaultAdapter,
    modules: [
        DefaultHTTPModule,
        ThrottlerModule,
        // Other modules...
    ],
    providers: [
        // Your providers...
    ],
});
```

Once added, the `ThrottlerModule` will automatically apply rate-limiting based on the default configuration or any custom settings you provide.

## Configuration

You can customize the behavior of the `@cmmv/throttler` module by adding a configuration object in your `.cmmv.config.cjs` file. The module uses a schema-based configuration system powered by `@cmmv/core`.

```javascript
require('dotenv').config();

module.exports = {
    ...

    throttler: {
        limit: 100,
        ttl: 60,
        gcInterval: 60
    },

    ...
};
```
<br/>

- **`limit`**: Defines the maximum number of requests allowed within the specified time window (`ttl`). Accepts a string value (e.g., `'100'`) to allow flexibility for dynamic configuration.
- **`ttl`**: The duration (in seconds) of the rate-limiting window. After this time elapses, the request counter resets.
- **`gcInterval`**: The interval (in seconds) at which the throttler cleans up expired rate limit entries to optimize memory usage.

## Key Features

- **REST, GraphQL, and RPC Support**: Applies rate-limiting across all major CMMV request-handling paradigms.
- **Global and Per-Endpoint Limiting**: Configure throttling globally or override it for specific routes or controllers.
- **Lightweight and Performant**: Optimized for minimal overhead, even under high request volumes.
- **Customizable**: Fine-tune rate-limiting rules via the configuration file or programmatically.
- **Seamless Integration**: Works out of the box with `@cmmv/server` and other CMMV modules.
- **Request Tracking**: Leverages the `requestId` from `@cmmv/server` for monitoring throttled requests.