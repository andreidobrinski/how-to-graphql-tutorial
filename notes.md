# How to GraphQL Notes

[Link to tutorial](https://www.howtographql.com/)

## Fundamentals

What is GraphQL

- new API standard developed by FB an open sourced
- enables declarative data fetching by client. Client describes exactly what it needs
- Uses a single endpoint instead of multiple
- Responds to queries

Challenges to REST

- increased mobile usage and need for efficient data loading was why graphql was started
- gql minimizes the amount of data the needs to travel over the network
- faster dev speed with gql with less API changes

Netflix and Coursera had similar solutions to gql but switched to gql after it was released

Gql solves over and underfetching:

- overfetching: downloading more data than you need to render a given UI requirement
- underfetching: one endpoint doesn't return enough of the right data, requiring to send additional requests

Schema

- a contract between client and server
- gql uses a strong type system
- FE/BE teams can work independently after a schema is defined

Schema Definition Language (SDL)

- defines simple types
- can add a relation between multiple types

Query

- how a client expresses to a server its data needs
- contains a root field and payload

Mutations

- how changes are made to the db
- can be creating new data or updating/deleting existing data
- follow same syntactical structure as queries but contain the `mutation` keyword

Gql generates unique ids for each new type created.

- can create a new type and immediately ask for it's ID

Subscriptions

- client asks for event, gql initiates and holds a steady connection to the server
- uses subscription keyword
- represents a stream of data sent to the client

Schema

- typically seen as a collection of types
- specifies capability of the API
- defines how clients can fetch and update data

Root Types

- exist in schema
- define entry points for the API
- Query, Mutation, Subscription

Example of a full schema

```
// root types
type Query {
  allPersons(last: Int): [Person!]!

  // allows query for all posts
  allPosts(last: Int): [Post!]!
}

type Mutation {
  createPerson(name: String!, age: Int!): Person!

  // allows for update of a person
  updatePerson(id: ID!, name: String!, age: String!): Person!

  // allows for delete of a person
  deletePerson(id: ID!): Person!
}

type Subscription {
  newPerson: Person!
}

// custom types
type Person {
  name: String!
  age: Int!
  posts: [Post!]!
}

type Post {
  title: String!
  author: Person!
}

// limitation: doesnt identify the relation between a person and post
```

Architectural Use Cases

1. Gql server with a connected DB

- often for greenfield projects
- single web server that implements gql specification
- server resolves queries and constructs response with data that it fetches from db
- gql is transport layer agnostic. can be implemented with any network protocol - tcp, websockets etc.
- doesn't care about data storage format, SQL or noSQL

2. Gql server to integrate existing systems

- thin layer in front of third/party or legacy systems. Integrates them with a gql API
- compelling with legacy infrastructures
- gql used to unify existing systems and hide complexity of data fetching logic
- doesn't care about data sources (db, web services, 3rd party APIs)
- gql can consolidate legacy systems, microservices and 3rd party APIs

3. A hybrid appraoch with a connected db and integration of existing systems

Resolves functions

- how gql is flexible for above use cases
- gql queries/mutations consist of a set of fields
- gql server has one resolver function per field
- each resolver retrieves the data for it's corresponding field

Imperative Data Fetching

1. build and send HTTP request
2. receive and parse server response
3. store data locally
4. display data in UI

Declarative Data Fetching

1. describe data requirements
2. display information in the UI
3. everything else is done by the gql client (relay or apollo)

## Advanced

Fragments

- helps makes queries reusable by representing what data to query

eg:
Type

```
type User {
  name: String!
  age: Int!
  email: String!
  street: String!
  zipcode: String!
  city: String!
}
```

Fragment

```
fragment addressDetails on User {
  name
  street
  zipcode
  city
}
```

This means that these two are the same:

```
{
  allUsers {
    ... addressDetails
  }
}

// and

{
  allUsers {
    name
    street
    zipcode
    city
  }
}
```

Aliases (Named Query Results)

- lets you add aliases to queries:

```
{
  first: User(id: "1") {
    name
  }
  second: User(id: "2") {
    name
  }
}
```

where `first` and `second` are the aliases

Types

- Scalar types: String, Int, Float, Boolean, ID. can add custom scalar types
- Object types: have fields and are composable
- Enums (technically a specific type of scalar types)
- Interface: describes a type to be implemented later
- Union: "|" operator, describes when something an be either type
- Conditional fragments: to define fields on subtypes

```
{
  allPersons {
    name # works for `Adult` and `Child`
    ... on Child {
      school
    }
    ... on Adult {
       work
    }
  }
}
```
