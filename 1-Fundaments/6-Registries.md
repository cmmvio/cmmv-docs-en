# Registries

Registries in CMMV serve as centralized storage for important records that are required later in the application. They are used for managing services, controllers, gateways, queues, and more. 

Registries are particularly useful in combination with decorators, which store and process metadata. For example, the **HTTP Registry** receives class definitions via decorators. Inside these classes, functions decorated with method-specific decorators act as request handlers that need to be processed by the HTTP adapter. These handler functions may have input decorators that appear in a non-predetermined order, requiring the registry to preprocess them so that they can be properly called.

Registries play a crucial role in:
- **Storing and formatting metadata** that will be used by adapters (e.g., HTTP, WebSocket).
- **Ensuring correct function execution** by preprocessing handlers and their parameters.
- **Maintaining singleton behavior**, as registries always work in a static manner.

## Why Use Registries?

This approach eliminates the need to manually declare dependencies in each module. Since registries use a **static class pattern**, registered services can be accessed from anywhere in the application while preserving their properties.

For example:
- Services and dependencies can be instantiated **once** and stored in the registry.
- These instances can be retrieved from any part of the application, such as controllers, gateways, and providers.
- More complex registries, such as **Controller Registries**, can preprocess handlers and parameters, making it easier for the framework to process these calls dynamically.

## Service Registry Example

Below is a simple implementation of a **Service Registry**, which is responsible for registering and managing services.

```typescript
export class ServiceRegistry {
    private static services = new Map<any, { name: string }>();

    public static registerService(target: any, name: string) {
        this.services.set(target, { name });
    }

    public static getServices() {
        return Array.from(this.services.entries());
    }

    public static getServicesArr() {
        return Array.from(this.services.entries()).reduce(
            (acc, [cls, instance]) => {
                acc[instance.name] = new cls();
                return acc;
            },
            {},
        );
    }

    public static getService(name: string) {
        for (const [target, metadata] of this.services.entries()) {
            if (metadata.name === name) return target;
        }
        return undefined;
    }

    public static clear() {
        this.services.clear();
    }
}
```

