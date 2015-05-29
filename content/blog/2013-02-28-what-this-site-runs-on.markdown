---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "What this site runs on"
date: 2013-02-28
comments: true
tags: SaaS
slug: "what-this-site-runs-on"
---
I've wanted to share some of my my experiences working in a Microsoft based SaaS environment and thought about blogging before, but I'm not really a writer. I finally started this site as an experiment to try technologies that I don't normally get to try in my day to day professional experience.
<!--more-->
The site has been up for a few days, no visitors, but maybe someone will read this someday.

Here's a description of what I've used to host this site for a just couple bucks a month while still getting approaching 100% up-time (in theory).

## Blog Framework
Tumbler, Wordpress, Blogger - all good options for hosting reverse chronological short-form writing, but none of them are interesting to implement from a technologists point of view.

### Octopress
On the other hand, [Octopress](http://octopress.org/) is exactly the type of blogging framework an engineer would like. The site tag line is "A blogging framework for hackers." Octopress is a wrapper for Jekyll which is the foundation of [Github Pages](http://pages.github.com/).

A basic Octopress site is incredibly simple to get running and produces a static HTML5 compliment site. I say simple, with the assumption you have the requisite version of [Ruby installer](http://rubyinstaller.org/). You'll also need the [Ruby DevKit](https://github.com/oneclick/rubyinstaller/wiki/Development-Kit) which has a lot more instructions than the Ruby Installer, but was actually very easy to set up as well. You don't need to know or manipulate Ruby to build or maintain an Octopress site, but you will if you want to make advanced changes to the build process. So far, I've made no changes to any .rb or .rake files.

Since Octopress generates static HTML, hosting options are pretty much unlimited. I already had Web Site Hosting configured on Amazon S3 with a simple page that redirected to my LinkedIn profile. I simply uploaded the new content and had a working site.

I've made some changes to the styles and templates, but it is largely unchanged from what you can grab from Github. All of the 3rd party integrations are already built in and just need a couple lines of configuration to enable.

### Amazon
I am using Amazon S3 to host the static files. The consumption based pricing is a great fit for a blog that no one reads, but you hope will be super popular some day. It costs pennies a month to host the 600kB making up the site. Outbound bandwidth will be free up to thousands of visitors a month at which point it will still probably cost pennies. Even if somehow I ended up with hundreds of thousands of visitors in an unexpected spike, I'm not worried about bandwidth costs; more about that later.

S3 offers aims for eleven nines of object durability and four nines of availability. That's a pretty good SLA for a service costing me less than a gallon of milk.

### CloudFlare
If you haven't heard of them before, [CloudFlare](http://www.cloudflare.com) is a Content Delivery Network (CDN) with a lot of features that go above and beyond what CDN's normally offer.

First off, they have a free tier which includes all of the basic CDN functionality. And what makes CloudFlare's free tier really fantastic for the small blogger, "CloudFlare will never bill you for bandwidth usage. We believe if your site suddenly gets popular or suffers an attack, you shouldn't have to dread your bandwidth bill." Since CloudFlare caches the site and hosts it on their global network, even if the site somehow got slash-dotted, it will instantly scale to serve those connections without consuming S3 resources and increasing my AWS bill.

CloudFlare can also dynamically optimize your site - minimize JS and CSS; Gzip content; optimize images - which make the site load quicker for the end user without having to engineer those optimizations. Eric Wendelin wrote a good article on [optimizing Ocotpress sites for S3](http://www.eriwen.com/performance/make-octopress-fast/ "Making Octopress Fast"), but all of that effort is automagically taken care of just by using CloudFlare's free service. Since I don't have much content or visitors I don't have good page load time stats, but subjectively it seems very fast.

### Final Thoughts
I have no expectation of ever needing the scalibility that S3+CloutFlare provides, but for a technical experiment it is fascinating. Simple web site hosting used to be $20+/month with limits on storage and bandwidth and questionable ability to scale with dramatic fluctuations in load. Now, for a dollar, you can host a site with approaching 100% availability even in the event of 15 minutes of internet fame.

Also, even though my focus is on Windows based web technologies, there's nothing specific to Windows or Microsoft technology in this solution. However, because Ruby is now so mature and easy to install on Windows, I could use Octopress without any hacking.

## Update 3/27
[One month technology review](/blog/2013/03/27/this-site-follow-up/ "This Site: Follow Up")