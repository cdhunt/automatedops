---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Tools: NSSM"
date: 2013-07-09
comments: true
tags: [Tools, Windows]
slug: "tools-nssm"
---
[The Non-Sucking Service Manager](http://nssm.cc/)

NSSM is a really simple application for creating a Windows service from any executable command. 

![](http://nssm.cc/images/install.gif)

It's similar to _srvany_ if you've ever used that before. NSSM includes support for handling application exits depending on the exit code. That's about it. There's not much to it, but it is quick and simple way to solve a not-uncommon problem&madsh;make something run without requiring me to log in and start it.
<!--more-->
I use NSSM to host a [Node.JS](nodejs.org) process and run a [Hubot](http://hubot.github.com/) instance. It's also an option for Java. There is a product called  [Java Service Wrapper](http://wrapper.tanukisoftware.com/doc/english/download.jsp) which offers a lot of options, but if you just want to run a Jar outside of a desktop session (eg [LogStash](http://logstash.net/)), again it's quick and easy.

The important part is that it's Non-Sucking.

