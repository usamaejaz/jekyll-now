---
layout: post
title: Using Twitter Streaming API to track keywords on NodeJS
image: /images/twitter-streaming-api.png
---

While building a social automation and management tool, I wanted to test Twitter Streaming API on NodeJS. 

<img src="{{ site.baseurl }}/images/twitter-streaming-api.png" alt="Twitter Streaming API on NodeJS" style="width: 100%;"/>

There are a lot of npm modules available for Twitter but I went with [`twit`](https://github.com/ttezel/twit). `twit` looked easy to use and pretty simple. It supports Twitter's REST and streaming APIs. 

Installing any npm package is as simple as...

```shell
npm install twit --save
```

`twit` documentation is well-written and can be found [here on github](https://github.com/ttezel/twit). After installing the package, all you need is Twitter API credentials. A twitter app can be created at [apps.twitter.com](https://apps.twitter.com). You should also be very careful in choosing the access level. If you are going to do more than just _reading_ the twitter world, you have to update your application's access level. 

## Using `twit` to access Twitter Streaming API

Quoted from the project's github page, the simple example to start can be seen below.

```javascript
var Twit = require('twit')

var T = new Twit({
  consumer_key:         '...',
  consumer_secret:      '...',
  access_token:         '...',
  access_token_secret:  '...',
  timeout_ms:           60*1000,  // optional HTTP request timeout to apply to all requests.
})

//
//  stream a sample of public statuses
//
var stream = T.stream('statuses/sample')

stream.on('tweet', function (tweet) {
  console.log(tweet)
})

//
//  filter the twitter public stream by the word 'mango'.
//
var stream = T.stream('statuses/filter', { track: 'mango' })

stream.on('tweet', function (tweet) {
  console.log(tweet)
})

//
// filter the public stream by the latitude/longitude bounded box of San Francisco
//
var sanFrancisco = [ '-122.75', '36.8', '-121.75', '37.8' ]

var stream = T.stream('statuses/filter', { locations: sanFrancisco })

stream.on('tweet', function (tweet) {
  console.log(tweet)
})

//
// filter the public stream by english tweets containing `#apple`
//
var stream = T.stream('statuses/filter', { track: '#apple', language: 'en' })

stream.on('tweet', function (tweet) {
  console.log(tweet)
})

```

It took me about 5 minutes to get started with twitter API on Node. 

In my case, I started with tracking some keywords and targeting the users who tweeted those specific keywords. This can also be used to automate your twitter account. Process DMs, mentions, likes and all that in real-time however you want. 