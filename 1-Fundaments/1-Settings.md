# Settings

The system configuration for a CMMV project is managed through a ``.cmmv.config.js`` file in the root directory. This file defines various settings, such as:

* **Server Settings:** Configures the host and port for the application.
* **I18n:** Manages internationalization with locale files and default languages.
* **Repository:** Defines the database type (e.g., SQLite) and connection details.
* **Head Configuration:** Sets HTML attributes, meta tags, and links for the application’s frontend.
* **Headers:** Security policies like Content-Security-Policy.
* **Scripts:** Specifies JavaScript files to load.

Modules can extend this configuration based on the application's requirements.

```typescript
module.exports = {
    server: {
        host: "0.0.0.0",
        port: process.env.PORT || 3000
    },

    i18n: {
        localeFiles: "./src/locale",
        default: "en"
    },

    head: {
        title: "Documentation | CMMV - A minimalistic Node.js framework",
        htmlAttrs: {
            lang: "pt-br"
        },
        meta: [
            { charset: 'utf-8' },
            { "http-equiv": "content-type", content: "text/html; charset=UTF-8" },
            { name: 'viewport', content: 'width=device-width, initial-scale=1' },
            { name: "robots", content: "noodp" }
        ],
        link: [
            { rel: 'icon', href: '/assets/favicon/favicon.ico' },
            { rel: "dns-prefetch", href: "https://docs.cmmv.io" },
            { rel: "preconnect", href: "https://docs.cmmv.io" },    
            { rel: "stylesheet", href: "/assets/docs.css" }            
        ]
    },

    headers: {
        "Content-Security-Policy": [
            "default-src 'self'",
            "script-src 'self' 'unsafe-eval' 'unsafe-inline'",
            "style-src 'self' 'unsafe-inline'",
            "font-src 'self'"
        ]
    },

    scripts: [
        { type: "text/javascript", src: '/assets/bundle.min.js'},
    ]
};
```

The default HTTP renderer in CMMV natively supports internationalization, SEO configuration, and security-focused custom headers. These features allow developers to manage localized content, improve search engine optimization, and apply security policies like Content-Security-Policy directly in the configuration. Additional settings for templates and views can also be added later, providing flexibility for customizing pages. More detailed instructions can be found in the "View" documentation, which explains how to implement these configurations within the template system.

## Assign

The ``Config.assign()`` method allows you to dynamically modify or override the system configuration at runtime. This method takes an object that merges or updates the existing configuration. Below is an example of how the configuration can be applied using ``Config.assign()``:

```typescript
import { Config } from '@cmmv/core';

Config.assign({
    server: {
        host: process.env.HOST || '127.0.0.1',
        port: process.env.PORT || 4000,
    },
    i18n: {
        localeFiles: './locales',
        default: 'en',
    },
    // Additional settings...
});
```

With ``Config.assign()``, you can flexibly modify settings such as server configuration, internationalization, and more, without hardcoding them directly in ``.cmmv.config.js``.

## API

The system configurations in a CMMV-based application can be accessed programmatically through the Config class from the ``@cmmv/core`` module. This allows developers to dynamically retrieve, modify, or delete configuration values throughout the application. For example, the server's host and port settings, database configurations, or internationalization options can be accessed and altered by calling methods like Config.get(), Config.set(), and Config.has(). These features enable a flexible and centralized approach to managing system settings.

```typescript
import { Config } from "@cmmv/core";

//Get
const serverPort = Config.get<number>('server.port');
console.log(`Server running on port: ${serverPort}`);

//Set
Config.set('server.host', '127.0.0.1');

//Delete
Config.delete('repository.logging');

//Get All
const allConfig = Config.getAll();
console.log(allConfig);

//Assign
Config.assign({
    server: { port: 4000 },
    repository: { type: 'sqlite' }
});
```

Each module in the CMMV system has its own set of configurations that can be added to the central configuration file (.cmmv.config.js). This allows developers to customize the behavior and features of the modules they are using. Because different modules may require specific settings, it's essential to review each module's documentation carefully to understand the available configuration options and how they integrate with the core system. This ensures optimal setup and use of advanced functionalities such as RPC, authentication, caching, and more.

