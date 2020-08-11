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

## 6.2.6 Ensure root is the only UID 0 account

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Any account with UID <code>0</code> has superuser privileges on the system.

#### Rationale:
This access must be limited to only the default <code>root</code> account and only from the system
console. Administrative access must be through an unprivileged account using an approved
mechanism as noted in Item 5.6 Ensure access to the su command is restricted.

#### Audit:
Run the following command and verify that only "root" is returned:
<pre><code># awk -F: '($3 == 0) { print $1 }' /etc/passwd
root</code></pre>

#### Remediation:
Remove any users other than <code>root</code> with UID <code>0</code> or assign them a new UID if appropriate.

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.

## 6.2.7 Ensure users’ home directories permissions are 750 or more restrictive

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
While the system administrator can establish secure permissions for users' home
directories, the users can easily override these.

#### Rationale:
Group or world-writable user home directories may enable malicious users to steal or
modify other users' data or to gain another user's system privileges.

#### Audit:
Run the following script and verify no results are returned:
<pre><code> #!/bin/bash
grep -E -v '^(halt|sync|shutdown)' /etc/passwd | awk -F: '($7 != "'"$(which
nologin)"'" && $7 != "/bin/false") { print $1 " " $6 }' | while read user
dir; do
 if [ ! -d "$dir" ]; then
 echo "The home directory ($dir) of user $user does not exist."
 else
 dirperm=$(ls -ld $dir | cut -f1 -d" ")
 if [ $(echo $dirperm | cut -c6) != "-" ]; then
 echo "Group Write permission set on the home directory ($dir) of user
$user"
 fi
 if [ $(echo $dirperm | cut -c8) != "-" ]; then
 echo "Other Read permission set on the home directory ($dir) of user
$user"
 fi
 if [ $(echo $dirperm | cut -c9) != "-" ]; then
 echo "Other Write permission set on the home directory ($dir) of user
$user"
 fi
 if [ $(echo $dirperm | cut -c10) != "-" ]; then
 echo "Other Execute permission set on the home directory ($dir) of user
$user"
 fi
 fi
done</code></pre>

#### Remediation:
Making global modifications to user home directories without alerting the user community
can result in unexpected outages and unhappy users. Therefore, it is recommended that a
monitoring policy be established to report user file permissions and determine the action
to be taken in accordance with site policy.


#### CIS Controls:
Version 7
14.6 Protect Information through Access Control Lists
Protect all information stored on systems with file system, network share, claims,
application, or database specific access control lists. These controls will enforce the
principle that only authorized individuals should have access to the information based on
their need to access the information as a part of their responsibilities.

## 6.2.8 Ensure users own their home directories

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The user home directory is space defined for the particular user to set local environment
variables and to store personal files.

#### Rationale:
Since the user is accountable for files stored in the user home directory, the user must be
the owner of the directory.

#### Audit:
Run the following script and verify no results are returned:
<pre><code>#!/bin/bash
grep -E -v '^(halt|sync|shutdown)' /etc/passwd | awk -F: '($7 != "'"$(which
nologin)"'" && $7 != "/bin/false") { print $1 " " $6 }' | while read user
dir; do
 if [ ! -d "$dir" ]; then
 echo "The home directory ($dir) of user $user does not exist."
 else
 owner=$(stat -L -c "%U" "$dir")
 if [ "$owner" != "$user" ]; then
 echo "The home directory ($dir) of user $user is owned by $owner."
 fi
fi
done</code></pre>

#### Remediation:
Change the ownership of any home directories that are not owned by the defined user to
the correct user.

#### Notes:
On some distributions the /sbin/nologin should be replaced with <code>/usr/sbin/nologin</code>.

#### CIS Controls:
Version 7
14.6 Protect Information through Access Control Lists
Protect all information stored on systems with file system, network share, claims,
application, or database specific access control lists. These controls will enforce the
principle that only authorized individuals should have access to the information based on
their need to access the information as a part of their responsibilities.

## 6.2.9 Ensure users’ dot files are not group or world writable

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
While the system administrator can establish secure permissions for users' "dot" files, the
users can easily override these.

#### Rationale:
Group or world-writable user configuration files may enable malicious users to steal or
modify other users' data or to gain another user's system privileges.

