---
layout: post
title:  "Connecting to WID via SSMS"
date:   2021-08-31 18:49:00 -0400
categories: WSUS SQL WID
---

When you need to run SQL queries directly against a WID DB, you can connect via SSMS.  They key is to run SSMS as admin and connect to the following DB Server:

```\\.\pipe\MICROSOFT##WID\tsql\query```

use Windows Authentication

and there you go.

And if using powershell, connect to ```np:\\.\pipe\MICROSOFT##WID\tsql\query``` as the instance.

Also, remember, WID cannot be connect remotely, you must be on the same server.

Enjoy

Jeff