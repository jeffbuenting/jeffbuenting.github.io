---
layout: post
title:  "Find available COM objects and WMI Classes with Powershell"
date:   2016-10-06 14:23:00 -0400
categories: Powershell
---
# Find available COM objects and WMI Classes with Powershell # 

When attempting to build powershell functions to an application without a documented API, one trick I have learned is to take a look at the WMI classes and COM Objects to see what they offer.  Here is how to list both WMI and COM objecs.

### WMI ###

{% highlight ruby %} 
get-wmiobject -List | ft name
{% endhighlight %} 

Simple and to the point.  This Lists all WMI Classes that are available on the computer.

### COM ###

{% highlight ruby %} 
Get-ChildItem HKLM:\Software\Classes -ErrorAction SilentlyContinue | Where-Object { $_.PSChildName -match '^\w+\.\w+$' -and (Test-Path -Path "$($_.PSPath)\CLSID")} | Select-Object -ExpandProperty PSChildName
{% endhighlight %} 

Little bit more complicated but the end results are the same

From here you can start playing with any interesting objects to see what they have to offer.

Enjoy

Jeff

#### External Resources: ####    
<https://social.technet.microsoft.com/Forums/scriptcenter/en-US/d61d53fa-2c40-4f12-8626-693728f09f67/how-do-i-list-all-classes-in-powershell?forum=ITCG>

<http://www.powershellmagazine.com/2013/06/27/pstip-get-a-list-of-all-com-objects-available/>
