+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
category = ["Blog"]
comments = true
date = "2016-01-28T13:01:30-05:00"
sharing = true
tags = ["powershell", "v5", "classes"]
title = "Testing Powershell Classes"
slug = "testing-powershell-classes"
+++

It's been a busy few months. I recently changed jobs and one of my first projects is building some new custom DSC Resources. I chose to use Class-based resources, because Classes are cool. There were some bumps along the way, one of them being how do you run Unit Tests on such a thing.

Part of the reason I went with a Class-based resource was because it would be easy to test. Do the methods return what I expect? Do the properties have the value I expect?

I quickly discovered that importing a Module does not make any of the types in the module available. If I can instantiate an instance of my class, how can I test it? I poked around Export-ModuleMember, New-ModuleManifest and Add-Type and didn't see any new option that would export my Type. The only option I found was to execute the Class definition as a script in the current session. So, I came up with something like this `Invoke-Expression (Get-Module myClassType).Definition`, but if you have more than just a class definition in your module, that could get messy.

Thanks to a suggestion from [Doug Finke](http://dougfinke.com/blog/), I found, what I assume, is the officially supported way to load Powershell Classes. 

> try the using keyword `using 'c:\a\b\target.psm1`

There no mention of this feature in the release note and there's no help for it. Searching the Interwebs only returned one useful page.
 
[PowerShell v5 'using namespace' syntax test](https://gist.github.com/altrive/c9b39f5bc8dcc8791274)

It seemed promising so I tried it, but no luck. However, Doug's suggestion and the Gist from Altrive suggest the feature is new and maybe was very recently in development. I happened to have download the v5 RTM before it was pulled from the Download Center. BINGO! The syntax `using module` doesn't even fully work in the Production Preview. 

## How To Use "Using"

Here's an example class just in case you haven't read anything about the new v5 feature.

```
# Save as myClassType.psm1
class myType
{
   [ValidateSet("val1", "Val2")]
   [string] $P1

   static [hashtable] $P2

   hidden [int] $P3

   myType ([string] $s)
   {
       $this.P1 = $s       
   }

   static [void] MemberMethod1([hashtable] $h)
   {
       [myType]::P2 = $h
   }

   [int] MemberMethod2([int] $i)
   {
       $this.P3 = $i
       return $this.P3
   }
}
```

Create a module manifest with the _RootModule_ being the psm1 file containing your class(es).

    New-ModuleManifest -Path myClassType.psd1 -Author "Chris Hunt" -RootModule myClassType.psm1 -PowerShellVersion 5.0 

## Results

![Powershell Using](/img/posh_using.png)

You can see `myType` isn't recognized even after I import the `myClassType` module. However, the _using_ keyword loads it up just like you'd expect from _Add-Type_.

If you use _using_ in a script, like a Pester script, it has to come before any other statements.

## But Wait, There's More

Again, following the hint from Altrive's Gist, you can see several options for this keyword.

    PS> [Enum]::GetNames('System.Management.Automation.Language.UsingStatementKind')
    Assembly
    Command
    Module
    Namespace
    Type

Cool stuff for a feature that is flying under the radar so far.