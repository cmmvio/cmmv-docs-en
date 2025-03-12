# Introduction

CMMV (Contract-Model-Model-View) is a **full-stack ecosystem** that automates the creation of an entire backend and frontend structure based on **SOLID principles**. By leveraging **TypeScript contracts**, CMMV generates:

- **Backend**: Models, database entities, services, controllers, RPC gateways, serialization (optimized with `json-fast-stringify`).
- **Frontend**: Form schemas, data table configurations, and UI components based on contracts.
- **Admin Dashboard**: A fully functional panel powered by `@cmmv/admin`, generated automatically from contracts and views.

CMMV **supports RESTful APIs, RPC, and GraphQL (future)**, enabling **seamless integration** with external applications. Future updates will introduce **transpilation of contracts into other programming languages**, ensuring cross-platform compatibility.

Additionally, CMMV provides **native integrations** for:
- **Vue 3, React, Angular** (for modern SPAs and dashboards)
- **Flutter & Electron** (for cross-platform mobile and desktop applications)

## Why CMMV?

CMMV was designed to **solve the bottlenecks** of modern backend and frontend development. While traditional frameworks require **manual setup of controllers, services, and views**, CMMV **automates this process**, eliminating redundancy and improving maintainability.

### 🔥 Key Benefits
<br/>

* 🚀 **Automatic Code Generation**: Define contracts once, generate everything else.
* 🔄 **Standardized Architecture**: Enforces best practices.
* 🔌 **Seamless Integrations**: Works with databases, authentication, caching, and more.
* 📡 **Multi-Protocol Support**: REST, RPC, GraphQL in a single system.
* ⚡ **Optimized Performance**: Uses `json-fast-stringify` for high-speed serialization.

## How It Works

CMMV **generates** all necessary backend and frontend components based on **TypeScript contracts**.

### **Backend Workflow**
<br/>

1. **Define Contracts**: Create models with decorators.
2. **Automatic Generation**:
   - **Models** (ORM Entities)
   - **Services** (Business Logic)
   - **Controllers** (REST API)
   - **Gateways** (WebSocket RPC)
   - **Serialization** (Optimized for performance)
3. **API Ready**: Expose endpoints with **REST, RPC, or GraphQL**.

### **Frontend Workflow**
<br/>

1. **Form & Table Schemas**: Based on contracts.
2. **UI Components**: Use `@cmmv/ui` for dashboards.
3. **Admin Panel**: `@cmmv/admin` auto-generates views.

## Auto-Generated Admin Panel

Once contracts and views are defined, `@cmmv/admin` will automatically generate:

* User Management
* Role-Based Access Control
* CRUD Operations
* GraphQL, REST, and RPC Support

## Integration
<br/>

* Vue 3 (recommended)
* React (future)
* Angular (future)
* Flutter (future)
* Electron (future)

### Database Integrations
<br/>

* MongoDB, PostgreSQL, MySQL, SQL Server
* Automatic indexing & relationship mapping
* CRUD APIs with filters, sorting, and pagination

### Caching & Performance
<br/>

* Built-in Redis & Memcached support
* Auto-caching decorators for services & controllers

### Job Queues & Messaging
<br/>

* Kafka, RabbitMQ, Redis Queue integration
* Async background jobs & event-driven architecture

## Future Roadmap

CMMV will continue expanding its full-stack automation capabilities:

* 🔄 Cross-Language Transpilation (C#, Go, Python, Java)
* 📡 Expanded GraphQL Features
* 📲 Flutter Mobile Support
* 🖥️ Electron Desktop Support
* 🌎 Internationalization Enhancements (i18n & l10n)
