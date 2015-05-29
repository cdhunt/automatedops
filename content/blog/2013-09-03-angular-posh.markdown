---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Angular Posh"
date: 2013-09-03
comments: true
tags: [PowerShell, AngularJS, Tools]
slug: "angular-posh"
---
I just recently found out about a fun project, [PoSH Server](http://www.poshserver.net/) that has actually been around quite a while. Executing PowerShell from a web page is a powerful idea for a Windows Administrator. Simply access a URL and get back some HTML formatted data&mdash;easy to run and consume the results. Just like everything involving PowerShell, there are a number of way to tackle this and I've tried quite a few including: PowerShellASP (now PowerShellServer) by [/n Software](http://www.nsoftware.com/powershell/); and [Edge](https://github.com/dfinke/edge-powershell) for NodeJS. The beauty of PoSH Server is that it's written entirely in PowerShell which makes it easy to run and tweak. You don't even need IIS installed. If you have PowerShell installed, install the module and run `Start-PoshServer`. That is all. Well, that's enough to get you started. Where you go from there is the challenging and fun part.

<!--more-->

## What does this have to do with Angles?

Maybe you're wondering what _Angular_ has to do with this or maybe you've already figured that out. I was able to very quickly build out a pretty useful website for displaying some useful admin information like server configs and log entries, but it quickly became very challenging to manage with each file being a big mess of PowerShell and HTML. I tried "templating" the pages by rendering different sections of content by piecing together multiple files. 


    Get-Content "$HomeDirectory\templates\head_nav.htm"
    # Some PowerShell that renders HTML dynamically here
    . $HomeDirectory\templates\foot.ps1


That was still kind of messy so I went looking for an existing template framework and started working with [AgularJS](http://angularjs.org/). I'm not saying it's the best and this isn't a review of any kind, but it provides the behavior-view separation I was looking for. I wanted to build static HTML templates with PowerShell acting as the back-end. PoSH Server does have the logic for making this possible with XML. Again, the server is written in PowerShell so you can pull up the code in the ISE and change the way it works. I added the following blocks of script to the PoSHServer.ps1 file to add some more  control over the content Mime type.

After the `if ($File -like "*.psxml")` block in `# PoSH API Support` section.


    elseif ($File -like "*.psjson")
    			{
    				$File = $File.Replace(".psjson",".ps1")
    				
    				# Full File Path
    				$File = [System.IO.Directory]::GetCurrentDirectory() + $File
    				
    				# Get Mime Type
    				$MimeType = "text/psjson"
    			}


After the `elseif ($MimeType -eq "text/psxml")` block in the `# Stream Content` section.


    elseif ($MimeType -eq "text/psjson")
    				{
    					try
    					{
    						$Response.ContentType = "application/json"
    						$Response.StatusCode = [System.Net.HttpStatusCode]::OK
    						$LogResponseStatus = $Response.StatusCode
    						$Response = New-Object IO.StreamWriter($Response.OutputStream,[Text.Encoding]::UTF8)
    						$Response.WriteLine("$(. $File)")
    					}
    					catch
    					{
    						Add-Content -Value $_ -Path "$LogDirectory\debug.txt"
    					}
    				}


The result of these two changes is if you request a URL like _script.psjson_, the server will set the _Response.ContentType_  to `"application/json"` and execute and return the results of script.ps1.

## Example

Here is a very basic Model-View-Controller application I set up to test the functionality.

**index.ps1**


    $name1 = [pscustomobject]@{name="bob"}
    $name2 = [pscustomobject]@{name="john"}
    $names = @($name1, $name2)
    
    $hash = @{title="Home";names=$names}
    $hash | ConvertTo-Json
    
    
    **index.html**
    
    
    <html ng-app>
    <head>
    	<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.0.7/angular.min.js"></script> 
    	<script>
    		function Ctrl($scope, $http) {
    		  $http.get('index.psjson').success(function(data) {
    			$scope.page = data;
    		  });
    		}
    	</script>
    
    	<link href="/css/bootstrap.css" rel="stylesheet">
    </head>
    <body>
    	<div ng-controller="Ctrl">
    		<h2>{{page.title}}</h2>
    
    		<ul class="unstyled">
            <li ng-repeat="name in page.names">&#123;&#123;name.name}}</li>
          </ul>
    		
    	</div>
    </body>
    </html>


And you get a nice simple list of names.

![Screen shot](/img/angular_posh.png)

Obviously, you don't need to return static data. There is a lot more you can do with PoSH Server and obviously a lot more you can do with Angular as well. I think I'll be doing a lot with both from now on.