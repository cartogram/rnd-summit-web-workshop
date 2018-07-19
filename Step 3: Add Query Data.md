# Step 3

## What is GrapQL?

Does anyone not know what GraphQL is?

_(Ask someone in the audience)_

GraphQL is a query language and runtime that sits between the front-end client and back-end data service.

Does anyone know why we use it at Shopify?

_(Ask a few people in the audience to give reasons)_

It is easiest to explain why we are so excited about GraphQL by first identifying the central problems with its main predecessor: REST.

1.  **Fetching data often requires multiple HTTP calls.**

Examine the following endpoints:

```
/products
/products/1
/products/1/variants
/products/1/variants/1
```

Rest is resource based, each url points to a single resource, but our client almost always needs data form multiple resources ––products and variants, for example. This results in our client having to reach out to multiple URLs to fulfill all of its data requirements.

2.  **A REST endpoint has no way of knowing what the client is actually using.**

Examine the following JSON response:

```
product: {
	unused_foo: “foo”,
	unused_bar: “bar”,
	images: […]
	variants: {
	// …more fields
	}
	// …more fields
```

When our client requests a REST endpoint we have no way of knowing if they are actually using every piece of data we return. The obvious problem here is that we are over fetching, sending more information across the network then we need, but these unused fields do more than impact the size of the response. They could be costly to compute, resulting in needless effort on the server's part, and depreciating any of these fields is seen as too high a risk, we could unknowingly break clients that rely on any field we depreciate.

**3. A REST API is weakly typed and relies on documentation to express its capabilities and data.**

Examine the types of values here:

```
product: {
	price: “$100.00”,
	createdAt: “2006-04-17T21:22:48.2698750Z”
	…
```

Finally, REST APIs are usually weakly-typed and lack machine-readable metadata. While there exists some solutions to this problem (like JSON Schema), the reality is that most REST APIs do not provide this sort of data and rely on typically incomplete or out-of-date documentation.

Lets re-visit each of these points from the perspective of how they are solved by GraphQL.

1.  **Fetching data often requires multiple HTTP calls.**

```
One Endpoint: https://…/graphql

Post Requests

{
	"query": "...",
	"operationName": "...",
}

JSON Responses

{
	"data": { ... },
	"errors": [ ... ]
}
```

While REST uses "resources" as its core concept, GraphQL’s conceptual model is an entity graph. As a result, entities in GraphQL are not identified by URLs and instead we pass a query document to a single endpoint. The GraphQL backend parses this query and runs an associated resolver function of each field. These resolve functions now how to respond with the correct value, and can involve reaching out to different services, databases or respond immediately if no dynamic data is required. Our client however, is not concerned with this process and can safely request a field in the query and expect a response containing the value.

2.  **A REST endpoint has no way of knowing what the client is actually using.**

```
Example Query

{
	shop {
		id
		name
	}
}

Example Response

{
	“shop”: {
		“id”: “123"
		“name”: “Snow Devil”
	}
}
```

In a GraphQL API, the clients determine the shape of the response and can request only the data they need in their UI. This solves any over-fetching of data: a big performance win and productivity win for the Frond-End developer. Because these queries are data, the GraphQL API is aware of what fields are being used and can safely depreciate any unused components of the API.

3.  **A REST API is weakly typed and relies on documentation to express its capabilities and data.**

```
Schema.graphql

type Product {
	price: Int!
	createdAt: DateTime!
	taxable: Boolean
…

schema.graphql.d.ts

interface Product {
	price: Schema.Money;
	taxable: boolean;
	createdAt: Date;
…
```

In a GraphQL API, we have a typed schema and documentation is a first-class citizen. Documentation lives side-by-side with the code that constitutes it. There is a tool available, GraphiQL, that empowers anyone to explore an API and run queries on it. Additionally, we can use the types in our schema to inform our front-end Typescript types. This dramatically reduces the amount of type-related bugs making their way into production.

So to review the key benefits of GraphQL are:

1.  Performance (one end-point, no more over-fetching)
2.  Declarative Data-fetching (client asks only for what it needs)
3.  Developer Experience (self documenting, strong type system)

We are going to learn more about GraphQL as we go, but hopefully this gives you a good primer on the core concepts and some context about why we are committed to GraphQL at Shopify.

What questions do you have?

Find your shop ID in internal dashboard
https://app.shopify.com/services/internal/shops/15012958

GraphiQL
https://app.shopify.com/services/internal/shops/15012958/graphql

- Intro to GraphiQL
- Create customer query in GraphiQL
- Move the query to `.graphql` file
- Add `import`

- Intro to Apollo (Instead of Query component, use the Query HOC)
  https://www.apollographql.com/docs/react/essentials/queries.html#basic
- Add `compose` https://www.apollographql.com/docs/react/essentials/queries.html#props
- Add `ComposedProps` https://www.apollographql.com/docs/react/essentials/queries.html#render-prop

---

**Knowledge Stop**
typescript advance

Intersection Types

http://www.typescriptlang.org/docs/handbook/advanced-types.html

Genetics

## http://www.typescriptlang.org/docs/handbook/generics.html

- Output the query `data`
- load data in `ResourceList`
- Update **TEST**

## Extra

1.  `CustomerShow`: Pass URL Params from React Router to query variables in compose
2.  `CustomerIndex`: re-ordering
