---
layout: post
title: "unio: One REST API Spec for All."
date: 2013-02-23 23:32
comments: true
categories: 
---

After several years of using and writing REST APIs, it becomes redundant to you as a developer to implement a client in whatever language you use when the need arises to integrate a new service into the application. 

REST API best practices are pretty well known and it's pretty straightforward to design a REST API. You essentially define a list like `GET /user`, `POST /status`, and so on.

A REST API could be described in JSON. As an example, see the JSON below - describing part of the Facebook REST API.

```javascript
{
    "name": "fb",
    "api_root": "https://graph.facebook.com",
    "resources": {
        "/me": {
            "methods": [ "get" ],
            "params": [
                {
                    "fields": "optional"
                },
                {
                    "access_token": "required"
                }
            ]
        }
        ...other REST API resources here...
    }
}
```

The Facebook REST API can be described as a list of resources, with the corresponding HTTP methods to use and the params allowed in the request. As can be seen in the JSON above, the `access_token` param is required, and the `fields` param is optional. Outlining the REST API in JSON allows one to perform application-level request validation - for example it would be trivial to write validation code to make sure that we don't try to accidentally send off a request to the resource without the `access_token`, since we know it would fail.

We could do the same with the Twitter REST API - see the JSON below.

```javascript
{
    "name": "twitter",
    "api_root": "https://api.twitter.com/1.1",
    "resources": {
        "/statuses/update.json": {
            "methods": [ "post" ],
            "params": [
                {
                    "status": "required"
                },
                {
                    "in_reply_to_status_id": "optional"
                },
                {
                    "lat": "optional"
                },
                {
                    "long": "optional"
                },
                {
                    "place_id": "optional"
                },
                {
                    "display_coordinates": "optional"
                },
                {
                    "trim_user": "optional"
                }
            ]
        }
        ...other REST API resources here...
    }
}
```

Since the APIs are so easily defined in JSON, I whipped up a quick client using `node.js`, published it on `npm` and put it on [Github](http://github.com/ttezel/unio). You can install it using `npm install unio`, and start making requests to the Facebook API (I will soon implement the Twitter API with it). You can also import JSON specs that look like the one above. Example usage below:

```javascript
var unio = require('unio')

var params = { q: 'coffee', access_token: 'FB_ACCESS_TOKEN' }

// use the facebook REST API

unio()
    .use('fb')
    .get('search', params, function (err, reply) {
        //...
    })

// import a new REST API definition (javascript object, or local fs path)

unio()
    .spec(jsonSpec)
    .use('my-new-api')
    .get('/some/url', { foo: 'bar' }, function (err, reply) {
        //...
    })
```

So I propose that we build a list of JSON documents describing as many REST APIs as possible, in order to save time implementing them over and over again. We can then import these specs as needed into our clients, and start making HTTP requests without wasting time implementing yet another REST API client. I have started a JSON REST API spec page [Here](). I invite anyone and everyone to message me and add more!


Find me on: [Github](https://github.com/ttezel) and [Twitter](https://twitter.com/tolga_tezel).
