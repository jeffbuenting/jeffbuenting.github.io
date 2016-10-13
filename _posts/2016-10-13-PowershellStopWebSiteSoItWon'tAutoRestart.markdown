---
layout: post
title:  "Stop IIS Website so it won't Auto Restart with Powershell"
date:   2016-10-13 11:23:00 -0400
categories: Powershell IIS
---
# Stop IIS Website so it won't Auto Restart with Powershell # 

Have you ever performed an `IISReset` and noticed that websites that were stopped via the GUI stay in a stopped state after the reset?  This is what most would consider expected behavior.  Now the powershell enthusiast in you would start to think, "Lets do this via powershell."  After all `Get-Website`,`Stop-Website` and `Start-Website` should take care it.  And you would be wrong.  Ok, only partially wrong.  See, when you set the website to Stopped via the GUI, not only does it stop the website, it also sets the serverAutoStart attribute to False.  Using the `Stop-Website` does not change this attribute.  So an `IISReset` starts the website.

To resolve this you must set the attribute using `Set-ItemProperty`.

{% highlight ruby %} 
$WebSite = Get-Website -Name JeffsPage 
set-itemproperty -Path "iis:\sites\$($WebSite.Name)" -Name serverAutoStart -Value False
$WebSite | Stop-Website
{% endhighlight %} 

Note the attribute name is case sensitive.

Now when you do an `IISReset`, the stopped website will stay that way.  

To change the setting back so that the default behavior is active,  simply set the attribute to True.

Enjoy

Jeff

#### External Resources: ####    
<http://serverfault.com/questions/703373/stopping-iis-websites-permanently-with-powershell>
