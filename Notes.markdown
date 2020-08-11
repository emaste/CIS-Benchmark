Bolded need reviewing
Italics have already been confirmed by Ed.


## Services:

Finished

## Network Configuration:

*Ensure secure ICMP redirects are not accepted - NA for FreeBSD* 
EM: no we do not have a concept of "secure ICMP redirects", the one sysctl
controls what Linux calls secure and insecure ICMP redirects

Ensure bogus ICMP responses are ignored - NA for FreeBSD
EM: note that we do have an ICMP related log message,
```
        if (pps > 0 && V_icmplim_output)
                log(LOG_NOTICE, "Limiting %s from %jd to %d packets/sec\n",
                        V_icmp_rates[which].descr, (intmax_t )pps, V_icmplim);
```
but this does not log per-packet, so makes a poor avenue for an attacker
looking for a remote DoS.  Likely no need to worry about this.

Ensure Reverse Path Filtering is enabled - NA for freebsd
EM: no sysctl, but could be a firewall topic

*Ensure DCCP is disabled - NA for FreeBSD*
EM: correct, seems there were some old patches but it is not integrated

3.4.2.2 Ensure iptables is not enabled - NA for FreeBSD
We should make this "ensure only one firewall (pf/ipfw) is enabled" with
a rationale that multiple firewalls could lead to confusion.

## Auditing and Logging.

(4.1.1.3)  Ensure auditing for processes that start prior to auditd is enabled - Not possible in FreeBSD
EM: To be clear, there is no mechanism to *not* audit processes started before
enabling auditing.

(4.1.1.3)  Ensure audit_backlog_limit is sufficient - Not possible in FreeBSD
EM: see qsize param in audit_control(5)

(4.1.3) Ensure changes to system administration scope (sudoers) is collected - Is this possible on FreeBSD?
EM: Make sure that audit records are generated for sudoers file(s), should be
like Linux (different path and config mechanism of course)

(4.1.5) Ensure session initiation information is collected - Not necessary of FreeBSD due to the 'lo' parameter already logging this.
EM: right we should probably document this as ensuring that the required config
is in place

(4.1.6) Ensure events that modify date and time information are collected - Not possible in FreeBSD
EM: make sure the settimeofday, clock_settime syscalls are audited

(4.1.7) Ensure events that modify the system's Mandatory Access Controls are collected - Not possible in FreeBSD
EM: audit records on /etc/mac.conf? mac syscalls are AUE_NULL

**4.1.7 Ensure events that modify user/group information are collected - Is this the 'aa' class in audit_config?**
EM: probably not, audit records on /etc/master.passwd, /etc/spwd.db, /etc/group/

(4.1.12) Ensure successful file system mounts are collected - Not possible in FreeBSD
EM: nmount syscall

(4.1.13) Ensure use of privileged commands is collected - Not possible in FreeBSD
EM: same as linux, look for suid/sgid process, make sure audit is enabled for them

(4.1.15) Ensure kernel module loading and unloading is collected - Not possible in FreeBSD
EM: modload syscall

(4.1.17) Ensure the audit configuration is immutable - Not possible on FreeBSD
EM: todo

4.2.2 Configure Journald - Systemd-journald looks to be Linux only so I skipped this section
EM: yes ignore

ipv6_privacy???


## Cron/Perodic:

Finished

## SSH

*5.2.20 Ensure system-wide crypto policy is not over-ridden - N/A for FreeBSD*
EM: yes this looks like a front-end for sshd configuration, not applicable to us


## User Accounts & Environment

(5.5.1.2) Ensure minimum days between password changes is 7 or more - No option for this in FreeBSD
EM: also it's a bad idea anyway

5.5.1.2 - No option to switch warning for individual users
EM: which was this?

Ensure inactive password lock is 30 days or less - No option on FreeBSD
EM: seems so

Ensure all users last password change date is in the past - No option on FreeBSD
EM: yes, check pw expiry date - `pw usershow <foo> -P`

Ensure system accounts are secured - No(?) direct translation to FreeBSD
EM: it's equivalent, here are some system accounts from my system:
```
git_daemon:*:964:964::0:0:git daemon:/nonexistent:/usr/sbin/nologin
pulse:*:563:563::0:0:PulseAudio System User:/nonexistent:/usr/sbin/nologin
polkit:*:562:562::0:0:PolicyKit User:/nonexistent:/usr/sbin/nologin
haldaemon:*:560:560::0:0:HAL Daemon User:/nonexistent:/usr/sbin/nologin
webcamd:*:145:145::0:0:Webcamd user:/var/empty:/usr/sbin/nologin
```
The `*` in the 2nd field is the disabled password, and note that they are all
nologin shell as with Linux.

## PAM and Password Settings

(5.2.2)Ensure lockout for failed password attempts is configured - NA for FreeBSD
EM: seems so, would require 3rd party pam modules

(5.2.3)Ensure password reuse is limited - NA for FreeBSD
EM: pam_passwdqc similar=deny is probabl ythe closest thing

## FileSystem

**6.1.5 Check if the find command is doable (personal note)**

## User and Group Settings 

(6.2.19) Ensure shadow group is empty - NA for FreeBSD
EM: indeed, NA

6.2.14 Ensure all groups in /etc/passwd exist in /etc/group - Script relies on Grep -P this is not an option of FreeBSd which breaks the script, can this be fixed?
EM: can use something like:
```
grep -v '^[:space:]*#' /etc/passwd | cut -s -d: -f4 | sort -nu > file1
grep -v '^[:space:]*#' /etc/group | cut -s -d: -f3 | sort -nu > file2
comm -23 file1 file2

```
this lists entries in /etc/passwd not in /etc/group. output should be empty.
