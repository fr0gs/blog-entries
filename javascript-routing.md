# Routing in Javascript

# Introduction

Originally web applications consisted in interconnected html documents that one could navigate through links between them. Every time a user clicked a link on a website a new  document would be generated in the server and sent back to the browser to be rendered in their screen.

Around the year 2005 the term *Single-Page Application (SPA)* became popular. Said term encompassed a new way or architecting websites to make them behave more like desktop applications: snappy, with graphical animations and smooth transitions between links.
This was achieved taking advantage of javascript, html & css, as new APIs became available to give the browser more native-like capabilities.

**SPAs** are based on a single document model. This means that web applications' lifespan happens on a single html page, along with the transitions between the different views. But since links no longer imply the fetching and generation of a new document, how are those transitions modelled? they are achieved by using a **router**.


# What is a Javascript Router?

A Javascript router is a key component in most frontend frameworks. It is the piece of software in charge to organize the states of the application, switching between different views. For example, the router will render the login screen initially, and when the login is successfull it will perform the transition to the user's welcome screen.


# How it works.

The router will be in charge of simulating transitions between documents by watching changes on the URL. When the document is reloaded or the URL is modified somehow, it will detect that change and render the view that is associated with the new URL to transition to.  

I wrote a small router in javascript to illustrate the idea. At the beginning we need two objects, one to store the routes, and other to store the templates, along with two simple functions to register them.

*Templates* are just one way of describing the DOM that will be generated when the transition from one route to the other is completed.
The whole javascript application will live in a div element.

```javascript
// Application div
const appDiv = "app";

// Both set of different routes and template generation functions
let routes = {};
let templates = {};

// Register a template (this is to mimic a template engine)
let template = (name, templateFunction) => {
  return templates[name] = templateFunction;
};

// Define the routes. Each route is described with a route path & a template to render
// when entering that path. A template can be a string (file name), or a function that
// will directly create the DOM objects.
let route = (path, template) => {
    if (typeof template == "function") {
      return routes[path] = template;
    }
    else if (typeof template == "string") {
      return routes[path] = templates[template];
    }
    else {
      return;
    }
};
```

Now we will be able to register templates and routes, creating the mapping between them:

```javascript
// Register the templates.
template('template1', () => {
    let myDiv = document.getElementById(appDiv);
    myDiv.innerHTML = "";
    const link1 = createLink('view1', 'Go to view1', '#/view1');
    const link2 = createLink('view2', 'Go to view2', '#/view2');

    myDiv.appendChild(link1);
    return myDiv.appendChild(link2);
});

template('template-view1', () => {
    let myDiv = document.getElementById(appDiv);
    myDiv.innerHTML = "";
    const link1 = createDiv('view1', "<div><h1>This is View 1 </h1><a href='#/'>Go Back to Index</a></div>");
    return myDiv.appendChild(link1);
});

template('template-view2', () => {
    let myDiv = document.getElementById(appDiv);
    myDiv.innerHTML = "";
    const link2 = createDiv('view2', "<div><h1>This is View 2 </h1><a href='#/'>Go Back to Index</a></div>");
    return myDiv.appendChild(link2);
});


// Define the mappings route->template.
route('/', 'template1');
route('/view1', 'template-view1');
route('/view2', 'template-view2');
```

For the **templates** we match a template name with a function that will generate javascript elements and append the resulting DOM to the div element where the application lives. This functionality in a real router would be taken over by the *templating engine*. For the **routes**, we just do the mapping between a route path and the corresponding template.

The *createLink* & *createDiv* are auxiliary functions to generate DOM:

```javascript
// Generate DOM tree from a string
let createDiv = (id, xmlString) => {
    let d = document.createElement('div');
    d.id = id;
    d.innerHTML = xmlString;
    return d.firstChild;
};

// Helper function to create a link.
let createLink = (title, text, href) => {
    let a = document.createElement('a');
    let linkText = document.createTextNode(text);
    a.appendChild(linkText);
    a.title = title;
    a.href = href;
    return a;
};
```

What is left is to have the logic to detect changes in the URL and resolve them to render the template. To do so, listen for the *load* & *hashchange* events. The former fires then a document is loaded, and the latter when the URL hash changes.

```javascript
// Give the correspondent route (template) or fail
let resolveRoute = (route) => {
    try {
     return routes[route];
    } catch (error) {
        throw new Error("The route is not defined");
    }
};

// The actual router, get the current URL and generate the corresponding template
let router = (evt) => {
    const url = window.location.hash.slice(1) || "/";
    const routeResolved = resolveRoute(url);
    routeResolved();
};

// For first load or when routes are changed in browser url box.
window.addEventListener('load', router);
window.addEventListener('hashchange', router);
```

That's it! Of course many functionality is lacking: the use of controllers to transform data before passing it to the views, nested routes, the use of [history api](https://developer.mozilla.org/en-US/docs/Web/API/History_API), etc.. but the idea of javascript routing is quite easy to grasp. The code together can be found in [this gist](https://gist.github.com/fr0gs/133127ce31d73e7010ef19db874c319b).


# Examples

  * [tildeio/router.js](https://github.com/tildeio/router.js/)
  * [Ember Router](https://guides.emberjs.com/v2.14.0/routing/)
  * [React Router](https://github.com/ReactTraining/react-router)
  * [Vue router](https://github.com/vuejs/vue-router)

# References

  * http://joakim.beng.se/blog/posts/a-javascript-router-in-20-lines.html
  * http://www.softfinity.com/blog/an-simple-introduction-to-url-routing/
  * http://krasimirtsonev.com/blog/article/A-modern-JavaScript-router-in-100-lines-history-api-pushState-hash-url
  * https://blog.risingstack.com/writing-a-javascript-framework-client-side-routing/


Have fun!
