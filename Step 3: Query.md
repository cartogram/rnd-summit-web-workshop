# Step 3 (30 min)

Now that we have our routes and components mocked up, we want to fetch real data from our shop. To do this we are going to use GraphQL.

## What is GrapQL?

Does anyone not know what GraphQL is?

_(Ask someone in the audience to explain)_

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

When our client requests a REST endpoint we have no way of knowing if they are actually using every piece of data we return. The obvious problem here is that we are over fetching, sending more information across the network then we need, but these unused fields do more than impact the size of the response. They could be costly to compute, resulting in needless effort on the server's part. lastly, depreciating any of these fields is seen as too high a risk, we could unknowingly break clients that rely on any field we depreciate.

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

While REST uses "resources" as its core concept, GraphQL’s conceptual model is an entity graph. As a result, entities in GraphQL are not identified by URLs and instead we pass a query document to a single endpoint. The GraphQL backend parses this query and runs an associated resolver function for each field. These resolver functions now how to respond with the correct value, and can involve reaching out to different services, databases or respond immediately if no dynamic data is required. Our client however, is not concerned with this process and can safely request a field in the query and expect a response containing the value.

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

First lets grab our shop's ID from (internal dashboard)[https://app.shopify.com/services/internal/shops/15012958] and go to the following link to load up the GraphiQL tool https://app.shopify.com/services/internal/shops/<YOUR SHOP ID>/graphql.

You will want to select the private app you created at the beginning of this workshop from the App dopdown list (it takes a long time to refresh). Now if you click the big green play button or `CMD` + `Enter` it will run the query on the left hand side and show the result on the right hand side. You just ran a GraphQL query.

Let's start writing the query we need on `CustomerIndex`. The first thing we need to write it our operation type. This can be either `query` (reading data), `mutation` (writing data) or `subscription` (subscribing a stream of changes on a node). We are only going to talk about `query` and `mutation` in this workshop, and in our case we want to read data for our page so we want to write a query. 

Next we are going to add the operation name, which goes after the operation type and can be any meaningful name we want. This is generally used for debugging and server-side logging. We are going to call our mutation `CustomerIndex`.

If we look back at our [mock up](https://projects.invisionapp.com/share/4DN0ENNZMGA#/screens/309239104) we see need to request a list of customers with their name and length of time they've been customers. At this point, we don't know the name of the query we want, but we can use the Schema to figure this out.

In GraphiQL the Schema can be explored using the drawer on the right hand side called `Docs`. We can explore the schema to determine all of the queries we can run on our GraphQL API as well as the Types of all the fields and variables.

Our final query looks like

```
query CustomerIndex {
  customers(first: 10) {
    edges {
      node {
        displayName
	lifeteimeDuration
      }
    }
  }
}
```

What questions do you have?

Next we want to actually use this query in our app. To do this we need talk about Apollo.

## Introduction to Apollo

Apollo is a community that builds a number of tools for developers to work with GraphQL on both the server and the client. We are going to use their JavaScript client for React in order to work with our GraphQL API on the frontend. GraphQL clients exist to abstract the common tasks that are agnostic to your specific app, such as sending queries and mutations, low-level networking details and maintaining a local cache. Apollo is one such client that we currently use in Shopify/web, but there are others such as Facebook's Relay and Lokka.

Apollo, thankfully, comes with our Webgen scaffold when you selected `yes` to GraphQL. 

Let's copy our query that we wrote and create new file in our CustomerIndex's graphql folder called `CustomerIndexQuery.graphql`. You will instantly see a type definition show up next to the file. 

> ### .graphql.d.ts
> You should see instantly, the `.graphql.d.ts` file that show up beside your `.graphql` file.
> This graphql typing is generated automatically for you by sewing-kit using the `schema.graphql` file from the graphql server. This typing can than be imported into your component to ensure typing continuity. 

To use this new query we are going to import a `graphql` component. 

`import {graphql} from 'utilities/graphql';`

This component is a React Higher Order Component (HOC), this is another compositional pattern in React similar to RenderProps (in fact, Apollo also exposes a Query component that uses a RenderProp, but we perfer to use the HOC versions in Shopify Web). HOCs are a function that take a component and return a new, enhanced component. When we use the `graphql` function to wrap our CustomerIndex component, we are enhancing it by giving it the ability to communicate, through Apollo, to our GraphQL API.

To implement this HOC component we are going to use another quilt library called `compose`.

`import compose from '@shopify/react-compose';`

This is a common utility that can be found in a number of different packages such as Redux, Recompose, and even Apollo has its own `compose` utility. We built and prefer to use the quilt implementation because it is standalone, its just a light-weight library that doesn't rely on those other frameworks, and has a less cumbersome Typescript implementation, which we will see for ourselves soon.

To add our composed component with graphql, we make the following changes.
```diff
- export defualt CustomerIndex;
+ export default compose<Props>(graphql())(CustomerIndex);
```

Next we want to import this GraphQL file into our `CustomerIndex.tsx` component, and add this as the first argument to our graphql function.

```diff
+ import customerIndexQuery, {
+   CustomerIndexQueryQueryData,
+ from './graphql/CustomerIndexQuery.graphql';

...

- export defualt CustomerIndex;
+ export default compose<Props>(graphql(customerIndexQuery))(CustomerIndex);
```

```diff
export interface Props {}
+ type ComposedProps = Props & GraphQLQuery<CustomerIndexQueryQueryData>;
```

Lets recall the type generic we talked about earlier, and introduce a new concept call Intersection types that allow us to combined properties of all of the types being intersected using the `&` operator. We will than replace the `Props` of our `CustomerIndex` to denote all the props that this component has access to from the component itself and from all of the composed HoCs.

```
-class CustomerIndex extends React.PureComponent<Props>
+class CustomerIndex extends React.PureComponent<ComposedProps>
```

With this HOC successfully implemented, we now have a `data` prop that contains all the information about our GraphQL query: whether it is still loading the data, whether there was an error, some other helpful methods for refetching and polling, and ofcourse, the return data itself. Like we talked about earlier, this data comes back to us in the same shape as our query.

Next we just need to fill in the real data in place our mock data.

---

- Add `compose` https://www.apollographql.com/docs/react/essentials/queries.html#props
- Add `ComposedProps` https://www.apollographql.com/docs/react/essentials/queries.html#render-prop


## Excercise

1.  `CustomerShow`: Pass URL Params from React Router to query variables in compose
2.  `CustomerIndex`: re-ordering

Apollo Doc about what options the HoC has: https://www.apollographql.com/docs/react/essentials/queries.html#props
Apollo Doc about what props are added by the Hoc: https://www.apollographql.com/docs/react/essentials/queries.html#render-prop

