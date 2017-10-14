---
layout: post
title: Browser Automation
---

Simulating a simple browser agent and executing GET or POST requests can be done by any HTTP Client. But what will you do if your target is something only a real browser can do?

In this world of javascript-rich applications, It's not possible for a simple HTTP client to render client-side apps or pages (since there is no real javascript environment). 

Even if Javascript is not a problem, I have seen people messing up their lives with spaghetti of messy codes just for simulating user actions using simple HTTP clients. 

Cookies? Redirects? Navigation? Form submits? Graphics? The list can go on... You cannot handle everything without a real browser. 

## Available options

We have a couple of options to solve our problem. Many of you may have heard about PhantomJS, Selenium, and so on. 
There are a lot of options available now. It depends on what you want to do. I am going to list some good options which you can consider for browser automation.

### PhantomJS

The scriptable webKit browser with Javascript API. This is the choice of many people and is also one of the best choices. PhantomJS was initially released in 2011. It improved over time. 

[Official Website](http://phantomjs.org/)

### SlimerJS

Inspired by PhantomJS, SlimerJS was created. It offers the same Javascript API. The difference between the two is that SlimerJS is built on top of Gecko, the browser engine of Mozilla Firefox. 

[Official Website](http://slimerjs.org)

### CasperJS

CasperJS was built to control PhantomJS or SlimerJS. CasperJS has a straightforward API and is also loaded with some APIs for testing. 

[Official Website](http://casperjs.org/)

### Selenium

Selenium supports different browser engines including Firefox, Safari, IE, Chrome, PhantomJS etc. It has web-drivers written for multiple programming languages so you can use Selenium wherever you want. Selenium is specifically for cross browser automation which is the reason it supports multiple browsers.

[Official Website](http://www.seleniumhq.org/projects/webdriver/)

### Puppeteer

This is the new and official Node library from Google Chrome team. Puppeteer controls Chrome over the DevTools protocol. Puppeteer works well, however it is still new. 

[Github](https://github.com/GoogleChrome/puppeteer)

### Horseman

Horseman lets you control PhantomJS from Node. It has simply and easy to use API with built-in jQuery support for easier page manipulation. Its API also looks similar to Nightmare's API.

[Official Website](http://www.horsemanjs.org/)

### Nightmare

A high-level browser automation library with a simple API for Node. Nightmare initially drove PhantomJS as its browser engine. Nightmare adopted Electron due to some performance gains over PhantomJS. Having Electron as the backend has many advantages. 

[Official Website](http://www.nightmarejs.org/)

### Automating Internet Explorer?

So you love IE and want to use that for browser automation? In that case, there are two PhantomJS ports for Internet Explorer.

#### SpecterJS

This is a work-in-progress but is ready to be tested. Its a scriptable headless Internet Explorer port of PhantomJS. Some features are still in progress and some are not implemented yet. 

[Github](https://github.com/andyjansson/specterjs)

#### TrifleJS

Another port of PhantomJS for Internet Explorer. It uses the .NET WebBrowser Class with a Javascript API running on the V8 Engine. TrifleJS looks better than SpecterJS. However, it also don't supports some of the phantomJS features. 

[Official Website](http://triflejs.org/)

## Conclusion

I wanted browser automation on Node. So, I decided to use __Nightmare__. Nightmare seems the best of all for now. Horseman is also a good option if you want to stick with PhantomJS. 

Puppeteer is so new. Having checked them all, I think Nightmare is the way to go for now. It's flexible, fast and super easy to use. 