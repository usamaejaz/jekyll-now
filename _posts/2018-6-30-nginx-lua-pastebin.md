---
layout: post
title: Building Pastebin for text using NGINX and Lua
---

At times I use different services for sharing text or code snippets. Pastebin is a well known term for such services (where you _paste_ whatever you want to share). The functionality is also simple (mostly) and that's why I decided to build a pastebin service using only NGINX. 

<img src="{{ site.baseurl }}/images/nginx-lua.png" alt="NGINX and Lua" style="width: 100%;"/>

I have been a fan of NGINX the moment I learnt of it. In my earlier days (about 5 yrs ago), I was an _Apache guy_. But the moment I became familiar with NGINX and its vast power, I never looked at any other webserver software. 

I often share text or code snippets online and that is why, It was fun to build it without using any "backend". NGINX alone was enough with its [awesome Lua module](https://github.com/openresty/lua-nginx-module) which offers unlimited possibilities. I wanted to make it simple so there is not much in it other than the only feature of storing plain-text pastes.

## NGINX Config

The whole backend of this pastebin is inside of the NGINX config. The Lua code within `content_by_lua_block` is all responsible for saving the paste. The code is saving the pastes to the local filesystem but there are endless possibilities (for example, storing in a database like mysql or redis). 

The following location block is responsible for all `/api/pastes` requests. The directory to which the paste is saved must also be writeable by the webserver user. 

```
# api for storing paste
location = /api/pastes {
	default_type "text/plain";

		client_max_body_size 128k;
		client_body_buffer_size 128k;
			
	content_by_lua_block {
		ngx.req.read_body()
		
		local method = ngx.req.get_method()

		if method == "POST" then
			local args, err = ngx.req.get_post_args(1)

			if err == "truncated" then
				ngx.say("error: only one POST parameter allowed.")
				ngx.exit(403)
			end

			if not args then
				ngx.say("error: failed to get post args: ", err)
				ngx.exit(500)
			end
			
			local content = args.content

			if content == nill then
				ngx.say("error: required parameter 'content' not set")
				ngx.exit(400)
			elseif content == "" then
				ngx.say("error: required parameter 'content' is empty")
				ngx.exit(400)
			end

			math.randomseed(os.clock()+os.time())
			local random = math.random
			local function randid()
				local template ='xxxx4xxxxxxyxxxx'
				return string.gsub(template, '[xy]', function (c)
					local v = (c == 'x') and random(0, 0xf) or random(8, 0xb)
					return string.format('%x', v)
				end)
			end
			local name = randid()
			local file, err = io.open("/var/www/nginx-pastebin/pastes/" .. name, "w")
			if file == nill then
				ngx.say("error: couldn't open file: ", err)
			else 
				file:write(content)
				file:close()
			end
			
			ngx.say("https://", ngx.var.host, "/", name)

		elseif method == "GET" then
			ngx.say("send a post request with 'content=your_text_content'")
		else 
			ngx.say("error: not allowed")
			ngx.exit(405)
		end
	}
}
```

This is an example of how services involving simple (or even complicated?) logic can be written using Lua inside NGINX. 

`ngx` has many other APIs and you can perform almost every NGINX-related action in your Lua code (rewriting, redirects, caching, serving content and much more).

## Demo and Complete Code

I have set this up on [pastebin.usamaejaz.com](https://pastebin.usamaejaz.com)  and the source code has been pushed to [github.com/usamaejaz/nginx-pastebin](https://github.com/usamaejaz/nginx-pastebin).

