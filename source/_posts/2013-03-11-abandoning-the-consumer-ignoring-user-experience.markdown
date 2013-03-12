---
layout: post
title: "Abandoning the Consumer: Ignoring User Experience"
date: 2013-03-11 16:36
comments: true
categories: user-experience
---

*Fair Warning: This post is relatively [SimCity](https://www.google.com/search?hl=en&tbm=nws&q=simcity)-centric.*

The title includes some strong words, yes - but you do not want your users even *feeling* forgotten about. In my line of work, the user is a very central part of the development cycle. This is true in part to the intimate nature of the software I write. My software travels with the user. Everywhere the user goes - my software is living in their pocket. In fact, not only is it always with the user, but the user may also be using my software in a specific environment. How the user uses and interfaces with my software is what is known as the *user experience*. It has nothing to do with what the UI looks like, the shadow angle of the button when pressed or the 'flat' nature of the design.

##The User's Experience as a Requirement
In today's market, the user is becoming ever more selective about the applications that they use and is raising the standards to which they will use when selecting their software using. Some of us have very specific quips about what they consider a "deal-breaker". For me, this could be something like not making your app iPhone 5 enabled when it supports iOS 5.0+ or when you still don't have retina assets for your app. This is inexcusable. I have heard others say that they hate when apps hijack the status bar for their own temporary in-app status. I personally love how that looks.

The above is said to emphasize that consumers care now more than ever about how their software both looks, feels, behaves, and how they can use it. If your users feel confused, nickel-and-dimed, or find using your software in any way difficult, they will most likely *not use it anymore*. Moreover, they will not tell their friends about it. If you screw up badly enough, they will actively tell their friends to avoid your software altogether.<!-- more -->

So taking this into consideration for your product, your business goals should be (in this order):

1. Make the user's life as easy as possible (UX)
2. Look good doing it (UI design)
2. Make money ($) as a result of 1 & 2

Master these and you end up with companies like Apple, who are *so* intently focused on the user's experience, they have a team that does nothing but [design and test the box for the "best" experience when opening your new iPhone](http://www.networkworld.com/community/node/79642).

##The Game Industry
The game industry is an incredibly complex industry. I don't pretend to be a game developer, publisher or in any way know the business ends of the gaming industry. However, I *am* a PC gamer and a software engineer in the business of pleasing my users. I have been a PC gamer since the DOS days, and I'm really not that old. I bring this up to say that I believe the game industry seems to be rapidly devolving when speaking in terms of the user's experience. They do all of this in the name of "piracy"[^1].

###DRM
The specific example of user-experience gone awry in the game industry is with DRM (digital rights management). This literally has nothing to do with any game individually and has everything to do with access control to a specific resource. The idea behind DRM being that the user is given an obstacle course of hoops and conditions to satisfy before they are given access to the resource (the game) they paid for. This is to prevent people who have *not* paid for the game from using the resource.

Some companies make DRM extremely transparent and pain-free. Steam is a company I will throw money at all day (happily, I might add) to buy games from because I *feel* that they are truly trying to make my experience playing games as easy as possible. DRM keys are automatically stored for me and prompted at me when I first launch a game. Even then, most of the time, I don't even need to do anything additional to play my games except download and launch them.

Others actively go out of their way to ensure that the minimum number of people will pirate the game. This comes at the direct expense of all of their paying customers. In the same fashion that you have to sit through three un-skippable FBI warnings before watching a movie that you just bought, gamers have to satisfy all of the DRM requirements of their games before they can play them. In the specific example of SimCity, they must always be online and authenticated to the internet to play what has (until now) always been a single-player centric game.

##EA and SimCity
*This is my personal account of my (user) experience with EA and it's brand-new "blockbuster", SimCity.*

I have been really excited about SimCity for a long time, so I preordered it. The only place it is available to play is directly through EA's subpar Origin client. I paid for the "digital" version on Origin. I later found out that they [do not offer refunds](https://help.ea.com/article/returns-and-cancellations) for digitally downloaded products and in fact [will ban your account](http://www.cinemablend.com/games/EA-Officially-Halts-SimCity-Refunds-Polygon-Drops-Review-Score-Even-Lower-53444.html) if you follow through with a chargeback - it's just their policy. You can, however, get a refund from Amazon if you buy it there. On launch day I started the download before work and then came home and was going to fire it up to play it. This was just the beginning.

###Initial User Experience
On first launch I was greeted with this lovely screen. {% img right /images/simcity1.jpg 300 300 %}A list of servers that I was not allowed to get on. Getting on a server is a prerequisite to playing the game since the SimCity servers do a couple of things:

1. Authenticate that you paid for the game
2. Store everything in the cloud all of the time
3. Offload part of the computational simulation to their servers so it's impossible to play offline

The first is simply annoying and is done every time you launch the game and continuously from then on. The second should be in addition to storing your data locally. The third [should have been killed off](http://www.rockpapershotgun.com/2013/03/11/simcity-is-inherently-broken-lets-not-let-this-go/) during product discovery for the game mechanics 6 years ago before any code was written.

I personally ran into just about every issue that I heard others talk about online:

1. Could not authenticate with the server
2. Could not load into the tutorial city
3. Once I loaded the tutorial city, the tutorial is evidently loaded from their servers, so the city was unusable since every button was disabled, however the music was very nice. The entire game was basically useless
4. Claiming new cities was not available, since even claiming a "private" region is done online
5. Second day - Was able to claim a new region and play for a while - tutorial still useless. Some crashes and hit-or-miss (mostly miss) loading.
6. Third day - Was able to play mostly, but now with all of the [features that had been removed in the latest update](http://www.joystiq.com/2013/03/07/non-critical-simcity-features-disabled-to-improve-server-issue/) (cheetah mode, achievements, some region filtering, etc.). What EA determined were "non-core" features.
7. [Simulation bugs galore](http://www.reddit.com/r/SimCity/comments/1a0taq/compiled_list_of_suggestions_and_bug_reports_to/)

###Spin Dr.
This experience was not isolated, which resulted in a ungodly amount of negative discussions on twitter. Last I checked, the [game's user-score on MetaCritic](http://www.metacritic.com/game/pc/simcity/user-reviews) was a paltry 1.6/10. The coveted "Critic" score is (currently) at [68/100](http://www.metacritic.com/game/pc/simcity/critic-reviews). Amazon even halted digital sales of the game and put up a "notice" to users buying the game of server issues that would prevent users from playing the game altogether. Polygon has even gone so far as to [amend their review of the game](http://www.polygon.com/game/simcity-2013/2630#review_update_3840603) several times and now sits at a 4/10 down from a pre-launch 9/10. EA has even actively told it's marketing partners to [halt all promotion for SimCity](http://www.ign.com/articles/2013/03/08/simcity-marketing-pulled-by-ea) altogether. It was also rumored [EA was paying hundreds of Chinese spammers](http://www.p4rgaming.com/?p=1473) to write positively about the game online.

I could go on, but it's really not necessary. Consumer trust and general distaste for anything your company produces is a difficult thing to recover from: "Hey have you heard of that new game EA is putting out?" "Yeah, it looked pretty cool, but do you remember what they did to the *last* one?" "Oh yeah, that sucks."

What happens in 10 years when EA decides continuing to run their servers is extremely cost prohibitive? I hope you like SimCity 2000.

##Users Don't Forget Being Bitten
EA and Maxis forgot one very important component of their game: the users. In an effort to maximize the number of people who would legitimately play the game, they made the experience so horrible and cumbersome for those who would pay legitimately for their product. Not only this, but the users are *forced* to play under a very specific set of circumstances, namely, always having a reliable internet connection.

I am curious to know what the dollar value is for the following two line-items on EA's financial balance sheet:

1. The cost of development & implementation of complex anti-piracy DRM measures and server infrastructure, as well as the cost of the loss of brand loyalty and trust to users as a result of poor user experience
2. The potential cost of losses to pirated copies of the game

If 1 < 2, then rest assured you'll be seeing much more of the above in the game industry in the years to come. As for me personally, I am going to actively go out of my way to not buy anything from EA as much as I can in the future. Or maybe they simply *assume* 1 < 2 and are just too big to care.

In the case of SimCity, the users *are* feeling forgotten about. They *will* lose your brand's trust when you take advantage of them.

##User Experience from the Top-Down
The user's experience shouldn't just be those that are designing a product's responsibility. You can have all of the most talented user-experience product gurus in the world, but if your management, marketing, and primary stakeholders (money people) don't understand this, the entire product's experience will reflect only those that the user remembers (the bad ones). The user experience, when left out, is what turns otherwise exceptional products into those that are at best mediocre.

Don't abandon the consumer. After all, they *are* the ones giving you their money.

*My personal recommendation for SimCity? Wait for the game to go on sale. While the game is moderately fun in it's current state, it's not as fun as it could be with all of the server issues worked out, not to mention all of the bugs and crippled features. Maybe by then we'll have an offline mode to play while on an airplane or bus.*

[^1]: Other industries do this, too, don't get me wrong