## Services:

2.2.1.1 - Is Chrony setup needed? I included a ntpd setup, should I also have a section for installing/configuring chrony?

**2.2.2 - SMTP - should smtp be disabled? or set to send-only?**

## Network Configuration:

(3.1.2) packet redirect sending - NA on FreeBSD?

**(3.2.3) Ensure secure ICMP redirects are not accepted - Neither inet6(4) or inet(4) have this parameter, is this doable with anything else?**

(3.2.6) Ensure bogus ICMP responses are ignored - NA for FreeBSD

(3.2.7) Ensure Reverse Path Filtering is enabled - NA for freebsd

(3.3.1) Ensure DCCP is disabled - Looks like DCCP isn't available on FreeBSD?

(3.4.1.2) 3.4.2.2 Ensure iptables is not enabled - NA for FREEBSD


## Auditing and Logging.

**4.1.1.1  Ensure auditd is installed - Is there a way to check if auditd is installed? I know it's normally packages with the base OS**

(4.1.1.3)  Ensure auditing for processes that start prior to auditd is enabled - Not possible in FreeBSD

(4.1.1.3)  Ensure audit_backlog_limit is sufficient - Not possible in FreeBSD

(4.1.3) Ensure changes to system administration scope (sudoers) is collected - Is this possible on FreeBSD?

(4.1.5) Ensure session initiation information is collected - Not necessary of FreeBSD due to the 'lo' parameter already logging this.

(4.1.6) Ensure events that modify date and time information are collected - Not possible in FreeBSD

(4.1.7) Ensure events that modify the system's Mandatory Access Controls are collected - Not possible in FreeBSD

**4.1.7 Ensure events that modify user/group information are collected - Is this the 'aa' class in audit_config?**

(4.1.12) Ensure successful file system mounts are collected - Not possible in FreeBSD

(4.1.13) Ensure use of privileged commands is collected - Not possible in FreeBSD

(4.1.15) Ensure kernel module loading and unloading is collected - Not possible in FreeBSD

(4.1.17) Ensure the audit configuration is immutable - Not possible on FreeBSD

4.2.2 Configure Journald - Systemd-journald looks to be Linux only so I skipped this section


## Cron/Perodic:

**5.1.4 - Secure at/cron/periodic to authorized users - Does periodic have allow/deny files? Couldn't locate them.**

## SSH
5.2.20 Ensure system-wide crypto policy is not over-ridden - N/A for FreeBSD

## User Accounts & Environment

(5.5.1.2) Ensure minimum days between password changes is 7 or more - No option for this in FreeBSD

5.5.1.2 - No option to switch warning for individual users

Ensure inactive password lock is 30 days or less - No option on FreeBSD

Ensure all users last password change date is in the past - No option on FreeBSD

Ensure system accounts are secured - No(?) direct translation to FreeBSD

## PAM and Password Settings

(5.2.2)Ensure lockout for failed password attempts is configured - NA for FreeBSD

(5.2.3)Ensure password reuse is limited - NA for FreeBSD

## FileSystem

**6.1.5 Check if the find command is doable (personal note)**

## User and Group Settings 

(6.2.19) Ensure shadow group is empty - NA for FreeBSD
