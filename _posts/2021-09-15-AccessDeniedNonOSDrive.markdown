---
layout: post
title:  "Access Denied When Connecting to a NON-OS Drive"
date:   2021-09-15 08:14:00 -0400
categories: Windows VMware GPO
---

## Issue   
New VMware Windows 2016 VM that will not allow access to non-OS drives.  Looking at the permissions show we have full access.  But still receiving access denied.  This appears to happen randomly.  It would work for awhile and allow access.  And then not work.  Usually after a reboot.   

Restore to an old snapshot did not make a difference.  Removing and rejoining the domain would work for awhile but then, WHAM! The GPO would get applied and things would come to a screeching halt.   

It is important to note that even the service accounts and the system account could not access the drives.  So in our case the web sites were down and the services would not start.

![AccessDeniedImage](..\Images\2021-09-15-AccessDeniedNonOSDrive/AccessDenied.jpg)
	 
## Resolution   
After some research, I found that with a hot swappable SCSI drive on a VMware VM, windows considers that a removable storage device.  Think USB drive.  This article describes the problem we were having and points to a Windows Active Directory Group Policy Object: https://serverfault.com/questions/944051/suddenly-inaccessible-local-drivess-on-several-windows-server-2016-esxi-6-5.  Using this new found info, I found this article on the affects of the default VMware setting of hotplug SCSI drives and Windows Removble Storage GPOs: https://kb.vmware.com/s/article/2097525.    

Ok, now how to we fix this?  Obviously we could back out the GPO with the setting.  That seemed like a long call to the Enterprise AD trying to explain why they should care, probably taking more time than we had.  The other was to follow this article and set hotplug capability for the affected VM.  https://kb.vmware.com/s/article/1012225.  This second solution is the one I chose to follow.  Testing in my lab showed it worked.  Even with the GPO still applied.  And finally applying to the client machine proved it worked there as well, thus allowing us to complete the deployment.

## Side Effects   
Will setting **devices.hotplug** to false cause any issues?  Not for the server itself.  If the Server Admin wants to hot add a NIC or another drive, they won’t be able to unless the Server is powered off.  So some convenience is lost, but this doesn’t happen that often.


Enjoy

Jeff