---------------------------**1:55pm (20min)**---------------------------

Now that we have our basic app scaffold, lets build out a new section for our list of Customer app. We are going to confine to a new section.

TODO either writh this out or use webgen

## Typescript Intro

* static type language =>the type of variable is known at **complie time**
* is optional
  * live in your `.ts`, `.tsx` file as you write `.js` or `.jsx`
  * but it complies, can can be written seperatly in `.d.ts` file. Majority of `.d.ts` are generated
* Props of TypeScript
  * Ramp up are difficult, but many long terms benefit
  * Find more errors at complie time
    ie. `null` & `undefine` edge cases
  * Documentation Tool
    ie. show `cmd+click` & ``
  * Community Support
    ie. show DefinitelyTyped, `@types` in `package.json`, (sometime better doc of the API than offical doc, but also sometime wrong)
  * Greater confidence when refactoring
    ie. will see in the project

## Add Route
1. Look at mock and mentally block out components
2. We have two pages so lets setup route & section
3. Explain container component vs regular component

## Add container component
1. Use `yarn create shopify component NewComp` to create `CustomerIndex` (maybe?)
2. Show this in browser in route

## Builid the Page itself
5. Goto [polaris.shopify.com](polaris.shopify.com) to find `Page` & `ResourceList`
6. Add mock data on top of `CustomerIndex`
7. Put in `Page` > `ResourceList` (parseGid)

---------------------------**2:15pm (20min)**---------------------------

## `CustomerShow`

React Router URL Params
https://reacttraining.com/react-router/web/example/url-params

---------------------------**2:35pm (5min)**---------------------------

Solution Explained

---------------------------**2:40pm**---------------------------

15 min break

---------------------------**2:55pm**---------------------------
