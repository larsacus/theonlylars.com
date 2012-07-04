---
layout: post
title: "Ditching NSLog, Part II: Good Tools Make Work Easy"
date: 2012-07-05 21:49
published: false
comments: true
categories: [Advanced iOS,iOS,Development,Third-Party,NSLog,Debugging,Logging]
---
#In Review
In [Part I of Ditching `NSLog`](/blog/2012/07/02/ditching-nslog-advanced-ios-logging-part-1/), we introduced our good friend `NSLog` and why there is a time and a place for him, but that those are few and far between.  We also discussed the different types of logging, and why you might want to use them, as well as how `NSLog`, in general, cannot satisfy these requirements.

In this post, we'll go over some of the options you have available in your developer tool-belt to make your life easier. I always tell myself that good tools (or even _the_ proper tool) make building things much easier.  The same is true for building an app as it is for building my new bike I have sitting in pieces in the garage. Obtain the right tools, and you'll be more likely to produce a solid product.

##Logging Libraries
###Macro Replacement
http://iPhoneIncubator.com/blog/debugging/the-evolution-of-a-replacement-for-nslog
###Cocoa Lumberjack
###NSLogger
###[JRLog](https://github.com/rentzsch/JRLog)
###[HOLog](https://github.com/holtwick/HOLog)
###[OCLog](https://github.com/shadowphoenix/OCLog)
###[ECLogging](https://github.com/elegantchaos/ECLogging)

##Logging Enhancements for Debugging
###AFHTTPRequestOperationLogger
###[TouchLogging](https://github.com/TouchCode/TouchLogging)
###[XLog](https://github.com/Maxwin-z/XLog)

***

_Disclaimer:_
_I don't pretend to know every nook and cranny about the topics I discuss.  I find teaching others is the best learning tool - and I like to learn.  As a result, you may disagree or \*gasp\* find errors.  If you disagree with any of this, write me a note at [@theonlylars](https://twitter.com/theonlylars) and let me know. I'll either correct it or simply agree to disagree._