---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Powershell Desired State Configuration"
date: 2013-06-04
comments: true
tags: [Windows, Powershell, TechEd, Automation]
slug: "powershell-desired-state-configuration"
---
![Lights Cross Processed by Tarnished_Plastic, on Flickr](http://farm6.staticflickr.com/5002/5346711465_faf0d5ca2b_n.jpg)

Yesterday, Microsoft revealed a new feature of Powershell v4 that is included in Server 2012 R2&mdash;Powershell Desired State Configuration. At first glance this looks to compete directly with Chef and Puppet, but during the presentation, OpsCode actually stepped in to present how they've already adopted the new technology into Chef. If you already use Chef and are familiar with the Ruby based DSL, you can continue to use it. System Center 2012 R2 is also built on top of this framework. It is the new way of thinking for Windows system configuration automation.

Here is an example configuration script. It only scratches the surface of the capabilities [demoed](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B302#fbid=LVg3LBOESu4) by Kenneth Hansen and Jeffrey Snover at TechEd.
<!--more-->

    Configuration NewWebServer
    {
      Node ("System1", "System2")
    
      WindowsFeature IIS
      {
        Ensure = "Present"
        Name   = "Web-Server"
      }
    
     WindowsFeature AspNet45
      {
        Ensure = "Present"
        Name   = "Web-Asp-Net45"
      }
    
      Website DefaultSite
      {
        Ensure       = "Present"
        Name         = "Default Web Site"
        State        = "Stopped"
        PhysicalPath = "C:\inetpub\wwwroot"
        Requires     = "[WindowsFeature]IIS"
      }
    
      File WebContent
      {
        Ensure          = "Present"
        SourcePath      = $SourcePath
        DestinationPath = $DestinationPath
        Recurse         = $true
        Type            = "Directory"
        Requires        = "[WindowsFeature]AspNet45"
      }
    
    }


A very important feature covered in the demo was using a Hashtable to define the environmental data. For example, the `$SourcePath` and `$DestinationPath` can be defined in a separate .psd1 file that contains environment specific values. 

Also covered in the demo was the DSC Pull feature that allows a host to pull necessary modules from a central server as necessary, very much like Chef client downloading necessary cookbooks. However, they showed something even more amazing that I think was not highlighted with enough emphasis. Not only did the hosts pull down all the necessary scripts to apply the configuration, but also the web site source files the configuration requires. So, they've also built package management into Powershell which the community has been asking for for years.

Very exciting stuff and I can't wait to get a hold of the preview later this month.

Would you like to know more?
---

Jeffrey Snover has submitted a proposal to talk on this technology at [DevOps Days -  Mountain View](http://www.devopsdays.org/events/2013-mountainview/proposals/Desired%20State%20Configuration%20and%20Microsoft%20Windows/) on June 21st and 22nd. I will actually be in Santa Clara for Velocity, but plan to head out before DoD starts.

Don Jones, Powershell MVP, has a [post](http://powershell.org/wp/2013/06/04/microsoft-announces-powershell-v4-dsc/ 'Microsoft announces PowerShell v4, DSC') on the topic as well.

>"There has been no announcement as yet on how far back PowerShell v4 will be made available, nor whether or not DSC is a PowerShell feature or a Windows Server 2012 R2 feature."


Image by by [Tarnished_Plastic](http://www.flickr.com/photos/samssnaps/5346711465/).