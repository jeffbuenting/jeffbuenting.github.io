---
layout: post
title:  "Bypass Powershell Execution"
date:   2018-11-23 08:38:00 -0400
categories: Powershell
---


Happy Thanksgiving everyone!

I recently ran into an issue where the powershell execution policy of the computer I was working on was set to restricted.  Meaning I could not run any scripts to gather information I needed to troubleshoot an issue.

Fortunately, I found this article by Scott Sutherland.

<https://blog.netspi.com/15-ways-to-bypass-the-powershell-execution-policy/>

Of particular interest to me, is setting the execution policy for the current user.

```Powershell
Set-Executionpolicy -Scope CurrentUser -ExecutionPolicy UnRestricted
```

This sets the execution policy for the current user and does not require admin rights.

Very helpful

Thank you Scott

Enjoy 

Jeff