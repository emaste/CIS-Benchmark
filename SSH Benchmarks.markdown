# **SSH Benchmarks**

## 5.2.1 Secure sshd_config:

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The /etc/ssh/sshd_config file contains configuration specifications for sshd. The
command below sets the owner and group of the file to root.

#### Rationale:
The /etc/ssh/sshd_config file needs to be protected from unauthorized changes by nonprivileged users.
#### Audit:
Run the following command and verify Uid and Gid are both 0/root and Access does not
grant permissions to group or other:

<pre><code># stat /etc/ssh/sshd_config
86 963266 -rw-r--r-- 1 root wheel 1935248 3330 "Jun 22 13:28:04 2020" "Nov 1 00:28:52 2019" "Jun 22 13:21:51 2020" "Nov 1 00:28:52 2019" 32768 8 0 /etc/ssh/sshd_config</code></pre>


#### Remediation:
Run the following commands to set ownership and permissions on /etc/ssh/sshd_config:
<pre><code># chown root:operator /etc/ssh/sshd_config</code></pre>
<pre><code># chmod og-rwx /etc/ssh/sshd_config</code></pre>

#### CIS Controls:

Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software



## 5.2.2 Ensure SSH access is limited:

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:

There are several options available to limit which users and group can access the system
via SSH. It is recommended that at least one of the following options be leveraged:

<code>AllowUsers</code>

The <code>AllowUsers</code> variable gives the system administrator the option of allowing specific
users to ssh into the system. The list consists of space separated user names. Numeric user
IDs are not recognized with this variable. If a system administrator wants to restrict user
access further by only allowing the allowed users to log in from a particular host, the entry
can be specified in the form of user@host.

<code>AllowGroups</code>

The <code>AllowGroups</code> variable gives the system administrator the option of allowing specific
groups of users to ssh into the system. The list consists of space separated group names.
Numeric group IDs are not recognized with this variable.

<code>DenyUsers</code>

The <code>DenyUsers</code> variable gives the system administrator the option of denying specific users
to ssh into the system. The list consists of space separated user names. Numeric user IDs
are not recognized with this variable. If a system administrator wants to restrict user
access further by specifically denying a user's access from a particular host, the entry can
be specified in the form of user@host.

<code>DenyGroups</code>

The <code>DenyGroups</code> variable gives the system administrator the option of denying specific
groups of users to ssh into the system. The list consists of space separated group names.
Numeric group IDs are not recognized with this variable.

#### Rationale:

Restricting which users can remotely access the system via SSH will help ensure that only
authorized users access the system.

#### Audit:

Run the following command:

<pre><code>sshd -T | grep -E '^\s*(allow|deny)(users|groups)\s+\S+'</code></pre> 

Verify that the output matches at least one of the following lines:

<pre><code>AllowUsers [userlist]
  
AllowGroups [grouplist]
  
DenyUsers [userlist]
  
DenyGroups [grouplist]</code></pre>
  
  
  
#### Remediation:

Edit the /etc/ssh/sshd_config file to set one or more of the parameter as follows:

<pre><code>AllowUsers [userlist]
  
AllowGroups [grouplist]
  
DenyUsers [userlist]
  
DenyGroups [grouplist]</code></pre>
  
  
#### CIS Controls:
Version 7
4.3 Ensure the Use of Dedicated Administrative Accounts
Ensure that all users with administrative account access use a dedicated or secondary
account for elevated activities. This account should only be used for administrative
activities and not internet browsing, email, or similar activities


## 5.2.3 Secure permissions on SSH private host key files:

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:

An SSH private key is one of two files used in SSH public key authentication. In this
authentication method, The possession of the private key is proof of identity. Only a private
key that corresponds to a public key will be able to authenticate successfully. The private
keys need to be stored and handled carefully, and no copies of the private key should be
distributed.

#### Rationale:
If an unauthorized user obtains the private SSH host key file, the host could be
impersonated


#### Audit:
Run the following command and verify Uid is 0/root and and Gid is 0/root. Ensure group
and other do not have permissions

<pre><code># find /etc/ssh -xdev -type f -name 'ssh_host_*_key' -exec stat {} \;
 File: ‘/etc/ssh/ssh_host_rsa_key’
 Size: 1679 Blocks: 8 IO Block: 4096 regular file
Device: ca01h/51713d Inode: 8628138 Links: 1
Access: (0600/-rw-------) Uid: ( 0/ root) Gid: ( 0/root)
Access: 2018-10-22 18:24:56.861750616 +0000
Modify: 2018-10-22 18:24:56.861750616 +0000
Change: 2018-10-22 18:24:56.873750616 +0000
Birth: -
 File: ‘/etc/ssh/ssh_host_ecdsa_key’
 Size: 227 Blocks: 8 IO Block: 4096 regular file
Device: ca01h/51713d Inode: 8631760 Links: 1
Access: (0600/-rw-------) Uid: ( 0/ root) Gid: ( 0/root)
Access: 2018-10-22 18:24:56.897750616 +0000
Modify: 2018-10-22 18:24:56.897750616 +0000
Change: 2018-10-22 18:24:56.905750616 +0000
Birth: -
 File: ‘/etc/ssh/ssh_host_ed25519_key’
 Size: 387 Blocks: 8 IO Block: 4096 regular file
Device: ca01h/51713d Inode: 8631762 Links: 1
Access: (0600/-rw-------) Uid: ( 0/ root) Gid: ( 0/root)
Access: 2018-10-22 18:24:56.945750616 +0000
Modify: 2018-10-22 18:24:56.945750616 +0000
Change: 2018-10-22 18:24:56.957750616 +0000
Birth: - </code></pre>

#### Remediation:
Run the following commands to set ownership and permissions on the private SSH host key
files.
<pre><code># find /etc/ssh -xdev -type f -name 'ssh_host_*_key' -exec chown root:root {}
\;
# find /etc/ssh -xdev -type f -name 'ssh_host_*_key' -exec chmod 0600 {} \;</code></pre>

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.
