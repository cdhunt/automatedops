---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
comments: true
date: "2017-05-25T15:41:00-04:00"
sharing: true
tags: ["go", "golang", "powershell", "community"]
title: "So You Want To Build A Golang App"
slug: "so-you-want-to-build-a-golang-app"
draft: false
---

Well, maybe you don't and you want to know why the hell I would. I've been building PowerShell tools for about 10 years and I'm pretty good at it. PowerShell is even available on Linux now. So, why [Go](https://golang.org/)? It's freaking everywhere. Here are some of the FOSS tools I've been working with that are built on Go.

- [GitLab CI Runner](https://gitlab.com/gitlab-org/gitlab-ci-multi-runner)
- [Telegraf](https://github.com/influxdata/telegraf/)
- [Packer](https://github.com/hashicorp/packer)
- [Terraform](https://github.com/hashicorp/terraform)
- [Kubernetes](https://github.com/kubernetes/kubernetes)

A lot of these tools have a lot of open issues around the Windows build and I can't contribute if I don't understand Go and the community is huge. The PowerShell Summit had about 250 attendees.

>GopherCon started in 2014, with an amazing first year attendance of over 700 people. Since then, we've doubled in attendance. We expect to sell out early this year, with a maximum capacity of **1500 people**. -https://www.gophercon.com/

I find in PowerShell that I have to build a lot of supporting functionality just to be able to build the valuable bit of functionality I originally wanted. In Go, the problem is that when you go looking for a package you have to choose from three or more. My first Go app has 20 dependencies and that's a lot of code I didn't have to write myself. Package management in PowerShell is still not mature enough to handle that kind of dependency chain.

I also like the language. It has nice programmery things like [Interfaces](https://gobyexample.com/interfaces) and [Goroutines](https://gobyexample.com/goroutines).

## Getting started with Go as a PowerShell developer

There is a number of good getting started resources.

- https://golang.org/doc/install
- https://gobyexample.com/

However, thinking about the features of Go compared to PowerShell helped me get my head around the language. So, here are some of those analogies.

### GOPATH

`$env:GOPATH` is your `$env:PSModulePath` for Go. It doesn't get set to some default when you install Go and without this nothing works. I have mine set to `~\go`.

### Packages

Packages are obviously like Modules. At the top of each file, you define what package the file is part of. If you want to build a binary, you need to have a `main` package with a `func main()`. Or, you can just name your package whatever if you are going to distribute it like a library. Packages also function a bit like a .Net Namespace and Go has a clever feature where if your function name starts with an uppercase letter it is exported, otherwise it stays private to the package.

### Cobra

I initially used the [flag](https://gobyexample.com/command-line-flags) package to handle command-line flags (parameters), but I found them lacking compared to PowerShell's fantastic parameter handling. A coworker suggested [Cobra](https://github.com/spf13/cobra) (the package powering the CLI for [Hugo.exe](http://gohugo.io/)).

The syntax to use Cobra is looks nothing like PowerShell parameter declarations, but it provides a lot of equivalent functionality. You can define commands and flags and Cobra takes care of building the Help output for you.

Here's an example command declaration:

```
var RootCmd = &cobra.Command{
    Use:   "hugo",
    Short: "Hugo is a very fast static site generator",
    Aliases: []string{"url", "address"},
    Example:    "An example of usage",
    Long: `A Fast and Flexible Static Site Generator built with
                love by spf13 and friends in Go.
                Complete documentation is available at http://hugo.spf13.com`,
    Run: func(cmd *cobra.Command, args []string) {
        // Do Stuff Here
    },
}
```

`Use` is the usage (syntax) string of the command. `Short` is like the PowerShell `Synopsis` and `Long` is like `Description`. There is even an `Example` section — Use it or [June](https://twitter.com/juneb_get_help) will find you. Yes, even if you are using Go! On top of that, there is an `Aliases` property.

Let me back up a second and cover something that took me the better part of a day to think about clearly. In PowerShell, you typically just invoke some function. That function probably is part of a Module and represents a small bit of the functionality the Module offers. When I started with Go I equated the executable I was building to a cmdlet. Cobra implements a CLI convention of `exe command -flag` and thinking of the exe as a cmdlet got me confused when I was trying to figure out what my commands should be. However, the exe is really like a Module and each command is like a cmdlet. 💡 It seems obvious in hindsight. It even says so in the README.

> The pattern to follow is APPNAME VERB NOUN --ADJECTIVE. or APPNAME COMMAND ARG --FLAG

The other common pattern of Cobra is putting each command in a separate file. Similar to how I like to put each public function for a PowerShell module in a separate file. This is extra useful when you do [cross-platform compiling](https://dave.cheney.net/2013/10/12/how-to-use-conditional-compilation-with-the-go-build-tool) and not all commands should be included for every platform.

### Glide

Remember when I said Go has a lot of options for packages? It even has options for package management. [Glide](https://glide.sh/) makes package management easier. `Glide up` is basically like `nuget restore` and it uses reflection to examine your source files and figure out which packages need to be installed. Super handy for CI.

### GOX

[Gox](https://github.com/mitchellh/gox) like Glide is about making a part of Go development single-line simple.

> Gox is a simple, no-frills tool for Go cross compilation

You can do cross compilation with `go build`, but Gox automates it and does it fast.

This is the command I use in AppVeyor for creating amd64 builds for both Windows and Linux. If you leave off the os and arch flags it compiles for over a dozen platforms.

```
gox.exe -os="windows" -os="linux" -arch="amd64" -ldflags="-X cmd.version=$env:APPVEYOR_BUILD_VERSION" -output="./build/{{.OS}}_{{.Arch}}/spinner_v$env:APPVEYOR_BUILD_VERSION"
```

The `-ldflags` part is sort of the community recommended way of versioning your binary in Go. This is setting the value of the `version` variable in `cmd` package.

### Speaking of AppVeyor

AppVeryor includes Go on all of its build agent flavors, however, Glide and Gox are not included. I also found I needed to set the environment variables to get Go builds working on AppVeyor.

Here's the `install` section of my AppVeryor.yml.

```
install:
  - set PATH=%GOPATH%\bin;%PATH%
  - ps: Start-FileDownload 'https://github.com/Masterminds/glide/releases/download/v0.12.3/glide-v0.12.3-windows-amd64.zip'
  - ps: Expand-Archive -Path glide-v0.12.3-windows-amd64.zip
  - ps: Copy-Item .\glide-v0.12.3-windows-amd64\windows-amd64\glide.exe .\glide.exe
  - go get github.com/mitchellh/gox
  - go version
  - go env
```

AppVeyor includes a cmdlet called `Start-FileDownload` that seems to work better in their agent than `Invoke-WebRequest -outfile`.

After that, the rest of the rest of the yml isn't that complicated.

```
test_script:
  - go test .\cmd --coverprofile=cover.cov

before_build:
  - glide up

build_script:
  - ps: gox.exe -os="windows" -os="linux" -arch="amd64" -ldflags="-X cmd.version=$env:APPVEYOR_BUILD_VERSION" -output="./build/{{.OS}}_{{.Arch}}/spinner_v$env:APPVEYOR_BUILD_VERSION"

after_build:
  - ps: .\create_archives.ps1

  - path: build/spinner*.zip
    name: spinner
```

The `create_archive.ps1` script just loops through all of the directories created by Gox and creates a zip file for each.

All of this was learned while developing [Spinner](https://github.com/Ticketmaster/spinner), a replacement for the closed source ServiceMoniter.exe included in some Microsoft published [Docker images](https://github.com/Microsoft/iis-docker/tree/master/windowsservercore).

I think I missed some stuff. I crammed a lot of learning into a few days. If you're interested in Go development on Windows and the above didn't answer your question, leave a comment or hit me up on [Twitter](https://twitter.com/LogicalDiagram).