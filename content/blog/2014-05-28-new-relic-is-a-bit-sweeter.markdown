---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "New Relic is a Bit Sweeter"
date: 2014-05-28
comments: true
tags: [tools, projects, devops, automation]
slug: "new-relic-is-a-bit-sweeter"
---
I've been working with New Relic a lot in recent weeks and have deployed their agent several times. Obviously, this required some automation and the best way to automate software installs on Windows is [Chocolatey](https://chocolatey.org). Unfortunately, New Relic didn't publish their agents to Chocolatey and the existing packages were out of date.

I brought up the request on the user forum and New Relic expressed interested, but also said, "feel free to give it a try yourself." So, I did.
<!--more-->
Here is the .Net agent.

[https://chocolatey.org/packages/NewRelic.NetAgent](https://chocolatey.org/packages/NewRelic.NetAgent "NewRelic .Net Agent")


    C:\> cinst NewRelic.NetAgent


And here is the Server Monitor.

[https://chocolatey.org/packages/NewRelicWindowsServerAgent](https://chocolatey.org/packages/NewRelicWindowsServerAgent "New Relic Servers (Windows)")


    C:\> cinst NewRelicWindowsServerAgent


Both of these run quiet installs and will not prompt for your New Relic license. You'll have to put that in the config files yourself. I'm sure I'll be working on automating that bit next as well.

Feel free to bug me if you find these packages are out of date.