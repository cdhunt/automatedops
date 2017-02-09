---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
comments: true
date: "2017-02-09T10:02:13-05:00"
sharing: true
tags: ["powershell", "collections",".net","itsajoke","butnotreally"]
title: "Working With The Collection Extension Methods (3 of 3)"
slug: "working-with-the-collection-extension-methods-(3-of-3)"
draft: false
---

## Review

In [Part 2](/blog/2017/02/06/working-with-the-collection-extension-methods-2-of-3/), we took a peek at the [Zip method](https://msdn.microsoft.com/en-us/library/dd267698%28v=vs.100%29).

```
posh> [System.Linq.Enumerable]::Zip

OverloadDefinitions
-------------------
static System.Collections.Generic.IEnumerable[TResult] Zip[TFirst, TSecond,
TResult](System.Collections.Generic.IEnumerable[TFirst] first, System.Collections.Generic.IEnumerable[TSecond] second,
System.Func[TFirst,TSecond,TResult] resultSelector)
```

## Play That Func-y Music

You may have heard a `Func` in C# called an anonymous function or a lambda.

In the MSDN example, it looks like this.

```
var totalCommission = quarterlySales.Zip(quarterlyRate,
    (first, second) => first * second).Sum();
```

In C#, the Zip method is a member of a Collection and in the above example, they are calling the Zip method of `quarterlySales` collection and passing in the `quarterlyRate` collection as well as the lambda expression.
The `quarterlySales` collection is passed to `first` and `quarterlyRate` is passed to second.
In PowerShell, extension methods are not wired up as magically as they are in C# and we can only call them as static methods using the format `[type]::method()`.
That means we have to provide three arguments for `Zip` and not just two as in the C# example.

I have not explored the technical differences between a Predicate and a Func in .Net.
From where I sit, they both appear to be anonymous functions.
However, you can't just simply pass a ScriptBlock as a Func parameter.

```
$first = New-Object System.Collections.Generic.List[string]

$first.Add("Mike")
$first.Add("Bob")
$first.Add("Wendy")
$first.Add("Michele")
$first.Add("Extra")

$last = New-Object System.Collections.Generic.List[string]

$last.Add("Smith")
$last.Add("White")
$last.Add("Williams")
$last.Add("French")

[System.Linq.Enumerable]::Zip($first, $last, {param($f, $l) "$f $l"})

posh> [System.Linq.Enumerable]::Zip($first, $last, {param($f, $l) "$f $l"})
Cannot find an overload for "Zip" and the argument count: "3".
At line:1 char:1
+ [System.Linq.Enumerable]::Zip($first, $last, {param($f, $l) "$f $l"})
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodException
    + FullyQualifiedErrorId : MethodCountCouldNotFindBest
```

> Cannot find an overload for "Zip" and the argument count: "3"

Clearly, the definition for `Zip` does expect three arguments, First, Second and resultSelector.
What PowerShell really means is "Cannot find an OverloadDefinition that takes the types of arguments you passed.
While PowerShell can cast a ScriptBlock into a Predicate for you, it cannot do the same for a Func.
To get the .Net to accept the arguments, we have to statically type them ourselves.

```
posh> [System.Linq.Enumerable]::Zip($first, $last, [system.func[string,string,string]] {param($f, $l) "$f $l"})
Mike Smith
Bob White
Wendy Williams
Michele French
```

You see the last argument is still constructed like a ScriptBlock that contains a Param block defining two parameters.
But, we're telling .Net that it is a `[system.func[string,string,string]]` - A Func that takes 3 strings.
Okay, so, you might be confused at this point.
The Param block of our function is only taking in two arguments.
Why `[string,string,string]`?
If you look back at the definition it should become clear.

```
Func[TFirst,TSecond,TResult]
```

The third type is the type of the return value of your anonymous function.

You could just as easily take in two Collections of strings and return an integer.

```
posh> [System.Linq.Enumerable]::Zip($first, $last, [system.func[string,string,int]] {param($f, $l) $f.length + $l.length})
9
8
13
13
```

## The Possibilities Are Enumerable

Now you should be able to look through all of the available `Linq.Enumerable` and understand how to construct the necessary arguments.

PowerShell (v5+) is getting extremely efficient at working with Arrays and Collections and using the build in Cmdlets with the Pipeline might still be faster - both to write and to run.
But, if you have one or more Collections and you need to do some processing of the contents, it might be worth testing a `Linq.Enumerable` method to see which is ultimately better for you.