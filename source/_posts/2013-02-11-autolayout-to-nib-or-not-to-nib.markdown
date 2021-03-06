---
layout: post
title: "Autolayout: To NIB or not to NIB"
date: 2013-02-11 19:39
comments: true
categories: iOS development autolayout
---
A coworker recently asked me at the end of last week what I thought, based on my experience on my last iOS 6-only project, would be the best approach to starting to learn and work with autolayout. He was specifically asking if he should bother working with xib files, or simply layout everything in code. He then asked that if he were to go with a xib file, what the best approach would be to balance the time/effort workflow for autolayout.

All of the below is 100% based on my experience on my last project that was iOS 6-only, where autolayout was extensively utilized.

##Autolayout Layout Construction Priority
Apple states in all of their documentation the following order you should begin implementing autolayout:

1. Nib file
2. Visual formatting language
3. Individual constraints

##To NIB
The priority above is, in my opinion, very dependent on what you are working on. If you have a pretty trivial layout that is simply being constrained based on something simple like an orientation change, then a xib file will probably be your best bet for a rapid layout that is unambiguous and doesn't give you any issue during maintenance with very little effort.

Another very helpful feature of autolayout's constraints is the fact that you can create an outlet to a single constraint. You can use this outlet to easily animate a change, change the constraints constant, or remove a constraint altogether without much additional effort. To animation a constraint change, simply update your constraint(s), call `-setNeedsLayout`, then call `-layoutIfNeeded` in an animation block. Very quick and easy.

###No Touching
[{% img right /images/no_touching_static.jpg 300 %}](/images/no_touching.gif) However, using xibs for autolayout starts to break down and become more time than it's worth when you get into complex layouts that depend on cross-view constraints, constraints depending on a view's content or complex constraint sets relating to many physically smaller views (the constraints simply become an absolute nightmare to *physically* click on and manage in the xib without screwing something else up).<!--more--> Couple this with the fact that a "feature" of auto layout nibs is to avoid ambiguous layouts. This means that Xcode will actually *add* constraints it "feels" are necessary for your layout despite you knowing better based on your semantic knowledge of the layout you're trying to build. These constraints are *not* removable and thus, a huge nuisance.

Even if you do end up successfully laying out your complex auto-layout layout in a nib and get it to behave the way you want, it's best to treat your xib file like George Bluth in prison and *never* touch that nib again. Any constraint that is touched could potentially be changed by Xcode because it thinks it knows better than you. You'll then be back at square one trying to figure out which constraints' priorities to change back to 1 to get your layout to behave the way you want. I hope you committed that xib into source control just now.

##Not to NIB
It's at this point that writing your layouts using the visual formatting language[^1] becomes quicker and less error-prone, even at the expense of having to do it in code. A layout you build in code is the same layout *every* time the code is run. It's like this because you built it. Only you know the semantics of the layout you are trying to construct. The code is not going to do anything you don't tell it to - you just need to practice and learn to get better at telling it what to do. Use the visual formatting language and you'll eventually make it out alive with a rock-solid layout that is much easier to maintain than the same complex layout in you would have had in your nib file.

##So What Should I Use?
Some general thoughts to consider when learning to work with autolayout:

###Always Use NIBs When Possible
Don't let interface builder get the best of you. At the first sign of interface builder throwing a fit with your layout and changing things from under your feet - switch to code. Autolayout really is hard, but interface builder sometimes makes it unnecessarily so. Always use a xib when you can. Even if you can only use the nib for a part of the layout, do the complex layout in code and leave the simple layout to the xib if you so choose. The xib is supposed to make your life easier, not more difficult.

###Use the VFL
Use autolayout's visual formatting language at the first sign of complexity or resistance from interface builder that is impeding your productivity.

###Hybrid Approach
For complex layouts, simply omit that view heirarchy from the nib if it's only a subset of the nib.

###Don't Use Autolayout
Sometimes you really may not need autolayout. Sometimes the simplest approach is the correct one.

###Autolayout is Hard
Yes, autolayout is hard. You'll often find me comparing the shape of autolayout's learning curve to that of the face of a cliff. I took it on myself to try and learn as much as I could about autolayout when I got back from WWDC last year. I was then fortunate to be one of a few people to actually get to use iOS 6 APIs in a production environment with a project at Mutual Mobile before iOS 6 was even out of beta. It is still the number one thing people come to me asking questions about at work - for good reason.

Many blog posts have been written by other developers on the nuances of autolayout. Bottom line - autolayout is really hard. It is the only API I can think of that is more closely related to an art than science. One that is only mastered with practice and many hours diagnosing ambiguous and over-constrained layouts in the debugger. The only true advice I could give another is this: "Autolayout is hard. Practice often if you care to learn it."

[^1]: This includes the [individual constraint APIs](https://developer.apple.com/library/mac/#documentation/AppKit/Reference/NSLayoutConstraint_Class/NSLayoutConstraint/NSLayoutConstraint.html#//apple_ref/occ/clm/NSLayoutConstraint/constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:)
