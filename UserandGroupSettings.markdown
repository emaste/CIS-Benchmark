# User and Group Settings

## 6.2.1 Ensure password fields are not empty

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
An account with an empty password field means that anybody may log in as that user
without providing a password.

#### Rationale:
All accounts must have passwords or be locked to prevent the account from being used by
an unauthorized user.

#### Audit:
Run the following command and verify that no output is returned:
<pre><code># awk -F: '($2 == "" ) { print $1 " does not have a password "}' /etc/master.passwd</code></pre>

#### Remediation:
If any accounts in the <code>/etc/master.passwd</code> file do not have a password, run the following command
to lock the account until it can be determined why it does not have a password:

<pre><code># passwd -l [username]</code></pre>
  
Also, check to see if the account is logged in and investigate what it is being used for to
determine if it needs to be forced off.

#### CIS Controls:
Version 7
4.4 Use Unique Passwords
Where multi-factor authentication is not supported (such as local administrator, root, or
service accounts), accounts will use passwords that are unique to that system.

## 6.2.2 Ensure no legacy “+” entries exist in /etc/passwd

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The character + in various files used to be markers for systems to insert data from NIS
maps at a certain point in a system configuration file. These entries are no longer required
on most systems, but may exist in files that have been imported from other platforms.

#### Rationale:
These entries may provide an avenue for attackers to gain privileged access on the system.

#### Audit:
Run the following command and verify that no output is returned:
<pre><code># grep '^\+:' /etc/passwd</code></pre>

#### Remediation:
Remove any legacy '+' entries from /etc/passwd if they exist.

#### CIS Controls:
Version 7
16.2 Configure Centralized Point of Authentication
Configure access for all accounts through as few centralized points of authentication as
possible, including network, security, and cloud systems.

## 6.2.3 Ensure root PATH Integrity

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The root user can execute any command on the system and could be fooled into executing
programs unintentionally if the PATH is not set correctly.

#### Rationale:
Including the current working directory (.) or other writable directory in root 's executable
path makes it likely that an attacker can gain superuser access by forcing an administrator
operating as root to execute a Trojan horse program.

#### Audit:
Run the following script and verify no results are returned:
<pre><code>for x in $(echo $PATH | tr ":" " ") ; do
 if [ -d "$x" ] ; then
 ls -ldH "$x" | awk '
$9 == "." {print "PATH contains current working directory (.)"}
$3 != "root" {print $9, "is not owned by root"}
substr($1,6,1) != "-" {print $9, "is group writable"}
substr($1,9,1) != "-" {print $9, "is world writable"}'
 else
 echo "$x is not a directory"
 fi
done</code></pre>

#### Remediation:
Correct or justify any items discovered in the Audit step.

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.

## 6.2.4 Ensure no legacy “+” entries exist in /etc/master.passwd

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The character '+' in various files used to be markers for systems to insert data from NIS
maps at a certain point in a system configuration file. These entries are no longer required
on most systems, but may exist in files that have been imported from other platforms.

#### Rationale:
These entries may provide an avenue for attackers to gain privileged access on the system.

#### Audit:
Run the following command and verify that no output is returned:
<pre><code># grep '^\+:' /etc/master.passwd</code></pre>

#### Remediation:
Remove any legacy '+' entries from <code>/etc/master.passwd</code> if they exist.

#### CIS Controls:
Version 7
16.2 Configure Centralized Point of Authentication
Configure access for all accounts through as few centralized points of authentication as
possible, including network, security, and cloud systems.

## 6.2.5 Ensure no legacy “+” entries exist in /etc/group

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The character '+' in various files used to be markers for systems to insert data from NIS
maps at a certain point in a system configuration file. These entries are no longer required
on most systems, but may exist in files that have been imported from other platforms.

#### Rationale:
These entries may provide an avenue for attackers to gain privileged access on the system.

#### Audit:
Run the following command and verify that no output is returned:
<pre><code># grep '^\+:' /etc/group</code></pre>

#### Remediation:
Remove any legacy '+' entries from <code>/etc/group</code> if they exist.

#### CIS Controls:
Version 7
16.2 Configure Centralized Point of Authentication
Configure access for all accounts through as few centralized points of authentication as
possible, including network, security, and cloud systems.
