---
description: 'Scheduled Task/Job: Cron'
---

# Linux-Persistence-T1053.003

**Watch list**

* `/etc/crontab`
* `/etc/cron.d/*`
* `/etc/cron.{hourly,daily,weekly,monthly}/*`
* `/var/spool/cron/crontab/*`

**Monitor cron with auditd**

```
-w /etc/cron.allow -p wa -k cron
-w /etc/cron.deny -p wa -k cron
-w /etc/cron.d/ -p wa -k cron
-w /etc/cron.daily/ -p wa -k cron
-w /etc/cron.hourly/ -p wa -k cron
-w /etc/cron.monthly/ -p wa -k cron
-w /etc/cron.weekly/ -p wa -k cron
-w /etc/crontab -p wa -k cron
-w /var/spool/cron/ -k cron
```



