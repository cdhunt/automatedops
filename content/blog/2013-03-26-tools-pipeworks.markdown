---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Tools: Pipeworks"
date: 2013-03-26
comments: true
tags: [Tools, Powershell, SOA]
slug: "tools-pipeworks"
---
[PowerShell Pipeworks](http://powershellpipeworks.com/) is a pretty interesting module for PowerShell. Most modules for Posh are focused on doing some discrete task(s). The design principle of Cmdlets is to make single function executables just like Unix shell commands.

I think Pipeworks is the first 3rd party _framework_ I've seen developed on Powershell. It's scripting glue you can use to build automation with Service Oriented Architecture principles. With PowerShell v3 and the whole Windows Management Framework it was packaged in, there is a new Powershell feature called Workflows. Workflows was designed and released also to solve the problem of orchestrating more complex sequences of logic.

<!--more-->

Pipeworks may be a valid replacement for Workflows; it may be complementary. I'm still trying to figure out the ideal use cases for both. Personally, this is extremely good timing as I've been trying to find a good way to start executing Posh functionality in a distributed fashion. I still think [Edge](/blog/2013/03/19/another-net-node-dot-js-project/) might have some value, but a lot of what I was hoping to accomplish with it can be reproduced with Pipeworks without the added dependency on Node.

## Why Node When You Can PSNode?

Probably the central feature of Pipeworks is building Web Services via PowerShell and to host those services in an IIS Worker Process. There is also a quick and easy way to start up a lightweight Web Server, `Start-PSNode`.

_Example_

[https://gist.github.com/cdhunt/5247383](https://gist.github.com/cdhunt/5247383)

**Note:** _Be sure to Select a subset of Proprties before sending it to `ConvertTo-Json`. `ConvertTo-Json` has a tendency to consume all resources on my machine indefinitely when serializing a complex object._

Technically one line to spin up a simple process list web service.

Here are some variables available in the PSNode runspace.

    $context - System.Net.HttpListenerContext
    $request - System.Net.HttpListenerRequest
    $response - System.Net.HttpListenerResponse
    $user - System.Security.Principal.WindowsPrincipal

## Consume

Pipeworks has a `Get-Web` Cmdlet that is similar to `Invoke-WebRequest` and `Invoke-RestMethod`, but with some additional parameters that make extracting data from a web response more efficient.

_Examples_

[https://gist.github.com/cdhunt/5247407](https://gist.github.com/cdhunt/5247407)

Get an deserialized object for from the above PSNode response. Same results as `Invoke-WebRequest`, but `Get-Web` doesn't stop there.

[https://gist.github.com/cdhunt/5247414](https://gist.github.com/cdhunt/5247414)

`Export-PSData` is another Pipeworks Cmdlet for persisting objects to disk.

## Lots more
There is a lot more to Pipeworks. There are 78 exported Functions and Cmdlets that all revolve around producing human and machine consumable content.
