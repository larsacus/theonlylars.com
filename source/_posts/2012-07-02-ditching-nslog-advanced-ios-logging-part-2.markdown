---
layout: post
title: "Ditching NSLog, Part II: Good Tools Make Work Easy"
date: 2012-07-29 22:09
published: true
comments: true
categories: [Advanced iOS,iOS,Development,Third-Party,NSLog,Debugging,Logging]
keywords: "NSLog,logging,iOS,development"
---
#In Review
In [Part I of Ditching `NSLog`](/blog/2012/07/02/ditching-nslog-advanced-ios-logging-part-1/), we introduced our good friend `NSLog` and why there is a time and a place for him, but that those are few and far between.  We also discussed the different types of logging, and why you might want to use them, as well as how `NSLog`, in general, cannot satisfy these requirements.

In this post, we'll go over some of the options you have available in your developer tool-belt to make your tasks easier to tackle. I always tell myself that good tools (or even _the_ proper tool) make building things much easier.  The same is true for building an app as it is for building my new bike I have sitting in pieces in the garage. Obtain and use the correct tools, and you'll be more likely to produce a solid product.

_Pre-Disclaimer: My original intent for the second part of this post was to actually __use__ every library before writing about them.  I found so many different libraries, that this never actually ended up happening.  I present here a synopsis of each logging library on the basis that I have used some of them, but I have not used them all.  Blame my day job and Battlefield 3. Yes, I am weak._