#### Audit:
Run the following script and verify no results are returned:
<pre><code>#!/bin/bash
grep -E -v '^(halt|sync|shutdown)' /etc/passwd | awk -F: '($7 != "'"$(which
nologin)"'" && $7 != "/bin/false") { print $1 " " $6 }' | while read user
dir; do
 if [ ! -d "$dir" ]; then
 echo "The home directory ($dir) of user $user does not exist."
 else
 for file in $dir/.[A-Za-z0-9]*; do
 if [ ! -h "$file" -a -f "$file" ]; then
 fileperm=$(ls -ld $file | cut -f1 -d" ")
 if [ $(echo $fileperm | cut -c6) != "-" ]; then
 echo "Group Write permission set on file $file"
 fi
 if [ $(echo $fileperm | cut -c9) != "-" ]; then
 echo "Other Write permission set on file $file"
 fi
 fi
 done
 fi
done</code></pre>

#### Remediation:
Making global modifications to users' files without alerting the user community can result
in unexpected outages and unhappy users. Therefore, it is recommended that a monitoring
policy be established to report user dot file permissions and determine the action to be
taken in accordance with site policy.

#### Notes:
On some distributions the /sbin/nologin should be replaced with /usr/sbin/nologin.

#### CIS Controls:
Version 7
14.6 Protect Information through Access Control Lists
Protect all information stored on systems with file system, network share, claims,
application, or database specific access control lists. These controls will enforce the
principle that only authorized individuals should have access to the information based on
their need to access the information as a part of their responsibilities.

## 6.2.13 Ensure no users have .rhosts files

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
While no <code>.rhosts</code> files are shipped by default, users can easily create them.

#### Rationale:
This action is only meaningful if <code>.rhosts</code> support is permitted in the file <code>/etc/pam.conf</code> .
Even though the <code>.rhosts</code> files are ineffective if support is disabled in <code>/etc/pam.conf</code> , they
may have been brought over from other systems and could contain information useful to
an attacker for those other systems.

#### Audit:
Run the following script and verify no results are returned:
<pre><code>#!/bin/bash
grep -E -v '^(root|halt|sync|shutdown)' /etc/passwd | awk -F: '($7 !=
"'"$(which nologin)"'" && $7 != "/bin/false") { print $1 " " $6 }' | while read user dir; do
    if [ ! -d "$dir" ]; then
        echo "The home directory ($dir) of user $user does not exist."
    else
        for file in $dir/.rhosts; do
            if [ ! -h "$file" -a -f "$file" ]; then
                echo ".rhosts file in $dir"
            fi
        done
     fi
done</code></pre>

#### Remediation:
Making global modifications to users' files without alerting the user community can result
in unexpected outages and unhappy users. Therefore, it is recommended that a monitoring
policy be established to report user <code>.rhosts</code> files and determine the action to be taken in
accordance with site policy.

#### CIS Controls:
Version 7

16.4 Encrypt or Hash all Authentication Credentials

Encrypt or hash with a salt all authentication credentials when stored.

## 6.2.14 Ensure all groups in /etc/passwd exist in /etc/group

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Over time, system administration errors and changes can lead to groups being defined in
<code>/etc/passwd</code> but not in <code>/etc/group</code>.

#### Rationale:
Groups defined in the <code>/etc/passwd</code> file but not in the <code>/etc/group</code> file pose a threat to
system security since group permissions are not properly managed.

#### Audit:
Run the following script and verify no results are returned:
<pre><code>#!/bin/bash
    for i in $(cut -s -d: -f4 /etc/passwd | sort -u ); do
    grep -q -P "^.*?:[^:]*:$i:" /etc/group
    if [ $? -ne 0 ]; then
        echo "Group $i is referenced by /etc/passwd but does not exist in /etc/group"
    fi
done</code></pre>

#### Remediation:
Analyze the output of the Audit step above and perform the appropriate action to correct
any discrepancies found.

#### CIS Controls:
Version 7

16 Account Monitoring and Control

Account Monitoring and Control


## 6.2.15 Ensure no duplicate UIDs exist

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Although the useradd program will not let you create a duplicate User ID (UID), it is
possible for an administrator to manually edit the <code>/etc/passwd</code> file and change the UID
field.

#### Rationale:
Users must be assigned unique UIDs for accountability and to ensure appropriate access
protections.

