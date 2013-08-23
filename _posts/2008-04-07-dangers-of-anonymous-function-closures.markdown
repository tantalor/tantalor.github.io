---
author: John Tantalo
comments: true
date: 2008-04-07 19:43:57+00:00
layout: post
slug: dangers-of-anonymous-function-closures
title: Dangers of anonymous function closures
wordpress_id: 17
categories:
- Code
- Web
tags:
- JavaScript
---


John Resig's [jQuery](http://jquery.com/) uses a standard technique of anonymous function closures to namespace its internal functionality.


>     (function(){
>       // jQuery stuff goes here...
>     })(); 


This is all well and good until you want to inline this code under some of your application code, as I did while working on [Eventful's MySpace application](http://www.myspace.com/topperformers). Consider this example, written in standard Javascript style.


>     var foo = function(bar) {
>       console.log("foo");
>       return bar;
>     }
>     
>     (function(){
>       console.log("bar")
>     })(); 


What do you expect this code to print? If you say "bar", you're wrong. It prints "foo bar" because in this context the anonymous function closure becomes a call to the function that prints "foo" and returns the function that prints "bar", which is then called.

Most of us probably wouldn't notice what just happened because we are so used to Javascript interpreters automatically inserting semicolons after function definitions.

The danger is assuming, as John Resig and hundreds others have, that your anonymous function closure is the first token in a new line. In my case, it wasn't. The solution? Either start meticulously sprinkling semicolons in your application code, or just add a single semicolon before your parenthetical block, guaranteeing it's the first token in the line.


>     ;(function(){
>       // jQuery stuff goes here...
>     })();
>      



