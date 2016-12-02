---
layout: post
title:  "List Assemblies loaded in powershell"
date:   2016-12-02 09:23:00 -0400
categories: Powershell 
---
# List Assemblies loaded in powershell # 

How do you list what assemblies are loaded in PowerShell?  In the past I have had to troubleshoot scoping issues trying to determine if a specific assembly was loaded in a specific scope.  I finally found this as a way to display the assemblies.

{% highlight ruby %} 
[appdomain]::CurrentDomain.GetAssemblies()
{% endhighlight %} 


Enjoy

Jeff

#### External Resources: ####    
https://social.technet.microsoft.com/Forums/windowsserver/en-US/7577cc21-cf56-48ad-a97c-4b3235cd6e39/how-to-list-assemblies?forum=winserverpowershell
