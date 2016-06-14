---
layout: post
title:  "Copy ADFS Claim Rules with Powershell"
date:   2016-06-14 10:38:00 -0400
categories: ADFS CRM Powershll
---
# Copy ADFS Claim Rules with Powershell #

At my job we create a lot of ADFS relying party trusts for our Dev, QA and Lab Mircosoft CRM environments.  It gets a bit tedious entering the claim rules over and over.  I finally took the time to learn how to do this via powershell.  And it is really simple.


### Export ADFS Claim rules ###

First Export the claim rules fo a file from a working ADFS Relying Party Trust.  All you need is the Issuance Transfor Rules.   
 
{% highlight ruby %}   
Get-ADFSRelyingPartyTrust -name “Jeffb01 – External” | Select-Object -ExpandProperty IssuanceTransformRules | out-file c:\temp\claimrules.txt
{% endhighlight %}


### Import ADFS Claim Rules ###

Once you have the rules stored in a text file, you can import them into your new relying party trust.

{% highlight ruby %}    
Set-ADFSRelyingPartyTrust -TargetName “portalQS – External’ -IssuanceTransformRulesFile c:\temp\claimrules.txt   
{% endhighlight %}
