---
layout: post
title:  "Configure Self hosted WebApi in Windows Service to Use SSL"
date:   2017-08-03 10:57:00 -0400
categories: SSL,Service,API
---
# Configure Self hosted WebApi in Windows Service to Use SSL #

We have recently deployed microservices to take the place of an all encompassing web app.  One of these Microservices actually responds to HTTP requests.  Of coure we need this to be secure.  But how do you bind an SSL Cert to a Windows service?

Thanks to this article we were able to get it working.  [https://vineetyadav.com/development/net/configure-self-hosted-webapi-in-windows-service-to-use-ssl.html>]

## Summary ##

Use the following command to register the SSL with port.

{% highlight ruby %} 
netsh http add sslcert ipport=0.0.0.0:8099 appid={214124cd-d05b-4309-9af9-9caa44b2b74a} certhash=‎c262318f5f1356f7f04961146efd1be743c0e9c0
{% endhighlight %}

PS: The appId is application identifier to refer to http and should remain same. Also, the ipport=0.0.0.0:8099 mean, listen on all IPs on 8099 port number.

The same can be verified using following command:

{% highlight ruby %} 
netsh http show sslcert
{% endhighlight %}

The same can also be deleted using following command.
{% highlight ruby %} 
netsh http delete sslcert ipport=0.0.0.0:8099
{% endhighlight %}

Save this and restart the windows service