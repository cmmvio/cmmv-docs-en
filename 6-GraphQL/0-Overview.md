# Overview

The `@cmmv/graphql` module provides seamless integration with GraphQL using `type-graphql` and `Apollo Server`. It automatically reads system contracts and generates the required types and resolvers, ensuring an effortless setup for GraphQL applications. Additionally, it includes authentication mechanisms that complement `@cmmv/auth`.

By default, the GraphQL server runs separately from the REST API on port **4000**, but this can be configured as needed. Future updates aim to integrate Apollo Server as middleware within `@cmmv/http`.

<div style="
    background-color: #DBEAFE;
    border-left: 4px solid #3B82F6;
    color: #1E40AF;
    padding: 1rem;
    border-radius: 0.375rem;
    margin: 1.5rem 0;
    font-size: 12px;
">
    <p style="font-weight: bold; margin-bottom: 0.5rem;">Important Notice</p>
    <p>
        The current version of <strong>@cmmv/graphql</strong> is still in beta and does not yet support customization of <strong>scalars, directives, middlewares, or plugins</strong>, nor does it allow replacing the underlying GraphQL server. These features are planned for future releases.
    </p>
</div>

## Installation

To install `@cmmv/graphql`, run:

```bash
$ pnpm add @cmmv/graphql apollo-server type-graphql graphql
```

## Configuration

To enable GraphQL support in your CMMV application, add the ``GraphQLModule`` to your setup:

```typescript
import { Application } from "@cmmv/core";
import { GraphQLModule } from "@cmmv/graphql";
import { DefaultAdapter } from "@cmmv/http";
import { AuthModule } from "@cmmv/auth";

Application.create({
    httpAdapter: DefaultAdapter,
    modules: [
        GraphQLModule,
        AuthModule
    ],
    contracts: [...],
});
```

## Configuration Options

The GraphQL module allows customizing its settings via the application configuration file.

To customize the GraphQL settings, update your .cmmv.config.cjs file:

```javascript
module.exports = {
    server: {
        host: process.env.HOST || "0.0.0.0",
        port: process.env.PORT || 3000,
    },

    graphql: {
        port: process.env.GRAPHQL_PORT || 4000,
    },

    ...
};
```

With this setup, the GraphQL server will listen on the configured port while maintaining compatibility with the rest of the CMMV framework.