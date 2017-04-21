---
layout: post
title:  "Removing Expired and Superceeded patches from updage groups and deployment packages"
date:   2017-04-03 13:16:00 -0400
categories: SCCM
---
# Removing Expired and Superceeded patches from updage groups and deployment packages #

As part of the SCCM Patching lifecycle, expired and superceeded patches need to be removed and cleaned up from the various software update groups and software update deployment packages.  Doing this manually can be a pain.  Especially when you have quite a few groups and packages to comb thru.  To automate this, I have come up with a powershell script that runs two custom cmdlets that I made to delete the updates.  This can either be run manually or better yet as a scheduled task.

In my environment I have the software update groups and Software update deployment packages named the same.  It is not required but it helps me keep everything sorted.  I split the updates up by year they were released so I have groups for 2017, 2016, etc.

Looping ing thru my update groups the first task is to get all updates that have either been expired or superceeded.

{% highlight ruby %}   
 Get-CMSoftwareUpdate -UpdateGroupName $SUGName -Fast | where { $_.issuperseded -or $_.IsExpired } 
{% endhighlight %}

Next we loop thru them and call the two custom cmdlets so they can be removed.

{% highlight ruby %}
Remove-SCCMSoftwareUpdateFromGroup -SoftwareUpdate $_ -softwareUpdateGroupName $SUGName 
Remove-SCCMSoftwareUpdateFromDeploymentPackage -SoftwareUpdate $_ -DeploymentPackage $SUGName
{% endhighlight %}

See below for a detailed discussion of each cmdlet.

Putting it all together:

{% highlight ruby %}
[CmdletBinding()]
Param()

$VerbosePreference = 'Continue'

Get-CMSoftwareUpdateGroup | Foreach {
    
    $SUGName = $_.LocalizedDisplayName

    Write-Verbose "Checking $SUGName"

    Get-CMSoftwareUpdate -UpdateGroupName $SUGName -Fast | where { $_.issuperseded -or $_.IsExpired } | Foreach {

        Remove-SCCMSoftwareUpdateFromGroup -SoftwareUpdate $_ -softwareUpdateGroupName $SUGName 
        Remove-SCCMSoftwareUpdateFromDeploymentPackage -SoftwareUpdate $_ -DeploymentPackage $SUGName -verbose

    }
}
{% endhighlight %}

### Remove-SCCMSoftwareUpdateFromGroup ###

Since I couldn't find a built in cmdlet to remove updates from a Software Update Group, I built my own.

Passing in the update or updates and the name of a software update group or groups to remove the update from.  It will default to all groups if no groups are provided.  Looping thru the groups it removes the update from the list members in each group.

{$ highlight ruby %}  

function Remove-SCCMSoftwareUpdateFromGroup {

<#
    .Synopsis
        Removes a Software update from a Software update group and deployment package.

    .Description
        Removes a Software update from a Software update group and deployment package.

    .Parameter SoftwareUpdate
        Update object that we want to remove.  use Get-CMSoftwareUpdate.

    .Parameter SoftwareUpdageGroupname

    .Example
        Remove the expired or superceeded updates from a Software Update Group

        Get-CMSoftwareUpdate -UpdateGroupName test -Fast | where { $_.issuperseded -or $_.IsExpired } | Remove-sccmSoftwareUpdateFromGroup -verbose

    .Notes
        Author : Jeff Buenting
        Date : 2017 MAR 22
#>

    [CmdletBinding()]
    Param (        
        [Parameter ( Mandatory = $True, ValuefromPipeline = $True ) ]
        [PSObject[]]$SoftwareUpdate,

        [String]$softwareUpdateGroupName
    )

    Begin {
        Write-Verbose "Getting Softwareupdate Group"
        $UpdateList = @()
        $SUGNames = @()

        if ( $SoftwareUpdateGroupname ) {
            Foreach ( $N in $SoftwareUpdateGroupName ) {
                $SUG = Get-CMSOftwareUpdateGroup -Name $N
            }
        }
        Else {
            Write-Verbose "Getting all SOftware update Groups"
           
            $SUG = Get-CMSoftwareUpdateGroup
        }

        $UpdateList = @()
    }

    Process {
        Foreach ( $S in $SoftwareUpdate ) {
            Write-verbose "Removing $($S.CI_ID)"
            
            # ----- Process each Software update Group
            Foreach ( $G in $SUG ) {
                Write-verbose "Checking Software Update Group $($G.LocalizedDisplayname)"
                Write-Verbose "Current Update Count = $($G.Updates.count)"

                $UpdateList = @()

                foreach ( $U in $G.Updates ) {
                    if ( $U -ne $S.CI_ID ) {
                        $UpdateList += $U
                    }
                }

                $G.Updates = $UpdateList

                Write-Verbose "Update count after removing listed update = $($G.Updates.count)"
            }
        }
    }

    End {
        Foreach ( $G in $SUG ) {
            Write-Verbose "Saving Updated SoftwareUpdate : $($G.LocalizedDisplayName)"
            
            $G.Put()
        }  
    }  
}  

