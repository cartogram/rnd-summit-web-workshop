# Step 4

There is a lot of overlap between how queries and mutations work in Apollo and GraphQL. First let's head back to our GraphiQL interface.

MATT EXPLAIN MUTATIONS

Just like before, we copy our Mutation query and create new file in our CustomerIndex's graphql folder called `CustomerUpdateMutation.graphql`. Again, we will instantly see a type definition show up next to the file.

Next we follow all the same steps.

* Import the GraphQL file at the of the `CustomerShow.tsx` component.
* Import in a GraphQL HOC from utilities and compose it using the `compose` utility from quilt.
* Import and use the generic type definition from the GraphQL queries utilties.

Just like when we added our query, we had access to the `data` prop, when composing a new mutation we receive a `mutate` prop. This prop is a function that we can invoke to trigger our mutation. 

Often we have multiple mutations on a single React component. Our `graphql` HOC has a second options paramater, where we can give our mutation a name. Doing so allows us call `this.props.customerUpdateMutation()` to trigger our mutation.



add it to a onsubmit and add submit to the onAction, pulling that in from the FormState renderProp




---

------------------------**4:25pm (20min)**---------------------------

## Extra

delete customer

---------------------------**4:45pm (5min)**---------------------------

Explain

---------------------------**4:50pm **---------------------------

We saw that we had to refresh our page to see the results from the mutation. While Apollo’s store will attempt to handle the UI updates after a query or mutation automatically, in some cases we need to tell it explicitly how to do this. Removing or appending from a list, as we did when adding a product, is one such case where we need to tell the Store to refetch our Products list.

So far in this workshop we have been using the very basics of the graphql HOC, passing it a query and a component, but there is additional a configuration object that can be passed in. We are going to work with this configuration a lot in the remaining chapters, starting here by looking at the `refectchQueries`.

`refetchQueries` will execute queries and normalize the results of those queries into the cache. We are going to use this to refetch the Customers query from before, but you can also use it to fetch brand new queries.

We are passing `refetchQueries` an array of strings that our Apollo Client will then try and match with of any queries we have and re-execute them with their current variables. If you refresh the app in the browser, try our Mutation again, and see the UI update automatically.
