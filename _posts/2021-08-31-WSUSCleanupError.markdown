---
layout: post
title:  "List Queries Running on SQL"
date:   2021-08-31 18:40:00 -0400
categories: SQL WSUS
---

Troubleshooting why my WSUS cleanup script was not completing correctly, I decided to see if any stored procedures were still running or hanging.  To do this you need to run the following query:

``` sql
SELECT sqltext.TEXT,
req.session_id,
req.status,
req.command,
req.cpu_time,
req.total_elapsed_time
FROM sys.dm_exec_requests req
CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS sqltext
```

And then if to stop a long running process simply use this:

``` sql
kill [session_id]
```

Enjoy

Jeff