## Schema Validation

CMMV introduces a schema-based validation system to ensure that all configurations defined in ``.cmmv.config.js`` adhere to the expected structure and types. This validation provides robust error handling and guarantees that your application operates with correctly defined settings.

Each module can define its own schema using the ConfigSchema interface. This schema specifies the required fields, their types, default values, and nested properties when applicable.

Here’s an example schema for an ``auth`` module configuration, demonstrating both flat and nested properties:

```typescript
import { ConfigSchema } from '@cmmv/core';

export const AuthConfig: ConfigSchema = {
    auth: {
        localRegister: {
            required: true,
            type: 'boolean',
            default: true,
        },
        localLogin: {
            required: true,
            type: 'boolean',
            default: true,
        },
        jwtSecret: {
            required: true,
            type: 'string',
            default: 'secret',
        },
        expiresIn: {
            required: true,
            type: 'number',
            default: 3600,
        },
        google: {
            required: false,
            type: 'object',
            default: {},
            properties: {
                clientID: {
                    required: true,
                    type: 'string',
                    default: '',
                },
                clientSecret: {
                    required: true,
                    type: 'string',
                    default: '',
                },
                callbackURL: {
                    required: true,
                    type: 'string',
                    default: 'http://localhost:3000/auth/google/callback',
                },
            },
        },
    },
};
```

CMMV automatically validates the loaded configuration against the defined schemas at runtime using the ``Config.validateConfigs()`` method. This ensures that all required fields are present, types match, and nested properties follow their specifications.

### Supported Types

The schema supports the following types:

* string
* number
* boolean
* object (including nested properties)
* array
* any (skips type validation)

### How Validation Works
<br/>

* **Load Configurations:** The ``Config.loadConfig()`` method loads the ``.cmmv.config.js`` file.
* **Validate Against Schemas:** Each module registers its schema, and the ``Config.validateConfigs()`` method ensures all configurations match their respective schemas.
* **Throw Errors for Invalid Configurations:** If any configuration value does not match the schema, an error is thrown with details about the invalid key and the expected type.

If a configuration does not meet the schema requirements, CMMV will throw an error like:

```typescript
Error: Configuration "auth.google.clientID" expects type "string" but received "undefined".
```

## Environment

