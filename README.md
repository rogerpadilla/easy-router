# prouter

[![build status](https://travis-ci.org/rogerpadilla/prouter.svg?branch=master)](https://travis-ci.org/rogerpadilla/prouter?branch=master)
[![coverage status](https://coveralls.io/repos/rogerpadilla/prouter/badge.svg?branch=master)](https://coveralls.io/r/rogerpadilla/prouter?branch=master)
[![dependencies status](https://david-dm.org/rogerpadilla/prouter/status.svg)](https://david-dm.org/rogerpadilla/prouter/status.svg)
[![dev dependencies status](https://david-dm.org/rogerpadilla/prouter/dev-status.svg)](https://david-dm.org/rogerpadilla/prouter/dev-status.svg)
[![npm downloads](https://img.shields.io/npm/dm/prouter.svg)](https://www.npmjs.com/package/prouter)
[![npm version](https://badge.fury.io/js/prouter.svg)](https://www.npmjs.com/prouter)

The power of the [express's routing-expressions style](https://expressjs.com/en/guide/routing.html) available in the frontend.

In web applications, it's useful to provide linkable, bookmarkable, and shareable URLs to meaningful locations within the app without reload the page. In that context, routing refers to the declaration that does the app that it wants to react to changes in the URL (path), and to trigger some callbacks (handlers) accordingly.

So basically, you give prouter a set of path expressions and a callback function for each of them, so prouter will call the callback(s) (passing contextual parameters) according to the activated path (in the URL). 

## Why prouter?
- **Unobtrusive:** it is designed from the beginning to play well with vanilla JS or with any library/framework out there: [Polymer](https://www.polymer-project.org/1.0/), [React](http://facebook.github.io/react/), [Handlebars](http://handlebarsjs.com/), etc.
- **Learn once and reuse it** Express.js is very well known and used across the world, why not bringing a similar API (wherever possible) to the browser? Under the hood, prouter uses the same (wonderful) library than express for parsing URLs [Path-to-RegExp](https://github.com/pillarjs/path-to-regexp).
- **Really lightweight:** 7kb (before gzipping).
- **Forward-thinking:** learns from others Router components like the ones of Express and Angular. Written in TypeScript for the future and transpiled to ES5 with UMD format for the present... thus it transparently supports almost every modules' style out there: ES6, CommonJS, AMD. And can be used also as global browser variable (via 'script' tag in your HTML).
- KISS principle: unnecessary complexity avoided.
- Unit tests for every feature are created.

## Installation

```bash
# Using NPM
npm install prouter --save

# Using Yarn
yarn prouter --save

# Or with Bower
bower install prouter --save

# Or just inject it using a 'script' tag in your HTML file
<script src="prouter.min.js"></script>
```

## Examples

### basic using ES6 Modules

```js
import { BrowserRouter } from 'prouter';

async function main() {
  // Instantiate the router
  const router = new BrowserRouter();

  const send = (html: string) => {
    document.querySelector('.router-outlet') = html;
  };

  // Declare the paths and its respective handlers
  router
    .use('/', async (req) => {
      const people = await personService.find();
      const html = PersonListCmp(people);
      send(html);
    })
    .use('/about', (req) => {
      send('<h1>Static About page.</h1>');
    });

  // start listening events for the routing
  router.listen();

  // programmatically navigate to any route in your router
  await router.push('/about');
}
```


### basic, including it via a 'script' tag in the HTML file

```js
const BrowserRouter = prouter.BrowserRouter;

// Instantiate the router
const router = new BrowserRouter();

// Declare the paths and its respective handlers
router
  .use('/', (req) => {
    // do some stuff...
  })
  .use('/about', (req) => {
    // do some stuff...
  });

// start listening events for the routing
router.listen();

// programmatically navigate to any route in your router
router.push('/about');
```

### using an interceptor/validator handler

```js
import { BrowserRouter } from 'prouter';

// Instantiate the router
const router = new BrowserRouter();

// Declare the paths and its respective handlers
router
  .use('(.*)', (req) => {
    // this handler will be for any routing event, before other handlers
    const srcPath = router.getPath();
    const destPath = req.originalUrl;
    console.log('coming from', srcPath);
    console.log('going to', destPath);
    const isAuthorized = validateUserAuthorization(req.originalUrl);
    if (!isAuthorized) {
      const msg = "You haven't rights to access the page: " + destPath;
      showAlert(msg);
      // don't execute other middlewares.
      return Promise.reject(msg);
    }
  })
  .use('/', (req) => {
    // do some stuff...
  })
  .use('/contact', (req) => {
    // do some stuff...
  });

// start listening events for the routing
router.listen();

// programmatically navigate to any route in your router
router.push('/contact');
```


### advanced using RouterGroup with ES6 Modules

```js
import { BrowserRouter, RouterGroup } from 'prouter';

// this can be in a different file for modularization of the routes,
// and then import it in your main routes file and mount it.
const productRouterGroup = new RouterGroup();

productRouterGroup
  .use('/', (req) => {
    // do some stuff...
  })
  .use('/create', (req) => {
    // do some stuff...  
  })
  .use('/:id', (req) => {
    const id = req.params.id;
    // do some stuff with the 'id'...
  });

// Instantiate the router
const router = new BrowserRouter();

// Declare the paths and its respective handlers
router
  .use('(.*)', (req) => {
    // this handler will be for any routing event, before other handlers
    console.log('request info', req);
  })
  .use('/', (req) => {
    // do some stuff...
  })
  // mount the product's group of handlers using this base path
  .use('/product', productRouterGroup);

// start listening events for the routing
router.listen();

// programmatically navigate to the detail of the product with this ID
router.push('/product/123');
```
