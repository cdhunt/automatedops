+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
category = ["Blog"]
comments = true
date = "2014-04-11T16:28:25-04:00"
sharing = true
tags = ["tools", "powershell", ".net"]
title = "Humanize follow-up"
slug = "humanize-follow-up"
+++

[Humanizer](http://humanizr.net/) helps you generate some nice output, but it's kind of a pain to use in PowerShell. You can use Update-TypeData to make the syntax easier to work with. Then, your code starts to look more like the examples on the project's site.

Here's a simple example.


    Add-Type -Path C:\Humanizer\Humanizer.dll
    
    Update-TypeData -TypeName System.Int32 `
        -MemberType ScriptProperty `
        -MemberName ToWords `
        -value {[Humanizer.NumberToWordsExtension]::ToWords($this)}
    
    PS C:\temp> $int = 1234
    PS C:\temp> $int.ToWords
    one thousand two hundred and thirty-four

<!--more-->
Keep in mind you can't access the property of an number character directly. The type hasn't been evaluated yet so you'll see the following error. It works if you put the number in parenthesis or access a variable of an Int type.


    PS C:\temp> 3.towords
    The term '3.towords' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path
    was included, verify that the path is correct and try again.At line:1 char:1
    + 3.towords
    + ~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (3.towords:String) [], CommandNo
       tFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
    
    PS C:\temp> (3).towords
    three


Here's another. This time using a *ScriptMethod*.


    Update-TypeData -TypeName System.String `
        -MemberType ScriptMethod `
        -MemberName ToQuantity `
        -value { switch ($args.Count) {
                1 { [Humanizer.ToQuantityExtensions]::ToQuantity($this, $args[0]) }
                default { throw "No overload for ToQuantity takes the specified number of parameters." }
              }}
    
    PS C:\temp> "string".ToQuantity(4)
    4 strings


Working with TimeSpans is much easier with a bit of setup.
    
    Update-TypeData -TypeName System.Int32 -MemberType ScriptProperty -MemberName Hours -value {[Humanizer.NumberToTimeSpanExtensions]::Hours($this)}
    Update-TypeData -TypeName System.Int32 -MemberType ScriptProperty -MemberName Minutes -value {[Humanizer.NumberToTimeSpanExtensions]::Minutes($this)}
    Update-TypeData -TypeName System.Int32 -MemberType ScriptProperty -MemberName Days -value {[Humanizer.NumberToTimeSpanExtensions]::Days($this)}
    Update-TypeData -TypeName System.Int32 -MemberType ScriptProperty -MemberName Weeks -value {[Humanizer.NumberToTimeSpanExtensions]::Weeks($this)}
    
    #Now
    Get-Date
    
    Friday, April 11, 2014 1:45:11 PM
    
    #Later
    (Get-Date) + (2).Weeks + (1).Days
    
    Saturday, April 26, 2014 1:45:11 PM


If you missed it, [here](/blog/2014/04/10/humanize-your-scripts/) are some more examples of what you can do with Humanizer.