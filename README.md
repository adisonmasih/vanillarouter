# VanillaRouter
Vanilla Router Is A Open-Source Library That Allows You To Implement No-Reload Page Routing using `history.pushState`, Support For HTML Components & Their Inclusion With In-Built Support For [TopBar.js](https://github.com/buunguyen/topbar).

# Version
Currently This Is The First Release Of Vanilla Router named **0.1 Alpha**.
It Is In Rapid Development & Bugs Can be Seen.

# Implementations

 - On-Page / No-Reload Page Routing
 - HTML Components (Referred To As **Drops**)
 - [TopBar.js](https://github.com/buunguyen/topbar) Support Included

# Documentation
## Including
Include Vanilla Router By Adding Either The File, Or The CDN To Your **`<Head>`** Tag
> **NOTE:** It is important to include the CDN / File Only In Your **Head** Tag
```
<head>
  <title>...</title>
  <script src="./vanillarouter.min.js"></script>
  // Or
  <script src="https://cdn.jsdelivr.net/gh/Adison-Masih/vanillarouter/vanillarouter.min.js"></script>
</head>
```
## Initializing
Initialize A New VanillaRouter Instance Like This:
```
const router = new VanillaRouter()
```
### Options
Vanilla Router Gives You Three Initialization Options:

- `useTopBar` is used to provide if you want to use the TopBar Package . Its Default Value Is `true`. You Will Learn about it later in this doc.
- `topBarConfig` is used to configure the `topbar` package. if not provided (or provided `none`), default configuration is used.
- `isProduction` can be used to avoid any errors that happen during runtime. default value is `false`. if set to `true`,  no errors will be thrown even when there are any (only Valid for VanillaRouter Package).

```
const router = new VanillaRouter({
  useTopBar: true,
  topBarConfig: 'none',
  isProduction: true,
})
```
## Structure
### The `<routable>` Element
Every Page you want to route to needs to contain a `<routable>` tag inside of the absolute `<body>` tag. All The contents from the routable will be taken by the demanding page when routed to this page. that means, anything outside the `<routable>` tag will not be rendered / added to DOM when routed to.

This Tag accepts three `html attributes`:
* `title`
* `script`
* `css`

The `title` attribute of the `<routable>` element will become the `<title>` of the page when  **ROUTED TO**.

```
<body>
  <routable title="Enter Page title here">
    Your Content Here...
  </routable>
</body>
```

Though You Can include as Many Scripts inside the `<routable>` element, they will only stay inside the `<routable>` element itself and will not be appended to the `<body>`.

```
<routable>
  <script>let x</script> // These Scripts
  <script>let y</script> // Will Stay Inside
  <script>let z</script> // The <routable> Element
</routable>
```

But This Behaviour Can Be Overriden For `one` script element by providing it with a unique `id` attribute and then passing the value of `id` attribute to the `script` attribute of the `<routable>` element

```
<routable script="myScript">
  <script id="myScript">let mine = "yours!"</script> 
  // This Script will be Appended to the end of the body when routed to the this page.
</routable>
```

As for the `script`, `css` attribute has the same story, add an id to the style element, pass it to the `css` attribute and it will be appended to the `<head>` element when routed to the current page.

### Absolute Path
If your application is in a directory or some nested url, you might need to consider absolute paths. it is by default set to `/`. you can change it globally by:
```
router.setABSPath('/your/nested/url')
```

###  How To Navigate
Well the part you've been waiting for is here. to navigate to a page, you need to use the `router.to()` method provided by the vanilla router instance. this method is `async` and can be `awaited`. it uses the `history.pushState` method to change the `url` and `state` of the page.

It takes three parameters:
* `url` - required*
* `component` - optional
* `title`  - optional

The `url` parameter will navigate to the url you pass in here. how this works is, it fetches the `url` you give, parses it, then takes the **valid** contents from the `routable` element, do some tweaks and displays the results blazing fast!

```
const router = new VanillaRouter({...options})
router.to("/your-url-here")
```

if you want to navigate to a `url` but want to fetch the contents from a different url, you can pass it as the `component`.

```
router.to('/a-different-url', '/real-url')
```

if you don't pass the `title` attribute in the `<routable>` element, you can pass it in the `title` parameter of this function. it will behave the same way and both work on fallback system.

```
<routable>
  I ain't got no title!
</routable>
```
```
router.to("/url", "/component", "I've Got You Covered!")
```

> **Bonus**: If You Have Set the `useTopBar` to `true` when initializing the router instance, you will see the bar on the top animating when you start to navigate and fades out when the results are displayed.

### The `<direct>` Element
Although, whatever you place outside the `<routable>` element won't be shown while routing, though it will require you to rewrite the page 2 times. which is pretty difficult according to me (opinions differ). for that purpose you have the `<direct>` element. it must be placed inside the `<routable>` tag, whatever you place inside the `<direct>` tag, in-spite of being inside `<routable>` will not be rendered while routing and only renderes when the page is directly visited.

```
<routable>
  This will be shown while Routing!
  <direct>
    This Won't Be Shown While Routing!
  </direct>
</routable>
```

### The `<routed>` Element
If You Want Something inside your `<direct>` tag to be rendered while routing, wrap it up in `<routed>` element and you're done! this is completely opposite to the `<direct>` element and can you be used to avoid writing same code multiple times.

### The `<rlink>` Element
Well, Attaching Event listeners to everything you want to route to can be difficult and weird. to avoid this, you get the `<rlink>` element.
It stands for `Routable Link`. it is a `<a href>` tag but in a `routed way 😉`. it is converted to anchor tag on runtime with the `stateful` attribute to indicate and differ it from normal anchor tags.
It Has Two Different Attributes for different use cases.

* `href` - the value of this will be the url routed to.
```
<rlink href="/your-url-here">Click Here!</rlink>
```
* `abshref` - it prepends the `absolute path` of your application to the provided value.
```
router.setABSPath('/nested/dir')
```
```
<rlink abshref="/your-url-here">Click Here!</rlink> // returns abspath + provided value
```

### Components / Drops
VanillaRouter provides you to break down your html code in form of components also known as `drops`.

To Create A `Drop`, create a file with any file name ending with the extension `.drop`. 

Then Place your `HTML ONLY` Contents inside it.

To Access with from another page use the `<drop>` element with the attribute `name` providing the filename of the drop without the `.drop` extension.

```
<drop name="navbar" />
```

### Replace Route
if you wan to use `history.replaceState` instead of `history.pushState`, you can use the `router.replace` method. it takes the same arguments as the `router.to` but uses `history.replaceState` instead.

### Router `home` method
If you want to route to the `abspath` of the router, which is `/` by default, you can use the `router.home` method. it takes no arguments.

```
router.home() // redirects to absolute path
```

### Handling Back And Forward Buttons (Pop State)
Currently `Back`  And `Forward` Buttons are not handled by the router. it is prone to the next update. but if you want to extend the library by adding your own handlers, you can do it so by:

```
router.handlePopState = () => {
  // your code here
}
```

`router.handlePopState` is executed when `window.onpopstate` occurs and is set by the router, so don't need to attach the `window.onpopstate` handler seperately so that you can extend the library by ease.
