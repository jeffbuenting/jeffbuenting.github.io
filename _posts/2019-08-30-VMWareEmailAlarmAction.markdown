---
layout: post
title:  "VMware setting mulitple email address on Alarms"
date:   2019-08-30 11:38:00 -0400
categories: VMware Powercli
---

Adding an email alarm action to one alarm is simple via the GUI.  It quickly becomes daunting and tedious when you want to do it to many alarms.  This begs to be automated via PowerCLI.  But, alas, there is a bug in the PowerCLI cmdlets.  They add a semicolon between email addresses which cause the sendemail VMware function to fail.  You can read more about this here: https://kb.vmware.com/s/article/2150106


VMware's work around is to create a separate alarm action for each recipient.  Cheesy but it works.  Use this script

```Powershell
$EmailAddrs = @('user1@mail.com','user2@mail.com','user3@mail.com')


get-alarmdefinition | foreach {
    # ----- remove existing Email actions
    $_ | Get-AlarmAction -ActionType SendEmail | Remove-AlarmAction -Confirm:$False

    # ----- loop thru Email addrs
    foreach ( $E in $EmailAddrs ) {
        # ----- Create action
        $AlarmAction = $_ | New-AlarmAction -Email -To $E -Subject 'Alarm {alarmName} on Virtual Machine : {targetName} is {newStatus}' 

        # ----- add triggers to action
        New-AlarmActionTrigger -AlarmAction $AlarmAction -StartStatus Green -EndStatus Yellow -Repeat:$False -ErrorAction SilentlyContinue
        New-AlarmActionTrigger -AlarmAction $AlarmAction -StartStatus Yellow -EndStatus Red -Repeat:$False -ErrorAction SilentlyContinue
        New-AlarmActionTrigger -AlarmAction $AlarmAction -StartStatus Red -EndStatus Yellow -Repeat:$False -ErrorAction SilentlyContinue
        New-AlarmActionTrigger -AlarmAction $AlarmAction -StartStatus Yellow -EndStatus Green -Repeat:$False -ErrorAction SilentlyContinue
    }
}
```

That is all.

Enjoy

Jeff