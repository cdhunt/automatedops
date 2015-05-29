---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Test All The Things"
date: 2014-03-27
comments: true
tags: [Tools, PowerShell, Testing, Psate, WatiN, .Net]
slug: "test-all-the-things"
---
This is a follow-up to my [last post](/blog/2014/03/21/test-it-with-psate/ "Test It With PSate") on using PSate for test automation. I demonstrated running `Invoke-WebRequest` and validating the StatusCode as a simple test for if a site is available. Now let's take it to the next level and run an artificial transaction. Again, PSate is a handy test runner to manage this and the open-source project, [WatiN](http://watin.org/ "Web Application Testing In .Net") *(pronounced as What-in)* is a great tool for automating a real browser.
<!--more-->

Here is the full script.
[https://gist.github.com/cdhunt/9809635](https://gist.github.com/cdhunt/9809635)

I also want to mention [FluentAutomation](http://fluent.stirno.com/ "Simple, fluent automated testing for web applications") as another good .Net web testing automation framework. For reasons probably irrelevant to you, I went with WatiN.

So, taking a look at this script, the first line is pretty obvious. Load up the WatiN library. If you are unfamiliar with the syntax of the next four lines, that is simply creating a type accelerator for `WatiN.Core.Find` to save me some typing later on. PSate supports [TDD](http://en.wikipedia.org/wiki/Test-driven_development "Test-driven development") and [BDD](http://en.wikipedia.org/wiki/Behavior-driven_development "Behavior-driven development") style tests. In the last post I used `TestFixture` and `TestCase` and in this one I use `Describing`, `Given` and `It`. For the purpose of this use case, they are interchangeable.

It takes very little effort to start automating a browser with WatiN. `$ie = New-Object WatiN.Core.IE('http://www.bing.com/')` will start up an instance of Internet Explorer and browse to the provided URL.

The next part has a few things going on.

`$ie.TextField([Find]::ByName('q')).TypeText('Automated Ops')`

The `TextField` method returns a *TextField* object representing a given page element. In that we use the `ByName` static method to search the page for an element with the name 'q'. This is where creating the custom type accelerator comes in. Without it you would have to type the fully qualified namespace of the class, such as `[WatiN.Core.Find]`. It's basically an alias for a .Net class. Then call the `TypeText` method on the *TextField* object to actually type some text into the Bing search textbox.

`$ie.Button([Find]::ByName('go')).Click()`

A bit of the same, but this time finding a *button* and calling the `Click` method.

Then we define a test.


    It 'Contains "Automated Ops"' {
      $ie.ContainsText('Automated Ops') | Should Be $true
    }


The browser object has a method that wills search all of the contents of the page for a given string and return `$true` if there is a match. Check that the results are indeed $true and be sure to `Close()` the browser at the end of your tests. The logic within the `Contains` block, both above and below the individual tests, will actually be called for each test (each "It"). That will produce a fresh browser session for every single test. That's important so one test doesn't break all subsequent tests and something you need to be aware of when writing test cases&mdash;There is no shared session between tests. If you want that, move the browser object instantiation to the Describes block.

When `Invoke-WebRequset` isn't enough, it is still pretty easy to take web access automation to the next level.