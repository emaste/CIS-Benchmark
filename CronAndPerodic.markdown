# Cron & Perodic

## 5.1 Ensure cron daemon is enabled

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>cron</code> daemon is used to execute batch jobs on the system.

#### Rationale:
While there may not be user jobs that need to be run on the system, the system does have
maintenance jobs that may include security monitoring that have to run, and cron is used
to execute them.

#### Audit:
Run the the following command to verify <code>cron</code> is enabled:
<pre><code># service cron rcvar
# cron: Daemon to execute scheduled commands
#
cron_enable="YES"</code></pre>

Verify result of <code>cron_enable</code> is "YES".

#### Remediation:
Run the following command to enable <code>cron</code>:
<pre><code># service cron start</code></pre>

#### Notes:
Additional methods of enabling a service exist. Consult your distribution documentation for
appropriate methods.

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.

## 5.1.2 Ensure permissions on /etc/crontab are configured

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>/etc/crontab</code> file is used by <code>cron</code> to control its own jobs. The commands in this item
make sure that <code>root</code> is the user and group owner of the file and that only the owner can
access the file.

#### Rationale:
This file contains information on what system jobs are run by cron. Write access to these
files could provide unprivileged users with the ability to elevate their privileges. Read
access to these files could provide users with the ability to gain insight on system jobs that
run on the system and could provide them a way to gain unauthorized privileged access.

#### Audit:
Run the following command and verify the the read/write permissions are correct, and the owner and owner group are set to root
and operator:
<pre><code># ls -l /etc/crontab
-rw------- 1 root operator [date and time] /etc/crontab</code></pre>

#### Remediation:
Run the following commands to set ownership and permissions on <code>/etc/crontab</code> :
<pre><code># chown root:operator /etc/crontab
# chmod 600 /etc/crontab</code></pre>

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.
