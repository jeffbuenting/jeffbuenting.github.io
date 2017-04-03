---
layout: post
title:  "Quick SQL Queries to find Tables, Columns and Stored Procedures"
date:   2017-04-03 13:16:00 -0400
categories: SQL
---
# Quick SQL Queries to find Tables, Columns and Stored Procedures #

Found these queries on this website.  https://cavemansblog.wordpress.com/2009/02/26/sql-how-to-2/

Including them here so I don't forget where I got them.  For more information follow the link above.

{% highlight ruby %}   
--Query to fetch the list of table that match a pattern
select * from information_schema.tables where table_name like '%Employee%'
 
--Query to fetch the list of Columns (with table info) that match a pattern.
select * from information_schema.columns where column_name like '%EmpId%'
 
--Query to fetch the list of Stored Procedures that match a pattern.
select ROUTINE_SCHEMA, ROUTINE_NAME, ROUTINE_DEFINITION from INFORMATION_SCHEMA.ROUTINES
where ROUTINE_TYPE='PROCEDURE' and routine_name like '%MyProc%'
 
{% endhighlight %}

