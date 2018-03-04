---
layout: post
title: Securely Bypassing X-Frame-Options or Content-Security-Policy in WebExtension
---

To embed third party content using `iframe`, the WebExtension has to intercept HTTP response and modify headers but isn't it bad in terms of security? That's what I thought. 

## Bypassing X-Frame-Options

If your browser extension is using `iframe` to embed third party content where the source is dynamic, you have no option other than to bypass the X-Frame-Options header.

```javascript
chrome.webRequest.onHeadersReceived.addListener(info => {
	const headers = info.responseHeaders; // original headers
	for (let i=headers.length-1; i>=0; --i) {
		let header = headers[i].name.toLowerCase();
		if (header === "x-frame-options" || header === "frame-options") {
			headers.splice(i, 1); // Remove the header
         }
    }
    // return modified headers
    return {responseHeaders: headers};
}, {
	urls: [ "<all_urls>" ], // match all pages
    types: [ "sub_frame" ]
}, ["blocking", "responseHeaders"]);
```

This may be required when you are providing some functionality which relies on `iframe`. For me, the reason was a feature which allowed the user to _embed_ third party content or web-app by its URL on the extension's page. 

**But wait... what about the `Content-Security-Policy` header?**

`Content-Security-Policy` header also has `frame-ancestors` directive which can be used to control if a page can be loaded in an `iframe` or not. 

`frame-ancestors` directive can specify a list of _allowed sources_ which can load the page in an iframe or prevent this for all parent origins. 

**Example CSP header**

```
script-src https://script-source1.com http://script-source2.com; frame-ancestors 'self';
```



The above header is not allowing any third party parent origin to load this page in iframe. 

## Bypassing Content-Security-Policy

This header can be bypassed the same way as shown above. 

```javascript
chrome.webRequest.onHeadersReceived.addListener(info => {
	const headers = info.responseHeaders; // original headers
	for (let i=headers.length-1; i>=0; --i) {
		let header = headers[i].name.toLowerCase();
		if (header == "content-security-policy") { // csp header is found
         	// modifying frame-ancestors; this implies that the directive is already present
            headers[i].value = headers[i].value.replace("frame-ancestors", "frame-ancestors https://yourpage.com/");
         }
    }
    // return modified headers
    return {responseHeaders: headers};
}, {
	urls: [ "<all_urls>" ], // match all pages
    types: [ "sub_frame" ]
}, ["blocking", "responseHeaders"]);
```

Now your origin page is whitelisted and can make use of `iframe` freely. 

## Security?

Simply bypassing the header by removing `X-Frame-Options` header can be enough for you. But if its bypassed, remember that the browser is vulnerable to attacks which make use of `iframe`s like the famous click-jacking technique. There are many possibilities. 

However, you can do this securely by making use of `Content-Security-Policy` (CSP) header. For instance, if you remove _X-Frame-Options_, make sure you add / modify CSP header with `frame-ancestors` directive to whitelist only your origin URL. So, it will not be open for everyone.