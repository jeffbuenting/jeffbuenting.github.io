---
layout: post
title:  "SQL Query to list DB Role Permissions"
date:   2017-04-03 13:16:00 -0400
categories: SQL
---
# SQL Query to list DB Role Permissions #


{% highlight ruby %}   
--Change blah to your DB Role and Database to the DB you are working with
 
 Use Database
 GO
 SELECT DB_NAME() AS 'DBName'
      ,p.[name] AS 'PrincipalName'
      ,p.[type_desc] AS 'PrincipalType'
      ,p2.[name] AS 'GrantedBy'
      ,dbp.[permission_name]
      ,dbp.[state_desc]
      ,so.[Name] AS 'ObjectName'
      ,so.[type_desc] AS 'ObjectType'
  FROM [sys].[database_permissions] dbp LEFT JOIN [sys].[objects] so
    ON dbp.[major_id] = so.[object_id] LEFT JOIN [sys].[database_principals] p
    ON dbp.[grantee_principal_id] = p.[principal_id] LEFT JOIN [sys].[database_principals] p2
    ON dbp.[grantor_principal_id] = p2.[principal_id]

WHERE p.[name] = 'blah'
{% endhighlight %}

https://dba.stackexchange.com/questions/36618/list-all-permissions-for-a-given-role