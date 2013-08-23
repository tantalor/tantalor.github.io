---
author: John Tantalo
comments: true
date: 2010-12-20 20:37:04+00:00
layout: post
slug: javascript-shebangs
title: Javascript Shebangs
wordpress_id: 141
categories:
- Code
- Web
tags:
- JavaScript
---

[Ringo](http://ringojs.org/), [Narwhal](http://narwhaljs.org/), [Node](http://nodejs.org/), and [JavaScriptCore](http://webkit.org/projects/javascript/) support executing scripts with a [shebang](http://en.wikipedia.org/wiki/Shebang_(Unix)).

Here are some quick examples to get you started.



## Ringo



    
        #!/usr/bin/env ringo
        print("ringo");
    




## Narwhal



    
        #!/usr/bin/env narwhal
        print("narwhal");
    




## Node



    
        #!/usr/bin/env node
        require("sys").print("node\n");
    




## JavaScriptCore



    
        #!/usr/bin/env jsc
        print("jsc");
    


JavaScriptCore nightly added support for shebangs on [6 December 2010](https://bugs.webkit.org/show_bug.cgi?id=49576).

Since Node and JavaScriptCore are binaries, you can use the full path instead of `env`.

