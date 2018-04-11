---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
comments: true
date: "2018-04-11T12:17:56-04:00"
sharing: true
tags: ["powershell", "design patterns", "composable magic"]
title: "Software Design Patterns in Powershell: Strategy Pattern"
slug: "software-design-patterns-in-powershell-strategy-pattern"
draft: true
---

## Background

Doug Finke and I have been collaborating for the past few years in different capacities. We both started working on a Powershell implementation of the Humanizer library for .Net an ultimately merged our ideas into [PowerShellHumanizer](https://github.com/dfinke/powershellhumanizer). It didn't stop there.

We interact regularly, sharing ideas, both good and bad (on both sides), giving and receiving feedback on approaches, code and more.

Recently we chatted about what it takes to go from PowerShell scripter to PowerShell open source developer. More on that in later blog posts.

As part of that, Doug brought up Design Patterns and started sharing scripts on implementing the patterns in PowerShell using classes. I've been exploring how these patterns work in the Powershell world. The first one I really got my head around was **Strategy**.

## The Strategy Pattern

The [Strategy](https://en.wikipedia.org/wiki/Strategy_pattern) pattern is a behavioral software design pattern. That is, it's a pattern you can use to select an algorithm at runtime based on the context.

For example, say you are writing a script to process log files and you have a few different log formats. The script does the same thing for each format at a high level, but the details of how to make sense of each format is a little different. You could write a big If/ElseIf block or a Switch statement, but you can take it just a bit further and employ the Strategy pattern to make it more flexible and reusable.

## My Example: The Build Script

### Motivation

I want to run a number of build stages to prepays a PowerShell module for publication. To start, I'll have defined three possible stages, "Clean", "Copy" and "Test".

```powershell
[Job]::New($Path, $Output).
    AddStage([Clean]::New()).
    AddStage([Copy]::New()).
    AddStage([Test]::New()).
    Invoke().
    GetResult()
```

I have a `Job` class that is responsible for executing a Build. Instead of defining all of the stages as methods of `Job` I've created a `Stage` abstract class and implemented various subclasses to implement the different stage _strategies_. The `Job` object is also where I set and store the context for the program. I don't need to pass `$Path` and `$Output` to every stage, they can access the parameters from the `Job`. Therefore, each _Stage_ is self-contained. You don't need to know how they work and what values to supply for each.

Here's what the `Stage` abstract class looks like.

```powershell
class Stage {
    [string] $Name
    hidden [DateTime] $StartTime = [DateTime]::Now

    Stage($Name) {
        $this.Name = $Name
    }

    [TimeSpan] GetElapsed(){
        return [DateTime]::Now - $this.StartTime
    }

    [string] GetHeader() {
        return "~ [{0}] ~" -f $this.Name
}
```

Powershell Classes don't have support for .Net Abstract classes so this is really just a base class that implements some common logic.

Here's the definition of the `Copy` subclass.

```powershell
class Copy : Stage {

    Copy () : base('Copy') { }

    Invoke([job]$J) {

        $J.LogHeader($this.GetHeader())

        Get-ChildItem -Path $J.Source | ForEach-Object {
            $_ | Copy-Item -Destination $J.Destination -ErrorAction Stop -Recurse
            $J.LogEntry("+ {0}" -f (
                Resolve-Path (Join-Path -Path $J.Destination -ChildPath $_.Name)
            ) )
        }

        $J.LogEntry("[in {0:N2}s]" -f $this.GetElapsed().TotalSeconds)
    }
}
```

It has one member, a method named `Invoke` that takes a single `Job` parameter.

Here's the `Clean` class.

```powershell
class Clean : Stage {

    Clean () : base('Clean') {}

    Invoke([Job]$J) {

        $J.LogHeader($this.GetHeader())

        if (Test-Path -Path $J.Destination) {
            Get-ChildItem -Path $J.Destination -Recurse | ForEach-Object {
                    $_ | Remove-Item -ErrorAction Stop -Confirm:$false -Recurse
                    $J.LogEntry("- {0}" -f $_.FullName)
            }
        }

        $J.LogEntry("[in {0:N2}s]" -f $this.GetElapsed().TotalSeconds)
    }
}
```

Once again, there is a single `Invoke` method. All of the unique logic for a stage is encapsulated in this `Invoke` method and the `[Job]$J` contains the context that each stage can use as it make sense for that implementation.

## Invoking

Now that all might seem like a lot of effort to effectively run a few lines of Powershell, however, we can add or remove stages from the build `Job` and not have to change anything about code that invokes the stages. Someone else could even implement new stages on their own and not need to touch the `Job` code as long as they follow the pattern.

Here's the `Job` class. As you can see, the `Job.Invoke()` method is pretty simple. The `Job` object is where all of the state, or context, is stored. Some stages need to just know the `$Source` path or just the `$Destination`; The `Copy` stage needs both but you don't need to know that. The

```powershell
class Job {
    [string] $Source
    [string] $Destination

    hidden [array] $Result = @()
    hidden [DateTime] $StartTime = [DateTime]::Now
    hidden [Stage[]] $Stages = @()

    Job ($Source, $Destination) {
        $this.Source = $Source
        $this.Destination = $Destination
    }

    [TimeSpan] GetElapsed(){
        return [DateTime]::Now - $this.StartTime
    }

    [void] LogHeader([string]$S) {
        $this.Result += $S
    }

    [void] LogEntry([string]$S) {
        $this.Result += "`t{0}" -f $S
    }

    [void] LogError([string]$S) {
        $this.LogEntry("`!![{0}]!!" -f $S)
    }

    [Job] AddStage([Stage]$S) {
        $this.Stages += $S

        return $this
    }

    [Job] Invoke() {
        $this.Stages | ForEach-Object {
            try {
                $_.Invoke($this)
            }
            catch {
                $this.LogError($_.Exception.Message)
                break
            }
        }

        return $this
    }

    [string] GetResult() {
        return $this.Result | Out-String
    }
}
```

If you're curious, here's what the script output looks like.

    ~ [Clean] ~
            - C:\source\github\testing\release\Assert
            - C:\source\github\testing\release\classes
            - C:\source\github\testing\release\functions
            - C:\source\github\testing\release\testing.psd1
            - C:\source\github\testing\release\testing.psm1
            [in 0.10s]
    ~ [Copy] ~
            + C:\source\github\testing\release\Assert
            + C:\source\github\testing\release\classes
            + C:\source\github\testing\release\functions
            + C:\source\github\testing\release\testing.psd1
            + C:\source\github\testing\release\testing.psm1
            [in 0.29s]
    ~ [Test] ~
            Test [.\release]
            [in 0.29s]


This is just a foundation. Once the pieces are in place, you can build on top of it.

- Create Cmdlets - `New-BuildJob`, `Add-BuildStage` and `Invoke-BuildJob` to build a Powershell Pipeline
- Design a DSL to simplify your Job definition

The Job logic, Stage definitions, and Interface are all separate and can be refactored with minimal impact on the other parts. That makes it safer to make changes and incrementally add value over time and that's a key difference between a single-use script and software design.