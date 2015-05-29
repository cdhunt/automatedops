+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
category = ["Blog"]
comments = true
date = "2015-05-29T10:17:45-04:00"
sharing = true
tags = ["Powershell", "Remoting", "tips"]
title = "Cross Domain Remoting"
slug = "cross-domain-remoting"
+++

I've seen a couple questions about cross-domain PowerShell Remoting online this week. I do this often and actually just recently finally started building some tooling around making it easier to work with on a daily basis.

----
>[http://www.reddit.com/r/PowerShell/comments/37nw55/crossdomain_remoting/](http://www.reddit.com/r/PowerShell/comments/37nw55/crossdomain_remoting/)

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="en" dir="ltr"><a href="https://twitter.com/LogicalDiagram">@LogicalDiagram</a> <a href="https://twitter.com/jsnover">@jsnover</a> <a href="https://twitter.com/almostjulian">@almostjulian</a> <a href="https://twitter.com/abhik501">@abhik501</a> Can you point me to solid directions, and what WMF versions are required?</p>&mdash; Rob Nelson (@rnelson0) <a href="https://twitter.com/rnelson0/status/603895080328568832">May 28, 2015</a></blockquote>

----

I referenced this [Scripting Guys article](http://blogs.technet.com/b/heyscriptingguy/archive/2013/11/29/remoting-week-non-domain-remoting.aspx) which has some good information on remoting to an untrusted domain.

## My Simple Steps

There is more than one way to do this it seems, but here is basically what I did.

1. Run `WinRM Quickconfig` on each server. [Here's](http://superuser.com/questions/671248/how-to-run-winrm-quickconfig-on-all-lan-computers-remotely) a hint on doing that in bulk.
2. Build a trusted hosts list and run `Set-Item wsman:\localhost\Client\TrustedHosts -value $list` on my desktop. `$list` is a string with a comma-delimited list of hosts.

That will allow you to create sessions using `Enter-PSSession -ComputerName someRemoteHost -Credential remoteDomain\myUser`. That's fine, but it's a bit to type, even using the `etsn` alias and not using named parameters. I also wanted to create a bunch of sessions at once so I could manage a group of servers together.

First, I followed [this post](http://www.adminarsenal.com/admin-arsenal-blog/secure-password-with-powershell-encrypting-credentials-part-1) to create a text file with my encrypted credential. With that, I don't have to provide a `[PSCredential]` everytime I start up a new session.

## My Module

    function Register-ServerRemoteSession
    {
        [CmdletBinding()]
        [Alias('rrs')]
        Param
        (
            # Param1 help description
            [Parameter(Mandatory, ValueFromPipelineByPropertyName, Position=0)]
            [string]
            $ComputerName,
    
            # Param1 help description
            [Parameter(ValueFromPipelineByPropertyName, Position=1)]
            [string]
            $SessionName,
    
            # Param2 help description
            [Parameter(Position=2)]
            [PSCredential]
            $Credential
        )
    
        Begin
        {
            if (-not $PSBoundParameters.Credential){
                $MyCredential = New-Object -TypeName System.Management.Automation.PSCredential `
                    -ArgumentList "remotedomain\myuser", (Get-Content "$($profile | Split-Path)\myuser.txt" | ConvertTo-SecureString)
            }
            else 
            {
                $MyCredential = $Credential
            }
    
        }
        Process
        {        
            New-PSSession -ComputerName $ComputerName -Name $SessionName -Credential $MyCredential
        }
    
    }
    
    function Unregister-ServerRemoteSession
    {
        [CmdletBinding(DefaultParameterSetName='ComputerName')]
        [Alias('urs')]
        Param
        (
            # Param1 help description
            [Parameter(ValueFromPipelineByPropertyName, Position=0, ParameterSetName='ComputerName')]
            [string]
            $ComputerName,
    
            # Param1 help description
            [Parameter(ValueFromPipelineByPropertyName, Position=1, ParameterSetName='SessionName')]
            [string]
            $SessionName
        )
    
        Process
        {        
            if ($PSBoundParameters.ComputerName)
            {
                Remove-PSSession -ComputerName $ComputerName
            }
            else
            {
                Remove-PSSession -Name $SessionName
            }
        }
    }
    
    Export-ModuleMember -Function *-ServerRemoteSession -Alias rrs, urs

## Using the module

The automatic credential object creation is all hardcoded so you would need to change that.

In my profile, I created some variables with some predefined server lists like this.

    $webservers = @(
                    [pscustomobject]@{'ComputerName'='10.1.2.80'; 'SessionName'='web1'},
                    [pscustomobject]@{'ComputerName'='10.1.2.81'; 'SessionName'='web2'},
                    [pscustomobject]@{'ComputerName'='10.1.2.82'; 'SessionName'='web3'}
    )
	
So, I can pipe that object to `Register-ServerRemoteSession`.

    PS C:\> $webservers | rrs
    
     Id Name            ComputerName    State         ConfigurationName     Availability
     -- ----            ------------    -----         -----------------     ------------
      3 web1            10.1.2.80       Opened        Microsoft.PowerShell  Available
      4 web2            10.1.2.81       Opened        Microsoft.PowerShell  Available
      5 web3            10.1.2.82       Opened        Microsoft.PowerShell  Available
	  
And now I have a bunch of open sessions and I can pop into one with `etsn -n web1`.

Or I can run a command across all open sessions.

    PS C:\> Invoke-Command -Session (Get-PSSession) -ScriptBlock {Get-EventLog -Newest 10 -LogName Application}
	
I hope to clean up the module and post it officially in the near future.