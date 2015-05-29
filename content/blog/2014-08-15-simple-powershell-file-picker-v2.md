+++
author = "Christopher Hunt"
authortwitter = "https://twitter.com/logicaldiagram"
date = "2014-08-15"
title = "Simple PowerShell File Picker v2"
tags = ["powershell", "tip"]
slug = "simple-powershell-file-picker-v2"
+++

I made a couple of changes to the function to support recursively stepping into directories. I started messing around with this snippet in order to reproduce the functionality of using the Windows OpenFile dialog with a one-liner. Now it's a bit more than a one-liner, but the GridView UI is actually pretty useful if you don't know exactly what file you're looking for.

<!--more-->

    function PickFile 
    ([string]$Path = $PWD)
    {
        $results = Get-ChildItem $Path | Out-GridView -PassThru
    
        if ($results.Count -eq 1 -and $results.PSIsContainer)
        {
            PickFile $results.FullName
        }
        else
        {
            Write-Output -InputObject $results
        }
    }
