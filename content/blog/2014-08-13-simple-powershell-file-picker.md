---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
title: "Simple PowerShell File Picker"
date: 2014-08-13
comments: true
tags: ["powershell", "tip"]
slug: "simple-powershell-file-picker"
---

Here is a simple function to create a basic graphical file picker.

Inspired by the more robust, but more verbose snippet posted on [powershell.com](http://powershell.com/cs/blogs/tips/archive/2014/08/13/using-the-openfile-dialog.aspx "Using the OpenFile Dialog").
<!--more-->

    function PickFile 
    [string]$Path = $PWD)
    
       Get-ChildItem $Path -File | Out-GridView -PassThru
    
![PickFile](/img/PickFile.gif)