#Logging Libraries
##Simple Macro Replacement
While not really a "library" per se, this _method_ is the most basic of all the options that will be listed here and will only get you part of the way to logging nerdvana. <!-- more --> The premise behind the macro log replacement is that you `#define` a preprocessor macro that will conditionally "comment out" your log statements at compile-time when you switch to a non-debug build mode.  Most (if not all) Xcode projects already have a `#define DEBUG` statement that you can use when running in your debug configuration. I believe I first saw this method evolve on [iPhone Incubator](http://iPhoneIncubator.com/blog/debugging/the-evolution-of-a-replacement-for-nslog), so I don't take any credit for this.  I have successfully used this in a largish project before and it has done it's job, but it is not optimal and can get messy due to it's "all or nothing" approach:

{% codeblock Conditionally Replacing NSLog lang:c %}
#ifdef DEBUG
  #define DLog(fmt, ...) NSLog((@"%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
#else
  #define DLog(...)
#endif
{% endcodeblock %}

For those of you who don't regularly use preprocessor macros, here's what's happening in this block of code in pseudocode:
```
if the macro `DEBUG` has been previously defined
  define a new function named `DLog` that takes parameters in `NSLog` form and write write them into NSLog
else
  define `DLog` to do nothing (non-op)
```

There are not many instances in which I recommend using preprocessor macros but, in my opinion, this is one instance in which it's use is acceptable.

The complex, angry-sounding capital-lettered arguments at the end of the `NSLog` definition are essentially helpers to output the function name and line number respectively that the command was called from.  This looks like the following when output in the console using the command `DLog(@"Logging like a boss");`:

`-[MyAppDelegate application:didFinishLaunchingWithOptions:] [Line 40] Logging like a boss`

Pretty helpful, I'd say.

__Helpfulness__: 3/10  
__Performance__: 1/10 - It's basically `NSLog`  
__Ease of Use__: 9/10

##NSLogger
[NSLogger](https://github.com/fpillet/NSLogger) is the Rolls Royce of logging frameworks. Just as not everyone would choose a Rolls over, say, a Maserati, NSLogger may also be overkill or simply inappropriate for a lot of developers. That being said - NSLogger does _everything_.  Tired of having to be physically connected to your machine to get console logs?  Tired of even having to be on the same _network_ as your device for logging?  NSLogger can help.  NSLogger comes with a standalone Mac app that can accept connections via bonjour, https, https, ssl, telegraph, etc.  It does it all.  You can even log binary data blobs.

###So what about performance?
NSLogger appears to try it's darndest to use a few CPU cycles as possible, even to the point of not sending logging info over the wire and caching logging info to memory and possibly even to disk until the desktop client is found.

The developer suggests creating a preprocessor macro to call the `NSLogger` methods in order to have them sequestered in release mode, so this doesn't really help for production debugging. For beta and other development activities, especially among distributed groups of test or QA persons, this one takes the cake.

__Helpfulness__: 6/10 - Really depends on what you need help with  
__Performance__: 5/10  
__Ease of Use__: 6/10

<!-- ##[JRLog](https://github.com/rentzsch/JRLog) -->
<!-- ##[HOLog](https://github.com/holtwick/HOLog) -->
<!-- ##[OCLog](https://github.com/shadowphoenix/OCLog) -->
##ECLogging
[ECLogging](https://github.com/elegantchaos/ECLogging) appears to be a rival (in my mind) of Cocoa Lumberjack (more on Lumberjack later). The basis behind ECLogging are "channels", "handling", and a boilerplate UI for both Mac and iOS apps to modify logging settings without having to build any of it yourself.

###Channels
"Channels" allow you to create "debug", "info", or "networking" channels (as an example) and are very similar to Cocoa Lumberjack's methodology on logging.  Two types of channels exist - `debug` and `log` channels. The difference between the two being that debug channel statements are completely compiled out at compile-time in release mode (much like our dear friend `DLog` above), and log channel statements are not and will appear in the console even in release mode. Create your own channel with a single-line of code, and start using it on the next line using either `ECLog(channel, @"log");` or `ECDebug(debugChannel, @"debug log")`.

###Handling
You also have the ability to change how the log "handles" objects. The default implementation will simply call `-description` on your object and output the response, just as `NSLog` does. Some examples on [ECLogging's Github Wiki](https://github.com/elegantchaos/ECLogging/wiki) show passing a `UIImage` into the log and handling that in a custom way.

###Boilerplate Settings UI
The single-coolest part of ECLogging is the drop-in view controller it gives you, which enables the ability to change the logging settings on the fly without having to manually write in any conditional logging code paths of your own.  Want to log to a file? Check it! Don't want to log to ASL anymore? Uncheck it! _In_ the UI, no less! You can even choose which channels to switch on and off on-the-fly. Very neat.

__Helpfulness__: 8/10  
__Performance__: x/10 - No mention of performance, and I haven't tested anything yet  
__Ease of Use__: 8/10

##Cocoa Lumberjack
The first time I found [Cocoa Lumberjack](https://github.com/robbiehanson/CocoaLumberjack/), I almost didn't know what to do with myself.  I had never been presented with so many options for logging in all my time with iOS or even prior, nor had I seen such logging practices actually put to use in any projects I have worked on. One thing was for sure: my console logs have always ended up _really_ messy by the end of a project. I could basically write an entire blog post solely on Cocoa Lumberjack, but the developer seems to have done a pretty good job of that on his own with the [Cocoa Lumberjack wiki](https://github.com/robbiehanson/CocoaLumberjack/wiki) and the readme. The developer touts that Cocoa Lumberjack is simple, fast, powerful, and flexible.  Let's see how true this is:

###Fast
The developer claims that Cocoa Lumberjack is (in most cases) an order of magnitude faster than NSLog.  He achieves this reusing a single ASL connection and posting messages to this connection asynchronously. Asynchronous logging is optional, as (for example) you obviously want an error to log immediately as the system may be in an unstable state. It's a little different using asynchronous logging for the first time, as you typically expect to step through your code and immediately see your log statements show up in the console.  You'll have to use the synchronous logs in order for this to work. The developer has spent a great deal of time architecting Cocoa Lumberjack from the ground-up with performance in mind. Check out the [Cocoa Lumberjack performance](https://github.com/robbiehanson/CocoaLumberjack/wiki/Performance) page for all the nitty-gritty behind all the benchmarks.

###Simple
The Cocoa Lumberjack framework is dead-simple to use:

1. Setup a logging destination
2. Log using a log-level logger
3. Profit?

{% codeblock Setting up and using Cocoa Lumberjack lang:c %}

[DDLog addLogger:[DDASLLogger sharedInstance]];

DDLogError("Image is nil!");
DDLogVerbose("newViewController viewDidLoad");

{% endcodeblock %}

You have access to four default levels of logging: `DDLogError`, `DDLogWarn`, `DDLogInfo`, `DDLogVerbose`. In addition to these, you can both [customize the names](https://github.com/robbiehanson/CocoaLumberjack/wiki/CustomLogLevels) of the default levels as well [as define your additional custom levels](https://github.com/robbiehanson/CocoaLumberjack/wiki/FineGrainedLogging).

And since the `DDLog` syntax is the same as `NSLog`, converting/using `DDLog` shouldn't even fire off any new neurons.

###Powerful
Much of Cocoa Lumberjack's power comes from it's simplicity. With the ability to create highly-tuned logging paths and mechanisms, Cocoa Lumberjack gives you, the developer, the ability to hone-in on issues that you're working with and trying to identify. With single log statements, you can deliver log messages to multiple destinations (file, ASL, network, etc.), as well as add custom destinations of your own. You choose the info and method of delivery for how you need to get things debugged.

###Flexible
The power doesn't end after you switch your build mode to release mode, either. Dynamically giving you the ability to change the log level at runtime can allow you to leave all of your logging architecture compiled-in but inactive until an issue arises in the field. Have your users log to a file when they're having a specific issue, then send the log file to your server for analysis. Your mother's `NSLog` can't do that.

__Helpfulness__: 10/10 - Extremely flexible  
__Performance__: 9/10  
__Ease of Use__: 8/10

#Logging _Enhancements_ for Debugging
I don't consider logging "enhancements" to be logging mechanisms in and of themselves, but more of an augment or helpful loggers for debugging that may or may not originate from you.

##AFHTTPRequestOperationLogger
[AFHTTPRequestOperationLogger](https://github.com/AFNetworking/AFHTTPRequestOperationLogger) is a logging enhancer for the ubiquitous [AFNetworking](https://github.com/AFNetworking/AFNetworking) library. It won't really help you unless you are using AFNetworking, but it can tremendously help out debugging networking issues you may be having with complex APIs.  It can be a bit verbose at times, even to the point of being straight up noisy, but the advantage of open source is that you can go change it if you don't like it.

<!-- ##[TouchLogging](https://github.com/TouchCode/TouchLogging) -->
##XLog
[XLog](https://github.com/Maxwin-z/XLog) is a small Xcode plugin that I have not yet tried out, but looks to be pretty neat and helpful.  The premise behind it is that it will conditionally color your lines of log output in the Xcode console depending on keywords.  For instance, if the word debug is found in your log, the console line is colored green.  I've actually filed a similar radar with apple to be able to have a multicolored log, so the prospect of this is pretty cool.

##XcodeColors
[XcodeColors](https://github.com/robbiehanson/XcodeColors) is a similar-looking Xcode plugin to XLog that colors your console output. The original was created by [DeepIT on Github](https://github.com/DeepIT), but now appears to be mostly maintained by [Robbie Hanson](https://github.com/robbiehanson), who created Cocoa Lumberjack (above). If you use Cocoa Lumberjack, I would use this.

#Conclusion
Logging is an important yet often overlooked practice that can make development and debugging much easier. Finding the right tool for a job can be a task in itself, but once the correct tool (either for you, or for the job) is found, your task becomes much easier or in some cases - possible.

Extensible logging enables you to customize your logging workflow to best suit your app, team, or QA environment. Performant logging means that you don't have to worry as much about production logging throwing a wrench in your highly-tuned scroll-view code. Just as performance and extensibility are important, if logging isn't simple - developer's just aren't going to want to utilize it. Logging must be as simple as `NSLog` or developers won't adopt it in their daily workflow.

It is my opinion that highly extensible, non-`NSLog` logging should be just as much an architectural consideration as coding standards, unit test coverage, and class prefix names on a project. Most developers may not know advanced logging techniques even exist, much less employ one in a large project in order to get more debugging done in less time. Don't spend more time debugging your code - equip yourself with the proper tools to spend more time making your app the best experience possible for your users, while also making your future-self love you (which we all know doesn't happen that often).

***

_Disclaimer:_
_I don't pretend to know every nook and cranny about the topics I discuss.  I find teaching others is the best learning tool - and I like to learn.  As a result, you may disagree or \*gasp\* find errors.  If you disagree with any of this, write me a note at [@theonlylars](https://twitter.com/theonlylars) and let me know. I'll either correct it or simply agree to disagree._