- Start Date: 2015-01-04
- RFC PR: (leave this empty)
- ember-cli Issue: (leave this empty)

# Summary

To enable for features like [Ember's engines RFC](https://github.com/emberjs/rfcs/pull/10) and browser APIs like [Service Workers](https://github.com/slightlyoff/ServiceWorker/blob/master/explainer.md), Ember CLI has to become configurable and build conventions around asset generation. These new APIs and conventions should be primitive APIs that enables aforementioned features.

# Motivation

Currently default Ember CLI builds generate only generate a handful of files:

- app.js
- app.css
- vendor.js
- vendor.css
- robot.txt
- index.html

While the current status quo is to minimize the ammount of HTTP requests that you make, these files eventually hit a point in which their size begins to impact performance. Currently there is no happy path which allows developers to declare bundles of files.  How this division occurs will depend on several different use cases.

Sometimes applications will only need to declare a small subset of the application to be funneled off into it's own set of files. An example of this would be bundling the dependency graph for [Ace Editor](http://ace.c9.io/) so that it can be asynchronously fetched the first time the user wants to use it.  Currently, applications need to load all assest up front even though users may not need the code.

In the case of Engines, an application may want to bundle along the route definition. This means that each route would have a corresponding asset bundle.  This style of bundling should be based upon convention and will require changes in Ember.

In the somewhat near future, we will want Ember CLI to build bundles and also just perform transpilation on files. Rational behind this type of workflow is account for [HTTP2 muliplexing](http://http2.github.io/faq/#why-is-http2-multiplexed), [Server Push](https://www.igvita.com/2013/06/12/innovating-with-http-2.0-server-push/), and [Service Workers](https://github.com/slightlyoff/ServiceWorker/blob/master/explainer.md).  In this world all "best practices" up to this point are thrown out the window and we want the individual files and not a concated files.  In the event the browser does not support HTTP2 or Service Workers we want to fallback to the bundled solution.

# Detailed Design

Desired outputs:

__Lazy load adhoc file__
```
dist/
  APP.js
  APP.css
  index.html
  vendor.css
  vendor.js
  d3/
    d3.js
```

__Engines__
```
dist/
  APP.js // Essientially runtime
  manifest.js // Manifest of non-local engines (service discovery)
  profile/
    engine.js
    engine.css
    edit/
      engine.js
      engine.css
  admin/
    engine.js
    engine.css
  vendor.js
  vendor.css
  index.html
```

__Fine Grain__
```
  dist/
    bundled/
      APP.js
      APP.css
      vendor.css
      vendor.js
    app.js
    index.html
    router.js
    ds/
      ds.js
    ember/
      ember.js
    ember-data/
      ember-data.js
    profile/
      route.js
      profile-card.js
      edit/
        route.js
        edit-profile.js
```
      