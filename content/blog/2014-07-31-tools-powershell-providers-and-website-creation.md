+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
comments = true
date = "2014-07-31T14:15:01-04:00"
sharing = true
tags = ["tools", "powershell", "blog", "foss"]
title = "Tools: PowerShell Providers & Website Creation"
slug= "tools-powershell-providers-and-website-creation"
+++

I found two great tools yesterday. Actually, that's an grossly inaccurate statement. One of the tools I learned about at the PowerShell Summit earlier this year, but was just finally published to GitHub. The second tool isn't really a tool, but a site indexing a bunch of tools, or frameworks, for generating static websites.

<!--more-->
StaticGen
===
Joel Bennet ([@Jaykul](https://twitter.com/Jaykul "Twitter")) posted on Twitter that he's relaunched his blog and built it using the Static Site Generator Nikola and mentioned a site [http://www.staticgen.com/](http://www.staticgen.com/ "StaticGen"). Before then, I knew about Jekyll and Octopress, which this site is built using. I had no idea there were so many options. Joel also noticed something about the list, there was not a single .Net based option. It didn't take much Googling to find there are some options and StaticGen is hosted on in a [GitHub repo](https://github.com/bitballoon/staticgen "bitballoon/staticgen") and "everybody is welcome to contribute." So, if you check out the site today, you will now find options for .Net based Static Site Generators.

Simplex
===
One of my favorite presentations at the PowerShell Summit was given by [Jim Christopher](http://www.beefycode.com/ "beefycode") on his [PowerShell Provider Framework](https://github.com/beefarino/p2f/ "P2F"). That opened my eyes to the power and simplicity of using PSProviders and P2F helps make building Providers "easy". During the talk, he mentioned he's working on a project that lets you build Providers using a PowerShell based [DSL](http://blogs.technet.com/b/heyscriptingguy/archive/2011/02/17/use-powershell-to-simplify-working-with-xml-data.aspx "Use PowerShell to Simplify Working with XML Data").

I have been working on a project this week to build a plugin for NewRelic to poll Perfmon counters. I knew what metrics I wanted to watch, but finding the exact counter Paths was a bit of a pain and required running Get-Counter -ListSet * to find the Set Name and then Get-Counter -ListSet "SetName" | Select Paths to get a list of Paths. I was constantly "zooming in and out" of a collection of hierarchical objects. Clearly this was a good use case for a Provider. I have used Jim's P2F to build a provider before and it involves a fair amount of C#. He could build a Provider in 20 minutes. It would take me, and my meager C# skills, at least a full day or two. So, I asked Jim about the PowerShell Provider project.



<blockquote class="twitter-tweet" data-partner="tweetdeck"><p><a href="https://twitter.com/LogicalDiagram">@LogicalDiagram</a> Here you go, lazy bones: <a href="https://t.co/2UkIKKl1gz">https://t.co/2UkIKKl1gz</a>.  I&#39;ll put it on psget as well.  Blog post forthcoming.</p>&mdash; jim christopher (@beefarino) <a href="https://twitter.com/beefarino/statuses/494505172026937344">July 30, 2014</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>


It took me maybe 30 minutes to pull down the source, build it, read the examples and come up with this short script to create a Permon Counter Provider.


    root {
        Get-Counter -ListSet * | Select-Object -ExpandProperty CounterSetName | foreach-object {
            $content = $_;
            script $content {
                Get-Counter -ListSet $content
            }.GetNewClosure();
        }
    }


Here's an example usage.


    PS C:> New-PSDrive perf -PSProvider simplex -root "C:\Scripts\PerfmonCountersDrive.ps1"
    CD perf:\
    PS perf:\> dir .\Processor | Get-Counter | Select -expand CounterSamples | ? InstanceName -eq '_total' | ft -AutoSize
     
    Path                                             InstanceName       CookedValue
    ----                                             ------------       -----------
    \processor(_total)\% processor time              _total        2.20832835820896
    \processor(_total)\% user time                   _total        1.35821890547264
    \processor(_total)\% privileged time             _total       0.970149253731343
    \processor(_total)\interrupts/sec                _total        6429.09274985706
    \processor(_total)\% dpc time                    _total                       0
    \processor(_total)\% interrupt time              _total                       0
    \processor(_total)\dpcs queued/sec               _total        866.239847045942
    \processor(_total)\dpc rate                      _total                      11
    \processor(_total)\% idle time                   _total        98.5678009950249
    \processor(_total)\% c1 time                     _total        0.38036815920398
    \processor(_total)\% c2 time                     _total                       0
    \processor(_total)\% c3 time                     _total        94.9976517412935
    \processor(_total)\c1 transitions/sec            _total        59.7406791066167
    \processor(_total)\c2 transitions/sec            _total                       0
    \processor(_total)\c3 transitions/sec            _total        8879.45627121346


Keep an eye on Jim's blog for a more in depth explanation of Simplex. UPDATE: Here is the promised post: [Introducing Simplex](http://www.beefycode.com/post/Introducing-Simplex.aspx "beefycode")
