---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
comments: true
date: "2016-06-21T14:20:30-05:00"
sharing: true
tags: ["powershell", "pester"]
title: "The Pester Pipeline"
slug: "the-pester-pipeline"
---

A couple of months ago I published a [module](https://github.com/Ticketmaster/poshspec) to help reduce the amount of code you had to write to build infrastructure validation tests with Pester. Since then, I've tried to understand more about how people use Pester. Specifically, how they work with the framework to solve their problems.
When you start to look around, the community effort to "Pester all the things" is much larger that I would have guessed.

So, I've started a list of projects that build on Pester.

### Operation Validation Framework
https://github.com/PowerShell/Operation-Validation-Framework

OVF prescribes a way of organizing tests to promote sharing and reuse.

### Plaster
https://github.com/PowerShell/Plaster

Plaster is a template-based file and project generator written in PowerShell. 

### Watchmen
https://github.com/devblackops/watchmen

Watchmen is a DSL for executing infrastructure test in Pester with a bunch of helpful functionality.

### Poshspec
https://github.com/Ticketmaster/poshspec

Poshspec is a collection of functions that test common infrastructure components.

### Repository for generic tests
https://github.com/pester/Pester/issues/523

This is a discussion in Github Issues for the Pester project around creating a common repository for reusable Pester tests.

### Remotely 
https://github.com/PowerShell/Remotely

Remotely is a module to aid in the remote execution of scripts with the intention of testing components remotely.

### Format-Pester
https://github.com/equelin/Format-Pester

Format-Pester uses [PSCribo](https://github.com/iainbrighton/PScribo) to produce document reports or Pester output in several formats.

## Standing On Shoulders
The fantastic effect of all of these projects building on Pester is that they work together to make something larger. You can create new test files from a Plaster template, build tests using Poshspec, package them with OFV and run them with Watchmen.