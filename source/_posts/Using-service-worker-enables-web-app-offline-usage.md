title: How to make website offline accessible
description: Progress web app - enable website offline access with service worker.
date: 2017-08-17 22:23:54
tags:
  - web
  - js
---

It is incredibly easy to make a rich web application like [psdetch](https://studio.psdetch.com) working offline with [Service Worker](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers). 5 minutes are all you need!

<!-- more -->
## Browser support -- by time being

* Chrome
* Firefox
* Edge -- In development
* Safari -- In development

More browser support details can be found [here](https://caniuse.com/#search=service%20worker)

## Requirement

To make your online only web app (like https://studio.psdetch.com) working offline, your web app needs to use `https` for security request.

## 5 mins development

Very simple. The steps below works on any web apps:

1. Follow [this guide](https://googlechrome.github.io/sw-toolbox/) to install `sw-toolbox`
2. Add `service-worker.js` at the root folder of your web app. Here, the location (root folder) is important.
3. Add following script to `index.html`

```html

<script src="/path/to/sw-toolbox/companion.js" data-service-worker="service-worker.js"></script>

```

4. Add content to `service-worker.js`. You can follow the tutorial [here](https://googlechrome.github.io/sw-toolbox/usage.html#main). But most situation you can just use following scripts:

```js
importScripts("/path/to/sw-toolbox/sw-toolbox.js");
/* this will cache all files of current web app with 
 * "fastest" strategy. 
 */
toolbox.router.get("/(.*)",toolbox.fastest);

/*
* Add external files here.e.g.
* toolbox.router.get("https://code.jquery.com/jquery-3.2.1.min.js",toolbox.fastest);
*/

```

You can check what I have used for [psdetch](https://studio.psdetch.com/service-worker.js).

And that's it. Your whole web app can now work offline. With `fastest` strategy, response is always returned from Cache and updated immediately if possible. This dramatically increased user experience.

![cat relax](https://drive.google.com/uc?export=download&id=0ByuKQDQ-pFtieUQ0QVNuR1RMUUE)