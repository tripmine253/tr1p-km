---
description: 'Create or Modify System Process: Systemd Service'
---

# Linux-Persistence-T1543.002

**Service Modifications**

* `/etc/systemd/system/sshd.service`: Secure Shell Service
* `/lib/systemd/system/systemd-logind.service`: Login Service
* `/lib/systemd/system/rsyslog.service`: System Logging Service
* `/lib/systemd/system/cron.service`: Regular background program processing daemon

**System Service Added**

* `/etc/systemd/system/`
* `/lib/systemd/system/`

Example bad service at `/etc/systemd/system/bad.service`

```
[Unit]
Description=Example of bad service

[Service]
ExecStart=python3 -m http.server --directory /

[Install]
WantedBy=multi-user.target
```

**Enumerate unit paths**

`systemd-analyze unit-paths` _`--user`_

**Enumerate enabled services**

`systemctl list-unit-files --type=service --all | awk '$3 == "enabled" {print $1}'`

User Service Added

* `~/.config/systemd/user/`

**Watch Directories**

* `/etc/systemd/*`
* `/usr/local/lib/systemd/*`
* `/lib/systemd/*`
* `/usr/lib/systemd/*`
* `<USER HOME>/.config/systemd/user`

***

#### **Detection with auditd**

```
-w /usr/lib/systemd/ -p wa -k systemd
-w /lib/systemd/ -p wa -k systemd

# Directories may not exist
-w /usr/local/lib/systemd/ -p wa -k systemd
-w /usr/local/share/systemd/user -p wa -k systemd_user
-w /usr/share/systemd/user  -p wa -k systemd_user
```

#### **Detection with sysmon**

```xml
<FileCreate onmatch="include">
        <Rule name="TechniqueID=T1543.002,TechniqueName=Create or Modify System Process: Systemd Service" groupRelation="or">
          <TargetFilename condition="begin with">/etc/systemd/system</TargetFilename>
          <TargetFilename condition="begin with">/usr/lib/systemd/system</TargetFilename>
          <TargetFilename condition="begin with">/run/systemd/system/</TargetFilename>
          <TargetFilename condition="begin with">/lib/systemd/system/</TargetFilename>
          <TargetFilename condition="contains">/systemd/user/</TargetFilename>
        </Rule>
      </FileCreate>
```

#### **DETECTION GAP (sysmon)**&#x20;

* **file modification of existing service**
* &#x20;**service file created external to** `/etc/systemd/systemd`

```
cat > /tmp/bad_sysmon.service << EOF
[Unit]
Description=Example of bad service

[Service]
ExecStart=python3 -m http.server --directory / 9998

[Install]
WantedBy=multi-user.target

EOF

mv /tmp/bad_sysmon.service /etc/systemd/system/bad_sysmon.service
ln -s /etc/systemd/system/bad_sysmon.service /tmp/bad_sysmon.service
mv /tmp/bad_sysmon.service /etc/systemd/system/multi-user.target.wants/bad_sysmon.service
```

#### **Detecting using auditbeats**

```
- module: file_integrity
  paths:
  - /bin
  - /usr/bin
  - /sbin
  - /usr/sbin
  - /etc
  - /etc/systemd/system
  - /lib/systemd/system
  - /usr/lib/systemd/system
  # recursive: true
```

#### **Hunting using osquery**

Listing systemd unit files with hashes

```
SELECT id, description,SELECT id, description, fragment_path, md5  
FROM systemd_units 
JOIN hash ON (hash.path = systemd_units.fragment_path) 
WHERE id LIKE "%service";
```

Listing startup services\


```
SELECT name, source, path, status, md5
FROM startup_items 
JOIN hash 
USING(path)
WHERE path LIKE "%.service"  AND status = "inactive"
ORDER BY name;
```



