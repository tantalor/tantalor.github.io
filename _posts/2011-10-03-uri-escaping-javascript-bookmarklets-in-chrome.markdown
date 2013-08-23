---
author: John Tantalo
comments: true
date: 2011-10-03 21:00:24+00:00
layout: post
slug: uri-escaping-javascript-bookmarklets-in-chrome
title: URI-Escaping JavaScript Bookmarklets in Chrome
wordpress_id: 173
categories:
- Code
- Web
tags:
- Chrome
- JavaScript
---

[John Gruber](http://daringfireball.net/) published a helpful [JavaScript Bookmarklet Builder](http://daringfireball.net/2007/03/javascript_bookmarklet_builder) which I used to generate my [IPA TTS bookmarklet](/blog/ipa-tts-bookmarklet).



A few users reported that [Chrome](http://www.google.com/chrome) choked on the bookmarklet. I found that the bookmarklet builder does not produce the output that Chrome expects. I was using Chrome 15.0.874.54 beta.



For example, take this simple script.



    (function () {
      // what is 1+1?
      alert(["1+1=",(1+1)].join(''));
    })();





Gruber's script produces this bookmarklet: [Example 1](javascript:(function%20()%20{alert([%221+1=%22,(1+1)].join(%27%27));})())



> javascript:(function%20()%20{alert([%221+1=%22,(1+1)].join(%27%27));})()




If you drag this example to your bookmark bar in Chrome, it will produce this error when invoked.





> Uncaught SyntaxError: Unexpected token %




What happened? If you edit the bookmark, you will see that Chrome double-escaped the string,



> javascript:(function%2520()%2520%7Balert(%5B%25221+1=%2522,(1+1)%5D.join(%2527%2527));%7D)()




I believe this happened because the string contained some non-escaped characters such as parentheses.



Gruber points out in a footnote that his script intentionally leaves some characters unescaped for the purpose of readability.



> 

> 
> It’s unclear to me what characters must be escaped in a bookmarklet URL. Some sources suggest that other punctuation characters, such as brackets and semicolons, ought to be escaped, too, but I can see no practical reason to do so. If you want to be really conservative and escape just about everything, change this line:


> 
> `uri_escape_utf8($bookmarklet, qq('" \x00-\x1f\x7f-\xff));`

> 
> to:


> 
> `uri_escape_utf8($bookmarklet);`

> 
> Personally, I prefer to keep the bookmarklet URL itself as readable as possible.






With this change, we get this bookmarklet: [Example 2](javascript:%28function%20%28%29%20%7Balert%28%5B%221%2B1%3D%22%2C%281%2B1%29%5D.join%28%27%27%29%29%3B%7D%29%28%29)



> javascript:%28function%20%28%29%20%7Balert%28%5B%221%2B1%3D%22%2C%281%2B1%29%5D.join%28%27%27%29%29%3B%7D%29%28%29




It may not be as readable as "Example 1", but it works.



Find my modifications to Gruber's JavaScript Bookmarklet Builder on GitHub
