---
layout: post
title:  "VMWare Ramdisk is out of space"
date:   2019-04-01 20:30:00 -0400
categories: VMWare
---


Long time no post.  So during my routine morning VMWare log review, I came across an error message in the event log that indicated the VMWare Ramdisk was full and files could not be written to.  To view information about the ramdisk, SSH into the ESXi host and run the follow:

'vdf -h'

with this command you can see how much free space each ramdisk is using.

Now you can list all files on that ramdisk to determine what file is taking up all of the room.

'ls -lah' 

will give you that informaion.

In my case, it was the ql_ima.log filling up.  This is a QLogic log file.  The fix is to install an updated driver for your specific model of NIC.  Or, if you have no need for that particular log file, you can disable is by deleting (or renaming) the ql_ima.cfg file.  Restart the hostd and vpxa services and delete the ql_ima.log file.


NOTE:  for a powershell function to view the ramdisk information see my Get-VMWareRamdisk function in my VMWare module.

<https://github.com/jeffbuenting/VMWare>

enjoy

Jeff