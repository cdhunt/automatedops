---
layout: post
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
title: "Nginx As a Forward Proxy"
date: 2013-03-16
comments: true
tags: [Tools, HTTP, Nginx]
slug: "nginx-as-a-forward-proxy"
---
Everyone wants to do SaaS these days and that includes Infrastructure Monitoring software. That's a problem when half of your servers do not have internet connectivity and you'd like it to stay that way.

<!--more-->

Microsoft's Routing and Remote Access Service is supposed to provide Forward Proxy functionality, but I couldn't get it to work. I think because it wants an IP specifically for RRAS that isn't used for normal server communication.

I've read about using Nginx as a Reverse Proxy and of course it's popularity as a web server is rising quickly.

It turns out it is fairly simple to use Nginx to set up a Forward Proxy on a Windows box. I was surprised to find they offer [Official Win32](http://wiki.nginx.org/Install#Official_Win32_Binaries "Nginx - Official Win32 Binaries") binaries.

It only takes a few small changes to the default config's `server` section to spin up a proxy. The usual port for an HTTP proxy is 8080. The resolver should be a DNS server that the host running Nginx can reach.

	listen       8080;
	server_name  localhost;

	location / {
		resolver 10.0.0.1;
		proxy_pass http://$http_host$uri$is_args$args;
	}

Simple to install and setup and it only consumes about 1.5MB of memory&#151;at least under light use. This is my first time using Nginx so I have no knowledge of it's performance or reliability on the Win32 platform.