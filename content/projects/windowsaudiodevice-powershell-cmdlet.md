---
title: "Windows Audio Device Cmdlet"
date: 2013-04-24
nodate: true
socialsharing: true

---
Basic command-line audio device control from Powershell including Nuget Package Manager Console.

[On GitHub](https://github.com/cdhunt/WindowsAudioDevice-Powershell-Cmdlet)

Features: Set Volume and toggle Mute on the Default Playback Device. Get a list of devices and set the Default Audio Device.

## Install.

1. [Download](https://github.com/cdhunt/WindowsAudioDevice-Powershell-Cmdlet/blob/master/AudioDeviceCmdlets.zip)
1. New-Item "$profile\Modules\AudioDeviceCmdlets" -Type directory -Force
1. Copy CoreAudioApi.dll, AudioDeviceCmdlets.dll and AudioDeviceCmdlets.dll-Help.xml
1. Import the binary module. This can go into your profile.
        
		Import-Module AudioDeviceCmdlets

1. You may need to set the execution policy.

        Set-ExecutionPolicy -ExecutionPolicy RemoteSigned

## Suggested Aliases. I may set these in the module in the future.
    New-Alias -Name Mute -Value Set-DefaultAudioDeviceMute
    New-Alias -Name Vol -Value set-DefaultAudioDeviceVolume

## Exposed Cmdlets
* Get-DefaultAudioDevice
* Get-AudioDeviceList
* Set-DefaultAudioDevice [-Index] &lt;Int&gt;
* Set-DefaultAudioDevice [-Name] &lt;String&gt;
* Set-DefaultAudioDevice [-InputObject] &lt;AudioDevice&gt;
* Set-DefaultAudioDeviceVolume -Volume &lt;float&gt;
* Get-DefaultAudioDeviceVolume
* Set-DefaultAudioDeviceMute
* Write-DefaultAudioDeviceValue [-StreamValue]

## Attribution
Based on work done by Ray M. [hosted](http://www.codeproject.com/Articles/18520/Vista-Core-Audio-API-Master-Volume-Control) on The Code Project
