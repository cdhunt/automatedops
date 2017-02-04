---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
comments: true
date: "2017-02-03T19:13:51-05:00"
sharing: true
tags: ["powershell", "collections",".net","itsajoke","butnotreally"]
title: "Working With The Collection Extension Methods (1 of 3)"
slug: "working-with-the-collection-extension-methods-(1-of-3)"
---

Sometimes, when you're working with some data, you want a little bit more functionality or performance than you get from the run of the mill arrays.
This is the first in a series of posts about `[System.Collections.Generic.List]`.

## Introductions

First things first, Generic Lists are statically typed.


```powershell
PS C:\Temp> $list = New-Object System.Collections.Generic.List[int]

PS C:\Temp> $list.add(1)

PS C:\Temp> $list.add("two")
Cannot convert argument "item", with value: "two", for "Add" to type "System.Int32": "Cannot convert value "two" to
type "System.Int32". Error: "Input string was not in a correct format.""
At line:1 char:1
+ $list.add("two")
+ ~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodException
    + FullyQualifiedErrorId : MethodArgumentConversionInvalidCastArgument
```

That just doesn't work. You can cheat though and create a `System.Collections.Generic.List[object]`.

## A Real List

Let's create a real list to mess around with for future examples.


```powershell
$list = New-Object System.Collections.Generic.List[string]

$list.Add("Mike")
$list.Add("Bob")
$list.Add("Wendy")
$list.Add("Michele")
```

On the surface, it looks just like an array.

```powershell
posh> $list
Mike
Bob
Wendy
Michele
```

But, take a look at the methods available for a Generic.List.

```powershell
$list.GetType().GetMethods() | select Name -Unique

Name
----
get_Capacity
set_Capacity
get_Count
get_Item
set_Item
Add
AddRange
AsReadOnly
BinarySearch
Clear
Contains
CopyTo
Exists
Find
FindAll
FindIndex
FindLast
FindLastIndex
ForEach
GetEnumerator
GetRange
IndexOf
Insert
InsertRange
LastIndexOf
Remove
RemoveAll
RemoveAt
RemoveRange
Reverse
Sort
ToArray
TrimExcess
TrueForAll
ConvertAll
ToString
Equals
GetHashCode
GetType
```

You have to use `GetType().GetMethods` in this case because PowerShell attempts to be helpful when you pipe to Get-Member and returns the members for the underlying type, `string`.
There is some interesting looking methods in there.

We're going to explore the functionality of a few of these in this series. Now, here is the first gotcha.

```powershell
posh> $list.FindAll

OverloadDefinitions
-------------------
System.Collections.Generic.List[string] FindAll(System.Predicate[string] match)
```

`FindAll` expects a `Predicate[string]` parameter? What is that?

## Next

In part 2, we'll find out what a Predicate is and how to make one in PowerShell.