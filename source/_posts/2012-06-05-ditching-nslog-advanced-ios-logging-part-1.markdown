---
layout: post
title: "Ditching NSLog Part I: You Deserve Better"
date: 2012-07-02 21:35
published: false
comments: true
categories: [iOS,development,third-party,NSLog,debugging,logging]
---
Every iOS developer has used `NSLog`. We use it as a debugging tool to see the what data the system sees at a particular time while the application is running, or even the path our data takes when moving through our applications. But `NSLog` is not the be-all, end-all logging mechanism many of you think it is.

##I Use NSLog and I think it's awesome. Isn't that enough?
Not really. Let me count the ways. You may think of `NSLog` as your awesome friend with the boat who lets you join him on the weekends, but in reality he's the slow guy in line paying for that Xbox with small coins and wadded singles when you just want to pay with plastic and _not_ be standing in line.

###Speed
`NSLog` is slow. This wouldn't be so bad if it were asynchronous - but it is most definitely very synchronous. This is why you can set a breakpoint, step through your code and instantly see your `NSLog` statements show up in the console after you step over them. Make enough calls with `NSLog` in highly-iterative, performance-oriented or `drawRect:` code and the performance-penalty begins to add up.
<!-- more -->

###Conditional Logic
`NSLog` is dumb. There is only one type of `NSLog` - the type that will display in your console. Yet, we will use our logging statements for many different purposes. Sometimes we log an error, a UI event, maybe a networking call, other times we are simply keeping track of a view controller's view lifecycle. These are all very different types of logs that you may or may not want to show up all the time.

###Beyond Debugging
`NSLog` will show up even in production code regardless of release setting unless you deliberately do something about it. It will rear it's ugly head all up in your production code, even on-device when disconnected with the same performance-penalties as before, but now nobody can even see the logs unless they can connect to their device's console. This is serving zero purpose to aid the user's experience in your application.

I'm sure we've all done the genius find/replace `NSLog` with `//NSLog`. Yes, this gets rid of the NSLogs in production, but it is a huge pain to keep up with and looks ugly to boot. Then after you do this, the reverse action isn't always as straight-forward. There may be `NSLog`s that you intentionally wanted commented out that are now uncommented as a result of you trying to undo your previous strike of genius.

There is a time and place for production logging, but chances are pretty good that you aren't doing it right if you're still using `NSLog`. Next we'll go over the different types of logging, then discuss their role in debugging in both development _and_ production after you ship your application to your users.

##Types of Logging
###Console Logging
This is the most basic type of logging, and one which you're probably most familiar with. The console log simply logs all of your statements to the debug log (if you're in Xcode or in the organizer) or to Console.app if you're running a mac app or running an app in the iPhone simulator detached from the debugger.

_Practicality: *Production* (used correctly) or *development* debugging_

###File Logging
File logging is just like it sounds. Instead of logging to the debug console, you simply log to a file. This is best demonstrated by simply having you look in the `/var/log/` folder on your Mac or Linux box.  You'll see a plethora of file logs chock full of information about what is happening on your system.  For iOS, this really doesn't make sense for your sandboxed application unless we as the developers know that we need to monitor that information.

One example of this necessity is that a particular user is having an issue you've never even heard of before and you would like to capture the log output to see what is going on. Simply activate file logging by some super-secret menu (see Tweetbot screenshot below) and make an easy mechanism for the user to send you that log in an email. Just be responsible and don't log personal information and always remember to bound the length of your logs - nobody likes 100MB log files.

_Practicality: *Production* debugging_

###Remote Logging
This is a little more difficult to accomplish and requires either some knowhow about client/server networking on a lower level than simply API consumption, or some help from a third party library.  The benefit to remote logging is that it combines all the best traits of console logging (real-time information) with file logging's ability to not have to be physically connected to the device in order to capture the logs. This would require some participation on the user's end, but if they're having extreme issues, they may be more than happy to let you remotely log their device while they demonstrate the bug for you.

_Practicality: *Production* debugging (with a little more user participation)_

##To Log, or Not to Log?
So how often should include a log statement in their code?  When using the "right" logging techniques, you should log as much as possible. Some examples include:

  - View/view controller life-cycles - calls to `viewDidLoad`, `dealloc`, etc.
  - Errors
  - Warnings
  - Unexpected user input
  - Whatevs

{% pullquote %}The "right" logging techniques depends highly on who you are as a programmer and even moreso on where you work. I know we have very specific coding standards at [Mutual Mobile](http://mutualmobile.com) so as to increase uniformity between codebases on disparate projects.  So the "right" logging techniques and practices I discuss here are those in which I believe most developers and users would benefit from. These practices are those in which you can dynamically choose which types of logs you would like to see depending on what you are working on.  {"In not so many words: dynamic runtime logging"}.{% endpullquote %}

Dynamic means that it involves conditional logic, and mucking with the logging mechanism at runtime means preprocessor macros are pretty much out of the picture.  In Part 2, we'll look at some techniques as well as full-blown open source libraries that will make our job of logging much easier, more dynamic, and eliminate logs in production for all but the cases we specifically care about. 
  