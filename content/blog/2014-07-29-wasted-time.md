+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
category = ["Blog"]
comments = true
date = "2014-07-29T14:21:43-04:00"
sharing = true
tags = ["powershell", "IT"]
title = "Wasted Time"
slug = "wasted-time"
+++

I have been sitting here staring at my computer for over two hours now. Essentially locked out of my system for a full disk virus scanned enforced by The Man (aka Corporate IT). The scan thrashes my slow laptop harddrive so much that opening even a simple web page requires a Buddhist monk's level of patience. The job has a runtime limit of two and a half hours. 
<!--more-->
While watching the progress bar&mdash;it's the only thing I can really do&mdash;it became clear that the scan would never inspect every file in the time limit. I opened up the log and confirmed there were a lot of scans that were terminated around 2.5 hours. Out of curiosity, I used PowerShell to do some basic analysis of this log.


    Select-String "Run time" C:\ProgramData\McAfee\DesktopProtection\OnDemandScanLog.txt | 
       Foreach {[Timespan]($_ -split ' ')[-1]} | 
       Measure-Object Totalhours -Average -Sum -Minimum -Maximum


> Count    : 46
Average  : 2.9225422705314
Sum      : 134.436944444444
Maximum  : 16.6644444444444
Minimum  : 0.000833333333333333
Property : TotalHours


With this "one-liner" I extracted every instance of the "Run time" log entry and grabbed the time value from the end of the line. Fortunately, the string was in a format that could be cast to a [Timespan] object and then I could pipe that through Measure-Object to get some aggregate stats.

You could have solved that with RegEx, but since the data I wanted was at the end of a well-defined line, it was easy enough to extract with simple syntax.

So, I found out there are occasions where the scan runs longer than 2.5 hours and that I've endured over 100 hours of impacted productivity. Stop opening email attachments people so IT can stop this type of invasive scanning.