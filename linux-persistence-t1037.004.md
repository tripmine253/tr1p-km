---
description: 'Boot or Logon Initialization Scripts: RC Scripts, init.d, motd'
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Linux-Persistence-T1037.004

`[ -f /etc/rc.local ] && cat /etc/rc.local || echo "missing"`

**Detect if rc-local service is enabled**

`systemctl list-unit-files --type=service --all | awk '$3 == "enabled" {print $1}' | grep 'rc-local.service'`

**List running**&#x20;

`systemctl list-units | grep 'running' | grep '.*.service'`

**Check syslog**

`cat /var/log/syslog | grep 'rc-local'`

**Detect with auditd**

```
-w /etc/rc.local -p wa -k rclocal    
```

**Detect with sysmon**

```xml
<FileCreate onmatch="include">
    <Rule name="TechniqueID=T1037.004,TechniqueName=Boot or Logon Initialization Scripts: RC Scripts" groupRelation="or">
        <TargetFilename condition="is">/etc/rc.local</TargetFilename>
    </Rule>
</FileCreate>
```

**Detect init.d scripts with auditd**

```
-w /etc/init.d/ -p wa -k init
```

**Watch**

```
systemctl list-unit-files | grep generated
```

**Motd modifications**\


```bash
/etc/update-motd.d/
```

* `00-header`
* `91-release-upgrade`
* `90-updates-available`
* `98-reboot-required`

**Detect with auditd**

```
-w /etc/update-motd.d/ -p wa -k motd
```

**Detect with sysmon**

```xml
<FileCreate onmatch="include">
    <Rule name="TechniqueID=T1037,TechniqueName=Boot or Logon Initialization Scripts: motd" groupRelation="or">
        <TargetFilename condition="begin with">/etc/update-motd.d/</TargetFilename>
    </Rule>
</FileCreate>
```

**Detect creation of systemd generators with auditd**

```
-w /etc/systemd/system-generators/ -p wa -k systemd_generator
-w /usr/local/lib/systemd/system-generators/ -p wa -k systemd_generator
-w /lib/systemd/system-generators/ -p wa -k systemd_generator
-w /usr/lib/systemd/system-generators -p wa -k systemd_generator
-w /etc/systemd/user-generators/ -p wa -k systemd_generator
-w /usr/local/lib/systemd/user-generators/ -p wa -k systemd_generator
-w /usr/lib/systemd/user-generators/ -p wa -k systemd_generator
```

\
