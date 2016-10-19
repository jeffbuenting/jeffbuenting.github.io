---
layout: post
title:  "Calling Powershell from a Scheduled Task"
date:   2016-10-19 11:23:00 -0400
categories: Powershell IIS
---
# Calling Powershell from a Scheduled Task # 

A recent conversation reminded me why your notes are so important.  Especially when you don't do something all the time ( thank you Scott for the reminder. )  We were discussing how he had forgotten how to do something and couldn't find his notes.  Now he had to research it all over.  I chuckled thinking this won't happen to me anywmore now that I have my notes up here on Github.  The last laugh is on me tho as I now need to add a scheduled task that calls a powershell script and I can't remember how to do it.  So, that is where this post is born.

Without further ado, here are the steps to create a scheduled task using powershell and a script file.

1. Create a Basic Task  
![CreateBasicTask]({{ site.url }}/Images/2016-10-19-CallingPowershellScriptFromScheduledTask/Step1-CreateBasicTask.PNG)
  
2. Define the Trigger  
  
3. For the Action select Start a Program  
![StartAProgram]({{ site.url }}/Images/2016-10-19-CallingPowershellScriptFromScheduledTask/Step3-StartAProgram.png)  
  
4. Here the Tricky part that I always forget
* Program/Script should point to the full path to powershell (C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe)
* Add Arguments should point to the Powershell Script file and look like this:
  * -File c:\scripts\Delete-TempFiles.ps1  
  * Any other powershell arguments can be added here as well  
![Programandarguments]({{ site.url }}/Images/2016-10-19-CallingPowershellScriptFromScheduledTask/Step4-Programandarguments.png)  

And that is it.

Enjoy

Jeff


