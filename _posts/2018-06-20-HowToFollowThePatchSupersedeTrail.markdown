---
layout: post
title:  "How to Tell What patches are superseded by which patches"
date:   2018-06-20 14:48:00 -0400
categories: SCCM
---

We use Tenable to scan our systems for vulnerabilities.  When the report lists an older patch that needs to be installed or more likely a registry key enabled, alot of the times that patch has been superseded.  In SCCM, I run a scheduled powershell script to clean up the superseded and expired patches to keep the number of patches downloaded to as small as possible.  Unfortunately this means I no longer can find information about that patch in SCCM.  To find the new patch I follow the following process.

  1. Search Microsoft Update catelog for the old patch.
  -- http://www.catalog.update.microsoft.com/home.aspx 
  
  2.  open the Patch info and look at the Package Detail tab
  -- This tab has information on which patches have been replaced by this one.  And which patches this one replaced.
  
  3.  Repeat until I find the newest patch that is still in SCCM.
  
  I can now use this to deploy the registry fix by creating an application for the fix and deploying it to a collection based on if the new patch has been installed.


Enjoy

Jeff
