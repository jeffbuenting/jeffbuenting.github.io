---
layout: post
title:  "Force PowerShell to use TLS 1.2"
date:   2018-07-17 12:38:00 -0400
categories: Powershell
---


Following best practice, we have been diabling TLS 1.0 and 1.1 from our servers.  One of the few issues that we ran into was making TLS 1.2 connections with PowerShell.  It seems PowerShell uses TLS 1.0 by default.  Errors like below will start to show up.

![TLS Error](/_posts/2018-07-1-Powershell_TLS_Error.PNG "TLS Error in PowerShell")

How do you force Powershell to use the newer and more secure TLS 1.2?  With some simple .Net magic.  A simple single line set your current session to use the correct TLS.

{% highlight ruby %}
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
{% endhighlight %}

Nice.  And to find out what versions of TLS powershell Supports.  Use System.Net.SecurityProtocolType

{% highlight ruby %}
[enum]::GetNames([System.Net.SecurityProtocolType])
{% endhighlight %}

![TLS Versions](https://github.com/jeffbuenting/jeffbuenting.github.io/tree/master/_posts/2018-07-1-Powershell_TLS_Enum.PNG "TLS Error in PowerShell")

As you can see.  TLS 1.3 is not supported yet.


Enjoy

Jeff
