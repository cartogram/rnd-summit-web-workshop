---------------------------**4:05pm (20min)**---------------------------

explain mutation (in Grqphil, it doesnt actually work)

Let's copy our query that we wrote and create new file in our CustomerIndex's graphql folder called CustomerUpdateMutation.graphql. You will instantly see a type definition show up next to the file.

import the graphql file at the of the component
pull in a graphql HOC from utilities and compose it.
pull in the generic type definition from the graphql queries utilties.


add the mutation to graphql.hoc with a name, and show `this.props.customerUpdateMutation()`
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
