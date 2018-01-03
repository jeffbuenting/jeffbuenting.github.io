---
layout: post
title:  "Extending SCCM Hardware Inventory"
date:   2018-01-03 10:21:00 -0400
categories: ADFS Powershll
---
# Extending SCCM Hardware Inventory #

Hardware Inventory is useful to collect information about the hardware configuration of clients.  It collects data about the machine via WMI.  Counterintuitive, this is also where the add/Remove programs data is collected.

Since data is collected via WMI, it makes sense that we can extend the information to custom classes.

Steps to extend Hardware Inventory

1. Create the MOF file to define the information you want to collect 
2. Backup the ...\Inboxes\CliFiles.src\HInv\Configuration.MOF
3. Copy the contents of the newly created MOF file between the add extension here at the bottom of the configuration.MOF
4. Manually compile this modified Configuration.MOF
5. Add the new class to the Hardware Inventory in the SCCM Client Policy

These two site give good examples on how to do this

https://www.mnscug.org/blogs/sherry-kissinger/422-dot-net-frameworks-mof-edit 

http://schadda.blogspot.com/2012/02/sccm-2012-customize-extend-hardware.html  

NOTE:  the report in the Minnesota website is not an SCCM Query.  See the following if that is what you need

```WQL
select SMS_R_System.NetbiosName, 
       SMS_G_System_DOTNETFRAMEWORKS.BuildNumber, 
	   SMS_G_System_DOTNETFRAMEWORKS.Version, 
	   SMS_G_System_DOTNETFRAMEWORKS.ServicePack, 
	   SMS_G_System_DOTNETFRAMEWORKS.Installed 
   from  SMS_R_System 
   inner join SMS_G_System_DOTNETFRAMEWORKS 
	  on SMS_G_System_DOTNETFRAMEWORKS.ResourceID = SMS_R_System.ResourceId 
   order by SMS_R_System.NetbiosName
```
