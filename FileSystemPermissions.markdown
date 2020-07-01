# File System Permissions

## 6.1.1 Audit system file permissions

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:



#### Rationale:
It is important to confirm that packaged system files and directories are maintained with
the permissions they were intended to have from the OS vendor.

#### Audit:
Run the following command to review all installed packages. Note that this may be very
time consuming and may be best scheduled via the cron utility. It is recommended that the
output of this command be redirected to a file that can be reviewed later.

<pre><code># pkg check -s > <filename></code></pre>

#### Remediation:
Correct any discrepancies found and rerun the audit until output is clean or risk is
mitigated or accepted.


#### Notes:
Since packages and important files may change with new updates and releases, it is
recommended to verify everything, not just a finite list of files. This can be a time
consuming task and results may depend on site policy therefore it is not a scorable
benchmark item, but is provided for those interested in additional security measures.
Some of the recommendations of this benchmark alter the state of files audited by this
recommendation. The audit command will alert for all changes to a file permissions even if
the new state is more secure than the default.

#### CIS Controls:
Version 7
14.6 Protect Information through Access Control Lists
Protect all information stored on systems with file system, network share, claims,
application, or database specific access control lists. These controls will enforce the
principle that only authorized individuals should have access to the information based on
their need to access the information as a part of their responsibilities.

## 6.1.2 Ensure permissions on /etc/passwd are configured

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>/etc/passwd</code> file contains user account information that is used by many system
utilities and therefore must be readable for these utilities to operate.

#### Rationale:
It is critical to ensure that the <code>/etc/passwd</code> file is protected from unauthorized write
access. Although it is protected by default, the file permissions could be changed either
inadvertently or through malicious actions.

#### Audit:
Run the following command and verify the the read/write permissions are correct, and the owner and owner group are set to root and operator:
<pre><code># ls -l /etc/passwd
-rw-r--r-- 1 root operator [date and time] /etc/passwd</code></pre>

#### Remediation:
Run the following command to set permissions on /etc/passwd :
<pre><code># chown root:operator /etc/passwd
# chmod 644 /etc/passwd</code></pre>

#### CIS Controls:
Version 7
16.4 Encrypt or Hash all Authentication Credentials
Encrypt or hash with a salt all authentication credentials when stored.

## 6.1.3 Ensure permissions on /etc/master.passwd are configured

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>/etc/master.passwd</code> file is used to store the information about user accounts that is critical to
the security of those accounts, such as the hashed password and other security information.

#### Rationale:
If attackers can gain read access to the <code>/etc/master.passwd</code> file, they can easily run a password 
cracking program against the hashed password to break it. Other security information that
is stored in the <code>/etc/master.passwd</code> file (such as expiration) could also be useful to subvert the
user accounts.

#### Audit:
Run the following command and verify the the read/write permissions are correct, and the owner and owner group are set to root and operator:

<pre><code># ls -l /etc/master.passwd
-rw-r----- root operator [date and time] /etc/master.passwd</code></pre>

#### Remediation:
Run the one of the following chown commands as appropriate and the chmod to set
permissions on /etc/shadow :
<pre><code># chown root:operator /etc/master.passwd
# chmod 640 /etc/master.passwd</code></pre>

#### CIS Controls:
Version 7
16.4 Encrypt or Hash all Authentication Credentials
Encrypt or hash with a salt all authentication credentials when stored.

## 6.1.4 Ensure permissions on /etc/group are configured

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>/etc/group</code> file contains a list of all the valid groups defined in the system. The
command below allows read/write access for root and read access for everyone else.

#### Rationale:
The <code>/etc/group</code> file needs to be protected from unauthorized changes by non-privileged
users, but needs to be readable as this information is used with many non-privileged programs.

#### Audit:
Run the following command and verify the the read/write permissions are correct, and the owner and owner group are set to root and operator:
<pre><code># ls -l /etc/group
-rw-r--r-- 1 root operator [date and time] /etc/group</code></pre>

#### Remediation:
Run the following command to set permissions on <code>/etc/group</code> :
<pre><code># chown root:operator /etc/group
# chmod 644 /etc/group</code></pre>

#### CIS Controls:
Version 7
16.4 Encrypt or Hash all Authentication Credentials
Encrypt or hash with a salt all authentication credentials when stored.

