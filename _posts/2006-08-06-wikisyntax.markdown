---
author: John Tantalo
comments: true
date: 2006-08-06 08:56:00+00:00
layout: post
slug: wikisyntax
title: WikiSyntax
wordpress_id: 4
categories:
- Ideas
- Web
---

I'm working on a [Greasemonkey](http://greasemonkey.mozdev.org/) script to add syntax highlighting to [Wikipedia](http://www.wikipedia.org/) in [Firefox](http://www.mozilla.com/firefox/). The trick is to replace the edit page's textarea with an editable iframe as the page loads, then you can do crazy fun stuff with the document which I will refrain from mentioning because it's much too nerdy.

At first I thought an extension was the way to go, but its turns out extensions are aimed at tweaking Firefox's interface, not fussing with the page. Greasemonkey is perfect for the job, since it basically just executes a script on every page that matches a given pattern. Plus, users don't have to go through the hassle of downloading crap and restarting the application.

[![WikiSyntax](http://www.johntantalo.com/blog/wp-content/uploads/2006/12/picture-1.thumbnail.png)](http://www.johntantalo.com/blog/wp-content/uploads/2006/12/picture-1.png)This screenshot shows link underlining, headers, and boldface (i.e., embiggening). I haven't figured out how to handle styling as the page is edited, though, but once that's out of the way the rest should just be a matter of regex hacking.

Here is a quick [WikiSyntax demonstration](http://johntantalo.com/wikisyntax/).
