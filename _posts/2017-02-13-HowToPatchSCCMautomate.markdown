---
layout: post
title:  "How to Automate Patching with SCCM"
date:   2017-02-13 2:21:00 -0400
categories: SCCM Powershell
---
# How to Automate Patching with SCCM #

Patching is a pain in the a, I mean, neck.  SCCM has improved it somewhat but it still requires human intervention.  I have finally worked out how to completely automate my patching, And it even scales.  

### Set up SCCM with a Software Update Point. ###
- This will require WSUS installed in your environment.

- Configure SUP to synchronize with WSUS on a schedule of your choosing.
  -	Since we wanted to patch weekly, we chose to sync once every Wednesday.

- Create an ADR (Automatic Deployment Rule)
  - This selects, downloads and deploys(to nothing) the updates you want.  
   - All patches for the past 30 days
   - Download and add to Patches deployment package
    - Really this should be named after the year.  So each year it would get renamed
   - ADR is scheduled for 3 pm each Tuesday

- Powershell scheduled task to move updates to current year’s update group and deploy
  - The following runs as a sched task every Tuesday at 5 pm.  
   - It copies all patches from the ADR Deployment group to the yearly patching group.
   - And deploys

{% highlight ruby %}   
<#
    .Description
        copies the patches downloaded via ADR to yearly software update group.  Then deploys the update group.

    .Note
        Author: Jeff Buenting
        Date: 28 Jul 2015
#>

$Year = Get-Date -UFormat %Y

$SoftwareUpdateGroup = "$Year - Patches"
$Collection = "Deploy Patches"

import-module ($Env:SMS_ADMIN_UI_PATH.Substring(0,$Env:SMS_ADMIN_UI_PATH.Length-5) + '\ConfigurationManager.psd1')

set-location RWV:
        
# ----- "If Software Update Group does not exist, Create it.  This is needed for year changes."
if ( (Get-CMSoftwareUpdateGroup -Name $SoftwareUpdateGroup) -eq $Null ) {
    New-CMSoftwareUpdateGroup -Name $SoftwareUpdateGroup -UpdateId (Get-CMSoftwareUpdate -UpdateGroupName 'Patches').CI_ID
}

# ----- Copy patches from ADR SUG to Year SUG
Get-CMSoftwareUpdate -UpdateGroupName 'Patches' | Add-CMSoftwareUpdateToGroup -softwareUpdateGroupName $SoftwareUpdateGroup

# ----- This check is required as deploying with same name will have duplicate deployments
$ExistingDeployment = Get-CMDeployment -CollectionName $Collection | where SoftwareName -eq $SoftwareUpdateGroup
$ExistingDeployment

if ( $ExistingDeployment ) {
    # ----- Write-Verbose "Deployment $($ExistingDeployment.SoftwareName) Found for collection $Collection"
    $ExistingDeployment | Remove-CMDeployment -Force
}

# ----- Deploy Software update
Start-CMSoftwareUpdateDeployment -SoftwareUpdateGroupName $SoftwareUpdateGroup -CollectionName $Collection -DeploymentName "Patching" -DeploymentType Required -EnforcementDeadlineDay (get-date -UFormat '%Y/%m/%d')
{% endhighlight %}

   
- Create Patching Collections.
  -	Sub collections are part of the master Patching collection.  This allows you to deploy to only one collection and have different maintenance windows on each sub collection 


### Optional SCVMM Hyper-v Servers ###
- Setup Client on Hyper-V hosts.  Exclude them from patching.  This is strictly for reporting
- Powershell Scheduled task to migrate updates from SCCM Current year softwear update group to SCVMM.
 
{% highlight ruby %}   
Import-Module C:\Scripts\scvmm\SCVMM_Module.psm1 -force

$Year = Get-Date -UFormat %Y

Get-CMSoftwareUpdate -UpdateGroupName "$Year - Patches" | Select-Object -ExpandProperty ArticleID | Get-SCVMMUpdate  -Products 'Windows Server 2012 R2','Microsoft Azure Site Recovery Provider' | Add-SCVMMUpdateToBaseline -BaselineName "$Year - Patches" -Verbose 

{% endhighlight %}
 

- SCVMM setup schedule to execute consistency check and remediate if needed. 



