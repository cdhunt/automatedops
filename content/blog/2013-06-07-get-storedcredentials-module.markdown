---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Get-StoredCredentials Module"
date: 2013-06-07
comments: true
tags: [Powershell, Security]
slug: "get-storedcredentials-module"
---
![Password Vault'](/img/vault.png)

If you're doing security right, the credential you use to log into your workstation is not the same used to managed the Production environment. Because of that practice, I have to run `Get-Credential` almost every time I open up a new Powershell session and type in details manually. There are plenty of options for storing credentials securely and one has been standard in Windows for over a decade&mdash;Windows Credential Manager. Why has no one built a script for getting and setting credentials from this store? <!--more--> Well, it's exposed through a Win32 API. For all my searching I could not find a .Net class for working with it or even an open source .Net wrapper. There's just this [example](http://www.microsoft.com/indonesia/msdn/credmgmt.aspx) code. Fortunately, a few people have pieced together the interesting bits to get credentials out of the Credential Manager. I cleaned up the code a bit and made it a Script Module so it will auto-load when I type the alias `gsc`.

The next step will be to [build a proxy](http://blogs.msdn.com/b/powershell/archive/2009/01/04/extending-and-or-modifing-commands-with-proxies.aspx) function that wraps the native Get-Credential so you can get a new `PSCredential` via the normal prompt or from the Credential Manager store with a single Cmdlet.

Here's the source:
[https://gist.github.com/cdhunt/5729126](https://gist.github.com/cdhunt/5729126)
