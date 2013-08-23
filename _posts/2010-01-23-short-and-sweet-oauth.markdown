---
author: John Tantalo
comments: true
date: 2010-01-23 05:28:37+00:00
layout: post
slug: short-and-sweet-oauth
title: Short and sweet OAuth
wordpress_id: 94
categories:
- Code
- Web
tags:
- oauth
- python
---

I recently grew quite frustrated when I had to update [Emend's](http://emendapp.com) Twitter bot to use [OAuth](http://oauth.net/).

There are, of course, [readily available implementations of OAuth](http://oauth.googlecode.com/svn/code/python/oauth/oauth.py) in python, even some [specifically engineered for App Engine](http://github.com/mikeknapp/AppEngine-OAuth-Library/raw/master/oauth.py) with lovely persistence and caching and KILL ME NOW.

I gave up after a couple hours of staring at this nonsense. I grew so displeased because my app doesn't need 95% of what these libraries offer. I only need to [sign requests](http://oauth.net/core/1.0/#signing_process) with a fixed _oauth_consumer_key_, _oauth_consumer_secret_, _oauth_token_, and _oauth_token_secret_. However, this simple task would require me to subclass or instantiate several object with Culver's library, and I can't even begin to fathom what I'd need to do with Knapp's offering.

(Aside: if you want to authorize and build access tokens for your app, I highly recommend [this excellent OAuth test client](http://term.ie/oauth/example/client.php))

I finally teased apart the core function of signing requests, thanks in large part to the [great reference example](http://oauth.net/core/1.0/#anchor30). If all you need is to access a protected resource, this will get the job done with a single function call, no object-orientation required.



[Source code and unit test of short and sweet OAuth.](http://gist.github.com/284452)

