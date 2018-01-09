---
layout: post
title:  "Parsing IIS Logs"
date:   2018-01-09 13:21:00 -0400
categories: IIS Powershell
---
# Parsing IIS Logs #

The other day I was asked if it was possible to search the IIS logs and extract specific enties.  I figured it wouldn't be that bad.  I know in the past I have loaded the files into Excel to search so I thought the files  could be loaded into poweshell as an objec.  Luckily I found this artical by Ben Taylor with a powershell cmdlet to convert an IIS log to a powerhsell object.  https://bentaylor.work/2016/09/parsing-iis-logs-to-powershell-objects/ 

From there it was a simple matter to manipulate the data and output

```Powershell
<#
    .Synopsis
        Converts plain text IIS logs into a ps Object
    .DESCRIPTION
        Converts plain text IIS logs into a ps Object
    .EXAMPLE
        Get-ChildItem '<path to logs>\*.log' | Convert-IISLogsToObject | Sort-Object c-ip -Unique
    .EXAMPLE
        Convert-IISLogsToObject -path (Get-ChildItem '<path to logs>\*log') | Where-Object { $_.'cs-username' -eq '<userName>' } | Sort-Object c-ip -Unique
    .NOTES
        General notes
    .AUTHOR
        Ben Taylor - 09/07/2016
#>
function Convert-IISLogsToObject {
    [CmdletBinding()]
    [OutputType([System.Management.Automation.PSCustomObject])]
    Param(
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [ValidateScript({ Test-Path -Path $_ })]
        [string[]]
        $path
    )

    Process {
        forEach($filePath in $path) {
            $headers = (Get-Content -Path $filePath -TotalCount 4 | Select -First 1 -Skip 3) -replace '#Fields: ' -split ' '
            Get-Content $filePath | Select-String -Pattern '^#' -NotMatch | ConvertFrom-Csv -Delimiter ' ' -Header $headers
        }
    }
}



$IISLog = @()

get-childitem -path c:\inetpub\logs\LogFiles\W3SVC2 | where lastwritetime -ge (Get-date 9/1/2017) | foreach {
    
   $Log = $_ 

   write-output "Processing log : $($Log.Name)"

    $IISLog += Convert-IISLogsToObject -path $Log.FullName | where { $_.'cs-method' -eq 'POST' -and $_.'cs-uri-stem' -like '/preauth*' } | Select-Object @{Name='LogName';Expression={ $Log.Name }},*

}

$IISLog | Export-Csv -Path c:\temp\PostPreauth_after9-01-2017.csv -NoTypeInformation
```




