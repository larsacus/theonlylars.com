---
layout: post
title: "Minty Boost! DubDub Edition"
date: 2012-06-04 23:46
comments: true
categories: wwdc electronics gadgets
keywords: "mintyboost,electronics,gadgets,wwdc"
---

Of all of the lists I have read about what to bring with you to WWDC, or suggestions on what veterans say you should do, the most consistent and unanimously present suggestion from all sources is that you will need to extend the battery life of your mobile device.  In the case of WWDC, there is a 99.99 (repeating, of course)% chance that it will be an iPhone. Being a procrastinator, I did not order one online like I should have from monoprice or similar.  Being a cheap, stingy software engineer, I refused to pay between $60-$100 for similar Li-Ion battery packs that carry anywhere from 3000-6500mAh of power. Being an electrical engineer by schooling, you tend to have [stuff like this lying around the house](http://www.ladyada.net/make/mintyboost/). Specifically, I had the minty boost powering an Arduino/Xbee assembly that is feeding temperature data from my HVAC exaust to Cosm.  You can read all about _that_ [here](/homedata). It ultimately didn't work for this application due to it's limited supply time (only lasted about 6 hours at a time).

{% img center /images/posts/mintyboost/minty-boost-1.jpg %} This will enable me to use two rechargeable AA batteries at a time to at least get my iDevice back on it's feet. I don't intend for this to get me to a full charge, but it should suffice to get me to the next wall outlet.

Also, since Altoids does not make the Altoids gum that the Minty Boost was originally designed for, I had to improvise using some engineering magic:

{% img center /images/posts/mintyboost/minty-boost-2.jpg %}
This Eclipse mints container works perfectly for the Minty Boost, even if I did have to reverse the lid to get it to fit.

According to the battery rating, as well as the specs on the [iPhone's battery](http://www.ifixit.com/Teardown/iPhone-4-Teardown/3130/1#s15307) and [Adafruit's own website](http://www.ladyada.net/make/mintyboost/power.html), I should be able to get about half a charge on two high-capacity NiMH AA batteries (the green Energizers shown above):

Input: `2 * 1.2V * 2450mAh * 80% = 4704 mWh`

Output: `4704mWh/5V * 80% = 753 mAh`

Charges: `1420 mAh iPhone 4 Capacity / 753 mAh = ~1/2` charge of iPhone 4 on two fresh AA rechargeable batteries

Good enough, I say.