The ``env`` setting allows you to control the environment of the application (e.g., ``development``, ``production``). This setting typically comes from environment variables using ``dotenv`` [NPM](https://www.npmjs.com/package/dotenv) to manage sensitive values securely.

```typescript
env: process.env.NODE_ENV, // Example: 'development' or 'production'
```

## Server Configuration

The server settings control various aspects of how the application is hosted and its behavior in terms of security, performance, and session management. The configuration supports environment variables for flexibility in different deployment environments.

```typescript
server: {
    host: process.env.HOST || '0.0.0.0',  // Default host
    port: process.env.PORT || 3000,       // Default port
    poweredBy: false,                     // Remove 'X-Powered-By' header
    removePolicyHeaders: false,           // Option to remove headers added by Helmet
    cors: true,                           // Enable Cross-Origin Resource Sharing (CORS)
    
    // Compression settings
    compress: {
        enabled: true,
        options: {
            level: 6,  // Compression level (0-9)
        },
    },
    
    // Security headers with Helmet.js
    helmet: {
        enabled: true,
        options: {
            contentSecurityPolicy: false, // CSP can be customized
        },
    },

    // Session management
    session: {
        enabled: true,  // Enable sessions
        options: {
            sessionCookieName: process.env.SESSION_COOKIENAME || 'cmmv-session',
            secret: process.env.SESSION_SECRET || 'secret',  // Session secret
            resave: false,    // Prevent resaving session data
            saveUninitialized: false, // Do not save uninitialized sessions
            cookie: {
                secure: true,  // Ensure secure cookie (only over HTTPS)
                maxAge: 60000, // Cookie expiration time in ms
            },
        },
    },
},
```

This section includes detailed control over CORS, session management, compression, and security features such as Helmet. You can also set up different environments using ``dotenv``.

## Internationalization 

The i18n (Internationalization) system provides a way to manage locale files and define the default language for the application. This helps with the translation and localization of content.

```typescript
i18n: {
    localeFiles: './src/locale',  // Path to locale files
    default: 'en',                // Default language (English)
},
```

## RPC 

The ``rpc`` configuration allows Remote Procedure Calls (RPC) to be used in the application. RPC improves the performance and modularity of the system by enabling communication via predefined contracts.

```typescript
rpc: {
    enabled: true,           // Enable RPC system
    preLoadContracts: true,  // Pre-load RPC contracts for efficiency
},
```

## View

View settings control how HTML is rendered, including options for minifying HTML and handling inline scripts. These settings can be adjusted to optimize the delivery of frontend content.

```typescript
view: {
    extractInlineScript: false, // Disable extraction of inline scripts for better control
    minifyHTML: true,           // Enable HTML minification for performance
},
```

## Repository

The ``repository`` settings define the database type, path, and options for synchronization and logging. By default, SQLite is used, but other databases can be configured as needed.

```typescript
repository: {
    type: 'sqlite',                // Default database type
    database: './database.sqlite',  // Path to the SQLite database file
    synchronize: true,              // Synchronize database schema
    logging: false,                 // Disable logging for performance
},
```

## Cache

The cache configuration uses Redis by default for caching data, improving the speed and performance of the application.

```typescript
cache: {
    store: '@tirke/node-cache-manager-ioredis',  // Redis cache store
    getter: 'ioRedisStore',                      // Cache store getter
    host: 'localhost',                           // Redis host
    port: 6379,                                  // Redis port
    ttl: 600,                                    // Time to live for cache (in seconds)
},
```

## Authentication 

This section handles user authentication, including local login/registration and third-party OAuth integration (e.g., Google). The configuration allows for flexible setup of authentication mechanisms.

```typescript
auth: {
    localRegister: true,  // Enable local user registration
    localLogin: true,     // Enable local user login
    jwtSecret: process.env.JWT_SECRET || 'secret',  // JWT secret for token signing
    expiresIn: 60 * 60,   // JWT token expiration time (in seconds)
    
    // Google OAuth configuration
    google: {
        clientID: process.env.GOOGLE_CLIENT_ID,               // Google client ID
        clientSecret: process.env.GOOGLE_CLIENT_SECRET,       // Google client secret
        callbackURL: 'http://localhost:3000/auth/google/callback',  // OAuth callback URL
    },
},
```

## Head 

The ``head`` configuration manages HTML attributes, meta tags, and link elements, which are essential for SEO and proper rendering of the application.

```typescript
head: {
    title: 'CMMV',  // Default title
    htmlAttrs: {
        lang: 'pt-br',  // Default language
    },
    meta: [
        { charset: 'utf-8' },
        {
            'http-equiv': 'content-type',
            content: 'text/html; charset=UTF-8',
        },
        {
            name: 'viewport',
            content: 'width=device-width, initial-scale=1',
        },
    ],
    link: [{ rel: 'icon', href: 'assets/favicon/favicon.ico' }],
},
```

## Headers 

```typescript
headers: {
    'Content-Security-Policy': [
        "default-src 'self'",
        "script-src 'self' 'unsafe-eval' 'unsafe-hashes'",
        "style-src 'self' 'unsafe-inline'",
        "font-src 'self'",
        "connect-src 'self'",
    ],
},
```

## Scripts

The ``scripts`` configuration defines JavaScript files to load in the application, which can be customized based on the project's needs.

```typescript
scripts: [
    {
        type: 'text/javascript',
        src: '/assets/bundle.min.js',  // Main JavaScript bundle
        defer: 'defer',  // Load script after HTML is parsed
    },
],
```

This completes the detailed documentation of the settings. The assign method in Config allows you to dynamically update or override these settings, providing flexibility across different environments and modules.