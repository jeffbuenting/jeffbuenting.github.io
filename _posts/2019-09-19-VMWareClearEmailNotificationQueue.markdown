---
layout: post
title:  "VMware Clearing the Email Notification Queue"
date:   2019-09-19 09:09:00 -0400
categories: VMware 
---

The other day, we were going to shutdown all VMs due to some maintenance.  To prevent extra email notifications being sent from vCenter, I removed the SMTP settings.  This worked great, however, after the event when I entered the info back into vCenter, all of the alerts that happened during that maintenance period flooded into our email.  Oops, I forgot that those notifications are queued up and will be sent whenever connectivity is restored.

So how to prevent this.  You can delete all of the messages from the queue.

1. SSH to vCenter
2. delete all files in the folder /var/spool/mqueue

Enjoy

Jeff