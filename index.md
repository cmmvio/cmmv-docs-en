<p align="center">
  <img src="assets/logo_CMMV2_icon.png" width="300" alt="CMMV Logo" />
</p>
<p align="center">Contract-Model-Model-View (CMMV) <br/> Building scalable and modular applications using contracts.</p>
<div class="flex flex-container">
    <a href="https://www.npmjs.com/package/@cmmv/core">
        <img src="https://img.shields.io/npm/v/@cmmv/core.svg" alt="NPM Version" />
    </a>
    <a href="https://github.com/andrehrferreira/cmmv/blob/main/LICENSE">
        <img src="https://img.shields.io/npm/l/@cmmv/core.svg" alt="Package License" />
    </a>
    <a href="https://dl.circleci.com/status-badge/redirect/circleci/QyJWAYrZ9JTfN1eubSDo5u/7gdwcdqbMYfbYYX4hhoNhc/tree/main" target="_blank">
        <img src="https://dl.circleci.com/status-badge/img/circleci/QyJWAYrZ9JTfN1eubSDo5u/7gdwcdqbMYfbYYX4hhoNhc/tree/main.svg" alt="CircleCI" />
    </a>
</div>

<br/>

# Introduction

CMMV (Contract-Model-Model-View) is a minimalistic framework designed to streamline the development of scalable and modular applications using TypeScript. By combining the power of contracts with a modular architecture, CMMV allows developers to define their entire application structure, from ORM entities to REST controllers and WebSocket endpoints, in a clear and maintainable way.

The project is divided into two main components:

1. **Core System**: The heart of CMMV, written in TypeScript and running on Node.js, is responsible for managing contracts, generating code, integrating with databases, and handling server-side operations. This component handles everything from parsing and code generation to integration with external services like cloud servers and databases.

3. **Backend**: The CMMV backend is inspired by [NestJS](https://nestjs.com/) in terms of structure and organization, using a similar format of decorators, services, controllers, and other architectural patterns. This allows for a familiar development experience for those who already use ``NestJS``, making it easier to create scalable and organized applications with a modular and object-oriented approach. Some implementations are quite different, especially when it comes to context and the need for dependency injection.

2. **Frontend**: CMMV utilizes its own reactivity system, inspired by [Vue 3](https://vuejs.org/) as the base framework. However, in the future, support for other frameworks such as React, Angular, and Vue will be available, even though their use is not recommended due to potential performance reduction and SEO issues. This approach simplifies the process of creating, managing, and deploying applications while offering optimal performance.

CMMV draws inspiration from a wide range of technologies and concepts, blending ideas from game development (e.g., Unreal Engine's Blueprints), component-based architectures (e.g., Delphi), and modern web development practices. It challenges traditional paradigms of web and application development, aiming to make the creation of complex systems as intuitive as possible.

This project reflects over 20 years of experience in different languages and frameworks, with influences from Delphi, Unity, Unreal, C#, C++, JavaScript, Node.js, TypeScript, VSCode.

We hope you find CMMV as exciting and powerful as we do. It's the culmination of nearly a decade of work and passion for simplifying and improving the development process.

## Why CMMV?

With over 20 years of experience in the tech industry as a programmer, I’ve developed various systems and projects used by millions of users. In 2020, I was working on my largest project to date, which was built on the following stack: backend using Node.js, TypeScript, NestJS, Nuxt.js, Redis, MongoDB, Elasticsearch, and RabbitMQ; frontend with Vue.js and Tailwind CSS; testing with Mocha, ESLint, and GitLab CI; infrastructure managed with Kubernetes and Nginx, and business intelligence powered by Grafana, Kafka, and IndexDB; along with a Flutter-based mobile app. Let's dive into the problems we faced in this setup.

First, the NestJS API, as more controllers and gateways were added, became increasingly difficult to manage and slowed down significantly, primarily due to dependency injection. Module management turned into a bureaucratic nightmare. Although the final application performed well, development became cumbersome. Furthermore, Nuxt’s SSR (server-side rendering) required integration, which often led to issues due to CORS policies. Communication between applications occurred via HTTP, and although NestJS supported RPC, Nuxt.js required custom proxies to implement WebSockets. Using Protobuf in the frontend presented additional challenges, and generating RPC controllers with Protoc resulted in a bloated codebase, making the application even heavier.

Second, Nuxt.js, while capable of SSR, still relied on proxies with APIs to load data since it ran on a Vite-based implementation. Static page generation at scale, with thousands of pages, became an unattainable task. We tried, but the time required for page generation was prohibitive. Using standard SSR, with the proxy API and HTTP+JSON communication, significantly increased the TTFB (Time to First Byte), making optimization challenging. Only by delivering content directly through a CDN could we mitigate these delays, but even then, the page load was too slow for ideal SEO. Additionally, Nuxt.js generated numerous JavaScript bundles and data files to supplement frontend data binding, increasing page load and making SEO optimization a constant battle.

It may seem counterintuitive to combine API and SSR into the same application due to competing processes, but consider that it's much simpler to create a single load balancer that serves both frontend and backend. With direct integration, SSR eliminates latency when fetching data—excellent for reducing page load times and generating static pages. When well-integrated with Redis cache and efficient database queries, SSR can be nearly as fast as pre-rendered static pages. Moreover, this architecture simplifies integrations like internationalization, structured data, and sitemaps, making them much easier to manage and serve.

Finally, the reduction in page onload time, which is also considered by search engines, is crucial. While Vue is an excellent tool, componentization in the frontend poses performance challenges due to potential deep coupling between components. Even with state management, critical issues related to element reactivity can lead to cascading update flows, which may either freeze the app or cause infinite loops. Avoiding such pitfalls requires a solid understanding of the underlying framework.

Considering all these factors, I created CMMV (Contract-Model-Model-View). Initially, the goal was to develop a complete solution that would meet my development needs while maintaining the familiar syntax of NestJS, Vue, etc., but resolving the problems that have plagued me over the years and hampered development.

After considering these factors, I created CMMV (Contract-Model-Model-View). My goal was not to compete with or replace any of the mentioned tools. All the technologies I’ve mentioned are of excellent quality and come highly recommended. They have large communities and are suitable for most projects. However, I had unique challenges in my projects, particularly around handling high volumes of traffic and the need for the best possible SEO performance. Despite my best efforts with my previous stack, the results were still not satisfactory for my specific needs. CMMV was born out of this necessity to address these exceptional requirements.

André Ferreira (CEO)

