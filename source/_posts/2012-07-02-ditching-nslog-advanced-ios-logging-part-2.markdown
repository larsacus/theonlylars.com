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

In this post, we'll go over some of the options you have available in your developer tool-belt to make your life easier. I always tell myself that good tools (or even _the_ proper tool) make building things much easier.  The same is true for building an app as it is for building my new bike I have sitting in pieces in the garage. Obtain and use the correct tools, and you'll be more likely to produce a solid product.

#Logging Libraries
##Macro Replacement
Not really a "library" per se, this method is the most basic of all the options that will be listed here and will only get you part of the way to logging nerdvana. <!-- more --> The premise behind the macro log replacement is that you `#define` a preprocessor macro that will conditionally "comment out" your log statements when you switch to a non-debug mode.  Most (if not all) Xcode projects already have a `#define DEBUG` statement that you can use when running in your debug configuration. I believe I first saw this method evolve on [iPhone Incubator](http://iPhoneIncubator.com/blog/debugging/the-evolution-of-a-replacement-for-nslog), so I don't take any credit for this.  I have successfully used this in a largish project before and it has done it's job, but it is not optimal and can get messy:

{% codeblock Conditionally Replacing NSLog lang:c %}
#ifdef DEBUG
  #define DLog(fmt, ...) NSLog((@"%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
#else
  #define DLog(...)
#endif
{% endcodeblock %}

For those of you who don't regularly use preprocessor macros, here's what's happening in this block of code in pseudocode:
```
if the variable `DEBUG` has been previously defined
  define a new function named `DLog` that takes parameters in `NSLog` form and write write them into NSLog
else define `DLog` to do nothing (non-op)
```

The complex, angry-sounding capital-lettered arguments at the end of the NSLog definition are essentially helpers to output the function name and line number respectively that the command was called from.  This looks like the following when output in the console using the command `DLog(@"Logging like a boss");`:

`-[MyAppDelegate application:didFinishLaunchingWithOptions:] [Line 40] Logging like a boss`

Pretty helpful, I'd say.

__Helpfulness__: 3/10

__Performance__: 1/10 (In that it's basically `NSLog`)

__Ease of Use__: 9/10

##Cocoa Lumberjack
The first time I found [Cocoa Lumberjack](https://github.com/robbiehanson/CocoaLumberjack/), I almost didn't know what to do with myself.  I had never been presented with so many options for logging in all my time with iOS or even prior, nor had I seen such logging practices actually put to use in any projects I have worked on. One thing was for sure: my console logs have always ended up _really_ messy by the end of a project. I could basically write an entire blog post solely on Cocoa Lumberjack, but the developer seems to have done a pretty good job of that on his own with the [Cocoa Lumberjack wiki](https://github.com/robbiehanson/CocoaLumberjack/wiki) and the readme.

__Helpfulness__: 10/10 (Extremely flexible)
__Performance__: 9/10

__Ease of Use__: 8/10

##NSLogger
[NSLogger](https://github.com/fpillet/NSLogger) is the Rolls Royce of logging frameworks. Just as not everyone would choose a Rolls over, say, a Maserati, NSLogger may also be overkill or simply inappropriate for a lot of developers. That being said - NSLogger does _everything_.  Tired of having to be physically connected to your machine to get console logs?  Tired of even having to be on the same _network_ as your device for logging?  NSLogger can help.  NSLogger comes with a standalone Mac app that can accept connections via bonjour, https, https, ssl, telegraph, etc.  It does it all.  You can even log binary data.

###So what about performance?
NSLogger appears to try it's darndest to use a few CPU cycles as possible, even to the point of not sending logging info over the wire and caching logging info to memory or even disk until the desktop client is found.

The developer suggests creating a preprocessor macro to call the `NSLogger` methods in order to have them sequestered in release mode, so this doesn't really help for production debugging. For beta and other development activities, especially among distributed groups of test or QA persons, this one takes the cake.

__Helpfulness__: 6/10 (Depending on what you need help with)

__Performance__: 5/10

__Ease of Use__: 7/10

<!-- ##[JRLog](https://github.com/rentzsch/JRLog) -->
<!-- ##[HOLog](https://github.com/holtwick/HOLog) -->
<!-- ##[OCLog](https://github.com/shadowphoenix/OCLog) -->
##ECLogging
[ECLogging](https://github.com/elegantchaos/ECLogging) appears to be a rival (in my mind) of Cocoa Lumberjack. The basis behind ECLogging are "channels", "handling", and a boilerplate UI for both Mac and iOS apps to modify logging settings without having to build any of it yourself.

###Channels
"Channels" allow you to create "debug", "info", or "networking" channels (as an example).  Two types of channels exist - `debug` and `log` channels. The difference between the two being that debug channel statements are completely compiled out at compile-time in release mode (much like our dear friend `DLog` above), and log channel statements are not and will appear in the console even in release mode. Create your own channel with a single-line of code, and start using it on the next line using either `ECLog(channel, @"log");` or `ECDebug(debugChannel, @"debug log")`.

###Handling
You also have the ability to change how the log "handles" objects. The default implementation will simply call `-description` on your object and output the response. Some examples on [ECLogging's Github Wiki](https://github.com/elegantchaos/ECLogging/wiki) show passing a UIImage into the log and handling that in a custom way.

###Boilerplate Settings UI
The single-coolest part of ECLogging is the drop-in view controller it gives you to change the logging settings on the fly without having to manually write in any conditional logging code paths of your own.  Want to log to a file? Check it! Don't want to log to ASL anymore? Uncheck it! You can even choose which channels to switch on and off on-the-fly.

__Helpfulness__: 8/10

__Performance__: x/10 (No mention of performance, and I haven't test anything yet)

__Ease of Use__: 8/10

#Logging Enhancements for Debugging
I don't consider logging "enhancements" to be logging mechanisms in and of themselves, but more of helpful loggers for debugging that may or may not originate from you.

##AFHTTPRequestOperationLogger
[AFHTTPRequestOperationLogger](https://github.com/AFNetworking/AFHTTPRequestOperationLogger) is a logging enhancer for the ubiquitous [AFNetworking](https://github.com/AFNetworking/AFNetworking) library. It won't really help you unless you are using AFNetworking, but it can tremendously help out debugging networking issues you may be having with complex APIs.  It can be a bit verbose at times, even to the point of being straight up noisy, but the advantage of open source is that you can go change it if you don't like it.

<!-- ##[TouchLogging](https://github.com/TouchCode/TouchLogging) -->
##XLog
[XLog](https://github.com/Maxwin-z/XLog) is a small Xcode plugin that I have not yet tried out, but looks to be pretty neat and helpful.  The premise behind it is that it will conditionally color your lines of log output in the Xcode console depending on keywords.  For instance, if the word debug is found in your log, the console line is colored green.  I've actually filed a similar radar with apple to be able to have a multicolored log, so the prospect of this is pretty cool.

###XcodeColors
[XcodeColors](https://github.com/robbiehanson/XcodeColors) is a similar-looking Xcode plugin to XLog in that it colors your console output. The original was created by [DeepIT on Github](https://github.com/DeepIT), but now appears to be mostly maintained by [Robbie Hanson](https://github.com/robbiehanson), who created Cocoa Lumberjack (above).

#Conclusion
YOU NEED A CONCLUSION

***

_Disclaimer:_
_I don't pretend to know every nook and cranny about the topics I discuss.  I find teaching others is the best learning tool - and I like to learn.  As a result, you may disagree or \*gasp\* find errors.  If you disagree with any of this, write me a note at [@theonlylars](https://twitter.com/theonlylars) and let me know. I'll either correct it or simply agree to disagree._