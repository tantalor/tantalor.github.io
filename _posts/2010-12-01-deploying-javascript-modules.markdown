---
author: John Tantalo
comments: true
date: 2010-12-01 17:08:25+00:00
layout: post
slug: deploying-javascript-modules
title: Deploying JavaScript Modules
wordpress_id: 114
categories:
- Code
---

JavaScript is used in many different environments, and each environment has its own method for deploying modules. Some allow importing objects into a local namespace, but notably web browsers do not. So, what is the best way to write a module that can be safely deployed across different environments?



### Anonymous closure module pattern



My example module will be implemented as an anonymous closure. It will have one public class and a private member.

{% highlight javascript %}
(function () {
  var PublicClass = function () {};
  var PrivateMember = new Object();
})();
{% endhighlight %}

I will incrementally add support for each environment by detecting whether that environment is present.



### Web browsers



Web browsers do not support importing into a local namespace, so I simply add _PublicClass_ to the global _window_ namespace.

{% highlight javascript %}
(function () {
  var PublicClass = function () {};
  var PrivateMember = new Object();
      
  if (typeof(window) !== 'undefined') {
    // browser
    window.PublicClass = PublicClass;
  }
})();
{% endhighlight %}

Client must first include the module with a _script_ HTML tag.

{% highlight html %}
<script type="text/javascript" src="sample-module.js"></script>
<script type="text/javascript">
  var instance = new PublicClass();
</script>
{% endhighlight %}


### CommonJS



[CommonJS](http://www.commonjs.org/) is a pattern implemented by some server-side JavaScript engines such as [Node](http://nodejs.org/) and [Narwhal](http://narwhaljs.org/). In this model, a module assigns its public interface to a special _exports_ object.

{% highlight javascript %}
(function () {
  var PublicClass = function () {};
  var PrivateMember = new Object();
    
  if (typeof(window) !== 'undefined') {
    // browser
    window.PublicClass = PublicClass;
  } else if (typeof(exports) !== 'undefined') {
    // commonjs
    exports.PublicClass = PublicClass;
  }
})();
{% endhighlight %}

Clients _require_ the module, which returns the module's exports.

{% highlight javascript %}
var PublicClass = require('./sample-module').PublicClass;
var instance = new PublicClass();
{% endhighlight %}

### JavaScriptCore

[JavaScriptCore](http://webkit.org/projects/javascript/) (or "jsc") does not follow the CommonJS pattern. Instead, when a file is included with the _load_ function, the last value in the file is returned to the caller.

I return an object literal from the anonymous function which simulates the _exports_ object in CommonJS. Since the anonymous function is executed as the last statement in the file, the exports are returned to the client. This case doesn't rely on any special variable such as _window_ or _exports_, so I will make it default rather than detecting the JavaScriptCore environment.

{% highlight javascript %}
(function () {
  var PublicClass = function () {};
  var PrivateMember = new Object();

  if (typeof(window) !== 'undefined') {
    // browser
    window.PublicClass = PublicClass;
  } else if (typeof(exports) !== 'undefined') {
    // commonjs
    exports.PublicClass = PublicClass;
  } else {
    // jsc (default)
    return {
      "PublicClass": PublicClass
    };
  }
})();
{% endhighlight %}

Clients _load_ the module, which returns the module's exports.

{% highlight javascript %}
var PublicClass = load('sample-module.js').PublicClass;
var instance = new PublicClass();
{% endhighlight %}

### Why not use a global variable?



There is another [JavaScript module pattern](http://ajaxian.com/archives/a-javascript-module-pattern) that declares a global variable to contain the module's public interface, as in this example.

{% highlight javascript %}
var PublicClass = (function () {
  var PublicClass = function () {};
  var PrivateMember = new Object();
  return PublicClass;
})();
{% endhighlight %}

This pattern will work perfectly in web browsers and JavaScriptCore, but it also pollutes JavaScriptCore's global namespace. Since JavaScriptCore supports local imports, global variables should be avoided. Furthermore, this pattern does not support multiple public interfaces, e.g., a single anonymous closure that exports two public classes.



### Example: GraphJS



[GraphJS](https://github.com/tantalor/graphjs/blob/master/lib/graph.js) is a JavaScript module of mine that uses the module deployment pattern I described. It works with Node, Narwhal, Ringo, JSC, and in web browsers.

