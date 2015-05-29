---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Test it with Psate"
date: 2014-03-21
comments: true
categories: [Tools, PowerShell, Testing, Psate]
slug: "test-it-with-psate"
---
Recently I have been putting effort into learning the art and science of [unit testing](http://en.wikipedia.org/wiki/Unit_testing "Unit testing") using [Pester](https://github.com/pester/Pester "Pester") and [PSate](https://github.com/jonwagner/PSate "PSate"). I was going to write up something on my experiences, but Jakub Jareš beat me to it with [this](http://www.powershellmagazine.com/2014/03/12/get-started-with-pester-powershell-unit-testing-framework/ "Get started with Pester (PowerShell unit testing framework)") good article on PowerShell Magazine. If you are new to the idea of unit testing, go read that article and then come back here.

So, what is the point of this article? I was working on an unusual maintenance recently and it was around 4 in the morning and it occurred to me that it would be nice if I had a big dashboard that showed everything was "green". <!--more--> It can take 15 minutes or more for monitoring to spool up and do all it's checks and what happens if you forget to re-enable monitoring after the maintenance. Your mind is completely unreliable after a 20 hour day. 

Since I have been working with these unit testing frameworks, it seemed like they would be a good framework to manage a sort of sanity checklist.

Here is a small example of some ways to use the PSate and [PShould](https://github.com/jonwagner/PShould "A fluent assertion library for PowerShell") modules to write some easy to read and maintain tests. I chose PSate over Pester simply because I prefer the output options, but Scott Muc recently released a major new version of Pester that is worth checking out. Both serve this purpose equally well.


    TestFixture "Services" {
        $services = Get-Service
        $testRunning = @("w32time", "Schedule")
        $testStopped = @("wmiApSrv")
    
        foreach ($t in $testRunning)
        {
            TestCase "$t Running" {
                $services.Where({$_.Name -eq $t}).Status | Should Be Running
            }
        }
    
        foreach ($t in $testStopped)
        {
            TestCase "$t Stopped" {
                $services.Where({$_.Name -eq $t}).Status | Should Be Stopped
            }
        }
    }
    
    TestFixture "Sites" {
        $uris = @("http://google.com")
    
        foreach ($u in $uris){
            TestCase "$u is accessible" {
                $results = Invoke-WebRequest -Uri $u
                $results.StatusCode | Should be 200
            }
        }
    
        TestCase "API check" {
            $results = Invoke-RestMethod https://status.github.com/api/status.json
    
            $results.status | Should be good
        }
    }

![Output](/img/psatetestresults.png)

And this is what the basic output from `Invoke-Tests` looks like when all test succeed. It is much more comforting to see a list of "green" tests than sit around hoping for no alerts from your monitoring. Instant warm fuzzies that help you get to sleep.

In the above script I check that some Services are either running or not. I check if a simple web request returns a HTTP status code of 200 and if the contents of the `status` property returned by a REST API method is "good". Also, there is an example of looping through an array to execute one defined TestCase with multiple values&mdash;remember [DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself "Don't Repeat Yourself"). You could extend this logic to have a text file listing the Services of interest to seperate configuration from logic for easy maintainability.

Building these test on PSate provides a lot of added functionality. You don't have to have all your tests in one file, but could have a folder full of test scripts and if you pass that path to `Invoke-Tests` it will run all tests in all files. That's another good option for keeping this maintainable as you add and remove tests.

Also, you'll get better at writing unit tests and that's good for everyone that might use your scripts.