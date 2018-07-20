# New Section (20 min)

## Adding a new Route and Component

Now that we have our basic app scaffold, lets build out a new section for our Customers routes. Make sure your dev server is running at this point. 

The first thing we are going to do is create a new folder under `app/sections`, call this folder `Customers`.

Now, in your terminal, type `yarn create component CustomerIndex`. When you run this commnad, it will provide for you a list of possible places you can generate this component.

No, we will not require graphql.
No, we do not need css.

There are three main type of components that we typically build with React, Pure Component (pure), Stateless Functional Component (function), and Component (class). Stateless Funcational Components, is just a function that takes arguments (props) and returns markup (JSX). They do not have state and generally pretty simple: lovingly referred to as "dumb". Next we have Pure and Class Components. These are the basic building block of a Class that you extend from the React the library, they are stateful, can use the React lifecycle events and usually more complex than Stateless Functinal Components. In most cases, you want to use a Pure Component over a Class Components because they are more performant. React components re-render themselves when their props or state change, this can be an expensive operation, and we want to limit the times this happens to only when we need it. Pure components will re-render less often because they use shallow comparison of their props and state.

So in this case, let's go with the pure component with is the default.

Now if you go into your `sections/Customer` folder, you should see a CustomerIndex component that has been scaffolded out for you. With a index, tests folder and `CustomerIndex.tsx` component file. 

In the `section/Customer/index.ts` this is the file you will use to import the components in this section with their appropriate routes. Let's copy and paste the contents of `Sectiongs/Home/index.tsx`. In the future, the Webgen should take care of this for you. Rename all of the `Home` to `Customer` (you can use `CMD` + `D` to help).

Go into `section/index.tsx` and export the Customers section from this file.

Next go to the `app/foundation/routes` file and add a new route. Copy and past the base route of the application and change the `Home` component to `Customer` instead. Remove the `exact` prop, rename the path to `/customers` and the component to the `Customers` component we just generated, import this component as well.

Now if you go to `localhost:8081/customers`, you will see this component being rendered. It just says "CustomerIndex" as that is the default from Webgen.

What question do you have?

## Adding Polaris components

Now that we have our page at our desired route, let's bring in Polaris and make it look better. Let's look back at our mock up on Invision.

INSERT LINK

We can see a list of customers with some avatars and a Page with a title. In Polaris, we convienently have a Page component. Head to [polaris.shopify.com](polaris.shopify.com) and search for Page.

Copy and paste the code snippet and paste it into our `CustomerIndex.tsx` page. When you save, prettier should take care of most of the formmating errors except that Page is not defined. We need to import it from polaris.

Add `import {Page} from '@shopify/polaris'` to the top of the page. Note, Webgen has taken care of installing this for us when we generated our app.

Delete all the props other than `title`, change this to `Customers` and refresh the page. You should see a Page with the title `Customers`. Sweet.

Next we need a `ResourceList` so head back to [polaris.shopify.com](polaris.shopify.com), search `ResourceList` and copy and paste the code snippet again. Paste it inside the `Page` component as its children.

We first need to import the new components. Make the following changes.

```diff

-import {Page} from '@shopify/polaris'
+import {Page, ResourceList, Card, Avatar, TextStyle} from '@shopify/polaris'
```

We still have one error on our renderItem prop, we will resolve this by adding a new private function for this prop. Underneath our render function, add a new private method and copy and paste the body of the inline function to this private method.

```
private function renderItem(item: type) {
  const {id, url, name, location} = item;
  const media = <Avatar customer size="medium" name={name} />;
  return (
    <ResourceList.Item
      id={id}
      url={url}
      media={media}
      accessibilityLabel={`View details for ${name}`}
    >
      <h3>
        <TextStyle variation="strong">{name}</TextStyle>
      </h3>
      <div>{location}</div>
    </ResourceList.Item>
  );
}
```

Next change the `renderItem` prop to use this method.

```diff
-renderItem={(item) => {
+renderItem={this.renderItem}
```

6. Add mock data on top of `CustomerIndex`

---

**Knowledge Stop**
typescript the basic during 7.
http://www.typescriptlang.org/docs/handbook/basic-types.html

- read `Page` & `ResourceList` Props type
- follow prop using `command+click`
- create error & read the error from below

---

---------------------------**2:15pm (20min)**---------------------------

## `CustomerShow`

React Router URL Params
https://reacttraining.com/react-router/web/example/url-params

---------------------------**2:35pm (5min)**---------------------------

Solution Explained

---------------------------**2:40pm**---------------------------

15 min break

---------------------------**2:55pm**---------------------------
