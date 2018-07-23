# Step 4: Mutations (40 min)

Where queries are to reading from a data store, mutation are to writing to it. There is a lot of overlap between how queries and mutations work in Apollo and GraphQL. First let's head back to our GraphiQL interface.

Mutations follow the same syntactical structure as queries, but they always need to start with the `mutation` keyword, start by typing that followed by opening and closing curly braces `{}`. This is called the operation type.

There generally are three kinds of mutations: creating, updating and deleting. For our case, we want to update our customer. so let's use the GraphiQL Docs interface to find the mutation we want update our customer, `customerUpdate`. Add that inside the curly braces. We are going to call our mutation `CustomerUpdate`.

```graphql
mutation CustomerUpdate {
  customerUpdate() {
  }
}
```



Next in our variable definitions we declare the types of variables you are planning to provide. In our case we have one variable `input` of type `!CustomerInput`. We get this information from the schema and Docs side bar in GraphiQL. We also want to pass this variable down to our mutation. 

```graphql
mutation CustomerUpdate($input: CustomerInput!) {
  customerUpdate(input: $input) {
  }
}
```

Finally, like with a query, we’re also able to specify a fieldset for a mutation in which we can ask for different properties back on the updated object.  


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

Just like before, we copy our Mutation query and create new file in our CustomerIndex's graphql folder called `CustomerUpdateMutation.graphql`. Again, we will instantly see a type definition show up next to the file.

Next we follow all the same steps.

* Import the GraphQL file at the of the `CustomerShow.tsx` component.
* Import in a GraphQL HOC from utilities and compose it using the `compose` utility from quilt.
* Import and use the generic type definition from the GraphQL queries utilties.

Just like when we added our query, we had access to the `data` prop, when composing a new mutation we receive a `mutate` prop. This prop is a function that we can invoke to trigger our mutation. 

Often we have multiple mutations on a single React component. Our `graphql` HOC has a second options paramater, where we can give our mutation a name. Doing so allows us call `this.props.customerUpdateMutation()` to trigger our mutation. 

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

This will add a prop to trigger this mutation in our RenderProp, which we will add as the onAction of our PrimaryAction.

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

With all that complete, we can go back to our browser and update customers. However, we have to refresh our page to see the results from the mutation. While Apollo’s store will attempt to handle the UI updates after a query or mutation automatically, in some cases we need to tell it explicitly how to do this. Removing or appending from a list, as we did when adding a product, is one such case where we need to tell the Store to refetch our Products list.

So far in this workshop we have been using the very basics of the graphql HOC, passing it a query and a component, but there is additional a configuration object that can be passed in. We are going to work with this configuration a lot in the remaining chapters, starting here by looking at the `refectchQueries`.

`refetchQueries` will execute queries and normalize the results of those queries into the cache. We are going to use this to refetch the Customers query from before, but you can also use it to fetch brand new queries.

We are passing `refetchQueries` an array of strings that our Apollo Client will then try and match with of any queries we have and re-execute them with their current variables. If you refresh the app in the browser, try our Mutation again, and see the UI update automatically.

```ts
graphql(customerUpdateMutation, {
  name: 'customerUpdateMutation',
   options: {
    refetchQueries: ['CustomerIndexQuery'],
  },
}),
```


## TODO

delete customer



