---
layout: post
title:  "VMWare PowerCLI Install and Problems"
date:   2018-12-04 11:30:00 -0400
categories: Powershell VMWare
---


Finally got my elevated priveledges for my new job.  This allowed me to install VMWare's PowerCLI.  Man, have I been looking forward to doing some vmware powershelling.  Installing has never been easier.  Follow these steps to install from the powershell gallery.  which makes it easy.  

```Powershell
install-module vmware.powercli
```

If you have a system that does not have internet access.  Then you can save the module to a machine that does and manually copy the files.

```Powershell
Save-Module vmware.powercli
```

Of course once I had it installed, I ran into a couple of issues.  The first issues was this error when importing the module

     Could not load file or assembly ‘log4net, Version=1.2.10.0, Culture=neutral, PublicKeyToken=692fbea5521e1304’
     or one of its dependencies. The system cannot find the file specified

Fortunately I found this article <https://infiniteloop.io/vmware-powercli-10-setup/>, which showed how to resolve the issue.  Basically copy the log4net file from the VMware.VimAutomation.Sdk folder  to C:\Windows\assembly\GAC_64\log4net\1.2.10.0__692fbea5521e1304.  You may need to create that location.

So that problem is resolved.  But then there was this

     Could not establish trust relationship for the SSL/TLS secure channel

Again, this took me awhile to figure out what the deal was. Turns out it was the certificate was not trusted by my workstation.  To get aground this you need to ignore certificate errors.  See this article <https://communities.vmware.com/thread/584530>

Once those issues were fixed.  PowerCLI works and I was happy.

Enjoy


Jeff