#### Audit:
Run the following script and verify no results are returned:
<pre><code>#!/bin/bash
cut -f3 -d":" /etc/passwd | sort -n | uniq -c | while read x ; do
    [ -z "$x" ] && break
    set - $x
    if [ $1 -gt 1 ]; then
        users=$(awk -F: '($3 == n) { print $1 }' n=$2 /etc/passwd | xargs)
        echo "Duplicate UID ($2): $users"
    fi
done</code></pre>

#### Remediation:
Based on the results of the audit script, establish unique UIDs and review all files owned by
the shared UIDs to determine which UID they are supposed to belong to.

#### CIS Controls:
Version 7

16 Account Monitoring and Control

Account Monitoring and Control

## 6.2.16 Ensure no duplicate GIDs exist

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Although the groupadd program will not let you create a duplicate Group ID (GID), it is
possible for an administrator to manually edit the <code>/etc/group</code> file and change the GID field.

#### Rationale:
User groups must be assigned unique GIDs for accountability and to ensure appropriate
access protections.

#### Audit:
Run the following script and verify no results are returned:

<pre><code>#!/bin/bash
cut -d: -f3 /etc/group | sort | uniq -d | while read x ; do
    echo "Duplicate GID ($x) in /etc/group"
done</code></pre>

#### Remediation:
Based on the results of the audit script, establish unique GIDs and review all files owned by
the shared GID to determine which group they are supposed to belong to.


#### CIS Controls:
Version 7

16 Account Monitoring and Control

Account Monitoring and Control

## 6.2.17 Ensure no duplicate user names exist

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Although the adduser program will not let you create a duplicate user name, it is possible
for an administrator to manually edit the <code>/etc/passwd</code> file and change the user name.

#### Rationale:
If a user is assigned a duplicate user name, it will create and have access to files with the
first UID for that username in <code>/etc/passwd</code> . For example, if "test4" has a UID of 1000 and a
subsequent "test4" entry has a UID of 2000, logging in as "test4" will use UID 1000.
Effectively, the UID is shared, which is a security problem.

#### Audit:
Run the following script and verify no results are returned:
<pre><code>#!/bin/bash
cut -d: -f1 /etc/passwd | sort | uniq -d | while read x ; do 
    echo "Duplicate login name ${x} in /etc/passwd"
done</code></pre>

#### Remediation:
Based on the results of the audit script, establish unique user names for the users. File
ownerships will automatically reflect the change as long as the users have unique UIDs.

#### CIS Controls:
Version 7

16 Account Monitoring and Control

Account Monitoring and Control

## 6.2.18 Ensure no duplicate group names exist

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Although the pw program will not let you create a duplicate group name, it is
possible for an administrator to manually edit the <code>/etc/group</code> file and change the group
name.

#### Rationale:
If a group is assigned a duplicate group name, it will create and have access to files with the
first GID for that group in <code>/etc/group</code> . Effectively, the GID is shared, which is a security
problem.

#### Audit:
Run the following script and verify no results are returned:
<pre><code>#!/bin/bash
cut -d: -f1 /etc/group | sort | uniq -d | while read x ; do 
    echo "Duplicate group name ${x} in /etc/group"
done</code></pre>

#### Remediation:
Based on the results of the audit script, establish unique names for the user groups. File
group ownerships will automatically reflect the change as long as the groups have unique
GIDs.

#### CIS Controls:
Version 7

16 Account Monitoring and Control

Account Monitoring and Control

## 6.2.19 Ensure all users' home directories exist

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Users can be defined in /etc/passwd without a home directory or with a home directory
that does not actually exist.

#### Rationale:
If the user's home directory does not exist or is unassigned, the user will be placed in "/"
and will not be able to write any files or have local environment variables set.

#### Audit:
Run the following script and verify no results are returned:
<pre><code>#!/bin/bash
grep -E -v '^(halt|sync|shutdown)' /etc/passwd | awk -F: '($7 != "'"$(which nologin)"'" && $7 != "/bin/false") { print $1 " " $6 }' | while read -r user dir; do
    if [ ! -d "$dir" ]; then
        echo "The home directory ($dir) of user $user does not exist."
    fi
done</code></pre>

#### Remediation:
If any users' home directories do not exist, create them and make sure the respective user
owns the directory. Users without an assigned home directory should be removed or
assigned a home directory as appropriate.

#### Notes:
The audit script checks all users with interactive shells except halt, sync, shutdown, and
nfsnobody.

#### CIS Controls:
Version 7

5.1 Establish Secure Configurations

Maintain documented, standard security configuration standards for all authorized
operating systems and software.
