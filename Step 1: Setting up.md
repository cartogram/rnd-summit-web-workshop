---------------------------**1:45pm(10min)**---------------------------
# Step 1

We are going to start by using Webgen to scaffold out our new project. Please bear in mind that the Webgen CLI is likely to go through drastic changes in the future. We put this together very quickly for this workshop and it is very early days for us working on it.

## Using Webgen to scaffold app

https://gist.github.com/michelleyschen/0a9c9ee2bc34e0ad273d5879e240ed59#file-step-1-setup-a-web-app-md

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


---------------**1:55pm**---------------------------
