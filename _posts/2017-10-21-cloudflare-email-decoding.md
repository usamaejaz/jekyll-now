---
layout: post
title: Decoding Cloudflare-protected Emails
---

You all know that Cloudflare protects your pages from bots and scrapers. It protects you from Email spam by hiding on-page emails and then using Javascript to decode them on page load. 

This technique protects emails from bots which cannot execute Javascript. It will not protect emails from [real browsers]({{ site.baseurl }}/browser-automation/). Using a real browser may also be memory hungry and slower than some simple HTTP requests. 

In case you want your code to look beyond Cloudflare's email protection, you will have to decode the emails yourself. 

Cloudflare's encoded email link may look like this:

```html
<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="543931142127353935313e352e7a373b39">[email&#160;protected]</a>
```

or this:

```html
<a href="/cdn-cgi/l/email-protection#5f323a1f2a2c3e323e3a353e25713c3032"><i class="svg-icon email"></i></a>
```

_The encoded string like `543931142127353935313e352e7a373b39` or `5f323a1f2a2c3e323e3a353e25713c3032` in the above examples will give you original email once decoded._

Cloudflare's Javascript to decode the emails is pretty simple (after beautifying of course). If you are on NodeJS, you can directly use the Javascript version of the decoder. If not, you can code the same implementation in whatever language you are using. 

Here are some implementations along with the pretty version of Cloudflare's javascript implementation.

## Javascript

```javascript
function cfDecodeEmail(encodedString) {
    var email = "", r = parseInt(encodedString.substr(0, 2), 16), n, i;
    for (n = 2; encodedString.length - n; n += 2){
    	i = parseInt(encodedString.substr(n, 2), 16) ^ r;
		email += String.fromCharCode(i);
    }
    return email;
}

console.log(cfDecodeEmail("543931142127353935313e352e7a373b39")); // usage
```

## Python

```python
def cfDecodeEmail(encodedString):
    r = int(encodedString[:2],16)
    email = ''.join([chr(int(encodedString[i:i+2], 16) ^ r) for i in range(2, len(encodedString), 2)])
    return email

print cfDecodeEmail('543931142127353935313e352e7a373b39') # usage
```

## PHP

```php

function cfDecodeEmail($encodedString){
  $k = hexdec(substr($encodedString,0,2));
  for($i=2,$email='';$i<strlen($encodedString)-1;$i+=2){
    $email.=chr(hexdec(substr($encodedString,$i,2))^$k);
  }
  return $email;
}

echo cfDecodeEmail('543931142127353935313e352e7a373b39'); // usage
```

## GO

```go
package main

import (
	"bytes"
	"strconv"
)

func cf(a string) (s string) {
	var e bytes.Buffer
	r, _ := strconv.ParseInt(a[0:2], 16, 0)
	for n := 4; n < len(a)+2; n += 2 {
		i, _ := strconv.ParseInt(a[n-2:n], 16, 0)
		e.WriteString(string(i ^ r))
	}
	return e.String()
}

func main() {
	email := cf("543931142127353935313e352e7a373b39") // usage
	print(email)
	print("\n")
}
```

[1]: https://gist.github.com/AbeEstrada/11e4511f9915b00f9714	"Go Implementation take from this gist"
[2]: http://blog.safebuff.com/2016/06/01/Cloudflare-Email-Protection-Decoder/	"PHP / Python implementation taken from safebuff.com"

_Now your bot can spam my mailbox too!_