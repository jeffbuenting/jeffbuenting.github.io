---
layout: post
title:  "WSUS Error during Cleanup"
date:   2021-08-31 18:49:00 -0400
categories: WSUS 
---

I was having problems running my WSUS cleanup script.  Running the command manually one option at a time, I ran into the following error while trying to delete unused updates:

``` powershell
invoke-wsusservercleanup -UpdateServer $W -CleanupObsoleteUpdates -verbose
invoke-wsusservercleanup : @errorMessage
spDeleteUpdate got error from spDeleteRevision
At line:1 char:1
+ invoke-wsusservercleanup -UpdateServer $W -CleanupObsoleteUpdates -ve ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidData: (Microsoft.Updat...rCleanupCommand:InvokeWsusServerCleanupCommand) [Invoke-WsusServerClea 
   nup], SqlException
    + FullyQualifiedErrorId : UnexpectedError,Microsoft.UpdateServices.Commands.InvokeWsusServerCleanupCommand
 
```
From here I recalled the timeouts I had prevously during this phase.  While I wasn't getting timeouts, it appeared the DB could not handle deleting all updates at once.  So, again, it was time to run the WSUS Cleantou Timeout SQL Script.

Enjoy

Jeff