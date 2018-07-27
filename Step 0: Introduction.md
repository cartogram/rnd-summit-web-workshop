## Introduction (20 min)

Hello and welcome to the Web Workshop.

## Workshop Goals

Now that we all know eachother a bit better, let's go over what we are going to cover today.

1.  Introduce you to a number of foundational principles and best practises for web development at Shopify.
2.  Demonstrate some new tools we've been working on help get you productive quickly.
3.  Expose you to different parts of the Web stack (Typescript, GraphQL, Polaris), giving you a base understanding of their individual purpose as well as how they work together.

### What Best Practises?

The Web Foundations team has created a living guide for how we build web UI at Shopify. This is a single Github repo that captures the principles used to make decisions, best practices and styleguides. Let's take a look at this repo together now.

- [Web Foundation Github Repo](https://github.com/Shopify/web-foundation)

If you go in the Decisions Records, you will see some history on why we do certain things, such as using React and Apollo, and why we don't do certain things like avoiding TestIDs and avoiding Jest snapshots.

But one of the most important parts of this repo is the Principles folder. The main principles are,

* **User over team over self**: The enduser (ususally merchants, sometimes other shopifolk) matter most in the decisions we make. If the enduser is not impacted one way or the other, make the decision that is best for your team, then finally consider yourself. This prioritization is the most important of the priciples and the others are simply more specific applications of optimizing for our users, over our team, over ourselves.

* **API over implementation**: Another way to phrase this principle is to expose only what you need to, never more. Exposing implementation details externally makes the piece of code harder to understand. We will come across this later in the workshop when we start looking at components. Components collect all the languages that go into creating them (at a minimum: CSS, HTML, and JavaScript) and expose an API for consuming them. In this way we separate concerns by component (API), not by language (implementation). We will be looking at a lot of React code today, which is very much the embodiment of this principle. 

* **Explicit over automatic**: As software developers we often try to be as DRY as possible, but we should never sacrifice readibility for DRYness. A great example of this pinciple is how we use CSS modules, explicitly providing styles at a component level rather than relying on the CSS cascade.

* **Community over ownership**: We are happy to use well-supported community projects over buiding things custom solutions, but we need to remember our first pricinple, place the user above ourselves. We often wrap third-party tools in first-party tools and expose a well-supported default. This is best demonstrated by Sewing Kit, which takes a set of tools we believe to be best-in-class and makes them trivially easy to use.

* **Declarative over imperative**: We prefer to write declarative code. Declarative code is generally more concise, readable and composable. We prefer to render a component that encapsulates a series of imperative DOM actions, this is why you will see render-less components such as `<EventListener />` or `<ScrollLock />`, than to expose classes or functions that one would call directly. Does anyone know what we mean when we say render-less component?

* **Isolation over integration**: We prefer to write code that exists independently. This isolation makes code easier to test, reuse, refactor, and remove, without any impact on the rest of the codebase. Think of Polaris, which is an isolated set of mostly presentational components: render markup only. They are highly shareable among many projects at Shopify. Likewise, we also have Quilt, which is a isolated set of non-presentational libraries that you can pull into your project as needed. This rule is bit trickier to understand, but think of it as compositon over inheritence. Think of when you might archetect a parent with child components, the parent would be responsible for passing layout to the parent. 

We are going to put many of these foundational principles into practise today.
