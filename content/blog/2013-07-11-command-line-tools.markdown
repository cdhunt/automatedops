---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Command line tools"
date: 2013-07-11
comments: true
tags: [Tools, Windows, PowerShell]
slug: "command-line-tools"
---
![](http://upload.wikimedia.org/wikipedia/en/7/7f/Windows_PowerShell_icon.png)
The good folks over a [Royal Pingdom](http://royal.pingdom.com/2013/07/11/network-administrators-command-line/) posted an article today entitled "Powerful command line tools for network administrators." Of course, by network administrator, they mean Linux admin. Here's my version for you Windows admins. I guess you could call it "PowerShell command line tools for network administrators."
<!--more-->
### mtr => pathping
Windows ships with a tool for testing the quality of a network path called _PathPing_. It doesn't do continuous pings like _mtr_, but it does combine the information collected from  a Tracert and then Pinging each hop.

    C:\> pathping www.pingdom.com

![](/img/pathping.png)

### netcat => ncat
This is not something I use nor can I think of a use case where I wouldn't use a different, more specialized tool, but there it is. YMMV.

<http://nmap.org/ncat/>

### pv => ?
This one I'm stuck on. You can easily tail a log file with PowerShell.

   gc .\access.log -wait | sls "/testpage.html"

That will get you a running list of lines that match the pattern as they are written.

The Powershell pipeline is a bit more complicated and I'm sure you could duplicate the functionality of _Pipe Viewer_, but possibly not in a one-liner. There is a cmdlet If you just want a progress bar, [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx), but you would still have to build the logic behind it.

### du => WinDirStat or PowerShell
Ok, not exactly a command line utility, but browsing through hierarchical structure is really a good use case for a GUI and [WinDirStat](http://windirstat.info/) does the job nicely.

You can get at similar data programatically from Posh. The Scripting Guys blog has a great [article](http://blogs.technet.com/b/heyscriptingguy/archive/2012/05/25/getting-directory-sizes-in-powershell.aspx "Getting Directory Sizes in PowerShell") on how to go about this. Or you can grab the script [here](http://gallery.technet.microsoft.com/scriptcenter/Outputs-directory-size-964d07ff "Script Center").

![](http://blogs.technet.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-00-76-18/2318.hsg_2D00_5_2D00_25_2D00_12_2D00_3.jpg)

### lsof => handle
[Handle](http://technet.microsoft.com/en-us/sysinternals/bb896655.aspx) is part of the Sysinternals tool-set built by Mark Russinovich and now owned and maintained by Microsoft.

It not only lists open file handles, but lets you forcibly close them when you need to unlock a file, but don't have the luxury of rebooting the server.

The same functionality exists in the GUI tool [Process Explorer](http://technet.microsoft.com/en-us/sysinternals/bb896653).

### grc => open to suggestions
This is another case of different ideologies. PowerShell returns objects to the pipeline. It's even frowned upon in the community to use [Write-Host](http://technet.microsoft.com/library/hh849877.aspx) to push simple strings to the screen. If you need to make the output more readable you can filter, group and sort as you like. So, _Generic Colouriser_ will probably never have a Windows' command line equivalent.

### mosh => workflow
[PowerShell Workflow](http://technet.microsoft.com/en-us/library/jj134242.aspx) is not a tool, but a feature of Posh v3 that helps you remotely manage servers asynchronously. It's not quite the same as _Mosh_, but Workflow could allow you to execute some automation to configure or repair a server while on an unreliable connection. The Workflow will continue to execute if you get disconnected and let you know the results when your connection is back up.

### Byobu => Windows
Even the Core version of Windows server still has Window Management functionality.

----
PowerShell is the new Windows shell. It's not really that new with the 4th version now in [public preview](http://www.microsoft.com/en-us/download/details.aspx?id=39347 "Windows Management Framework 4.0 Preview").

Here are some PowerShell modules I've mentioned in the past that are worth checking out if you haven't already.

* [Carbon](/blog/2013/03/06/tools-carbon/)
* [Pipeworks](/blog/2013/03/26/tools-pipeworks/)

Do you have any better suggestions or suggestions for Windows only tools?