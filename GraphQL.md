# GraphQL
- [source for my notes](https://graphql.org/learn/)

GraphQL is a _query language_ and a _server-side runtime_. You can create a **GraphQL Service**, but GraphQL isn't a database, and it's not tied to any specific database or framework.

You can make a **GraphQL Service**. In order to do that, you have to define the following:
- types & fields - kind of similar to an object, or a named tuple in python
- functions for each type

Once you have these things, and you're running the GraphQL Service, you can send it GraphQL queries. The service takes these queries and does the following:
- validates the queries (the query can only refer to the types and the functions defined in the service)
- executes the queries

## Queries
