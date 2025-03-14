# Pagination

To maintain consistency with **RESTful APIs**, **`@cmmv/graphql`** implements **built-in pagination support**.
Since GraphQL resolvers use the same **services as REST and RPC**, pagination is **automatically applied** to **`find` queries**.

This ensures:
✅ **Seamless integration** between GraphQL, REST, and RPC.
✅ **Standardized pagination parameters** across all APIs.
✅ **Flexible filtering, sorting, and searching options**.

## How Pagination Works

The system defines two pagination-related types:

- **`PaginationArgs`** → Used in queries to request paginated data.
- **`PaginationResponse`** → Defines the response structure for paginated results.

**All auto-generated `find` resolvers** **automatically support pagination**, meaning developers don't need to configure it manually.

## Pagination Request Parameters

GraphQL queries use the **`PaginationArgs`** type, which provides standard parameters for controlling results.

```typescript
import { ArgsType, Field, Int } from "@cmmv/graphql";
import GraphQLJSON from "graphql-type-json";

@ArgsType()
export class PaginationArgs {
    @Field(() => Int, {
        description: "Maximum items per page.",
        nullable: true,
        defaultValue: 10
    })
    limit?: number;

    @Field(() => Int, {
        description: "Number of skipped items.",
        nullable: true,
        defaultValue: 0
    })
    offset?: number;

    @Field({
        description: "Field used for sorting.",
        nullable: true,
        defaultValue: "id"
    })
    sortBy?: string;

    @Field({
        description: "Sorting order: 'asc' or 'desc'.",
        nullable: true,
        defaultValue: "asc"
    })
    sort?: string;

    @Field({
        description: "Search term.",
        nullable: true
    })
    search?: string;

    @Field({
        description: "Field where search is applied.",
        nullable: true
    })
    searchField?: string;

    @Field(() => GraphQLJSON, {
        description: "Flexible filtering options.",
        nullable: true
    })
    filters?: Record<string, any>;
}
```

## Query Parameters Explained

| Parameter    | Type    | Default | Description |
|-------------|--------|---------|-------------|
| `limit`   | Int    | **10**  | Maximum number of items per page. |
| `offset`  | Int    | **0**   | Number of items to skip before returning results. |
| `sortBy`  | String | **id**  | Field used for sorting the results. |
| `sort`    | String | **asc** | Sorting order (**asc** or **desc**). |
| `search`  | String | _null_  | General search term to filter results. |
| `searchField` | String | _null_ | Specific field where the search should be applied. |
| `filters` | JSON   | _null_  | JSON object with flexible filter conditions. |

The **`filters`** field supports **complex filtering**, allowing **multiple conditions and dynamic fields** using **`graphql-type-json`**.

## Pagination Response Structure

Paginated queries return a **`PaginationResponse`** object, containing the requested items and metadata.

```typescript
import { ObjectType, Field, Int } from "@cmmv/graphql";
import GraphQLJSON from "graphql-type-json";

@ObjectType()
export class PaginationResponse {
    @Field(() => Int, { description: "Maximum items per page." })
    limit!: number;

    @Field(() => Int, { description: "Number of skipped items." })
    offset!: number;

    @Field({ description: "Sorting field." })
    sortBy!: string;

    @Field({ description: "Sorting order: 'asc' or 'desc'." })
    sort!: string;

    @Field({ description: "Applied search term.", nullable: true })
    search?: string;

    @Field({ description: "Field where search was applied.", nullable: true })
    searchField?: string;

    @Field(() => GraphQLJSON, { description: "Applied filters.", nullable: true })
    filters?: Record<string, any>;
}
```

## Response Fields Explained

| Field       | Type    | Description |
|------------|--------|-------------|
| `limit`   | Int    | Number of items per page. |
| `offset`  | Int    | Number of skipped items. |
| `sortBy`  | String | Field used for sorting. |
| `sort`    | String | Sorting order (**asc** or **desc**). |
| `search`  | String | Applied search term (if any). |
| `searchField` | String | Field where the search was performed. |
| `filters` | JSON   | JSON object with applied filter conditions. |

## Paginated GraphQL Query

Developers can use **GraphQL queries** to fetch paginated results with sorting and filtering.

```graphql
query {
    userFind(limit: 5, offset: 0, sortBy: "createdAt", sort: "desc", search: "John") {
        count
        data {
            id
            name
            email
        }
        pagination {
            limit
            offset
            sortBy
            sort
            search
            searchField
            filters
        }
    }
}
```

**Response**

```json
{
    "data": {
        "userFind": {
            "count": 100,
            "data": [
                { "id": "1", "name": "John Doe", "email": "john@example.com" },
                { "id": "2", "name": "John Smith", "email": "smith@example.com" }
            ],
            "pagination": {
                "limit": 5,
                "offset": 0,
                "sortBy": "createdAt",
                "sort": "desc",
                "search": "John",
                "searchField": null,
                "filters": null
            }
        }
    }
}
```

### Advanced Filtering with JSON

The **`filters`** field enables advanced filtering, allowing **multiple conditions** and **dynamic fields**.

```graphql
query {
    userFind(filters: { role: "admin", status: "active" }) {
        count
        data { id name role status }
    }
}
```

**Response**

```json
{
    "data": {
        "userFind": {
            "count": 3,
            "data": [
                { "id": "1", "name": "Alice", "role": "admin", "status": "active" },
                { "id": "2", "name": "Bob", "role": "admin", "status": "active" }
            ]
        }
    }
}
```

GraphQL pagination in `@cmmv/graphql` ensures:
- **Unified support across REST, RPC, and GraphQL**.
- **Efficient data retrieval with limit, offset, sorting, and filtering**.
- **Flexible JSON-based filters for advanced queries**.
- **Auto-generated support for `find` queries** without additional setup.

By embedding pagination directly into GraphQL, **CMMV ensures a seamless experience**, reducing complexity while maximizing **performance and scalability**.
