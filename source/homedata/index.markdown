---
layout: page
title: "Welcome to My Place - It's Hot in Here"
date: 2012-06-03 21:42
comments: true
categories: home sensor data sensor-data arduino temperature a/c air-conditioning cosm pachube
keywords: "ladera-vista,reviews,home,apartment,austin,apartments,a/c,air-conditioning,repairs"
---

This page will summarize the status of all systems in my apartment. Server status, a/c status, whatever. More stuff seems to be added every week.  For now, it's just home comfort status as I get my other systems up and monitored. I'm currently monitoring temperature and humidity in two different places (bedroom ambient and bedroom HVAC exhaust) that I relay up to [Cosm](https://cosm.com/feeds/47043).  I initially did this because our apartment's ([Ladera Vista](http://www.laderavista.net/), for the curious - also so I can bump this page up in search results) air has never really been able to keep our apartment at a temperature we were comfortable with but could never prove it. So now we can:

{% img https://api.cosm.com/v2/feeds/47043/datastreams/0.png?width=730&height=250&colour=%23f15a24&duration=1day&title=Bedroom%20Ambient%20(24%20hr)&show_axis_labels=true&detailed_grid=true&scale=manual&min=70&max=80&timezone=UTC %}

Notice how in this feed, the A/C unit doesn't actually ever shut off - also note the temperature differential between ambient and actual register temp.  It should get extremely cold (14°-21°F lower than ambient) for a short period of time, then shut off for an even longer time, then repeat. But as you can see here, this is not the case - it will get mildly cold (~10°F cooler than ambient), then comically try and cool off our apartment, which it fails at, because you can see it run for forever without cycling:

{% img https://api.cosm.com/v2/feeds/47043/datastreams/2.png?width=730&height=250&colour=%23f15a24&duration=1day&title=Bedroom%20HVAC%20Vent%20(24%20hr)&show_axis_labels=true&detailed_grid=true&scale=manual&min=50&max=80&timezone=UTC %}

{% pullquote %}If you're reading this going "{"Hey, man, it's not that warm in your apartment! I can see the graph only goes up to 78°F on any given day!"}".  Well, Mr. Graph Reader, you are correct. However, these are the bedroom temperatures. The bedroom is host to the only vent in the house that actually blows anything. This vent will blow probably 75% of the ft³/min that the entire apartment will see.  The wussy vent in the front of the apartment that is supposed to service the entire other 500ft² (out of 800) will blow way less than half the ft³/min that the bedroom vent blows, making the temperature differential between the bedroom and the living room 10°F or more depending on the outside temperature (which last summer was 110°F). It's simply unbearable to be in the living room during a decently warm (>85°F) day, which is very often in Texas. {% endpullquote %}

And for those curious, we do notify the apt staff. They'll come by and look at stuff, but in general really don't care or understand how hot it is in here.