---
layout: post
title:  "SCCM Application Script Detection Method"
date:   2017-08-20 11:46:00 -0400
categories: Powershell SCCM
---
# SCCM Application Script Detection Method #

SCCM 2012 R2 introduced applications.  They differ from packages in that they are much more flexible.  One of the benifits is they have various ways to detect if the application is installed.  These are called Detection Methods.  It is possible to use a script for this.  But there are some specific rules on what is returned via the various output streams. 

Use the following table to determine how you can use the output from a script to determine whether an application is installed.

Script exit code | Data read from STDOUT | Data read from STDERR | Script result | Application detection state
 --- | --- | --- | --- | -----
0 | Empty | Empty | Success | Not installed
0 | Empty | Not empty | Failure | Unknown
0 | Not empty | Empty | Success | Installed
0 | Not empty | Not empty | Success | Installed
Non-zero value | Empty | Empty | Failure | Unknown 
Non-zero value | Empty | Not empty | Failure | Unknown
Non-zero value | Not empty | Empty | Failure | Unknown
Non-zero value | Not empty | Not empty | Failure | Unknown
 
 
 For more information see the following technet article
 https://technet.microsoft.com/en-us/library/gg682159.aspx#BKMK_Step4
 
 
