---
layout: post
title: "iOS 6 \"Smart\" App Banners"
date: 2012-09-29 21:34
comments: true
categories: [Basic iOS,iOS,Development,iOS 6,html,web]
keywords: "iOS 6,iOS,iOS Smart Banners, iOS 6 Smart Banners, App Store Smart Banners, Apple Smart Banners, App Website Link, iOS Smart Banner Affiliate, App Store Affiliate, Octopress smart banners"
---

When iOS 6 was announced, one of the lesser features was a "Smart" App Banner that displayed unobtrusively over your webpage and displayed an app that users could directly download on the app store from your webpage.  After the keynote, I forgot to investigate the documentation about it all through the iOS 6 Beta and never really heard much about it until the GM release of iOS 6.  So I went to check out how to do this thinking it was going to be super-involved, at least html-wise.

Are you ready?

{% codeblock lang:html %}
<meta name="apple-itunes-app" content="app-id=379660205"/>
{% endcodeblock %}

That's it.  Add this meta tag to the `<head>` section of your website template. Then, replace your app ID with the one iTunes Connect displays for your app in the app details.
  
If you're on an iOS 6 device, then you will see this at the top of this webpage:

{% img center /images/posts/smartbanners/droidlightbanner.png %}

When the user taps on the banner, iOS will take the user to the app store to complete the transaction.  When the user returns to your website, a progress indicator will display showing the progress of the app install.  After the download and installation is complete, the banner's action will launch the app.
  
You can even go one or two steps further and pass in affiliate link information when your app gets downloaded or have information passed to your app on first launch in the app's url handling delegate method.  More detail can be found in the documentation on [Apple's website](http://developer.apple.com/library/ios/#Documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html).

If you're running an [Octopress](http://octopress.org) blog and aren't sure where to make this change to include it in your blog, mosey on over to `source\_includes\head.html` and add it in with the rest of the `<meta` tags. 

I've installed it on my blog with a link to [Droid Light](http://itunes.apple.com/us/app/droid-light-customizable-flashlight/id379660205?ls=1&mt=8), since that's the only personal app I have that actually moves any units on the app store.
