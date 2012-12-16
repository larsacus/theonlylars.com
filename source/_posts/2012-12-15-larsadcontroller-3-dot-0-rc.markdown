---
layout: post
title: "LARSAdController 3.0 RC"
date: 2012-12-15 15:25
comments: true
published: true
categories: iOS development ads open-source ARC LARSAdController
keywords: "iAd,iOS,iOS development,AdMob,iAd Integration,easy,LARSAdController,ad mediation,iOS mediation,iOS house ads"
---

_*Note: This is pre-release software and is still in-development. If you need a production version, please refer to the [master branch](https://github.com/larsacus/LARSAdController) for production code.*_

I've been working hard on refactoring [LARSAdController 2.0](https://github.com/larsacus/LARSAdController) into a more modular and maintainable state over the past few months. I think it's finally in a place that could warrant some final testing from other developers before being merged into the master branch. 

Let's take a look at what's changed:

##A New Class Prefix
I've been using the class prefix `LARS` for all of my open-source classes. While this really is short and unique, it's always felt pretty egotistical to have my name plastered all over my classes. This is just how I feel from this side of the keyboard. Does it look the same from the user's end? Let me know what you think.

Anyway, I've migrated new classes to the new `TOL` prefix, for [@TheOnlyLars](https://twitter.com/theonlylars). This feels more anonymous and I haven't seen the prefix used by anyone else before, yet. I'd love to know if someone well known _is_ using the `TOL` class prefix. If I feel like I like this prefix better, I may just start using it for everything.

I also like that it sounds like "toll". I have grand visions of having some cool project named `TOLBooth`, `TOLRoad`, or `TOLHouse`. I can't think of practical project ideas to put those names on, however.

##Modular Ad Networks
The big new feature for this release is modular ad networks. Ad junkies will know this as ad mediation. AdMob has an ad mediation framework, so does AdWhirl, MobClix, and others. This is my implementation. The actual mediation logic is in a single .h/.m file pair. The other files support the ad networks. I feel this is a very lightweight implementation compared to other company's mediation platforms, not to mention it's open source so you can peek under the hood to see how another developer has solved a problem.

Right now I've built ad network adapters for iAds and AdMob ads. These are the only two ad networks that I use in Droid Light, so I don't know much about others, and these two seem to be the most popular for banner ads on iOS.

###House Ads
A third planned adapter I'm particularly excited about is an adapter for "house" ads, _but I have not started on it as of yet_. House ads would basically amount to an image and a tap destination that you supply yourself. It's an ad for yourself. <!-- more -->This will ideally be loaded dynamically from a remote server somewhere so you won't have to upload a new binary to the app store every time you want to change the ads.

You could have this house ad link to another page in your app, have it load a remote URL for another app you have in the app store, or even let users know a new version is available. This would be able to be mediated in just like any other ad network.

This is not necessarily part of this release, but is something that is going to be at least started by then.

###Additional Ad Networks
Additional ad networks can be easily created by conforming your `NSObject` subclass to the `TOLAdAdapter` protocol. As long as you have logging enabled, I feel the warnings and assertions, as well as the header documentation have been well-built enough to make this a pretty painless process.

###Priority
You can now prioritize which ad network you would like to have highest priority. Version 2.0 and prior had their priority fixed to have iAds serve first and have AdMob ads slaved to display when there was no available iAd inventory to display.

This worked for the first couple years of iAd as the eCPM and fill rate was so much better than anything else. Since iAds are basically paying out on par or worse than AdMob ads with way worse fill rates, you may not want to have iAds be your highest priority network. This is now possible.

##ARC
`LARSAdController` 3.0 is now ARC-enabled. You're welcome.

##Presentation Types & Pinning Location
In 2.0 and prior versions, you were limited to only displaying the ad at the bottom of your view and having it slide from the bottom as your animation. Two new properties have been added on `LARSAdController`: `presentationType` and `pinningLocation`.

###presentationType
You now have the option to change the animation sliding direction of your ads when a new ad is loaded and animated on screen. You can have it slide in from any edge. I thought about adding different transition types (flip, etc.), but thought it really unnecessary unless this is something other's think they want.

###pinningLocation
You also now have the option to change the physical location that the ad container lives on screen. In addition to the good ol' bottom of your view, you can pin the ad to the top of the view. This is not revolutionary.

##A View Controller Subclass
I've created a new view controller subclass that is the _easiest_ method to integrating ads into your view controller's view. For simple configurations, simply having your view controller inherit from `TOLAdViewController` will display an ad on `viewDidLoad:`, just like is suggested in the setup instructions. Literally no other work is needed from you in the simplest case.

###Conditionally Display Ads
If you need to conditionally display ads while using the view controller subclass, overriding `shouldDisplayAds` will enable you to add some logic to determine if the subclass should add the ad container to your view or not. If you return `NO`, then nothing else is performed and no ad is added to your view hierarchy. The base implementation always returns `YES`.

I am dogfooding this view controller to myself in Droid Light, so it works for more than just the trivial implementations.

##Debug Logging
I've added pretty extensive debug logging for what's currently constructed. If something goes wrong, you should be able to tell _what_ is going on and on what line in what file. Runtime warnings are unconditionally displayed regardless of debug log state and should probably be taken care of.

To enable logs, simple define the `LARSADCONTROLLER_DEBUG` macro somewhere.

#Help?
I primarily created `LARSAdController` because I was unsatisfied with the big ad mediation networks. They also felt pretty heavyweight and a lot of overhead to simply run two mediated ad networks on my low-traffic app[^1]. This is my simple solution to theirs.

This post is primarily asking for a handful of people to clone, implement, and just see if they see anything awry with their particular implementation needs. Detailed code samples and changes are on the README on the project repo's branch, [AdapterAbstraction](https://github.com/larsacus/LARSAdController/tree/AdapterAbstraction).

You can find the [3.0 branch here](https://github.com/larsacus/LARSAdController/tree/AdapterAbstraction) and file [issues here](https://github.com/larsacus/LARSAdController/issues). Don't be shy about issues. If it's not a bug, but simply something you didn't expect and want clarification, file an issue.

If you're so inclined, let me know what you think: [@theonlylars](https://twitter.com/theonlylars)

---
[^1]: That and I _hate_ when 3rd party libraries make me use the `-all_load` compiler flag