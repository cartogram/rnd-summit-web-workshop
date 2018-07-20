# Step 1 (30 min)

We are going to start by using Webgen to scaffold out our new project. Please bear in mind that the Webgen CLI is likely to go through drastic changes in the future. We put this together very quickly for this workshop and it is very early days for us working on it.

## Using Webgen to scaffold app

### Prequisites

In order to use Webgen, we reccommend you use download and use VS Code. This is not manditory, but there is an enhance developer experience that is internally support that you don't want to miss out on.

* https://code.visualstudio.com/

Make sure you have installed Node version ^8.9.4 and make sure you have `yarn`.

* https://yarnpkg.com/en/docs/install#mac-stable
* https://nodejs.org/en/

https://gist.github.com/michelleyschen/0a9c9ee2bc34e0ad273d5879e240ed59#file-step-1-setup-a-web-app-md

### The Commands

Every Webgen command is prefixed with `yarn create shopify`. The command to scaffold a new app is called `app`.

`yarn create shopify app`

Notice, every part of this command has a space between it. Before running this, lets add the `--help` flag so we can see what sort of options we can pass to this command.

`yarn create shopify app --help`

As you can see, this command (and every Webgen command) takes just one required argument, `<name>`, and a number of options flags (prefixed with `--`). These flags can be used to configure the final app output, but we can also follow a series of prompts that are displayed after we run the command. The answers to the prompts will overide any flags for the same configuration, and you can by-pass the prompts by setting the `--prompt` flag to false.

We are just going to run the command with defaults, so in your teminal run:

`yarn create shopify app CustomersApp`

### App Structure Walkthrough

In the root of this project, there are a number of configurations files and a few Javascript files to get us started. It is always best to start with the `package.json`, this file tells you what npm packages are included and a few sensible default scripts that you will run. If you were to open your console and run `yarn && yarn dev`, that will start the development server on your computer. It will take a moment to compile (and show a waiting screen on localhost: 8080) but when ready you can visit `localhost:8080` and see the default app with a title. Go to `app/sections/Home/HomeIndex/HomeIndex.tsx`.

_(Hot Tip: You can use `CMD` + `P` to search for this file.)_

This is code responsible for what you just saw in the browser. It pulls in a `Page` component from Polaris and fills in the title prop from the `name` argument from the Webgen command on the page.

If you go up a folder to `sections`, every folder here should map to a first-level route in your application. Home is slightly unique because it is responsible for the `/` route, but when we create our next section you see more of how this works in most cases.

Components can be stored a few places in our app, the first one being the `app/components` folder. Thinking back to the priciple of "Isolation over integration", components are only stored in their highest shared level. This `componenents` folder ––being at the `app` level–– should only contain components that are reused throughout your entire application, but you will often have other, area-specific `components` folders throught your app. Components will often start out being very specific, and move upwards as they becomes more generic.

Next, in `app/foundation` this is where we store single-use components that are usually involved in the base setup of your app. Things like Routes will live here as well as various providers _(potentially explain provider)_ in your application. 

This default app is actually a server-side renderering node and react application, which means the initial render happens on the server using node, but eact subsequent render happens on the client, and we split the code responsible for each in their respective folder: server code lives in `server` and client code lives in `client`. 

Open up `sever/index.js`, this is the entry point of our application on the server which imports a new Koa app.

_Who has heard of Koa?_

Koa is a minimalistic node framework for modern Javascript apps that we use extensive at Shopify. It is built around the ES2016 `async` and `await` keywords.

In Koa you express your application logic as a series of asynchronous functions called middleware, which is just a fancy word for functions that all operate on a `context` or `ctx` object, and await on a `next` function to yield flow back into the rest of the app. Before we actually start building out our app, let's just write some code to better explain this concept and comment out the `session` `shopifyGraphQLProxy` and `renderApp` middlewares.

We are going to add another middleware function. This going to be an `aysnc` function with a second `next` paramater. This is a function that resolves to a promise that we can `await` on, telling Koa to pause this current middleware and move on to the next one in the chain.

```js
app.use(async function(ctx, next) {
  console.log('Middleware 1');
  await next();
});
```

_an example koa middleware_

If you refresh the browser where you are running the app it should still look the same, but you should see `Middleware 1` printed in the console. Let's add another middleware, but this time after our initial middleware. So our entire middleware chain looks like this.

```js
app.use(async function(ctx, next) {
  // log 'Middleware 1'
  console.log('Middleware 1');
  // then pause and wait for the next middleware
  await next();
});

app.use(function(ctx) {
  // then log 'Middleware 2'
  console.log(`Middleware 2`);
  // and set the body of our response to 'Hello Unite'
  ctx.body = 'Hello Unite :)';
});

app.use(async function(ctx, next) {
  // the app will never get here
  console.log('Middleware 3');
  await next();
});
```

_example koa middlewares with a terminating middleware_

If you refresh the browser, you should see `Middleware 1` and `Middleware 2` printed in the console, but not `Middleware 3`. That’s because Koa ends the request once the middleware Promise chain is resolved. That means the response was sent to the client before we got to our third middleware. We can solve this by changing our index function into an `async` function that has `await next()` like our other middleware does.

We said Koa "pauses" the excecution of the function, so let's log some messages after our `next()` calls to get a better idea of what is meant by this.

```js
app.use(async function(ctx, next) {
  // log 'middleware 1'
  console.log('middleware 1');
  // then pause and wait for the next middleware
  await next();
  // then log 'back to middleware 1'
  console.log('back to middleware 1');
});

app.use(async function(ctx, next) {
  // log 'middleware 2'
  console.log(`middleware 2`);
  // then set the body to 'Hello Unite :)'
  ctx.body = 'Hello Unite :)';
  // then pause and wait for the next middleware
  await next();
  // then log 'back to middleware 2'
  console.log('back to middleware 2');
});

app.use(async function(ctx, next) {
  // first log middleware 3
  console.log('middleware 3');
  // then wait for any further downstream middleware
  await next();
  // then log back to middleware 3;
  console.log('back to middleware 3');
});
```

_example koa middleware with logic after next()_

This time in the console, we see this:

```bash
Middleware 1
Middleware 2
Middleware 3
Back to Middleware 3
Back to Middleware 2
Back to Middleware 1
```

_example console log_

As you can see, Koa made its way up the middleware chain pausing each function when we `await` on `next()` before passing the flow to the next middleware in the chain. It does this until there are no more middleware left and then it resumes each middleware in the reverse order they were added.

As we gradually back the middleware that we commented out see how it affects the initial render of our app.

Are there any questions about Koa so far?

### What are we going to build?

Today we are going go step by step and build a simple Customer management Shopify app that uses GraphQL to query and mutate data from the Shopify Admin GraphQL API. It will:

* List the first 10 customers from your shop
* Provide editing and saving cababilities for each customer

As front-end developers we often start with a mock from a designer, lets have a look at what might this look for our app.

* **Invision Link:** https://invis.io/4DN0ENNZMGA#/309239104_Customer_Index
