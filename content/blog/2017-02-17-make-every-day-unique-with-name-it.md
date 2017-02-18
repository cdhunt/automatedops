---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
comments: true
date: "2017-02-17T10:10:18-05:00"
sharing: true
tags: ["powershell", "project", "random"]
title: "Make every day unique with NameIt"
slug: "make-every-day-unique-with-name-it"
draft: false
---

# Introduction

This is half of a tandem blog posting about [NameIt](https://github.com/dfinke/NameIT).
You can catch the other half over at [Doug Finke's Blog](https://dfinke.github.io/#blog).
If you've landed here first, take a few minutes to go read Doug's post and get the full background on the history of this project.
Doug started [NameIt](https://github.com/dfinke/NameIT) as a simple module to generate random strings based on a template, like `???##`, which would give you three random characters and two digits.
Over the course of over a year and 60+ commits later, the module had grown to a fairly robust random data generator.

# Good Day Stupid Coat

I'm going to share a simple use case that helps me keep me straight when I get into the weeds of serious PowerShell development.
I often find I wind up with more than one PowerShell window open. Either because I'm remoting into one or more machine or just testing in a different session.
Sometimes I have a remote desktop open with PowerShell running with a local PowerShell session running as well.
With all of these similar looking Windows, I have on more than one occasion executed some code in the wrong window.
Sometimes, that just leads to confusion.
Sometimes, that leads to breaking things.
After working on some new functionality for NameIt, the idea occurred to me that I should customize my PowerShell sessions to make them distinct.

Like any good PowerShell user, I had already customized my Prompt.
Including some logic to make it obvious which session was running under the Admin context.
So, I decided to give each session a name.

```
$SessionTitleName = Invoke-Generate '[Adjective][Noun]'
```

The code is very straight forward.
[NameIt](https://github.com/dfinke/NameIT) includes a collection of English Nouns and Adjectives which makes it easy to generate a pronounceable name.

Here is what it looks like.

![GoodDay StupidCoat](/img/gooddaystupidcoat.png)

The code for my Prompt function is up on [GitHub](https://github.com/cdhunt/Profile/blob/master/ScriptsToProcess/Prompt.ps1).

# Works for other names as well

This works really well for naming things when you don't really care what the name is, but want it easy to read and say.

Just be careful, sometimes the names can strike a nerve.
I'm looking at you HopelessProject.
