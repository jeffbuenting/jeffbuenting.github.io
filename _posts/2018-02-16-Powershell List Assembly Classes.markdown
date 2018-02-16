---
layout: post
title:  "PowerShell List Classes in an Assembly"
date:   2018-02-16 14:20:00 -0400
categories: SCCM
---
# PowerShell List Classes in an Assembly #

Ever wonder how to find out what assemblies and classes are available to you in powershell?  

To list the assemblies currently loaded run the following command

```PowerShell
[appdomain]::currentdomain.getassemblies()
```

Once you have this information you can then get the assemblies in each 

```PowerShell
$Assembly = [appdomain]::currentdomain.getassemblies | where fullname -like *xrm*
$assembly.GetTypes() | ? {$_.IsClass -and $_.IsPublic}
```

Thanks to these two websites for the information.

Assemblies Loaded in Powershell
https://richardspowershellblog.wordpress.com/2007/09/30/assemblies-loaded-in-powershell/     

List Classes in an Assembly
https://social.technet.microsoft.com/Forums/en-US/b135b226-4b5d-4e00-9b3a-7bcb0d5b2123/reflection-get-all-classes-in-an-assembly?forum=winserverpowershell

Enjoy

Jeff
