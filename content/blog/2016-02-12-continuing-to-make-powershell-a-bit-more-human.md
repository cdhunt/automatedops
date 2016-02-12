+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
category = ["Blog"]
comments = true
date = "2016-02-12T11:14:30-05:00"
sharing = true
tags = ["tools", "powershell", ".net"]
title = "Continuing To Make PowerShell A Bit More Human"
slug = "continuing-to-make-powershell-a-bit-more-human"
+++

Almost [two years ago](/blog/2014/04/10/humanize-your-scripts/), I learned about a cool project that does some very clever string manipulation. I started messing around with [Humanizer](http://humanizr.net/) and extending existing Types to add some new properties that show off the functionality of Humanizer. Doug Finke started a project at the same time to do the same thing, but using functions. So, we combined our efforts to create one module, [PowerShellHumanizer](https://github.com/dfinke/PowerShellHumanizer). I, personally, haven't done much with it since then. Until last week.

I was writing yet-another-exe-wrapper module and wanted a way to take PowerShell-style, PascalCase parameters and turn them into named parameters the EXE was expecting without writing `if ($PSBoundParmeter.ContainsKey('AnotherParameter')) {"another_parameter=$AnotherParameter")}` over and over for each property for each function. When I started thinking about parsing parameter names, I remembered that is exactly what Humanizer and sure enough, it already has an Underscore method. Bingo. Now I can loop through all the bound parameters and build string of arguments for the EXE and use that same code for every single function. 

```powershell
$cmdArray = New-Object System.Collections.ArrayList

foreach ($key in $TaskParams.Keys)
{
    $name = $key.Underscore
    [void]$cmdArray.Add("$name=$($TaskParams[$key])")
}

return $cmdArray -join ' '
```
This builds up a list of arguments in the format `parameter_one=value` assuming the PowerShell function parameter is `ParameterOne`. 

If I had had to write that Underscore functionality myself, I probably would not have even bothered and just ended up writing all those if-thens. I happened to co-author this particular module, but it still demonstrates the power of community and the PowerShell Gallery. 

This week, I started working on this post and realized I was manually converting a Title Case string to a URL slug which led to another contribution to the PowerShellHumanizer module &mdash; `ConvertTo-HyphenatedString`.

```powershell
PS C:\> "Continuing To Make Powershell A Bit More Human" | ConvertTo-HyphenatedString
continuing-to-make-powershell-a-bit-more-human
```
See the URL in the address bar? PowerShell to the rescue.

But wait, there's more. Just a couple of days ago, Jason Shirk posted a [suggestion](https://github.com/dfinke/PowerShellHumanizer/issues/3) to add custom formatting to some types. Brilliant! Why didn't we think of that before? I added support for TimeSpan like Jason suggested as well as the LastWriteTime and Length properties for DirectoryInfo and FileInfo. Here's a Gif Doug recorded to demonstrate the feature.

![](https://raw.githubusercontent.com/dfinke/PowerShellHumanizer/master/Videos/ETS.gif)

With these three ways of Huamnizing PowerShell, there is probably a lot more we can do.

Do you have any suggestions?