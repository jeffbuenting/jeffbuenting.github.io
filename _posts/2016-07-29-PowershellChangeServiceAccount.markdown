---
layout: post
title:  "Powershell Changing a Service's logon account"
date:   2016-07-29 16:15:00 -0400
categories: Powershll
---
# Powershell Changing a Service's logon account # 

Since I have been working on automating our software installs, I have had to create services and modify the service's properties during this process.  I have used the SC.exe command line method, but this doesn't feel very 'Powershelly'.  After that I used the Get-WMIObject.  This worked but as we all know, Get-WMIObject is somewhat depricated.  As The Microsoft Scripting guy says in this post( https://blogs.technet.microsoft.com/heyscriptingguy/2014/11/02/weekend-scripter-simplify-to-troubleshoot-powershell-script/), 

"Since Windows PowerShell 3.0, I can use Get-CimInstance. It is faster and more robust, and it permits lots of cool things for retrieving data (such as using Cim-Sessions"

So Next I moved on to using Get-CimInstance.  This link explains both the Get-WMIObject and Get-CimInstance methods.

https://4sysops.com/archives/managing-services-the-powershell-way-part-8-service-accounts/

Enjoy

Jeff