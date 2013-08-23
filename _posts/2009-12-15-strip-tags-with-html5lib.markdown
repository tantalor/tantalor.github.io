---
author: John Tantalo
comments: true
date: 2009-12-15 04:14:37+00:00
layout: post
slug: strip-tags-with-html5lib
title: Strip tags with html5lib
wordpress_id: 82
categories:
- Code
tags:
- html5lib
- python
---

There are a couple posts [out](http://code.google.com/p/html5lib/issues/detail?id=62) [there](http://deathofagremmie.com/2009/04/12/using-html5lib-to-sanitize-user-input/) that discuss stripping tags with [_html5lib_](http://code.google.com/p/html5lib/), but they seem intent on preserving the "acceptable elements" such as `<span>` and `<code>`.

This is fine unless you really want to _friggin' strip out the tags_, like I needed for [Emend](http://emendapp.com). The following is my solution.



[Source code for stripping tags with html5lib and unit test.](http://gist.github.com/256684)

For example,


    
    >>> from strip_tags import strip_tags
    >>> strip_tags('<p>foo</p> <script>bar</script>')
    u'foo bar' 



Thanks go to [Edward O’Connor](http://edward.oconnor.cx/) for pointing me towards _html5lib_ in the first place. It's a huge improvement over [_HTMLParser_](http://docs.python.org/library/htmlparser.html).
