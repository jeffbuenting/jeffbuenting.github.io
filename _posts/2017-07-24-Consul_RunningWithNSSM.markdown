---
layout: post
title:  "Running Consul with Non-Sucking Service Manager (NSSM)"
date:   2017-07-24 10:57:00 -0400
categories: Consul,NSSM,Microservices
---
# Running Consul with Non-Sucking Service Manager (NSSM) #

Consul ( https://www.consul.io/ ) is a tool for discovering and configuring services in your infrastructure.  In order to get it to run as a service ( So no one has to be constantly logged into the server) you need to wrap it in a tool to convert it to a service.  I am using the Non-Sucking Service Manager (NSSM) (https://nssm.cc/) to accomplish this.

This is the command line I run to get it to run as a service.

{% highlight ruby %}   
 "c:\program files\NSSM\win64\nssm.exe" install Consul "c:\program files\consul\consul.exe" agent -config-file """c:\program files\consul\basic_config.json"""
{% endhighlight %}

Notice the triple """ around the config file path.  Without it, the new service will not find the config file during start up.

One other thing to note.  In the config json file any windows path with a backslash (\) needs two (\\).  An example can be seen below

{% highlight ruby %}   
{
  "datacenter": "DC1",
  "data_dir": "c:\\program files\\consul\\data",
  "log_level": "INFO",
  "node_name": "Server1",
  "server": true
}
{% endhighlight %}