---
layout: post
title: "Pinterest API Usage Framework"
description: "Pinterest API"
category: SHU
tags: [Pinterest,API]
---
{% include JB/setup %}

> Pinterest provide a restful service (json) to access every pins in the of all users.

-----------------------------------------------------------------------------
> Pinterest API v3


```
https://api.pinterest.com/v3/pidgets/boards/[USERNAME]/[BOARDNAME]/pins
https://api.pinterest.com/v3/pidgets/users/USERNAME/pins/
```

-----------------------------------------------------------------------------

> another version

	• I've added paging support for all endpoints (I don't think boards works, not tested). 
	• For each request just add the querystring parameter page on the end:
		

> **Pins: /{username}/pins**
	
	Returns an array of the specified user's pins. Limited to 50 results.

```
Example: http://pinterestapi.co.uk/jwmoz/pins from http://pinterest.com/jwmoz/pins/
```

**Request**
 $ curl http://pinterestapi.co.uk/jwmoz/pins
**Response**

{% highlight json linenos %}
{
    "body": [
        {
            href: "/pin/82190761922627506/",
            src: "http://media-cache-ec3.pinimg.com/236x/8f/89/c5/8f89c589227d78a9f2781eb1c2e79ee6.jpg",
            desc: "Iguazu Falls",
            board: {
                href: "/jwmoz/take-me-there/",
                name: "Take me there"
            },
            domain: "famouswonders.com",
            attrib: false
        },
        {
            href: "/pin/82190761920945513/",
            src: "http://media-cache-ak2.pinimg.com/236x/d5/43/85/d543854261c0323e813ef0f991d9089a.jpg",
            desc: "Samui lake by jwmoz, via Flickr",
            board: "Been & Seen Thailand",
            board: {
                href: "/jwmoz/been-seen-thailand/",
                name: "Been & Seen Thailand"
            },
            domain: "flickr.com",
            attrib: {
                attrib: "by jwmoz",
                href: "http://www.flickr.com/photos/jwmoz/",
                source: "Flickr",
                img: "http://passets-ak.pinterest.com/images/api/attrib/flickr.png"
            }
        }
    ],
    "meta": {
        "count": 2
    }
}
{% endhighlight %}

> **Boards: /{username}/boards**
Returns an array of the specified user's boards. Limited to 50 results.

```
Example: http://pinterestapi.co.uk/jwmoz/boards from http://pinterest.com/jwmoz/boards/
```


**Request**

 $ curl http://pinterestapi.co.uk/jwmoz/boards
 
**Response**

{% highlight json linenos %}
{
    "body": [
        {
            name: "JMOZ",
            href: "/jwmoz/jmoz/",
            src: "http://media-cache-ec0.pinimg.com/216x146/dc/ab/56/dcab566549e1d626110af92bf321f.jpg"
        },
        {
            name: "Take me there",
            href: "/jwmoz/take-me-there/",
            src: "http://media-cache-ak0.pinimg.com/216x146/e2/d6/15/e2d615ac2ff7a43213d6c7a6bca21.jpg"
        },
    ],
    "meta": {
        "count": 2
    }
}
{% endhighlight %}

-----------------------------------------------------------------------------
> **Usage**


- http://media-cache-ec3.pinimg.com/236x/8f/89/c5/8f89c589227d78a9f2781eb1c2e79ee6.jpg  --> replace "236x" with "originals"

- http://media-cache-ec3.pinimg.com/originals/8f/89/c5/8f89c589227d78a9f2781eb1c2e79ee6.jpg