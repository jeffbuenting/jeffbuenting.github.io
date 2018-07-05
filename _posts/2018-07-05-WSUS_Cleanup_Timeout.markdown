---
layout: post
title:  "How to fix WSUS Server Cleanup Timeouts"
date:   2018-07-05 15:38:00 -0400
categories: WSUS SQL Powershell
---


For the past 1.5 weeks, I have been trying to resolve a timeout issue whenever I attempted to run the WSUS Cleanup wizzard.  This was happening for both the gui and the powershell script.  Apparently running the script I posted in a previous blog entry once per month is not enough.  I am going to schedule it for once per week.

But like I said, it won't run until I resolve the timeout issue.  I tried several different articles until I found this one.  <http://pnijjar.freeshell.org/2016/wsus-stuck/> Using the SQL script allowed me to clean up uneeded or unused updates.  Once that was done the cleanup wizard worked again.

Run this script against the SUSDB on the SQL server.

{% highlight ruby %}
USE SUSDB
DECLARE @var1 INT, @curitem INT, @totaltodelete INT
DECLARE @msg nvarchar(200)
CREATE TABLE #results (Col1 INT) INSERT INTO #results(Col1)
EXEC spGetObsoleteUpdatesToCleanup
SET @totaltodelete = (SELECT COUNT(*) FROM #results)
SELECT @curitem=1
DECLARE WC Cursor FOR SELECT Col1 FROM #results
OPEN WC
FETCH NEXT FROM WC INTO @var1 WHILE (@@FETCH_STATUS > -1)
BEGIN SET @msg = cast(@curitem as varchar(5)) + '/' + cast(@totaltodelete as varchar(5)) + ': Deleting ' + CONVERT(varchar(10), @var1) + ' ' + cast(getdate() as varchar(30))
RAISERROR(@msg,0,1) WITH NOWAIT
EXEC spDeleteUpdate @localUpdateID=@var1
SET @curitem = @curitem +1
FETCH NEXT FROM WC INTO @var1
END
CLOSE WC
DEALLOCATE WC
DROP TABLE #results
Download SQL Manage
{% endhighlight %}

It will take awhile and may error out.  But running it again should complete and delete the updates.