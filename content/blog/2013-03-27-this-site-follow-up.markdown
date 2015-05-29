---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "This Site: Follow-Up"
date: 2013-03-27
comments: true
tags: SaaS
slug: "this-site-follow-up"
---

Following up to the original [post](/blog/2013/02/28/what-this-site-runs-on/ "What This Site Runs On").

I've been running this site for over a month now and have a full AWS billing cycle report I can share.
<!--more-->
    Rate: $0.095 per GB - first 1 TB / month of storage used
    Used: 0.179 GB-Mo
    Cost: $0.02

    Rate: $0.01 per 1,000 PUT, COPY, POST, or LIST requests
    Used: 1,453 Requests
    Cost: $0.01

    Rate: $0.01 per 10,000 GET and all other requests
    Used: 32,989 Requests
    Cost: $0.03

For a grand total of 6 cents a month to run this site.

Granted, I don't exactly have a popular blog. According to Google Analytics, there have been 164 page views in the last 30 days.

CloudFlare suggests different numbers and they claim to be more accurate because the requests actually go through there service instead of depending on JavaScript snippet execution. CF says 721 page views with 229 of them from crawlers. CloudFlare also offers the details about what the service saved you in bandwidth consumption.

    23,703 requests saved by CloudFlare
    47,068 total requests

    930.6 MB bandwidth saved by CloudFlare
    1.9 GB total bandwidth

So, in theory, CloudFlare saved me $0.02 in additional GET requests or 33%. In this case, their service actually saves a lot more because they host one DNS zone for free while Amazon charges $0.50/month + queries.

## Availability
I was surprised to find that [Pingdom](http://stats.pingdom.com/4jx3ikbj055t/782118 "Blog Hunt Public Status Page") is reporting 16 minutes of downtime in the last 30 days. All of the failures were encountered from European POPs. Availability in the US has been 100%.

Of course, for six cents a month, it's hard to complain about 3-nines of availability.

## Performance
Analytics reports 0.00 seconds for every page load. I'm not sure if that's because there's not enough data or CloudFlare caching causes problems with that part of Google's JavaScript.

Pingdom reports an average response time of 585ms. I was expecting closer to 100ms given that it's static content on some of the largest distributed computing platforms available today, but again I can't complain. It may be that CloudFlare just doesn't cache much of the site since the page view frequency is so low.

## Final Thoughts
Yes, there are free options, but this has been a fun experiment. Now I am trying to learn the ways of SEO and build an audience. I would like to see how this platform scales and how the cost scales with it.

I keep looking for free or very cheap solutions to take advantage of to add value to either the reader or content manager.

And, of course, I try to come up with useful content to publish as well.