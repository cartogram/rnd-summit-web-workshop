# Step 4: Mutations (40 min)

Where queries are to reading from a data store, mutation are to writing to it, and there is a lot of overlap between how these both work in Apollo and GraphQL.

First let's head back to GraphiQL.

Mutations follow the same syntactical structure as queries, but they always need to start with the `mutation` keyword, start by typing `mutation` followed by opening and closing curly braces `{}`. Again, this is called the operation type.

For our case, we want to update our customer. Once again let's use the GraphiQL Docs interface to find the mutation we want from our Schema that corresponds to updating a customer, `customerUpdate`. Add `customerUpdate` inside the curly braces and finally give our mutation an operation name of `CustomerUpdate`.

>Note: In the Shopify GraphQL API mutations and queries are always named resource-first. This is by design, making it easy to find all possible operations on a single resource when they are ordered alphabetically. 

```graphql
mutation CustomerUpdate {
  customerUpdate() {
  }
}
```

Next in our variable definitions we declare the types of variables you are planning to provide. In our case we have one variable, we are going to make a new varible `$input` of type `!CustomerInput`. We get this information from the schema and Docs side bar in GraphiQL. In GraphQL, variables are prepended by the `$` sign. We also want to pass this new variable down to our mutation, which is expecting an `input` parameter. All together our mutation should now look like this.

```graphql
mutation CustomerUpdate($input: CustomerInput!) {
  customerUpdate(input: $input) {
  }
}
```

Finally, like when writing a query, we’re also able to specify a fieldset for a mutation in which we can ask for different properties back on the updated object. This is a unique and interesting characteristic of GraphQL mutations: after they complete the 'write' part of their operation, they can follow that with a 'read'. We are going to ask for the `id`, `firstName` and `lastName` files on the customer we just updated.

Below that we also want to request the `UserError` field which will return an array of validation errors that might occur during our mutation. 

In terms of IDL, the UserError type looks like this:

type UserError {
  message: String!
  field: [String!]
}

The message is the error message that should be shown to the end user while field is an array of strings, forming a path to the field in error. We won't go into these in today's workshop, but it's good practise to request these fields with most mutations.

```graphql
mutation CustomerUpdate($input: CustomerInput!) {
  customerUpdate(input: $input) {
    customer {
      id
      firstName
      lastName
    }
    userErrors {
      field
      message
    }
  }
}
```

With our mutation now complete, you will notice that we cannot run it inside of GraphiQL. This is because the GraphiQL interface is 'read only'. We can, just like before, copy this mutation and create new file in our `CustomerShow`'s graphql folder called `CustomerUpdateMutation.graphql`. Again, we will instantly see a type definition show up next to the file.

Next we follow all the same steps.

* Import the GraphQL file at the of the `CustomerShow.tsx` component.
* Import in a GraphQL HOC from utilities and compose it using the `compose` utility from quilt.
* Import and use the generic type definition from the GraphQL mutation utilties. This time also passed in `Varaibles`

Just like when we added our query, we had access to the `data` prop, when composing a new mutation we receive a `mutate` prop. This prop is a function that we can invoke to trigger our mutation. 

Often we have multiple mutations on a single React component. Our `graphql` HOC has a second options paramater, where we can give our mutation a name. Doing so allows us to call `this.props.customerUpdateMutation()` to trigger our mutation. 

```ts
export default compose<Props>(
  // other HOCs...
  graphql(customerUpdateMutation, {
    name: 'customerUpdateMutation',
  }),
)(CustomerShow);
```

We are going to add a new private method to take care calling our mutation when the form submits and sending in the fields we want to update as variables.

```ts
@autobind
private async handleCustomerUpdate({fields}: FormData<Fields>) {
  const {
    customerUpdateMutation,
    match: {
      params: {customerId},
    },
  } = this.props;

  await customerUpdateMutation({
    variables: {
      input: {
        id: composeGid('Customer', customerId),
        firstName: fields.firstName.value,
        lastName: fields.lastName.value,
      },
    },
  });
}
```

We want to add this method to the `onSubmit` prop of `FormState`:

```ts
onSubmit={this.handleCustomerUpdate}
```

We can then trigger this mutation in our RenderProp using the `submit` property. We will add as the `onAction` of our `PrimaryAction` for the `Page`.

```ts
return (
  <FormState
    initialValues={initialValues}
    onSubmit={this.handleCustomerUpdate}
  >
    {(formDetails: FormDetails<Fields>) => {
      const {fields, submit, submitting, dirty} = formDetails;

      return (
        <Page
          title="Customer"
          breadcrumbs={[{content: 'Customers', url: '/customers'}]}
          primaryAction={{
            content: 'Save',
            onAction: submit,
            loading: submitting,
            disabled: !dirty,
          }}
        >
          <FormLayout>
            <TextField label="First Name" {...fields.firstName} />
            <TextField label="Last Name" {...fields.lastName} />
          </FormLayout>
        </Page>
      );
    }}
  </FormState>
);
```

With all that completed, we can go back to our browser and update customers. However, if we navigate back to `CustomerIndex`, we have to refresh our page to see the results from the mutation. While Apollo’s store will attempt to handle the UI updates after a query or mutation automatically, in some cases we need to tell it explicitly how to do this. Removing or appending from a list, or updating two seperate query results, are some cases where we need to tell the local Store to refetch our list.

So far in this workshop we have been using the very basics of the graphql HOC, passing it a query and a component with some variables, but there are many additional configurations that can be passed in. One such configuration is called `refectchQueries`.

`refetchQueries` will execute queries and normalize the results of those queries into the cache. We are going to use this to refetch the Customers query from before, but you can also use it to fetch brand new queries.

```ts
graphql(customerUpdateMutation, {
  name: 'customerUpdateMutation',
   options: {
    refetchQueries: ['CustomerIndexQuery'],
  },
}),
```

We are passing `refetchQueries` an array of strings that our Apollo Client will then try and match with of any previouisly fetched queries we have and re-execute them. In our case, we want to re-fetch the `CustomerIndexQuery`. If you try our mutation again, now you can see the UI update automatically.


## Excercise: Delete Customer

Now we are going to set you off on your own for another excercise to delete customers.

* Another Graphql Mutation for deletion
* Add this new query to the graphql HOC
* Write the UI to Trigger the mutation