(% endhighlight %}

### Remove-SCCMSoftwareUpdateFromDeploymentPackage ###

This one is a little more complicated as I had to go thru the relationships in multiple wmi classes to get from update to deployment package ID.  The relation is like this

SMS_SoftwareUpdate <-> SMS_CiToContent <-> SMS_PackageToContent <-> SMS_SoftwareUpdatesPackage

Again passing in the software update or updates and deployment package to remove them from the cmdlet loops thru the updates and first checks to make sure it is not a member of a software update group.  Basically if it is then it is considered deployed and it shouldn't be removed from a deployment package as that removes it from the deployment point.  If the update is not a member of a software update group then the Update CI_ID is converted via the relationship above to a PackageToontent ID and using the RemoveContent method is removed from the deployment package.

At the end after all update have been removed, the deploymnet package is refreshed on the deployment point, thus removing the files and freeing up space.

{% highlight ruby %}  

function Remove-SCCMSoftwareUpdateFromDeploymentPackage {

<#
    .Synopsis
        Removes a software update from a Software Update Deployment Package

    .Description
        If a software update is no longer a member of a Software Update Group, it should be removed from any Software update deployment packages.  This will remove it from the SCCM Deployment Point thus freeing up disk space.

    .Parameter SoftwareUpdate
        Software update to remove from the Deployment Package.

    .Parameter DeploymentPackage
        Deployment Package name.

    .Example
        Remove a software update from a software update deployment Package.

        Get-CMSoftwareUpdate - ID 1692243 -Fast | Remove-SCCMSoftwareUpdateFromDeploymentPackage -DeploymentPacage "test"

    .Example
        Remove all expired and superceeded patches from a software update group and Software Update Deployment Package.

        $SUGName = '2012 - Patches'
        Get-CMSoftwareUpdate -UpdateGroupName $SUGName -Fast | where { $_.issuperseded -or $_.IsExpired } | Foreach {
            Remove-SCCMSoftwareUpdateFromGroup -SoftwareUpdate $_ -softwareUpdateGroupName $SUGName 
            Remove-SCCMSoftwareUpdateFromDeploymentPackage -SoftwareUpdate $_ -DeploymentPackage $SUGName -verbose
        }

    .Link
        Used alot of code from Trevou Sullivan. So a big thank you to him.  I did modify it so it will work for any update passed not just delete expired and superceeded.

        https://trevorsullivan.net/2011/11/29/configmgr-cleanup-software-updates-objects/

    .Notes
        Author : Jeff Buenting
        Date : 2017 APR 19
    
#>

    [CmdletBinding()]
    Param (
        [Parameter ( Mandatory = $True, ValueFromPipeline = $True ) ]
        [PSObject[]]$SoftwareUpdate,

        [Parameter ( Mandatory = $True ) ]
        [String]$DeploymentPackage
    )

    Begin {
        Write-verbose "Getting Sotware Update Deployment Package $DeploymentPackage"
        Try {
            # ----- The built in cmdlet Get-CMSoftwareUpdateDeploymentPackage does not have a method to remove updates.  Using CIMInstance also does not return a usable method.  THerefor I must use the depricated get-WMIObject.
            $DP =  gwmi -Class sms_softwareupdatespackage -Namespace root\sms\site_rwv -filter "Name = '$DeploymentPackage'" -ErrorAction Stop
        }
        Catch {
            $EXceptionMessage = $_.Exception.Message
            $ExceptionType = $_.exception.GetType().fullname
            Throw "Remove-SCCMSoftwareUpdateFromDeploymentPackage : Error getting Software Update Deployment Package $DeploymentPackage`n`n     $ExceptionMessage`n`n     Exception : $ExceptionType"       
        }
    }

    Process {    
        Write-Verbose "Removing the following updates from $DeploymentPackage"
        Foreach ( $S in $SoftwareUpdate ) {
            # ----- If the update is a member of a software update group and not a member of another Deployment package then we can't remove the update until it does not belong to a group
            Write-Verbose "Check if the update is a member of a software update group"

            Try {
                $SUG = $S | Get-SCCMSoftwareUpdateMemberof -ErrorAction Stop
            }
            Catch {
                $EXceptionMessage = $_.Exception.Message
                $ExceptionType = $_.exception.GetType().fullname
                Throw "Remove-SCCMSoftwareUpdateFromDeploymentPackage : Error Checking if Update is a member of a Software Update Group`n`n     $ExceptionMessage`n`n     Exception : $ExceptionType"       
            } 

            if ( $SUG ) {
                Write-Warning "$($S.ArticleID) is a member of Software UpdateGroup(s):`n$($SUG | out-string)"
                Write-Warning "Not sure if I want to have a force switch included so that the update is removed from the Software Update Groups"
            }
            else {
                Write-Verbose "$($S.ArticleID) is not a member of a software update group."
                
                # ----- Convert Software update ID to Package Content ID.
                # Software packages are a little bit different from the other software updates objects. This is how the various objects relate:
                # SMS_SoftwareUpdate <-> SMS_CiToContent <-> SMS_PackageToContent <-> SMS_SoftwareUpdatesPackage

                Try {
                    $Update = Get-CIMInstance -ComputerName rwva-sccm -Namespace "root\sms\site_rwv" -query "SELECT SMS_PackageToContent.* From SMS_PackageToContent Join SMS_CIToContent ON SMS_CIToContent.ContentID=SMS_PackageToContent.ContentID where SMS_CIToContent.CI_ID = $($S.CI_ID) and SMS_PackageToCOntent.PackageID = '$($DP.PackageID)'" -ErrorAction Stop
                }
                Catch {
                    $EXceptionMessage = $_.Exception.Message
                    $ExceptionType = $_.exception.GetType().fullname
                    Throw "Remove-SCCMSoftwareUpdateFromDeploymentPackage : Error converting Software Update CI_ID to Package Content ID $DeploymentPackage`n`n     $ExceptionMessage`n`n     Exception : $ExceptionType"       
                }

                if ( $Update ) {
                    $Update.contentid

                    # ----- Remove from software update package
                    if ( ($DP.RemoveContent( $Update.ContentID,$False )).ReturnValue -ne 0 ) {
                        Throw "Remove-SCCMSoftwareUpdateFromDeploymentPackage : Error Removing $($S.ArticleID) from $($DP.Name)"
                    }
                }
                Else {
                    Throw "Remove-SCCMSoftwareUpdateFromDeploymentPackage : $($S.ArticleID) does not exist in Software Update Group $($DP.Name)"
                }
            }     
        }
    }

    End {
        # ----- Refresh the software update package distribution point
        Write-Verbose "Refreshing distribution points for Software Update Group: $($DP.Name)"
        if ( ($DP.RefreshPkgSource()).ReturnValue -ne 0 ) {
            Throw "Remove-SCCMSoftwareUpdateFromDeploymentPackage : Error refreshing the deployment point for Software updatae group $($DP.Name)"
        }
    }
}
{% endhighlight %}

Both cmdlets can be downloaded from my SCCM github repository.

And I would like to thank Trevou Sullivan as I used his code as a starting point.  I did modify it so it will work for any update passed not just delete expired and superceeded.

https://trevorsullivan.net/2011/11/29/configmgr-cleanup-software-updates-objects/

 