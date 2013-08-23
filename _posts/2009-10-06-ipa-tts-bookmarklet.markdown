---
author: John Tantalo
comments: true
date: 2009-10-06 17:33:49+00:00
layout: post
slug: ipa-tts-bookmarklet
title: IPA TTS bookmarklet
wordpress_id: 70
categories:
- Code
- Web
tags:
- bookmarklet
- ipa
- tts
---

**Last modified [2 October 2011](https://gist.github.com/202523/8b3b7339bb37682d3233471f77cb54b44db948b1)**

I present the IPA ([International Phonetic Alphabet](http://en.wikipedia.org/wiki/International_Phonetic_Alphabet)) TTS ([text-to-speech](http://en.wikipedia.org/wiki/Text-to-speech)) [bookmarklet](http://en.wikipedia.org/wiki/Bookmarklet). ([Source](http://gist.github.com/202523))

Bookmarklet: [IPA TTS](javascript:%28function%20%28%29%20%7Bvar%20selection%20%3D%20window.getSelection%20%3F%20window.getSelection%28%29%20%3Adocument.getSelection%20%3F%20document.getSelection%28%29%20%3Adocument.selection%20%3F%20document.selection.createRange%28%29.text%20%3A%20%27%27%3Bif%20%28selection%29%20%7Bselection%20%3D%20String%28selection%29%3B%7Dif%20%28selection%29%20%7Bvar%20match%20%3D%20selection.match%28%27%5C%2F%28.%2A%29%5C%2F%27%29%3Bvar%20ipa%3Bif%20%28match%20%26%26%20match%5B1%5D%29%20%7Bipa%20%3D%20match%5B1%5D%3B%7Dif%20%28%21ipa%29%20%7Bipa%20%3D%20prompt%28%22Couldn%27t%20find%20any%20IPA.%20Type%20it%20in%20instead%3F%22%29%3B%7Dif%20%28ipa%29%20%7Bipa%20%3D%20ipa.replace%28%2F.%2Fg%2C%20function%20%28c%29%20%7Bif%20%28%2F%25u0%28...%29%2F.test%28escape%28c%29%29%29%20%7Breturn%20escape%28c%29.replace%28%2F%25u0%28...%29%2Fg%2C%20%27%26%23x%241%3B%27%29%3B%7D%20else%20%7Breturn%20c%3B%7D%7D%29%3Bvar%20request%20%3D%20%7Btxt%3A%20%27%3Cphoneme%20alphabet%3D%22ipa%22%20ph%3D%22%27%2Bipa%2B%27%22%3E%20%3C%2Fphoneme%3E%27%2Cvoice%3A%20%27crystal%27%7D%3Bvar%20iframe%20%3D%20document.createElement%28%27iframe%27%29%3Biframe.name%20%3D%20iframe.id%20%3D%20%22iframe%22%2Bnew%20Date%28%29.getTime%28%29%3Bdocument.body.appendChild%28iframe%29%3Bvar%20form%20%3D%20document.createElement%28%27form%27%29%3Bform.target%20%3D%20iframe.name%3Bform.method%20%3D%20%27post%27%3Bform.action%20%3D%20%27http%3A%2F%2F192.20.225.36%2Ftts%2Fcgi-bin%2Fnph-nvdemo%27%3Bform.acceptCharset%20%3D%20%22iso-8859-1%22%3Bform.style.display%20%3D%20%27none%27%3Bfor%20%28var%20name%20in%20request%29%20%7Bvar%20input%20%3D%20document.createElement%28%27input%27%29%3Binput.name%20%3D%20name%3Binput.value%20%3D%20request%5Bname%5D%3Bform.appendChild%28input%29%3B%7Ddocument.body.appendChild%28form%29%3Bform.submit%28%29%3B%7D%7D%20else%20%7Balert%28%22Select%20some%20IPA.%22%29%3B%7D%7D%29%28%29%0A) (drag to your bookmark bar)

How to use: select any text that contains IPA and click on the bookmarklet. Wait a second for the iframe at the bottom of the page to load.

Try these examples: /ˌɪntəˈnæʃnəl/, /ˌɪntɚˈnæʃnəl/



### How does this work?



The bookmark creates a POST request to the [AT&T; Natural Voices® Text-to-Speech Demo](http://www2.research.att.com/~ttsweb/tts/demo.php) with the IPA text in [SSML](http://en.wikipedia.org/wiki/Speech_Synthesis_Markup_Language). The AT&T; demo has modest support for IPA, but often fails. If you know of another IPA TTS engine, please let me know or [fork the bookmarklet and add it](https://gist.github.com/202523).

