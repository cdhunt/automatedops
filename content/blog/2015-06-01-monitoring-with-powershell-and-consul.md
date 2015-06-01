+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
category = ["Blog"]
comments = true
date = "2015-06-01T13:01:30-04:00"
sharing = true
tags = ["powershell", "tools", "monitoring", "consul"]
title = "Monitoring With PowerShell and Consul"
slug = "monitoring-with-powershell-and-consul"
+++

## Getting Started

I recently read this article, [Consul for Cluster Health Monitoring](https://vividcortex.com/blog/2015/05/22/consul-for-cluster-health-monitoring/), and was interested if I could use this to execute PowerShell based health checks.

Consul has a Win32 build so it seemed reasonable despite there being no Windows examples in the documentation.

Here's the command I ran to start up a Consul server. Ideally, you'd want a few of these in each physical site and then a Consul agent on each node. You can read more about that on their [documentation](https://www.consul.io/docs/guides/datacenters.html).

    C:\Temp> consul agent -config-file="C:\Temp\consul\serverconf.json" -bootstrap-expect 1 -config-dir="C:\Temp\consul\conf"

### serverconf.json

    {
      	"datacenter": "dev",
      	"data_dir": "C:\\Temp\\consul\\data",
      	"log_level": "INFO",
      	"server": true,
    	"ui_dir": "C:\\Users\\chunt\\Downloads\\0.5.0_web_ui\\dist"
    }

### service.json

    {"check": 
    	{
    		"id": "W32Time",
    		"name": "w32time", 
    		"script": "PowerShell -ExecutionPolicy Bypass -NoProfile -File c:\\Temp\\consul\\conf\\ServiceCheck.ps1", 
    		"interval": "30s"
    	}
    }

### ServiceCheck.ps1

    $Service = Get-Service w32Time
    if ($Service.Status -ne 'Running') 
    	{
    		$Service | Write-Output
    		exit 2
    	} 
    else 
    	{
    		$Service | Write-Output
    		exit 0
    	}

The only important bit is that you return an exit code of 1 or greater (1 for warning, greater for failure). PowerShell usually exits with a status code of 0 even if your script throws a bunch of errors.

### Consul UI

Here's what the check looks like in the Web UI. Not only does it show a status, but includes the output form the last run. That allows you to provide some additional context in your checks. In this case, I just pass through the output of `Get-Service`.

![Working](/img/consulthealthcheck.png)

![Not Working](/img/consulthealthcheckwarn.png)

## Summary

Is this better than the monitoring you have? Probably not, unless you have no monitoring. In which case, yes and it's free and *simple* to set up. The real value with this approach is it forms the foundation of a functional CMDB which you can build on for other purposes. Consul is a distributed system meant to store the current state of your infrastructure&mdash;what services are running on what systems and, in this case, are they actually running. If there's interesting to you, go read some more about [Consul](https://www.consul.io/).