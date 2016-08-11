---
layout: post
title:  "SQL list a Databases Stored Procedures"
date:   2016-08-01 11:07:00 -0400
categories: SQL AccidentalDBA
---
# SQL list a Databases Stored Procedures # 

As an Accidental DBA, I don't do specific tasks regular enough to remember where or how to find things.  Case in point stored procedures.  I believe I have looked them up twice in the past year.  Each time I have had to Google how to find them.  Hopefully the next time I need to do this I either remember or remember to look here.

Without further ado, this is where you can find a databases stored procedures.

1. Open SMSS
2. Expand databases
3. Expand databases
4. Expand Programmability
5. Open Stored Procedurs

And there you go, a list of stored procedures stored in the database.

Or Run this SQL Script

{% highlight ruby %} 
USE Database_MSCRM

SELECT obj.Name as SPName, 

modu.definition as SPDefinition, 

obj.create_date as SPCreationDate

FROM sys.sql_modules modu 

INNER JOIN sys.objects obj 

ON modu.object_id = obj.object_id 

WHERE obj.type = 'P' 

ORDER BY SPName
{% endhighlight %}  



http://www.sqlservercurry.com/2009/03/list-all-stored-procedures-of-database.html

Enjoy

Jeff
