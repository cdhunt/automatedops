+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
category = ["Blog"]
comments = true
date = "2014-04-10T16:31:27-04:00"
sharing = true
tags = ["tools", "powershell", ".net"]
title = "Humanize your scripts"
slug = "humanize-your-scripts"
+++

This is a quick example of some of the stuff you can do with [Humanizer](http://humanizr.net/) to make your script's output a bit more user friendly.


### Strings from numbers.

    PS C:\temp> [Humanizer.NumberToWordsExtension]::ToWords(2)
    two
    
    PS C:\temp> [Humanizer.NumberToWordsExtension]::ToWords(253)
    two hundred and fifty-three
    
    PS C:\temp> [Humanizer.ToQuantityExtensions]::ToQuantity("box", 4)
    4 boxes

<!--more-->
### New up a TimeSpan object.

    PS C:\temp> [Humanizer.NumberToTimeSpanExtensions]::Weeks(2)
    
    Days              : 14
    Hours             : 0
    Minutes           : 0
    Seconds           : 0
    Milliseconds      : 0
    Ticks             : 12096000000000
    TotalDays         : 14
    TotalHours        : 336
    TotalMinutes      : 20160
    TotalSeconds      : 1209600
    TotalMilliseconds : 1209600000


### Remove underscores.

    PS C:\temp> [Humanizer.StringHumanizeExtensions]::Humanize("Underscored_input_String_is_turned_INTO_sentence")
    Underscored input String is turned INTO sentence


### Truncation

    PS C:\temp> [Humanizer.Truncator]::Truncate("Long text to truncate", 10)
    Long text…


### Turn TimeSpan objects into human readable values.

    PS C:\temp> $past = get-date
    
    PS C:\temp> $later = Get-Date
    
    PS C:\temp> [Humanizer.TimeSpanHumanizeExtensions]::Humanize( $later - $past)
    11 seconds
    
    PS C:\temp> dir | select -First 5 @{l="File last modified";e={[Humanizer.DateHumanizeExtensions]::Humanize($_.LastWriteTime)}}
    
    File last modified                                                                                                                                                                                                                                                                 
    ------------------                                                                                                                                                                                                                                                                 
    7 months ago                                                                                                                                                                                                                                                                       
    7 months ago                                                                                                                                                                                                                                                                       
    7 months ago                                                                                                                                                                                                                                                                       
    7 months ago                                                                                                                                                                                                                                                                       
    one year ago          


### A bunch of way to get a specific DateTime without date maths.

    PS C:\temp> [Humanizer.In+Five]::Months
    
    Wednesday, September 10, 2014 7:37:55 PM
    
    PS C:\temp> [Humanizer.In+Five]::Years
    
    Wednesday, April 10, 2019 7:49:23 PM
    
    PS C:\temp> [Humanizer.In]::MayOf(2015)
    
    Friday, May 01, 2015 12:00:00 AM
    
    PS C:\temp> [Humanizer.In]::April
    
    Tuesday, April 01, 2014 12:00:00 AM
    
    PS C:\temp> [Humanizer.In+Two]::MonthsFrom([Humanizer.On+April]::The10th)
    
    Tuesday, June 10, 2014 12:00:00 AM


### Even support for Roman Numeral generation.

    PS C:\temp> [Humanizer.RomanNumeralExtensions]::ToRoman(2014)
    MMXIV


Update: Check out the [follow-up](/blog/2014/04/11/humanize-follow-up/) on how to make this easier to work with.