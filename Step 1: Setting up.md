---------------------------**1:45pm(10min)**---------------------------
# Step 1

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

Notice, every part of this command has a space between it. Before run this, lets add the `--help` flag so we can see what sort of options we can pass to this command.

`yarn create shopify app --help`

As you can see, this command (and every Webgen command) takes just one required argument, `AppName`, and a number of options flags (prefixed with `--`). These flags can be used to configure the final app output, but we can also use follow a series of prompts that are displayed after we run the command. The answers to the prompts will overide any flags for the same configuration, and you can by pass the prompts by setting the `--prompt` flag to false.

We are just going to run the command with defaults, so in your teminal run:

`yarn create shopify app CustomersApp`

## Project Sturcture Explain
https://github.com/michelleyschen/shopify-app/blob/master/README.md

### What are we going to build?

https://gist.github.com/michelleyschen/0a9c9ee2bc34e0ad273d5879e240ed59#file-step-0-building-an-app-md

Today we are going go step by step and build a simple Customer management Shopify app that uses GraphQL to query and mutate data from the Shopify Admin GraphQL API.

### App Structure Walkthrough

In the root of this project, there are a number of configurations files and a few Javascript files to get us started.

Open up `index.js`, this is the entry point of our application which imports `index.js` and mounts it on port 3000.

Koa is a minimalistic node framework for modern Javascript apps that we will be using for our server in this workshop. It is built around the ES2016 `async` and `await` keywords.

In Koa you express your application logic as a series of asynchronous functions called middleware, which is just a fancy word for functions that all operate on a `context` or `ctx` object, and await on a `next` function to yield flow back into the rest of the app. Before we actually start building out our app, let's just write some code to better explain this concept.

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

Are there any questions about Koa so far?


---

-

---------------**1:55pm**---------------------------
