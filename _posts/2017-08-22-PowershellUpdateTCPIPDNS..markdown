---
layout: post
title:  "Change TCPIP DNS settings via Powershell"
date:   2017-08-22 11:57:00 -0400
categories: Powershell DNS
---
# Change TCPIP DNS settings via Powershell #

We are upgrading AD to Windows 2016.  New Domain Controllers on new Servers.  Once all server roles have been migrated from the old to the new servers, the old ones can be decommisioned.  Since we are using new servers with new IPs we need to change the DNS servers that the clients reference.  Easy to do with DHCP, but static assigned IPs require a bit more work.  To prevent having to log onto each server to make the change, this powershell script can be used to add the new DNS server IPs to the list of DNS servers in the TCPIP Config.


{% highlight ruby %} 
$Servers = Get-ADComputer -Filter * | Select-Object -ExpandProperty Name 

$FailedServers = @()
$CantConnect = @()

foreach ( $S in $Servers ) {
    Write-Output "--------"
    Write-Output "Updating $S"

    # ----- Check if Server is online.  skip if not reachable.
    if ( -Not (Test-Connection -ComputerName $S -Quiet -Count 1) ) { 
        $CantConnect += $S
        Continue
    }

    Try {
        $Session = New-CimSession -ComputerName $S -ErrorAction Stop
    
        $Adapter = Get-NetAdapter -CimSession $Session -ErrorAction Stop


        Foreach ($A in $Adapter ) {
            

            if ( $A | Get-NetIPAddress -ErrorAction Stop | where DHCP -ne Enabled ) {
                Write-Output "Found : $($A.Name)"

                $DNS = $A | Get-DNSClientServerAddress -AddressFamily IPv4 -ErrorAction Stop | Select-Object -ExpandProperty ServerAddresses

                Write-Output "Existing DNS Hosts : $DNS "

                if ( $DNS -notcontains '192.168.1.43' ) {
                    $DNS += '192.168.1.43'

                    $A | Get-NetIPConfiguration -ErrorAction Stop

                    $A | Set-DNSClientServerAddress -ServerAddresses $DNS
                }
            }
        }
    }
    Catch [Microsoft.Management.Infrastructure.CimException] {
        # ----- Because we are running old Powershell versions the above cmdlets don't work
        Write-Warning "Trying WMI on $S"
        $Net = Get-WmiObject -Class WIN32_NetworkAdapterConfiguration -ComputerName $S | Where { $_.DHCPEnabled -eq $False -and $_.IPAddress -ne $Null }
        $DNS = $Net.DNSServerSearchOrder
        $DNS += '192.168.1.43'
        $Net.SetDNSServerSearchOrder($DNS)
    }
    Catch [System.Management.Automation.PSArgumentException] {
        # ----- Because we are running old Powershell versions the above cmdlets don't work
        Write-Warning "Trying WMI on $S"
        try {
            $Net = Get-WmiObject -Class WIN32_NetworkAdapterConfiguration -ComputerName $S -erroraction Stop | Where { $_.DHCPEnabled -eq $False -and $_.IPAddress -ne $Null }
            $DNS = $Net.DNSServerSearchOrder
            $DNS += '192.168.1.43'
            $Net.SetDNSServerSearchOrder($DNS)
        }
        Catch {
            $EXceptionMessage = $_.Exception.Message
            $ExceptionType = $_.exception.GetType().fullname
            Write-Warning "$S ----- WMI ERROR.`n`n     $ExceptionMessage`n`n     Exception : $ExceptionType"
            $FailedServers +=$S
        }
    }
    Catch {
        $EXceptionMessage = $_.Exception.Message
        $ExceptionType = $_.exception.GetType().fullname
        Throw "ERROR.`n`n     $ExceptionMessage`n`n     Exception : $ExceptionType"
        
        Write-Warning "$S --- Failed to update"

        $FailedServers +=$S
    }
}

Write-Output "These Servers failed: "
$FailedServers

Write-Output "These Servers Cannot be Contacted: "
$CantConnect
{% endhighlight %}

I had to add the WMI section because some of our servers are running older versions of the OS that do not have the necesarry classes that the native PowerShell cmdlets use.

Note: This script can be used to remove DNS IPs with some modification.