---
author: John Tantalo
comments: true
date: 2008-09-26 23:15:44+00:00
layout: post
slug: ie6-submit-click-on-text-input-submit
title: IE6 submit-click on text input submit
wordpress_id: 32
categories:
- Code
tags:
- IE6
- JavaScript
---

Found this fun bug in IE6 today. If you have a text input and submit input unassociated with a form, then submitting the text input (by hitting return or enter when focused) will cause a click event on the submit input.

The solution is simple and intuitive: eliminate unassociated submit inputs and submit buttons.

    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN"
    "http://www.w3.org/TR/html4/DTD/strict.dtd">
    <input type="text"/>
    <input type="submit" onclick="this.value='clicked';" />

[Demo (IE only)](http://software.hixie.ch/utilities/js/live-dom-viewer/?%3C!DOCTYPE%20html%20PUBLIC%20%22-%2F%2FW3C%2F%2FDTD%20HTML%204.01%2F%2FEN%22%0D%0A%22http%3A%2F%2Fwww.w3.org%2FTR%2Fhtml4%2FDTD%2Fstrict.dtd%22%3E%0D%0A%3Cinput%20type%3D%22text%22%2F%3E%0D%0A%3Cinput%20type%3D%22submit%22%20onclick%3D%22this.value%3D'clicked'%3B%22%20%2F%3E)
