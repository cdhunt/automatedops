---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
comments: true
date: "2017-01-13T10:33:33-05:00"
sharing: true
tags: ["PowerShell", "Classes"]
title: "Creating and using Custome Attributes in PowerShell Classes"
slug: "creating-and-using-custome-attributes-in-powershell-classes"
draft : true
---

## Time to hang some decorations

You may not know it, but you have likely been using .Net Attributes in PowerShell for sometime.

```powershell
[cmdletbinding()]
```

```powershell
[Parameter(Mandatory=$true)]
```

Those are a couple of the common attributes used in PowerShell development. You notice, they don't *do* anything exactly. Attributes are bits of declarative code that helps inform other parts of your code about the decorated class, method, property, etc. It's Metadata.

Now, with the improved support for creating new .Net Types in PowerShell v5, it's pretty easy to create your own Custom Attributes.

*Quick note: A lot of this is a bit speculative. I would recommend against using Custom Attributes because the conditions for them working as expected are very fragile. Things I say don't work in this post, might work with slight tweaks here or there. However, if you're reading this it's probably because you're thinking of doing it so hopefully you start a bit better informed.*

## Why would you?

There are probably many more reasons that I can think of or articulate, but let me give you an example.

### Invoker

This is a module I built to facilitate building modules that are simply PowerShell proxies for CMD executables. Make it so executable arguments like `-f` is mapped to a PowerShell parameter named `-Path` which is a consistent and common parameter across the PowerShell environment. You could also add Validation and additional parameter Help.

The exact details of the inner workings of this module aren't important for this discussion. Just keep in mind that I was looking for a way to declare a relationship between the executable argument and a PowerShell function parameter. Describing metadata about parameters is already something you do with Attributes, so I thought I'd see what

### Source

Here is the entire source for Invoke for reference.

```powershell
enum Style
{
    Slash
    Backslash
    Dash
    DoubleDash
}

enum ValueSeparator
{
    Space
    Colon
}

class Option : System.Attribute
{
    [string] $Name
    [bool] $Sensitive
    [bool] $EncloseValueIfSpacesPresent
    [Style] $Style
    [ValueSeparator] $Separator
    [object] $Value

    Option() { }

    Option([string] $name)
    {
        $this.Name = $name
        $this.EncloseValueIfSpacesPresent = $true
        $this.Sensitive = $false
        $this.Style = [Style]::Dash
        $this.Separator = [ValueSeparator]::Space
    }

    Option([string]$name, [bool]$sensitive)
    {
        $this.Name = $name
        $this.EncloseValueIfSpacesPresent = $true
        $this.Sensitive = $sensitive
        $this.Style = [Style]::Dash
        $this.Separator = [ValueSeparator]::Space
    }

    Option([string]$name, [bool]$sensitive, [Style]$style)
    {
        $this.Name = $name
        $this.EncloseValueIfSpacesPresent = $true
        $this.Sensitive = $sensitive
        $this.Style = $style
        $this.Separator = [ValueSeparator]::Space
    }

    Option([string]$name, [bool]$sensitive, [Style]$style, [ValueSeparator]$separator)
    {
        $this.Name = $name
        $this.EncloseValueIfSpacesPresent = $true
        $this.Sensitive = $sensitive
        $this.Style = $style
        $this.Separator = $separator
    }

    Option([string]$name, [Style]$style, [ValueSeparator]$separator)
    {
        $this.Name = $name
        $this.EncloseValueIfSpacesPresent = $true
        $this.Style = $style
        $this.Separator = $separator
    }

    Option([string]$name, [bool]$sensitive, [Style]$style, [ValueSeparator]$separator, [bool]$encloseValue)
    {
        $this.Name = $name
        $this.EncloseValueIfSpacesPresent = $encloseValue
        $this.Sensitive = $sensitive
        $this.ParameterStyle = $style
        $this.Separator = $separator
    }
}

class Executable
{
    [string]$Path

    [void] Invoke()
    {
        Invoke-Expression -Command $this.GetInvokeString($false)
    }

    static [string]GetParamStyle ([Style]$p) {
        $value = switch($p)
        {
            Slash { '/' }
            Backslack { '\' }
            Dash { '-' }
            DoubleDash { '--' }
            Default { '-' }
        }
        return $value
    }

    static [string]GetParamSeparator ([ValueSeparator]$p) {
        $value = switch($p)
        {
            Space { ' ' }
            Colon { ':' }
        }
        return $value
    }

    static [string]GetParamEnclosedValue ($p) {
        $value = [string]::Empty

        if($p.EncloseValueIfSpacesPresent -eq $true -and $p.Value -match '\s')
        {
            $value += '"'
            $value += $p.Value
            $value += '"'
        }
        else
        {
           $value += $p.Value
        }

        return $value
    }

    static [string]GetParamValue ($p) {

        if ($p.Value -is [string]) {
            return [Executable]::GetParamEnclosedValue($p)
        }

        if ($p.Value -is [System.Management.Automation.SwitchParameter] -or $p.Value -is [bool]) {
            return [string]::Empty
        }

        return $p.Value
    }

    [string] GetInvokeString()
    {
        return $this.GetInvokeString($true)
    }

    [string] GetInvokeString([bool]$hideSecrets)
    {
        $cmd = "& $($this.Path) "
        $params = @()
        foreach($param in $this.GetArguments())
        {
            $cmdParam = [Executable]::GetParamStyle($param.Style)

            $cmdParam += $param.Name

            $cmdParam += [Executable]::GetParamSeparator($param.Separator)

            if ($param.Sensitive -and $hideSecrets) {
                $cmdParam += '*****'
            }
            else {
                $cmdParam += [Executable]::GetParamValue($param)
            }

            $params += $cmdParam
        }

        $cmd += $params -join ' '
        return $cmd
    }

    [Option[]] GetArguments()
    {
        $type = $this.GetType()
        $properties = $type.GetProperties()

        $result = @()

        foreach($property in $properties)
        {

            $value = $property.GetValue($this)

            if ($null -ne $value -and ((-not ($value -is [bool])) -and $value -ne $false) )
            {

                $attr = [System.Attribute]::GetCustomAttribute($property, [Option])
                if($null -ne $attr)
                {
                    $attr.Value = $value
                    $result += $attr
                }
            }
        }

        return $result
    }
}
```

### Explanation

You should quickly be able to identify how you define a new Attribute type. It is standard PowerShell class syntax.

```powershell
class Option : System.Attribute
```

The bulk of the class definition is various constructors. When you work with Attributes normally, you can provide named parameters. For example `[Parameter(Position=0)]`. However, because of how getters and setters are defined with PowerShell classes when you create a Customer Attribute via PowerShell, this functionality does not work and only Positional parameters are support. So, I created a bunch of constructors to make that easier. Functionally, an Attribute is just a Class with some Properties and optionally some Constructor methods. In this case, Constructors don't seem to be optional because, as I said, named parameters don't work. At the end of the day, you are creating a Class with a few Properties. Later on, in the rest of your code, you can access these objects and the Property Values.

The second half of this module is a Class named `Executable`. The short description of this Class is, "It's a fancy string builder". At the end of the day, all I'm doing is building a string to invoke.