---
description: 'Event Triggered Execution: Unix Shell Configuration Modification'
---

# Linux-Persistence-T1546.004

| File                                            | Description                                                              |
| ----------------------------------------------- | ------------------------------------------------------------------------ |
| /etc/profile                                    | Systemwide files executed at the start of login shells                   |
| /etc/profile.d/                                 | All .sh files are executed at the start of login shells                  |
| /etc/bash.bashrc                                | Systemwide files executed at the start of interactive shells             |
| /etc/bash.bash\_logout                          | Systemwide executed as a login shell exits                               |
| \~/.bashrc                                      | User-specific startup script executed at the start of interactive shells |
| \~/.bash\_profile, \~/.bash\_login, \~/.profile | User-specific startup script, but only the first file found is executed  |
| \~/.bash\_logout                                | User-specific clean up script at the end of the session                  |

**Detect modifications of shell configs with auditd**

```
## Shell/profile configurations
-w /etc/profile.d/ -p wa -k shell_profiles
-w /etc/profile -p wa -k shell_profiles
-w /etc/shells -p wa -k shell_profiles
-w /etc/bashrc -p wa -k shell_profiles
-w /etc/csh.cshrc -p wa -k shell_profiles
-w /etc/csh.login -p wa -k shell_profiles
-w /etc/fish/ -p wa -k shell_profiles
-w /etc/zsh/ -p wa -k shell_profiles
-w /etc/bash.bashrc -p wa -k shell_profiles
-w /etc/bash.bash_logout -p wa -k shell_profiles
-w /root/.bashrc -p wa -k shell_profiles
-w /root/.bash_logout -p wa -k shell_profiles
-w /root/.profile -p wa -k shell_profiles
-w /root/.bash_profile -p wa -k shell_profiles
-w /root/.bash_login -p wa -k shell_profiles

```
