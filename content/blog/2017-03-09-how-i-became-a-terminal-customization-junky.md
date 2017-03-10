---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
comments: true
date: "2017-03-09T15:46:14-05:00"
sharing: true
tags: ["powershell", "terminal", "prompt", "tools"]
title: "How I became a terminal customization junky"
slug: "how-i-became-a-terminal-customization-junky"
draft: false
---

# In the beginning, there was `PS C:\>_`

I have been a regular PowerShell users for years.
Probably about a decade now and I avoided customizing the shell on my workstation for the vast majority of that time.
Despite PSRemoting being a thing for almost the entirety of PowerShell's existence now.
For one reason or another, I would usually end up RDPing into a server just to open PowerShell.
I did not want to become dependent on modules like PSReadLine or console emulators like ConEmu for fear I would be handicapped when I had to use PowerShell on another system.
That is no longer the case.
With a push toward Immutable Infrastructure, I can't even log into most servers if I wanted to.
No RDP.
No WinRM.
Nada.
So, I no longer have a good excuse not to tweak my everyday shelling experience.

And, I may have gone a bit overboard.

![Hyper](/img/Hyper_2017-03-09.png)

# How many modules does it take to get to the center of your Prompt?

In my case, it takes 4 separate modules to generate this PowerShell prompt.

 - [PowerLine](https://github.com/Jaykul/PowerLine) - Does most of the work of formating the Prompt
 - [PSGit](https://github.com/PoshCode/PSGit) - Provides Git repo state.
 - [NameIt](https://github.com/dfinke/NameIT) - Generates the random session name.
 - [PowerShellHumanizer](https://github.com/dfinke/PowerShellHumanizer) - Humanizes the previous command duration Timespan.

The font is [Fira Mono](https://github.com/powerline/fonts/blob/master/samples/All.md#firamono).
Fira also looks fantastic in VS Code with [ligatures](https://github.com/tonsky/FiraCode#fira-code-monospaced-font-with-programming-ligatures) [enabled](https://github.com/tonsky/FiraCode/wiki/VS-Code-Instructions).

And, as you might have guessed with all of this code behind my prompt, it can be a little sluggish.
Not slow enough that it bothers me, but rendering is noticeably not instantaneous.
Whatever, it's pretty and chock full of information.

# That's not all

Why stop with prompt customization when there are all sorts of console emulators available?
I've heard some good things about [Hyper](https://hyper.is/) so I downloaded it and tried to figure out if it would work with PowerShell.
Sure enough.
It's just two lines of config that need editing.

```javascript
    shell: 'powershell.exe',
    shellArgs: [''],
```

Only two lines if you want the bare bones experience.
However, the whole point of Hyper is that it is extensible.
After trying out a bunch, here is what I settled on for day to day use.

_**Update - March 10th**:_ It appears that the 1.3.0 release that came out as I was writing this has some [serious issues](https://github.com/zeit/hyper/issues/1221) with CPU consumption on Windows.

## Extensions

### hyperpower
You can see some magenta dots sort of floating above my prompt in the screenshot. Hyperpower is a particle generator that shots particles matching your color palate out from the cursor. It's very gimmicky, but I can't find a good reason to turn it off.

### hyperlinks
This turns any hyperlink output to the shell into clickable links. You can see in the screenshot that links are a different color. It's fantastic for following up links in cmdlet help text.

### hyper-tabs-enhanced
Adds some additional options for managing Tabs. Did you notice in the screenshot that I have two tabs open? That's also where the randomized session name comes in handy. The text color of the background tabs will change if there is any new output.

### hyperterm-material
The Material Design colors scheme for Hyper. I really liked the Monokai theme, but that theme had parameters as dark gray on black.

### hyperterm-cursor
Allows seeing the char behind your cursor by a color difference.

You can find a large list of extensions and themes at the [Awesome-Hyper](https://github.com/bnb/awesome-hyper) repo.

## Ninja Cat

![Ninja Cat](/img/ninjacat.png)

Oh yes, and Hyper supports the [Zero Width Joiner](http://emojipedia.org/emoji-zwj-sequences/)-based emoji in Windows 10 which means NinjaCat is sitting up in the title watching everything I do.
You can also technically do silly stuff like this.

![100%](/img/emojifunction.png)

Yes, emoji functions.

# It's not all Unicorn-Riding-Ninja-Cats

As I mentioned, my prompt can seem like it's in need of a cup of coffee.
Also, Hyper has some keybindings that overlap with the default keybindings of PSReadline.
`Ctrl+R` is a big one.
TFW you try to do a reverse history search and reload your shell instead.
Some of the plugins are not tested/functional on Windows.
However, like all things OSS these days, Windows support is picking up.
The [1.3.0 release notes](https://github.com/zeit/hyper/releases/tag/1.3.0) specifically called out some improvements for Windows.

# Change of Scenery

I have enjoyed applying a fresh coat of paint over the worn out blue console. I might even take a crack at developing an extension when I find a bit more spare time.