## 6.1.5 Ensure no world writable files exist

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
FreeBSD-based systems support variable settings to control access to files. World writable files
are the least secure. See the chmod(2) man page for more information.

#### Rationale:
Data in world-writable files can be modified and compromised by any user on the system.
World writable files may also indicate an incorrectly written script or program that could
potentially be the cause of a larger compromise to the system's integrity.

#### Audit:
Run the following command and verify no files are returned:
<pre><code># df --local -P | awk '{if (NR!=1) print $6}' | xargs -I '{}' find '{}' -xdev -type f -perm -0002</code></pre>

The command above only searches local filesystems, there may still be compromised items
on network mounted partitions. Additionally the --local option to df is not universal to all
versions, it can be omitted to search all filesystems on a system including network mounted
filesystems or the following command can be run manually for each partition:

<pre><code># find <partition> -xdev -type f -perm -0002</code></pre>

#### Remediation:
Removing write access for the "other" category ( chmod o-w <filename> ) is advisable, but
always consult relevant vendor documentation to avoid breaking any application
dependencies on a given file.
  
#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.
13 Data Protection
Data Protection

## 6.1.6 Ensure no unowned files or directories exist

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Sometimes when administrators delete users from the password file they neglect to
remove all files owned by those users from the system.

#### Rationale:
A new user who is assigned the deleted user's user ID or group ID may then end up
"owning" these files, and thus have more access on the system than was intended.

#### Audit:
Run the following command and verify no files are returned:
<pre><code># find / -nouser</code></pre>

#### Remediation:
Locate files that are owned by users or groups not listed in the system configuration files,
and reset the ownership of these files to some active user on the system as appropriate.

#### CIS Controls:
Version 7
13.2 Remove Sensitive Data or Systems Not Regularly Accessed by Organization
Remove sensitive data or systems not regularly accessed by the organization from the
network. These systems shall only be used as stand alone systems (disconnected from the
network) by the business unit needing to occasionally use the system or completely
virtualized and powered off until needed.

## 6.1.7 Ensure no ungrouped files or directories exist


#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Sometimes when administrators delete groups from the password file they neglect to
remove all files owned by those groups from the system.

#### Rationale:
A new user who is assigned the deleted user's user ID or group ID may then end up
"owning" these files, and thus have more access on the system than was intended.

#### Audit:
Run the following command and verify no files are returned:
<pre><code># find / -nogroup</code></pre>

#### Remediation:
Locate files that are owned by users or groups not listed in the system configuration files,
and reset the ownership of these files to some active user on the system as appropriate.

#### CIS Controls:
Version 7
13.2 Remove Sensitive Data or Systems Not Regularly Accessed by Organization
Remove sensitive data or systems not regularly accessed by the organization from the
network. These systems shall only be used as stand alone systems (disconnected from the
network) by the business unit needing to occasionally use the system or completely
virtualized and powered off until needed.

## 6.1.8 Audit SUID executables

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The owner of a file can set the file's permissions to run with the owner's or group's
permissions, even if the user running the program is not the owner or a member of the
group. The most common reason for a SUID program is to enable users to perform
functions (such as changing their password) that require root privileges.

#### Rationale:
There are valid reasons for SUID programs, but it is important to identify and review such
programs to ensure they are legitimate.

#### Audit:
Run the following command to list SUID files:
<pre><code># find / -perm -4000 -print</code></pre>


#### Remediation:
Ensure that no rogue SUID programs have been introduced into the system. Review the
files returned by the action in the Audit section and confirm the integrity of these binaries.

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.

## 6.1.9 Audit SGID executables

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The owner of a file can set the file's permissions to run with the owner's or group's
permissions, even if the user running the program is not the owner or a member of the
group. The most common reason for a SGID program is to enable users to perform
functions (such as changing their password) that require root privileges.

#### Rationale:
There are valid reasons for SGID programs, but it is important to identify and review such
programs to ensure they are legitimate.  Review the files returned by the action in the audit
section and check to see if system binaries have a different md5 checksum than that of
the package. This is an indication that the binary may have been replaced.

#### Audit:
Run the following command to list SGID files:
<pre><code># find / -perm -2000 -print</code></pre>


#### Remediation:
Ensure that no rogue SGID programs have been introduced into the system. Review the
files returned by the action in the Audit section and confirm the integrity of these binaries.

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.
