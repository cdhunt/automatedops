---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Software Deployments with psake on Windows"
date: 2013-04-07
comments: true
categories: [Deployment, Automation, Powershell, Windows, Qopy, Tools]
slug: "software-deployments-with-psake-on-windows"
---
I'm working on the finishing touches of a Deployment Automation script useing [psake](https://github.com/psake/psake) (pronounced like sake&#151;the Japanese rice wine) as the framework for creating discreet jobs and enforcing dependencies. 

<!--more-->

There are a lot of options for Deployment Automation on Windows, but I thought _psake_ would provide a solid enough framework without adding dependencies on commercial software.

When I started working with _psake_, I quickly found that there were some features that looked interesting and useful based on the function names, but the documentation was not entirely clear on all functionality and implementation. After some trial and error I was able to figure out how most of the functions work. So, here are my tips and tricks for building a deployment automation script on top of _psake_.

For some background, the application I was targeting this script for includes a couple separate web sites and a Windows' service that handles asynchronous task execution. One of the sites depends on the service to be available, however the other is completely standalone.

## Properties
One of my initial goals when I started on this project was to build a list of static variables utilizing the [Properties](https://github.com/psake/psake/wiki/What-is-the-structure-of-a-psake-build-script%3F) function. Ultimately, I was hoping to dynamically populate the property values from some text file so we could maintain separate configuration values for each environment without modifying the base script. It turns out it's actually a lot easier than I originally thought. I wound up putting the Properties function in a separate PS1 file and then I just use the _psake_ [Include](https://github.com/psake/psake/wiki/How-can-I-access-functions-that-are-in-other-script-files-from-within-psake%3F) function. No need to build a custom import function. 

In addition _psake_ provides a method for [overriding the Properties](https://github.com/psake/psake/wiki/How-can-I-override-a-property-defined-in-my-psake-script%3F) in the script file with with a command-line parameter. You don't have to completely forgo using your well tested processes in those situations where you have a non-standard deployment.

## Task Dependency
The dependency control _psake_ provides is fantastic. Here's a summary of the Tasks in my script.

	Task default -Depends DeployApp, DeployService,  StartPDCService

	Task PropertiesCheck

	Task StopService

	Task DeployApp0 -Depends PropertiesCheck, StopService

	Task DeployApp1 -Depends PropertiesCheck

	Task DeployService -Depends PropertiesCheck, StopService

	Task StartService -Depends StopService

That Task list gets executed in this order. You can see that _psake_ determines the most depended upon task and executes it first and then on down the chain.

    [PropertiesCheck]
    [StopService]
    [DeployApp0]
    [DeployApp1]
    [DeployService]
    [StartService]

Only one of the web applications has a dependency on the Service and if either of the `DeployApp0` or `DeployService` Tasks are configured to run, they will trigger the `StopService` Task. Which brings me to the next feature.

## Task Toggles
You can combine the Precondition parameter of the Task function along with a simple `[bool]` variable in the Properties list to build toggles.

    Task DeployApp0 -PreCondition { return $DeployApp }

Again, you can set this to `$true` as the default value in the file and override it at the commandline as desired to control which deployment tasks are executed.

## app_offline.htm
ASP.Net applications support a nice little feature triggered by the addition of a file named `app_ofline.htm` to the root of the web site. IIS will shutdown the worker processes for that application and release, file handles and render the HTML contents for any user requests.

You can use a pair of additional parameters of Task to take care of deploying that file and cleaning it up.

    -PreAction { Copy app_offline.htm (Join-Path $Destination app_offline.htm) | Out-Null; Start-Sleep -Seconds 10 }

    -PostAction { Remove-Item (Join-Path $Destination app_offline.htm) }

This takes care of file locks that could cause a problem with the file copy and also restarts the application worker process which you'll want to do anyway to ensure all the new files are loaded cleanly.

## Do the work
All of the path validation occurs in the PropertiesCheck Task so the logic of the actual deployment Tasks just consists of building some path strings to the necessary artifacts and executing a copy process using the [Qopy](https://github.com/cdhunt/Qopy) module I've also been working on separately. The _psake_ module includes a helper function called Exec that handles calling command line executables and catches the DOS return value so you could use XCopy or run an MSI installer just as easily.

## Final Thoughts
It is great to have a well designed build script framework that targets and takes advantage of the strengths of Powershell. I think _psake_ is a good foundation for building many types of multi-step automation and doesn't have to be limited to build scripts.