Source: [https://github.com/cmmvio/cmmv/blob/main/packages/core/registries/service.registry.ts](https://github.com/cmmvio/cmmv/blob/main/packages/core/registries/service.registry.ts)

<br/>

* Registers services using registerService(target, name).
* Retrieves a service instance using getService(name).
* Retrieves all registered services using getServices() or getServicesArr().
* Clears all registered services when necessary.

## Generic Registries

To further simplify the creation of registries and custom decorators, CMMV includes a generic abstraction called GenericRegistry. This allows:

* The creation of class, method, and parameter decorators in a structured way.
* Metadata to be stored and retrieved efficiently without requiring Symbols or Reflection APIs.

```typescript
const META_OPTIONS = Symbol('controller_options');

export class GenericRegistry {
    public static controllers = new Map<
        any,
        {
            options?: any;
            handlers: any[];
        }
    >();

    public static registerController(target: any, options: any) {
        if (!this.controllers.has(target)) {
            this.controllers.set(target, {
                handlers: [],
                options,
            });
        } else {
            this.controllers.set(target, {
                ...this.controllers.get(target),
                options,
            });
        }
    }

    public static registerHandler(target: any, handlerName: string) {
        let controller = this.controllers.get(target.constructor);

        if (!controller) {
            const options =
                Reflect.getMetadata(META_OPTIONS, target.constructor) || {};
            this.registerController(target.constructor, options);
            controller = this.controllers.get(target.constructor);
        }

        if (controller) {
            const handler = controller.handlers.find(
                msg => msg.handlerName === handlerName,
            );

            if (!handler) controller.handlers.push({ handlerName, params: [] });
        }
    }

    public static registerParam(
        target: any,
        handlerName: string,
        paramType: string,
        index: number,
    ) {
        let controller = this.controllers.get(target.constructor);

        if (!controller) {
            const options =
                Reflect.getMetadata(META_OPTIONS, target.constructor) || {};
            this.registerController(target.constructor, options);
            controller = this.controllers.get(target.constructor);
        }

        if (controller) {
            let handler = controller.handlers.find(
                msg => msg.handlerName === handlerName,
            );

            if (!handler) {
                handler = { handlerName, params: [] };
                controller.handlers.push(handler);
            }

            handler.params = handler.params || [];
            handler.params.push({ paramType, index });
        }
    }

    public static getControllers() {
        return Array.from(this.controllers.entries());
    }

    public static getHandlers(target: any): any[] {
        const controller = this.controllers.get(target.constructor);
        return controller ? controller.handlers : [];
    }

    public static getParams(target: any, handlerName: string): any[] {
        const queues = this.controllers.get(target.constructor);

        if (!queues) return [];

        const handler = queues.handlers.find(
            handler => handler.handlerName === handlerName,
        );

        return handler ? handler.params : [];
    }

    public static clear() {
        this.controllers = new Map<
            any,
            {
                options?: any;
                handlers: any[];
            }
        >();
    }
}
```

Source: [https://github.com/cmmvio/cmmv/blob/main/packages/core/registries/generic.registry.ts](https://github.com/cmmvio/cmmv/blob/main/packages/core/registries/generic.registry.ts)

## Using GenericRegistry

### Controller Decorator
<br/>

```typescript
function Controller(options?: any) {
    return function (target: any) {
        GenericRegistry.registerController(target, options);
    };
}
```

### Route Decorator
<br/>


```typescript
function Route(route: string, method: string = 'GET') {
    return function (target: any, handlerName: string) {
        GenericRegistry.registerHandler(target, handlerName);
    };
}
```

### Parameter Decorator
<br/>


```typescript
function Param(type: string) {
    return function (target: any, handlerName: string, index: number) {
        GenericRegistry.registerParam(target, handlerName, type, index);
    };
}
```

### Example Usage
<br/>


```typescript
@Controller({ prefix: '/users' })
class UserController {
    @Route('/get/:id', 'GET')
    getUser(@Param('id') id: number) {
        return { id, name: "John Doe" };
    }
}
```

### Retrieving Registered
<br/>

```typescript
// Get all controllers
const controllers = GenericRegistry.getControllers();
console.log(controllers);

// Get handlers of a specific controller
const handlers = GenericRegistry.getHandlers(UserController);
console.log(handlers);
```

### How does the application read the controllers?
The application, through HTTP adapters, reads the controllers registered in the Registry, instantiates the classes based on the providers defined in the constructor, and processes the handlers with their respective parameters and decorators. This enables dynamic route binding and correct request execution.

Below is the implementation of the default HTTP adapter:

```typescript
const controllers = ControllerRegistry.getControllers();

controllers.forEach(([controllerClass, metadata]) => {
    const paramTypes =
        Reflect.getMetadata('design:paramtypes', controllerClass) || [];
    const instances = paramTypes.map((paramType: any) =>
        this.application.providersMap.get(paramType.name),
    );

    const instance = new controllerClass(...instances);
    const prefix = metadata.prefix;
    const routes = metadata.routes;

    routes.forEach(route => {
        let fullPath = `\${prefix}${route.path ? '/' + route.path : ''}`;
        fullPath = fullPath.replace(/\/\//g, '/');
        const method = route.method.toLowerCase();

        if (this.instance[method] && fullPath !== '/*') {
            const handler = async (req: any, res: any, next: any) => {
                ...
            };
        }
    });
});
```

Source: [https://github.com/cmmvio/cmmv/blob/main/packages/http/default.adapter.ts#L236](https://github.com/cmmvio/cmmv/blob/main/packages/http/default.adapter.ts#L236)

### Handler parameters

In the case of handler parameters, their retrieval varies depending on the implementation. However, for controllers, these parameters are extracted directly from the req object provided by the HTTP server.

Below is the implementation:

```typescript
private buildRouteArgs(req: any, res: any, next: any, params: any[]) {
    const args: any[] = [];

    params?.forEach(param => {
        const [paramType, paramName] = param.paramType.split(':');
        switch (paramType) {
            case 'body':
                args[param.index] = req.body;
                break;
            case 'param':
                args[param.index] = req.params[paramName];
                break;
            case 'query':
                args[param.index] = req.query[paramName];
                break;
            case 'queries':
                args[param.index] = req.query;
                break;
            case 'header':
                args[param.index] = req.headers[paramName.toLowerCase()];
                break;
            case 'headers':
                args[param.index] = req.headers;
                break;
            case 'request':
                args[param.index] = req;
                break;
            case 'response':
                args[param.index] = res;
                break;
            case 'next':
                args[param.index] = next;
                break;
            case 'session':
                args[param.index] = req.session;
                break;
            case 'user':
                args[param.index] = req.user;
                break;
            case 'ip':
                args[param.index] = req.ip;
                break;
            case 'hosts':
                args[param.index] = req.hosts;
                break;
            default:
                args[param.index] = undefined;
                break;
        }
    });

    return args;
}
```

Source: [https://github.com/cmmvio/cmmv/blob/main/packages/http/default.adapter.ts#L478](https://github.com/cmmvio/cmmv/blob/main/packages/http/default.adapter.ts#L478)

## Registries vs Reflection 

Using registries instead of reflection and symbols offers significant advantages in terms of performance, efficiency, and maintainability. Unlike reflection, which dynamically retrieves metadata every time it is accessed, registries store and process metadata only once during application initialization. This eliminates redundant lookups and ensures faster execution by avoiding runtime metadata resolution. Registries pre-process and structure data for efficient usage, making controller handlers and parameters readily available for immediate execution without additional processing. This eliminates unnecessary runtime calculations, allows efficient retrieval of decorators' metadata, and ensures structured, ordered data for function execution.

Reflection-based approaches require repeated metadata lookups at runtime, do not provide structured storage, and rely on TypeScript’s reflection behavior, which may have limitations in some environments. Registries, on the other hand, store structured data statically, allow direct key-based access instead of repeated lookups, and remain framework-agnostic by not depending on TypeScript's reflection system. By managing metadata centrally, registries provide better consistency across different modules, eliminating the need for scattered metadata retrieval calls and ensuring a predictable structure for decorators.

Additionally, registries integrate seamlessly with dependency injection, making it easy to retrieve and inject dependencies across the application. This approach simplifies metadata storage and retrieval, offering better performance, structured access, and maintainability compared to reflection-based methods. By loading metadata once and structuring it efficiently, the application can dynamically access and utilize decorators' metadata without performance penalties, making registries a more efficient and scalable solution.