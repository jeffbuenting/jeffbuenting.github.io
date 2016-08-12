---
layout: post
title:  "Allowing Password Expiration Notification thru ADFS"
date:   2016-08-12 14:23:00 -0400
categories: ADFS AD Password
---
# Allowing Password Expiration Notification thru ADFS # 

I strongly dislike attempting to log into a website or application and not getting a message letting me know my password has expired.  Just a blank screen.  And what about getting a warning letting you know your password will expire in a couple of days.  Wouldn't that be nice.  Internal apps can do this, but what about hosted apps.  My company provides hosted Microsoft Dynamic CRM platforms for our customers.  We use ADFS to connect CRM to AD.  With ADFS 2.0 and below, you could not enable password expiration messages to flow thru the logon process.  So users were always calling to figure out why they couldn't log on.  

ADFS 3.0 to the rescue.  This version allows you to change your password and sends notifications when your password is within 14 days of expiring.  This is how you set it up.

First, you need to enable ADFS password updating endpoint.

![ADFS Enable Password Update Endpoint](http://http://jeffbuenting.github.io/images/ADFSEnableUpdatePassword.PNG)

Second, add a new claim rule to your relying party trust.

{% highlight ruby %} 
c1:[Type == "http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime"]
=> issue(store = "_PasswordExpiryStore", types = ("http://schemas.microsoft.com/ws/2012/01/passwordexpirationtime", "http://schemas.microsoft.com/ws/2012/01/passwordexpirationdays", "http://schemas.microsoft.com/ws/2012/01/passwordchangeurl"), query = "{0};", param = c1.Value);
{% endhighlight %} 

![ADFS Password Expiry Claim Rule](http://http://jeffbuenting.github.io/images/ADFSPasswordExpiryClaimRule.PNG)

That is all there is to it.  Alot simpler than one would imagine.

Enjoy

Jeff

#### External Resources: ####    
https://technet.microsoft.com/en-us/library/dn864829(v=ws.11).aspx

https://blogs.msdn.microsoft.com/samueld/2015/05/13/adfs-2012-r2-now-supports-password-change-not-reset-across-all-devices/