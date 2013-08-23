---
author: John Tantalo
comments: true
date: 2013-04-29 16:55:33+00:00
layout: post
slug: extracting-video-from-brightcove-player-with-rtmpdump
title: Extracting video from Brightcove player with rtmpdump
wordpress_id: 284
categories:
- Web
---

Video embedded in a Flash player prevents easy extraction of the video file. This post describes how to discover and extract video from a Brightcove Flash player with rtmpdump.

In the past, the `flv` files were easily recovered by [interrogating the cache](http://mcchrystals.wordpress.com/2008/12/30/firefox-download-brightcove-videos/). This method relies on the browser logging HTTP network activity, but recently Flash players have switched to [RTMP](http://en.wikipedia.org/wiki/Real_Time_Messaging_Protocol), a streaming protocol, which is not logged by the browser.

If the Flash player has a fallback for mobile devices then it is rather easy to [trick the player into HTML5 mode](http://www.tjkelly.com/how-to-download-brightcove-video/) and recover an `mp4` file.

I intended to extract a video from [AMC's website](http://www.amctv.com/mad-men/videos/next-on-mad-men-episode-606), which did not have an HTML5 fallback, so I had to recover the stream used by Flash player.

The first step was to examine the network activity for clues about the video source. Brightcove makes this easy because their logging activity includes information about the video.

I opened Chrome's network activity panel and searched for any requests containing "mp4", a common format for video on the web. This reveals a 1x1 tracking pixel with a bunch of logging parameters,



    http://ma156-r.analytics.edgesuite.net/9.gif?a=S~b=9c1e67f75e9bf1686~c=D2C517F3C2DC4F16AF5BE404B5127F4DD9F3E19B~d=EA2FDD29066B9E06BFA83A8F42BF1A84C16C5DFB~e=1~f=R~g=0~h=1.2~i=1.1~k=89AEF5EBD081F29F468FCD00F20201C817C78AD1~am=D~_ac=&mp4;:rtmp_pd/196217268/196217268_2330789578001_AMC-MM-606-NextOn.mp4~ag=www.amctv.com~al=Mac%20OS~dx=1.679~en=Next%20on%20Mad%20Men:%20Episode%20606~pd=760214963001~_tt=Next%20on%20Mad%20Men:%20Episode%20606~_cd_1557=196217268~cm=Akamai~v=17~w=1486~aa=cp126124.edgefcs.net~ab=ondemand/&mp4;:rtmp_pd/196217268/196217268_2330789578001_AMC-MM-606-NextOn.mp4~ad=1935~ae=rtmp~af=http://admin.brightcove.com/%5B%5BIMPORT%5D%5D/79423.analytics.edgekey.net/csma/brightcove/BrightcoveCSMALoader.swf~ai=Mozilla/5.0%20(Macintosh;%20Intel%20Mac%20OS%20X%2010_8_3)%20AppleWebKit/537.31%20(KHTML,%20like%20Gecko)%20Chrome/26.0.1410.65%20Safari/537.31~aj=11,7,700,169~ak=Flash_PlugIn~an=172~ao=1311~ap=1483~at=Netscape~au=832*455~_aw=rtmp://cp126124.edgefcs.net/ondemand/&mp4;:rtmp_pd/196217268/196217268_2330789578001_AMC-MM-606-NextOn.mp4~ay=csma-3.0.27:brightcoveLoader-1.0.25~az=1.2~ba=900000~bb=23.59.190.199~va=1~_cd_1759=cp126124.edgefcs.net~mb=4845000~qoe=99.5068


One of these parameters looks like a URL,

    rtmp://cp126124.edgefcs.net/ondemand/&mp4;:rtmp_pd/196217268/196217268_2330789578001_AMC-MM-606-NextOn.mp4

So what is RTMP? And how do we get a video out of it? Googling for "edgefcs" led me to [rtmpdump](http://rtmpdump.mplayerhq.hu/), a command-line tool for dumping RTMP streams to files. Usage is uncomplicated,

    rtmpdump --rtmp rtmp://cp126124.edgefcs.net/ondemand --playpath mp4:rtmp_pd/196217268/196217268_2330789578001_AMC-MM-606-NextOn.mp4 -o 606.mp4

This command downloads the RTMP stream into a local file, "606.mp4", which VLC can play.
