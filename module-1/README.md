<h1 align='center'>Module 01: Getting Started with GraphQL</h1>

## Topics:

1. Getting Started with GraphQL
2. Setting Up a GraphQL Project
3. Defining Schema and Resolvers
4. Retrieving Single Products by ID
5. Structuring Your Project Files
6. Querying Category Data
7. Establishing a One-to-One Relationship - Product and Category
8. Establishing a One-to-Many Relationship - Category and Products
9. Creating a One-to-Many Relationship - Product and Reviews
10. Wrapping Up

## Table of Contents:

- [Defining Schema and Resolvers](#defining-schema-and-resolvers)

## Defining Schema and Resolvers

```gql
type Book {
  title: String
  author: Author
}

type Author {
  name: String
  books: [Book]
}
```

A schema defines a collection of types and the relationships between those types. In the example above, a `Book` can have an associated `author`, and an `Author` can have a list of `books`.

Because these relationships are defined in a unified schema, client developers can see exactly what date is available and then request a specific subset of the data with a single optimized `query`.

### Query

A request for specific data from a GraphQL server. Clients define the structure of the response, enabling precise and efficient data retrieval.

> Note that the schema is not responsible for defining where data comes from or how it's stored. It is entirely implementation-agonstic.

Each field's returns data of the type specified. A field's return type can be a `Scalar`, `Object`, `Enum`, `Union`, or `Interface`.

### Field nullability

By default, it's valid for any field in your schema to return `null` instead of its specified type. You can require that a particular filed doesn't return `null` with an exclamation mark `!`, like so:

```gql
type Author {
  name: String! # Can't return null
  books: [Book]
}
```

These fields ar `non-nullable`. If your server attempts to return `null` for `non-nullable` field, an error is thrown.

### Nullability and lists

With a list filed, an exclamation mark `!` can appear in any combination of two locations:

```gql
type Author {
  books: [Book!]! # This list can't be null AND its list *items* can't be null
}
```

- If `!` appears inside the square brackets, the returned list can't include items that are `null`.
- If `!` appears outside the square brackets, the list itself can't be `null`.
- In any case, it's valid for a list field to return an empty list.

Based on the above principles, the below return types can potentially return these sample values:

| Return Types | Example Allowed Return Values                           |
| ------------ | ------------------------------------------------------- |
| `[Book]`     | `[]`, `null` `[null]`, and `[{title: "City of Glass"}]` |
| `[Book!]`    | `[]`, `null`, and `[{title: "City of Glass"}]`          |
| `[Book]!`    | `[]`, `[null]`, and `[{title: "City of Glass"}]`        |
| `[Book!]!`   | `[]` and `[{title: "City of Glass"}]`                   |

### Supported Types

Every typ definition in GraphQL schema belongs to one of the following categories:

- [Scalar](#scalar-types)
- [Object](#object-types)
- [Input]()
- [Enum]()
- [Union]()
- [Interface]()

### Scalar types

Scalar types are similar to primitive types in your favorite programming languages. They always resolve to concrete data.

GraphQL's default scalar types are:

- `Int`: A signed 32‐bit integer
- `Float`: A signed double-precision floating-point value
- `String`: A UTF‐8 character sequence
- `Boolean`: true or false
- `ID` (serialized as a String): A unique identifier that's often used to refetch an object or as the key for a cache. Although it's serialized as a String, an `ID` is not intended to be human‐readable.

### Object types

Most of the types you define in a GraphQL schema are object types. An object type contains a collection of fields, each of which has its own type.

Two object types can include each other as fields, as is the case in our example schema from earlier:

```gql
type Book {
  title: String
  author: Author
}

type Author {
  name: String
  books: [Book]
}
```

### The `__typename` field

Every object type in your schema automatically has a field named `__typename` (you don't need to define it). The `__typename` field returns the object type's name as a String (e.g., `Book` or `Author`).

GraphQL clients use an object's `__typename` for many purposes, such as to determine which type was returned by a field that can return multiple types (i.e., a union or interface). Apollo Client relies on `__typename` when caching results, so it automatically includes `__typename` in every object of every query.

Because `__typename` is always present, this is a valid query for any GraphQL server:

```gql
query UniversalQuery {
  __typename
}
```
