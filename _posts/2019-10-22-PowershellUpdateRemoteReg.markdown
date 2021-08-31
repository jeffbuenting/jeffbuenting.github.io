---
layout: post
title:  "Powershell Update Remote Registry"
date:   2019-10-22 11:11:00 -0400
categories: Powershell Registry
---


I never can remember the correct way to remotely examine and update the registry with Powershell.  So, for posterity, lets add it here.

The current way to do it is with `Get-ItemProperty`.  This will get the entire registry key object's properties.  More info than we really need.  to get just the key's value, you need to do this.  `(Get-ItemProperty -path $RegPath -Proptery $Property).$Property`

To check if the Registry key exists:   
```Powershell
if ( -not (Get-ItemProperty -path $RegPath -Proptery $Property).$Property )   
```   

To check if the Registry key's value matches:   
```Powershell
if ( (Get-ItemProperty -path $RegPath -Proptery $Property).$Property -eq $Value )
```

To Create the key if it does not exist:
```Powershell
New-ItemProperty -Path $RegPath -Property $Property -PropertyType $Type   
```   

And finally to update the value:
```Powershell 
Set-ItemProperty -path $Regpath -Name $Property -Value $Value   
```

And to do this on a remote registry, wrap it in an `Invoke-command` statement.

enjoy

Jeff