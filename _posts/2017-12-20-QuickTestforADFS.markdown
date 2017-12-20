---
layout: post
title:  "Quick test to make sure ADFS is Working"
date:   2016-06-14 10:38:00 -0400
categories: ADFS Powershll
---
# Quick test to make sure ADFS is Working #

If you follow several of the ADFS step-by-step installs found on the internet, several of them use a functional test of connecting to https://<FQDN ADFS>/adfs/ls/idpinitiadedsignon.  This works with no problems for Windows 2012 R2 ADFS 3.0.  Windows 2016 ADFS 4.0, not so much.  The problem is that this endpoint is not enabled by default in 2016.

I will leave it up to Rhoderick Milne of Microsoft to fully explain.

https://blogs.technet.microsoft.com/rmilne/2017/06/20/how-to-enable-idpinitiatedsignon-page-in-ad-fs-2016/

