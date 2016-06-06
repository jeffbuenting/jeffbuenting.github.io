---
layout: post
title:  "SQL Table Variables"
date:   2016-06-06 10:38:00 -0400
categories: SQL
---
# Table Variables #

Temporary Table created to hold a set of data.  Automatically cleaned up at end of query.

### Example ###

Creates the Table adds data and displays all the data

{% highlight ruby %}
USE [MyDB]

DECLARE @SourcesToDelete TABLE (Name VARCHAR(512) )
INSERT INTO @SourcesToDelete VALUES ('WhatsUp')
INSERT INTO @SourcesToDelete VALUES ('GoAway')

select * from @SourcesToDelete
{% endhighlight %}

### Resources ###
* https://msdn.microsoft.com/en-us/library/ms175010.aspx   
   * https://msdn.microsoft.com/en-us/library/ms188927.aspx   
* https://www.simple-talk.com/sql/t-sql-programming/temporary-tables-in-sql-server/   
* http://odetocode.com/articles/365.aspx    
