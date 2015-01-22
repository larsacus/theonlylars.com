---
layout: post
title: "Rapid Content Prototyping With PlaceKit"
date: 2013-07-01 08:00
comments: true
categories: open-source iOS
description: "PlaceKit is a central, lightweight, dependency-less library for all of your content, placeholder, test data and random numerical needs that you might run into during prototyping or in the early phases of production development."
---

Last week I open-sourced [MMProgressHUD](https://github.com/mutualmobile/MMProgressHUD), a small work-project that I've been working on for what seems like an eternity. I alluded in my last post that I was also releasing a personal project later in the week. I went ahead and open-sourced that project which I've been calling "[PlaceKit](https://github.com/larsacus/PlaceKit)" for the Objective-C "[Back On the Map](https://objectivechackathon.appspot.com)" hackathon this past Saturday.

##What is PlaceKit?
Think of PlaceKit as a central, lightweight, dependency-less library for all of your content, placeholder, test data and random numerical needs that you might run into during prototyping or in the early phases of production development.

Let's go through a couple of scenarios that you might find yourself needing PlaceKit:

1. You want to try something out fast, but need some content to show how it might look with real text or images
2. You want to create a demo application for some particular widget you are working on, and need a set of fake data to display. Ditto for needing randomized data and geometry for creating random views or random offsets.
3. You are working on a production app, but the server API is not ready yet, so you now have to come up with a large dataset of fake data to populate a fake database that you can use in your application until it is ready.

These are the primary scenarios I built PlaceKit to make easier. We all know about using lorem ipsum, but when we need it, we typically will open up our favorite browser, google "lorem ipsum generator" and then copy/paste the text into the appropriate place we would like it to show up. This is all well and great, but it really isn't scalable, isn't randomized and also won't simulate fetching from the network. This scenario is also true for images.
<!-- more -->

##What Does it Do?
It basically saves you time. If you don't like to save time, then feel free to not use it. It will also add a little more life into your app in the early stages of development or when you're just trying something new out and need some basic data to get you by. PlaceKit can also add a little variety into your placeholders - developers all get bored occasionally. If you want the generic grey image placeholders, PlaceKit has those. If you would to motivate your designers to get you assets with hideous images of meat- or kitten-based pixels, then PlaceKit also has that.

My favorite features of PlaceKit are the geometrical and random numerical APIs: These APIs will give you easy access to random geometries like random rects, random points or a random point enclosed in a given rect. The numerical APIs will give you incredibly easy access to random numerical values such as random percentages, random float in a given range, or random phone numbers.

If you have AFNetworking already integrated into your project, or simply want the ease of a built-in category on UIImageView, PlaceKit also has a wrapper around the UIImageView+AFNetworking category for the image APIs. This will enable you to easily and asynchronously request placeholder images of a given type without the need for the block-based APIs.

##Interested?
If you are interested in the above, have suggestions on how to improve PlaceKit or simply just want to peek under the hood, you can check out the source available on GitHub [here](https://github.com/larsacus/PlaceKit). Full information on what PlaceKit is capable of is fully documented in the header files [here](https://github.com/larsacus/PlaceKit/blob/master/PlaceKit/PlaceKit.h).
