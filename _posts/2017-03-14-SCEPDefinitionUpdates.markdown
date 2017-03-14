---
layout: post
title:  "System Center Endpoint Protection Definition Updates"
date:   2017-03-14 11:30:00 -0400
categories: SCCM Powershell WSUS SCEP Antivirus Malware
---
# System Center Endpoint Protection Definition Updates #

Let me begin by saying [Happy PI Day](https://en.wikipedia.org/wiki/Pi_Day) !

Today I was examining my newly setup SCCM Endpoint Protection and I noticed that the update definitions where not being pushed to the clients.  Now that is an issue. Because I wanted to control where the updates came from, I was using Config Manager to download and push the update.  Unfortunately this only works during an SCCM Maintenance WIndow.  Since I am unwilling to have maintenance windows defined every 8 hours during the day SCCM can't push the definitions.  I have been burned by maintenance windows in the past and am a bit gun shy.

So how do you do this?  Easy.  Follow these two articles and you should be good

Create Scheduled task to automatically download definitions to a shared folder. <https://gallery.technet.microsoft.com/scriptcenter/SCEP-Definition-Updates-to-fde57ebf> 

Configure SCEP Policies in SCCM to allow downloading definitions from a UNC Path. <https://blogs.technet.microsoft.com/charlesa_us/2015/05/20/configmgr-2012-how-to-deploy-scep-definition-updates-via-unc-share-for-isolated-environment/>