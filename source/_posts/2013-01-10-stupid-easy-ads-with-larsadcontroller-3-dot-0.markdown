---
layout: post
title: "Stupid Easy iOS Ad Mediation with LARSAdController 3.0"
date: 2013-01-10 20:10
comments: true
published: true
categories: iOS development ads open-source LARSAdController
keywords: "iAd,iOS,iOS development,AdMob,iAd Integration,easy,LARSAdController,ad mediation,iOS mediation,iOS house ads"
description: "LARSAdController, the easiest open-source ad mediation platform, has been upgraded to version 3.0 - this updated tutorial will show you how to easily integrate your iOS app with multiple mediated ad networks in any order including iAds and Google Ads (AdMob ads)."
---

##Roots
When I originally built LARSAdController, I was building it to fulfill a very specific purpose for my first app (Droid Light): leverage a singleton to serve a single ad instance for iAds using best practices, and serve AdMob ads as a backup when iAds (stellar) inventory ran dry. Since then, I've expanded it to support multiple devices, as well as multiple orientations, all while being super-easy to integrate.

##3.0
Version 3.0 now supports all of the things that the old version did, but now allows you much more flexibility and freedom in how you would like your ads to be used.

###Major Changes from v2.x

A detailed changelog of what has changed can be found in the [LARSAdController 3.0 release-candidate post](/blog/2012/12/15/larsadcontroller-3-dot-0-rc/), but here's the `tl;dr` version:

- `TOL` as a new class prefix
- Arbitrary ad networks
- Ad network priority
- Conversion to ARC
- Presentation types
- Pinning locations (have ad reside at the top of your view)
- Easy setup with a new view controller subclass `TOLAdViewController`
- Optional verbose debug logging for development
- Upgrade base iOS dependency to iOS 5.0

##Easy Integration
So let's get started integrating your ad with some ad networks. If you would like to use Google as your ad vendor, then you will need to go ahead and setup an account with them and have your publisher ID handy.

This is going to be so stupid simple it's going to blow your mind[^1].<!-- more -->

