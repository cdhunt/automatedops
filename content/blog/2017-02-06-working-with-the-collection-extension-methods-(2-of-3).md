---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
comments: true
date: "2017-02-06T08:44:47-05:00"
sharing: true
tags: ["powershell", "collections",".net","itsajoke","butnotreally"]
title: "Working With The Collection Extension Methods (2 of 3)"
slug: "working-with-the-collection-extension-methods-(2-of-3)"
draft: false
---

## Predicates

Okay. So, we have this collection.

```
posh> $list
Mike
Bob
Wendy
Michele
```
Which has a method called FindAll that takes a Predicate.

```
posh> $list.FindAll

OverloadDefinitions
-------------------
System.Collections.Generic.List[string] FindAll(System.Predicate[string] match)
```

What is a predicate?
[WikiPedia](https://en.wikipedia.org/wiki/Predicate_(mathematical_logic)) simply puts it like this.

> A predicate is a statement that may be true or false depending on the values of its variables

If you've written some SQL in your life, a `Where` clause is a predicate.
It is the test of values you want to return.
You probably do this all of the time in PowerShell as well.

```
Get-ChildItem | Where-Object {$_.Length -gt 1000}
```

So, if the ScriptBlock of a Where-Object command is a predicate will that work for FindAll?
Almost.
Since we are not in a pipeline, we don't have the automatic `$_` variable.
You can tell by the method definition, `System.Predicate[string]` that the method will operate on a string.
Basically, we need to give the method a way to bind the values in the collection to a variable in the predicate ScriptBlock.
This is simple enough to do with a `param` block.

Our complete Predicate object ends up looking like this.

```
posh> $list.FindAll({param($s) $s.length -ge 7})
Michele
```

Another useful method of the Generic.List is `TrueForAll()`.
It has the same method parameter.

```
posh> $list.TrueForAll({ param([string]$s) $s.Length -lt 8})
True

posh> $list.TrueForAll({ param([string]$s) $s.Length -lt 7})
False
```

## We are only getting started

In [part 1](/blog/2017/02/03/working-with-the-collection-extension-methods-1-of-3/), we listed a bunch of methods available for Generic.List.
That is actually not all of the methods available.
Generic.List implements IEnumerable which provides a long list of additional methods for querying and manipulating collections.

- [Enumerable Methods](https://msdn.microsoft.com/en-us/library/system.linq.enumerable_methods%28v=vs.100%29.aspx)

I think most of these are extension methods.
Extension methods add additional methods to another type.
Unfortunately, PowerShell does not wire up extension methods and you have to work with them as static methods.
So, let's examine one of those methods, Zip.
This isn't a method to create a .Zip file archive, but a method that, "Applies a specified function to the corresponding elements of two sequences, producing a sequence of the results."
In layman's terms, takes two collections in and spits a single collection out based on a function you provide.

```
posh> [System.Linq.Enumerable]::Zip

OverloadDefinitions
-------------------
static System.Collections.Generic.IEnumerable[TResult] Zip[TFirst, TSecond,
TResult](System.Collections.Generic.IEnumerable[TFirst] first, System.Collections.Generic.IEnumerable[TSecond] second,
System.Func[TFirst,TSecond,TResult] resultSelector)
```

There is a lot there, but the part I want to draw you attention to is `System.Func[TFirst,TSecond,TResult] resultSelector`.
This is the parameter where you supply your function to combine the two collections.
You'll notice it is not a Predicate type, but a Func.

## This concludes our broadcast day

In [part 3](/blog/2017/02/09/working-with-the-collection-extension-methods-3-of-3/), we'll break down what this definition is asking for and how to build one in PowerShell.
