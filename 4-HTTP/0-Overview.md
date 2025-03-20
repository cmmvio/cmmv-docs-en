# Overview

Repository: [https://github.com/cmmvio/cmmv/tree/main/packages/http](https://github.com/cmmvio/cmmv/tree/main/packages/http)

The CMMV framework introduces its own default server implementation, ``@cmmv/server``, which offers superior performance and seamless integration with the overall CMMV ecosystem. This server is highly optimized and designed to provide built-in support for critical features such as compression, routing, request handling, static file serving, security, and middleware management. Because ``@cmmv/server`` is developed as a core part of CMMV, it allows for better control over feature enhancements, bug fixes, and performance improvements, making it the recommended option for most applications.

The server is flexible and shares many of the same APIs and capabilities as [Express](https://expressjs.com/) and [Fastify](https://fastify.dev/), which ensures an easy transition if you're familiar with those frameworks. However, ``@cmmv/server`` also includes enhanced integration with CMMV’s contracts, modules, and services, providing a more consistent developer experience across different layers of the application.

Key Features:
* **HTTP and HTTPS Support:** The adapter can initialize servers using both HTTP and HTTPS based on configuration.
* **Middleware Management:** Preconfigured middlewares such as compression, CORS, Helmet (security), and session handling are included.
* **Static File Serving:** Automatically serves static files from the /public directory.
* **View Engine:** Supports rendering HTML views using CMMVRenderer, a custom template engine with security options like CSP.
* **Controller Registration:** Automatically registers controllers by scanning the ControllerRegistry and mapping HTTP methods (GET, POST, PUT, DELETE) to paths.
* **Session and Security Headers:** Adds session management using express-session and security headers like Content Security Policy, XSS Protection, and HSTS.
* **Request Tracking:** Each request is assigned a unique requestId for telemetry and monitoring.
* **Open Connection Tracking:** Tracks and closes open connections when the server shuts down.
* **Error Handling:** Captures and logs errors during request processing, providing detailed error messages.

## Default Server

<br/>

```typescript
import { Application } from '@cmmv/core';
import { DefaultAdapter, DefaultHTTPModule } from '@cmmv/http';

Application.create({
    httpAdapter: DefaultAdapter,
    modules: [DefaultHTTPModule, ...],
    services: [...],
    contracts: [...],
});
```
