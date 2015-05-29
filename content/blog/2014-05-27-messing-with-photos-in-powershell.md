+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
category = ["Blog"]
comments = true
date = "2014-05-27T15:12:49-04:00"
sharing = true
tags = ["tools", "powershell", ".net"]
title = "Messing with Photos in PowerShell"
slug = "messing-with-photos-in-powershell"
+++

In continuing with the habit of building wrappers around .Net libraries mentioned on [Scott Hanselman's blog](http://www.hanselman.com/blog/NuGetPackageOfTheWeekImageProcessorLightweightImageManipulationInC.aspx "Lightweight image manipulation in C#"), I have written a module around [James M South's](http://twitter.com/James_M_South "James_M_South") [ImageProcessor](http://imageprocessor.org/ "ImageProcessor").

Here's an example.

    Get-PIPImage -Path C:\Pictures\DSC06199.JPG | 
        Set-PIPImageSize -Width 500 -Height 500 -MaintainAspect | 
        Add-PIPFilter -Filter lomograph | 
        Add-PIPRoundedCorners | 
        Set-PIPImageFormat -Format Png | 
        Save-PIPImage -Path c:\temp\whisky.png

And here is the result.

![Whisky](/img/whisky.png)

<!--more-->
No need to show the original, but it was a 16MP Jpeg straight from the camera.

The project is on [GitHub](https://github.com/cdhunt/PIP "PowerShell ImageProcessor") as usual. There is still some work to do, but it's a quick easy way to prep some photos  for sharing.