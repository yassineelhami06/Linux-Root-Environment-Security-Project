# Linux Root Environment Security Commands

This file contains the commands and configurations used to secure the Linux root account, implement controlled administrative access, and enforce system hardening.  

It complements the main README, which provides an overview and security principles.

---

## 1. Disable Direct Root Login

Lock the root account password:

```bash
passwd -l root
```
Disable SSH login for root _(edit the SSH configuration file /etc/ssh/sshd_config)_:
```bash
PermitRootLogin no
```

Effect: Root cannot log in locally or remotely.
## 2. Controlled Privilege Escalation via sudo

Install sudo:
```bash
apt install sudo
```

Assign authorized users:
```bash
usermod -aG sudo user2
```

Effect: Root access possible only via sudo; all actions are logged.

## 3. Restrict su Command

Create a group for authorized users:
```bash
groupadd sugroup
usermod -aG sugroup user2
```

Configure PAM to restrict su _(edit /etc/pam.d/su and add)_:
```bash
auth required pam_wheel.so use_uid group=sugroup
```

Effect: Only approved users can switch to root.

## 4. Remove Root Shell Access

Set root shell to nologin:
```bash
usermod -s /usr/sbin/nologin root
```

Effect: Root cannot be used as an interactive account.

## 5. Secure Sessions and Enforce Strong Passwords

Timeout inactive sessions _(edit /etc/profile or a shell profile file)_:
```bash
export TMOUT=300
```

Enforce strong password policies via PAM _(edit /etc/pam.d/common-password and add)_:
```bash
password requisite pam_pwquality.so retry=3 minlen=12 ucredit=-1 lcredit=-1 dcredit=-1
```
## 6. Harden Sudo Configuration

Require TTY _(edit /etc/sudoers with visudo)_:
```bash
Defaults requiretty
```

Log input/output _(edit /etc/sudoers with visudo)_:
```bash
Defaults log_input,log_output
```

Restrict commands per user _(edit /etc/sudoers with visudo)_:
```bash
user2 ALL=(root) /usr/bin/systemctl, /usr/bin/apt
```
## 7. Audit and Protect the System

Log all sudo actions _(edit /etc/sudoers with visudo)_:
```bash
Defaults logfile="/var/log/sudo.log"
```

Track root commands via auditd:
```bash
auditctl -a always,exit -F euid=0 -S execve -k ROOT_CMDS
```

Constrain critical services with AppArmor:
```bash
apt install apparmor apparmor-profiles
systemctl enable apparmor --now
aa-enforce /etc/apparmor.d/usr.sbin.sshd
```

Protect critical system files:
```bash
chattr +i /etc/passwd /etc/shadow /etc/group /etc/sudoers
```
## 8. Example Commands & Expected Output
| User   | Command                    | Output / Effect                         |
| ------ | -------------------------- | --------------------------------------- |
| user2  | su                         | su: Authentication failure              |
| user2  | sudo ls                    | user2 is not in the sudoers file        |
| debian | sudo apt update            | Reading package lists... Done           |
| debian | sudo bash                  | Not allowed to execute '/bin/bash'      |
| root   | su -                       | This account is currently not available |
| root   | ps -u root                 | root processes (systemd, auditd, sshd…) |
| debian | sudo ausearch -k ROOT_CMDS | All root command executions logged      |
| debian | sudo cat /var/log/sudo.log | Displays sudo activity                  |
