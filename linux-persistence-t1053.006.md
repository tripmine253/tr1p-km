---
description: 'Scheduled Task/Job: Systemd Timers'
---

# Linux-Persistence-T1053.006

**List existing timers**

`systemctl list-timers`

`TIMERLIST="$(systemctl list-timers | egrep -o '[[:punct:][:alnum:]]+.timer')"`

`for x in $TIMERLIST;do ls -la "/lib/systemd/system/$x";done`

`ls -la /etc/systemd/system/timers.target.wants/*`

`ls /lib/systemd/system/*.timer | xargs awk '{print FILENAME,$0}'`
