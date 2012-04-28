---
layout: post
title: "easily integrate your iOS app with iAd and AdMob"
date: 2012-04-27 21:30
comments: true
categories: iOS,development,iAd,AdMob,ads,open-source,ARC
description: "Easily integrate iAds into your apps with just two lines of code and back them with Google's AdMob ads."
keywords: "iAd,iOS,iOS development,AdMob,iAd Integration,easy,tutorial,iAd tutorial,iOS iAd tutorial,LARSAdController"
---
I think Apple's iAds had great potential.  But let's face it.  They really aren't pulling the weight they should.  When they do have inventory, however, they often make much more than any other ad service.  Let me show you how to not only quickly integrate iAds into your app, but do it properly all while using Google's AdMob service as a backup.

If you watch Apple's WWDC video on proper integration of iAd or read the iAd implementation documentation, you will see that they want you to use a single shared instance of your ads in all of your view controllers.  Since I also wanted that instance to be backed by Google ads when there was no iAd inventory, the best solution I could think of was the singleton.  This lead me to:

##Introducing [LARSAdController](https://github.com/larsacus/LARSAdController)
I built [LARSAdController](https://github.com/larsacus/LARSAdController) in order to be a one-stop, single line integration of iAds (and later AdMob ads) into a `UIView`. I initially only supported iPods/iPhones in portrait orientation. It now has support for both iPods/iPhones as well as iPads in *any* orientation.

##Integrating [LARSAdController](https://github.com/larsacus/LARSAdController)
1. First, you will need to clone the source from [Github](https://github.com/larsacus/LARSAdController):

    `$ git clone git@github.com:larsacus/LARSAdController.git`
    
    or add it as a git submodule:
    
    `$ git submodule add git@github.com:larsacus/LARSAdController.git Vendor/LARSAdController`

2. Add folder or `LARSAdController.m/h` to your project
3. Download and copy the [Google ad SDK](https://developers.google.com/mobile-ads-sdk/download#downloadios) into your project and go ahead and sign up for a Google publisher account at [AdMob.com](https://admob.com) if you haven't already
4. Ensure the proper frameworks have been added to your application in order for iAds and AdMob ads to compile properly:
    1. iAd.framework
    2. AudioToolbox.framework
    3. MessageUI.framework
    4. SystemConfiguration.framework
    
    You can add additional frameworks to your project in Xcode 4 by clicking on your project file in the project navigator on the left and going to `Build Phases > Link Binary With Libraries > Click + Symbol`

    Whew! What a lot of stupid setup and we haven't even written any code, yet! If you want to write code, you may be a little disappointed by this post, because there really isn't any.

5. Now go to `Product > Build` or type '⌘+B' to build the project.  If your project builds without any errors, hurray! Skip to step 7.  If not, continue on...
6. {% pullquote %}If you have errors and you followed the above *and* you're have ARC enabled, you will need to tell the compiler not to use ARC with [LARSAdController](https://github.com/larsacus/LARSAdController) as it is not supported, yet. Disable ARC on [LARSAdController](https://github.com/larsacus/LARSAdController) by following the same steps in step 4 to add frameworks to a project, but go to the `Compile Sources` section. Find `LARSAdController.m` in the list of files and double-click in the region to the right marked `Compiler Flags`. Add a new flag called `-fno-objc-arc`. A good way to remember this command is to read it like this: {"F, no, Objective-C, I don't want ARC on this file!"}.  Moving along... Hit '⌘+B' to verify everything is building correctly. {% endpullquote %}
7. *Now* we're ready to write some code! Get your typing fingers ready, because text doesn't just copy/paste itself! Add the following two lines of code to one of your view controller's view lifecycle load or appear methods.  I've found that `viewWillAppear:` seems to be the best when pushing/popping view controllers with ads on them:
    
  {% codeblock lang:objc %}
  - (void)viewWillAppear:(BOOL)animated{
      [[LARSAdController sharedManager] addAdContainerToView:self.view withParentViewController:self];
      [[LARSAdController sharedManager] setGoogleAdPublisherId:myPublisherId];
  }
  {% endcodeblock %}
  
  
  If you are only supporting a single orientation, or a single set of orientations (portrait/upside down portrait), then you're basically done.  Just add the first single line of code to any view controller that you want to host ads in. Just be sure to call `setGoogleAdPublisherId:` the first time you add LARSAdController to a view. The ads will automatically be added to the bottom of the view and appear/hide whenever they have inventory available.

##Multiple Orientations
  
If you would like to support multiple orientations, you will need to add the following line to the first call you make to add LARSAdController to a view:
``` objc
    [[LARSAdController sharedManager] setShouldHandleOrientationChanges:YES];
```

This will automatically listen for orientation changes and adjust the ad accordingly! See how easy that was! I told you if you were looking to type code, you were going to be disappointed.

Simply call the code in step 7 in any view you need the ad banner to be in, and it will automatically remove it from the old view and add it to the bottom of the new view.

## In Conclusion
I think this is an extremely easy, proper way just to implement iAds into your app, even if you aren't using AdMob ads.  If you're registered to use AdMob ads, then it's an even *more* efficient method, as it's only one line of code more in order to integrate and you will *always* have an ad displaying in your app.

If you're using [LARSAdController](https://github.com/larsacus/LARSAdController) in your app, I'd love to know. You can send me an email if you can find it, [fork the repo](http://github.com/larsacus/LARSAdController) and change the readme on GitHub, or even better, just send me a quick note at [@theonlylars](http://twitter.com/theonlylars) and I'll add it to the README file.
    
