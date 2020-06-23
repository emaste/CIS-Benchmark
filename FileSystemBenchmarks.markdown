# File System Permissions

## 6.1.1 Audit system file permissions

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:
The RPM package manager has a number of useful options. One of these, the -V for RPM
option, can be used to verify that system packages are correctly installed. The -V option can
be used to verify a particular package or to verify all system packages. If no output is
returned, the package is installed correctly. The following table describes the meaning of
output from the verify option:

Code Meaning
S File size differs.
M File mode differs (includes permissions and file type).
5 The MD5 checksum differs.
D The major and minor version numbers differ on a device file.
L A mismatch occurs in a link.
U The file ownership differs.
G The file group owner differs.
T The file time (mtime) differs.

The rpm -qf command can be used to determine which package a particular file belongs to.
For example the following commands determines which package the /bin/bash file
belongs to:

# rpm -qf /bin/bash
bash-4.1.2-29.el6.x86_64
# dpkg -S /bin/bash
bash: /bin/bash

To verify the settings for the package that controls the /bin/bash file, run the following:

# rpm -V bash-4.1.2-29.el6.x86_64
.M....... /bin/bash
# dpkg --verify bash
??5?????? c /etc/bash.bashrc

Note that you can feed the output of the rpm -qf command to the rpm -V command:

# rpm -V `rpm -qf /etc/passwd`
.M...... c /etc/passwd
S.5....T c /etc/printcap

#### Rationale:
It is important to confirm that packaged system files and directories are maintained with
the permissions they were intended to have from the OS vendor.

#### Audit:
Run the following command to review all installed packages. Note that this may be very
time consuming and may be best scheduled via the cron utility. It is recommended that the
output of this command be redirected to a file that can be reviewed later.

# rpm -Va --nomtime --nosize --nomd5 --nolinkto > <filename>

#### Remediation:
Correct any discrepancies found and rerun the audit until output is clean or risk is
mitigated or accepted.

#### References:
1. http://docs.fedoraproject.org/enUS/Fedora_Draft_Documentation/0.1/html/RPM_Guide/index.html

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

## 6.2.3 Ensure permissions on /etc/shadow are configured

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>/etc/shadow</code> file is used to store the information about user accounts that is critical to
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

## 6.2.4 Ensure permissions on /etc/group are configured

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
