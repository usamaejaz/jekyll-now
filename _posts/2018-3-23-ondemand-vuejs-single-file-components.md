---
layout: post
title: Using VueJS Single File Components On Demand
---

I have been using VueJS for many projects. For some reasons, I enjoy working with VueJS more that React. Both frameworks are good enough for simple to complex web apps (and SPAs).

However, what if I don't want to use VueJS on all of my frontend? How can we use VueJS on demand on specific pages?

While there is no problem in this approach and it is possible to use Vue like that. The vue instance can use template from a string, HTML element or template literal but it demands runtime "compiling" of the template which has some side effects as compared to using the pure render function or an already compiled template. 

The runtime compiling doesn't work in environments where statements like `eval` or `new Function` is disabled (example: in a chrome app). Template compiling also takes some time. 

VueJS has different builds for this matter. The one with template compiler is 30% more in size as compared to _runtime only_ build which lacks the template compiler. For these reasons, the best option was to use single file components. But It was not a single page application (SPA) and using a root Vue instance covering all the frontend was not an option. 

So, loading Single File Components on demand with Webpack code splitting was the goal for me. 

## Loading VueJS Component On Demand

The code below is an example to achieve this. 

```javascript
// to render vue components
window.__loadComponent = (name, selector, cb = null) => {
  const el = document.querySelector(selector);
  if (!el)
    return console.error("Selector " + selector + " not found in document.");
  const mountCb = c => {
    const instance = new (Vue.extend(c))().$mount(el);
    cb && cb(instance);
  };
  if (name === "post-editor") {
    require(["./components/PostEditor.vue"], mountCb);
  } else if (name === "scheduled-posts") {
    require(["./components/ScheduledPosts.vue"], mountCb);
  }
};
```

I created a function which is supposed to be called when I want to load the Vue component. This code loads the vue component and then creates a vue instance and mounts it to our _DOM_ element. Remember that we are selectively loading vue component which means there is no _root vue instance_. 

Webpack's code splitting feature will be used for `require` statements like these. It is also required to explicitly place `require()` calls in code otherwise webpack's code-splitting will not work as expected. 

Now that we have a way to load vue components. When I need my component, I can simply load it like the following example.

```javascript
// loading the component
__loadComponent("post-editor", "#postEditor");

// with callback
__loadComponent("post-editor", "#postEditor", vueInstance => {
    // do something with `vueInstance`
})
```



**Now this is exactly what I need:**

- Single File Components (.vue)
- Faster Rendering (since there is no runtime compiling of templates)
- Can Use Runtime-Only Build (reduced size of the build)
- Loaded On Demand