###Integrate the Source
This is by far the most difficult part of the entire process if you don't use cocoapods. I highly suggest using cocoapods, so I'm going to go over that first. All of these instructions are mirrored on the `README` file on [LARSAdController's github page](https://github.com/larsacus/LARSAdController).

If you don't have cocoapods setup, visit [cocoapods.org](http://cocoapods.org) for installation instructions. I promise, it's really easy, too. Then come back here.

####Cocoapods
To include the LARSAdController source with your project, you will need to include the LARSAdController dependency in what is known as a "Podfile". In your project's root directory, you will need to create a new empty text file named `Podfile`.

#####Podfile Setup
Inside `Podfile`, you will need to declare your project's platform and base deployment target. LARSAdController only supports base deployment targets of 5.0 and above. To do this, add this as the first line:

`platform :ios, '5.0'`

This tells cocoapods that our platform is iOS and that our deployment target is iOS 5.0 and above.

In order to tell cocoapods to install LARSAdController, simply specify that you would like your project to include LARSAdController as a "dependency":

`pod 'LARSAdController', '~>3.0'`

This will install the latest v3 code including every minor revision until v4.0 (3.1, 3.2, etc)[^2].

Your podfile should now look like this:  
{% codeblock lang:objc %}
platform :ios, '5.0'

pod 'LARSAdController', '~>3.0'
{% endcodeblock%}

This will setup your project for both Google ads and iAds.

#####Components
If you are only interested in iAds *or* Google Ads, then you *can* (but you don't have to) specify a specific component of LARSAdController in your podfile instead of the above:

For only GoogleAds: `pod 'LARSAdController/GoogleAds', '~>3.0'`

For only iAds: `pod 'LARSAdController/iAds', '~>3.0'`

If you're only interested in iAds, then this is a great way to avoid the bloat of the Google ads SDK in your project when you don't even need it. Including both lines would be functionally the same as simply using `pod 'LARSAdController', '~>3.0'` in your project.

#####Install
From here, save your podfile, and in terminal, run `pod install` to kickoff the installation. Cocoapods creates a new `xcworkspace` file with your pods already downloaded and configured for your project to use without you having to manage any of the dependencies.

You must now open the `<your_app>.xcworkspace` file to use LARSAdController.

Done. [Click here](#decide-where) to skip the manual process instructions since you don't need them.

####Manually
If you're not using cocoapods, in order to use `LARSAdController`, you will need to clone this repo and add the `Source/` directory to your project, as well as add the required frameworks - all by hand.

...like a caveman

#####Git Submodule
You can do this the old way by simply cloning the repository and adding the files to your project as a git submodule as shown below:

`git add submodule <third_party_folder> https://github.com/larsacus/LARSAdController.git --recursive`

Where `<third_party_folder>` is the folder where all of your third party code lives in your project file structure.

##### Framework Requirements
In order to compile, you will need to include the following Apple frameworks:

######iAds

  1. `iAd.framework`
  2. `AdSupport.framework` (weak-link for iOS 6)
  
######Google Ads

  1. `StoreKit.framework`
  2. `AudioToolbox.framework`
  3. `MessageUI.framework`
  4. `SystemConfiguration.framework`
  5. `CoreGraphics.framework`
  6. `AdSupport.framework` (weak-link for iOS 6)

You will also need the `Google AdMob SDK` available from [Google](https://developers.google.com/mobile-ads-sdk/download#downloadios). If you are using the `--recursive` call above, then a submodule of the googleads sdk will be cloned to the `Example/AdMob` folder.

###Decide Where
Decide where you want your ads to live. Try and stay away from trying to use them anywhere that the ad is going to be moving around (like in a table view cell). Publishers hate this and it could result in false impressions and possibly low revenues.

You have the option of placing your ad at the top or bottom of a view. By default, LARSAdController will place your ad at the bottom of a given view. Specify if you want your ad at the top of the view by calling the following somewhere in your app delegate (like in `applicationDidFinishLaunching:`):

`[[LARSAdController sharedManager] setPinningLocation:LARSAdControllerPinLocationTop];`

If you would like to dynamically change the pinning location depending on view controller, then place this call in `viewDidLoad:` of the view controller you would like to change and where you would like the ad to be.

###Register Ad Networks
You will need to specify which ad networks you would like to use and in which order they should display. Calling the below code will register LARSAdController with the iAd network as a primary network. You will be calling this sometime before your first ad is requested, which we'll go over next.

`[[LARSAdController sharedManager] registerAdClass:[TOLAdAdapteriAds class]];`

To add Google ads as a secondary network to iAds, then call the following sometime _after_ the above call:

`[[LARSAdController sharedManager] registerAdClass:[TOLAdAdapterGoogleAds class]];`

If you would like the reverse - to have Google ads served before iAds, then simply reverse the order in which the ad networks were registered.

###Add Banner to View
To install the ad banner in each of your views (view controllers), call

`[[LARSAdController sharedManager] addAdNetworkToViewInViewController:self];`.

I've found the best place to call this method is in your view controller's `viewWillAppear:` method.

####Even Simpler
Guess what? Integrating ads can be even easier than above. If you have each of your view controllers inherit from the base class `TOLAdViewController`, ads will automatically installed in your view on `viewWillAppear:`:

{% codeblock lang:objc %}
@interface MYBestViewControllerEvar : TOLAdViewController
//BOOM - Ads
@end
{% endcodeblock %}

I've even included a method you can override in your view controller to conditionally display these ads. This is convenient when you don't want to display ads under certain conditions (like when a user purchases an in-app upgrade):

{% codeblock lang:objc %}
- (BOOL)shouldDisplayAds{
  return ([self.purchaseTracker hasPurchasedUpgrade] == NO);
}
{% endcodeblock %}

##Test
Make sure you test that everything is working correctly in your particular app before you ship to the app store. [Submit an issue on github](https://github.com/larsacus/LARSAdController/issues) if something feels wrong.

##Done
Easy. Total lines of code I've counted for a standard integration of two ad networks in a single view controller is two if you use the view controller subclass. Two lines of code to have ads properly served in your app. If you use cocoapods, you can go from nothing to proper ads in less than a minute.

[LARSAdController](https://github.com/larsacus/LARSAdController) v3.0 is released under the very liberal MIT license on github. If you're using LARSAdController in your app, I'd love to know. You can send me an email if you can find it, [fork the repo](http://github.com/larsacus/LARSAdController) and change the readme on GitHub, or even better, just send me a quick note at [@theonlylars](http://twitter.com/theonlylars) and I'll add it to the bottom of the README file.

If you find an bug or have a suggestion, feel free to [submit a new issue on github](https://github.com/larsacus/LARSAdController/issues) *with reproduction information and preferably source code* as well as a description of what you're trying to accomplish if it even *feels* like it's out of the normal use case.

---

[^1]: I take no responsibility for the actual rupturing of the cranium, inter-cranial blood vessels, or ocular regions as a result of reading this. Any reading from this point on is purely at your own risk. 

[^2]: Since cocoapods takes largely from the ruby community, the `~>` operator will function exactly like it does there. You can read more about this "pessimistic operator" on the [rubygems site here](http://docs.rubygems.org/read/chapter/16#page74).
