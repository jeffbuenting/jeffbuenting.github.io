---
layout: post
title:  "Powershell DSC SendConfigurationApply Error"
date:   2016-11-06 13:23:00 -0400
categories: Powershell DSC
---
# Powershell DSC SendConfigurationApply Error # 

Learning Powershell Desired State Configuration (DSC) and discovering that there is very little 'official' documentation.  Or at least I have not found it.  That means as I run into problems, I have had to find someone else who has had this issue or figure it out myself.

So today I am playing with Powershell DSC and partialconfigurations. https://msdn.microsoft.com/en-us/powershell/dsc/partialConfigs  I have found many that don't seem to like them, but I think they can be useful.  I had both configs working indivually so I know the .MOF files are correct.  But when I tried to add the second config to the local config, I got a somewhat cryptic SendConfigurationApply error.  (unfortunately I do not have a snapshot of this error.  I will get one when I get a chance.)

Is this a problem with the MOF on my pull server, the local config, or something else.  Turns out to be both a procedural and local error.  Seems that when modifying the pull server MOFs the local configs can get confused.  Easiest way to resolve that is to flush the local MOFs and pull the configs from the server again.  Run this command to flush the local MOFs:

`get-childitem c:\windows\system32\configuration -Filter *.mof* | Remove-Item`

The procedural error may have solved this issue in the first place.  When running the `Start-DSCConfiguration' cmdlet, include the -Force switch.  This seems to work around this issue.

Here are the procedures to pull from a DSC Server

1. Build local MOF   

2. Set the Local Configuration Manager (LCM) with the MOF configs   
*  `Set-DSCLocalConfigurationManager -path c:\dscconfig -force -verbose   

3. Build the partial configs into one complete config on the server   
*  `Update-DSCConfiguration -Wait -Verbose`   

4. At this point you can wait for the next pull cycle or force a pull with the following command    
*  `Start-DSCConfiguration -UseExisting -Wait -Force -Verbose`

### External Resources ###
- Partial Configuration discussions
*   https://powershell.org/forums/topic/multiple-configurationnames/
*   https://blog.wouterspaans.nl/2015/07/20/what-lego-has-to-do-with-powershell-dsc-partial-configurations/
*   https://automationnext.wordpress.com/2016/04/19/powershell-desired-state-configuration-partial-configurations-without-configurationid/

- Powershell DSC Logs
*   https://blogs.msdn.microsoft.com/powershell/2014/01/03/using-event-logs-to-diagnose-errors-in-desired-state-configuration/
*   https://www.penflip.com/powershellorg/the-dsc-book/blob/master/trouble-shooting-dsc-and-configurations.txt
*   

- Flush Mof files
*   http://stackoverflow.com/questions/25877068/how-do-i-resolve-cannot-invoke-the-sendconfigurationapply-method-when-perform


Enjoy

Jeff


