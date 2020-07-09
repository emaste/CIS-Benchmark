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

## 5.1.3 Ensure permissions on /etc/cron.d are configured
#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>/etc/cron.d</code> directory contains system <code>cron</code> jobs that require more
granular control as to when they run. The files in this directory cannot be manipulated by
the <code>crontab</code> command, but are instead edited by system administrators using a text editor.
The commands below restrict read/write and search access to user and group root,
preventing regular users from accessing this directory.

#### Rationale:
Granting write access to this directory for non-privileged users could provide them the
means for gaining unauthorized elevated privileges. Granting read access to this directory
could give an unprivileged user insight in how to gain elevated privileges or circumvent
auditing controls.

#### Audit:
Run the following command and verify the the read/write permissions are correct, and the owner and owner group are set to root
and operator:
<pre><code># ls -l /etc/cron.d
-rw------- 1 root operator [date and time] /etc/cron.d</code></pre>

#### Remediation:
Run the following commands to set ownership and permissions on <code>/etc/crontab</code> :
<pre><code># chown root:operator /etc/crontab
# chmod 600 /etc/cron.d</code></pre>

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.
  
 
## 5.1.4 Ensure at/cron/periodic are restricted to authorized users

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Configure <code>/etc/cron.d/cron.allow</code> and <code>/var/at/at.allow</code> to allow specific users to use these
services. If <code>/etc/cron.d/cron.allow</code> or <code>/var/at/at.allow</code> do not exist, then <code>/etc/cron.d/cron.deny</code> and <code>/var/at/at.deny</code> are checked. Any user not specifically defined in those files is allowed to
use at and <code>cron</code>. By removing the files, only users in <code>/etc/cron.d/cron.allow</code> and <code>/var/at/at.allow</code>
are allowed to use at and cron. Note that even though a given user is not listed in
<code>cron.allow</code> , cron jobs can still be run as that user. The <code>cron.allow</code> file only controls
administrative access to the crontab command for scheduling and modifying cron jobs.

#### Rationale:
On many systems, only the system administrator is authorized to schedule cron jobs. Using
the <code>cron.allow</code> file to control who can run cron jobs enforces this policy. It is easier to
manage an allow list than a deny list. In a deny list, you could potentially add a user ID to
the system and forget to add it to the deny files.

#### Audit:
Run the following commands and ensure <code>/etc/cron.d/cron.deny</code> and <code>/var/at/at.deny</code> do not exist:

<pre><code># stat /etc/cron.d/cron.deny
stat: /etc/cron.d/cron.deny: stat: No such file or directory</code></pre>

<pre><code># stat /var/at/at.deny
stat: /var/at/at.deny: stat: No such file or directory</code></pre>

Run the following command and verify Uid and Gid are both root and operator and Access does not
grant permissions to group or other for both <code>/etc/cron.d/cron.allow</code> and <code>/var/at/at.allow</code> :

<pre><code># ls -l /etc/cron.d/cron.allow
-rw------- 1 root operator [date and time] /etc/cron.d/cron.allow</code></pre>

<pre><code># ls -l /var/at/at.allow
-rw------- 1 root operator [date and time] /etc/at/at.allow</code></pre>

#### Remediation:
Run the following commands to remove <code>/etc/cron.d/cron.deny</code> and <code>/var/at/at.deny</code> and create and
set permissions and ownership for <code>/etc/cron.d/cron.allow</code> and <code>/var/at/at.allow</code> :
<pre><code># rm /etc/cron.d/cron.deny
# rm /var/at/at.deny</code></pre>

<pre><code># touch /etc/cron.d/cron.allow
# touch /var/at/at.allow</code></pre>

<pre><code># chmod 600 /etc/cron.d/cron.allow
# chmod 600 /var/at/at.allow</code></pre>

<pre><code># chown root:operator /etc/cron.d/cron.allow
# chown root:operator /var/at/at.allow</code></pre>

#### CIS Controls:

Version 7

16 Account Monitoring and Control
Account Monitoring and Control
