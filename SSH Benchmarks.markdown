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
</code></pre>


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

<pre><code># ls -l ~/.ssh/id_rsa
-rw------- 1 root wheel [date and time] /root/.ssh/id_rsa
</code></pre>

#### Remediation:
Run the following commands to set ownership and permissions on the private SSH host key
files.
<pre><code># chown root:operator ~/.ssh/id_rsa
# chmod </code></pre>

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.

## 5.2.4 Ensure permissions on SSH public host key files

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
An SSH public key is one of two files used in SSH public key authentication. In this
authentication method, a public key is a key that can be used for verifying digital signatures
generated using a corresponding private key. Only a public key that corresponds to a
private key will be able to authenticate successfully.

#### Rationale:
If a public host key file is modified by an unauthorized user, the SSH service may be
compromised.

#### Audit:
Run the following command and verify Access does not grant write or execute permissions
to group or other for all returned files

<pre><code># ls -l ~/.ssh/id_rsa.pub
-rw-r--r-- 1 root wheel [date and time] /root/.ssh/id_rsa.pub</code></pre>

#### Remediation:
Run the following commands to set permissions and ownership on the SSH host public key
files

<pre><code># chown root:operator ~/.ssh/id_rsa.pub
# chmod </code></pre>

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.

## 5.2.5 Ensure SSH LogLevel is appropriate

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:

<code>INFO</code> level is the basic level that only records login activity of SSH users. In many situations,
such as Incident Response, it is important to determine when a particular user was active
on a system. The logout record can eliminate those users who disconnected, which helps
narrow the field.

<code>VERBOSE</code> level specifies that login and logout activity as well as the key fingerprint for any
SSH key used for login will be logged. This information is important for SSH key
management, especially in legacy environments.

#### Rationale:
SSH provides several logging levels with varying amounts of verbosity. <code>DEBUG</code> is specifically
**not** recommended other than strictly for debugging SSH communications since it provides
so much data that it is difficult to identify important security information.

#### Audit:
Run the following command and verify that output matches:

<pre><code># sshd -T | grep loglevel
LogLevel VERBOSE
OR
loglevel INFO</code></pre>

#### Remediation:
Edit the <code>/etc/ssh/sshd_config</code> file to set the parameter as follows:
<pre><code>LogLevel VERBOSE
OR
LogLevel INFO</code></pre>

#### Default Value:
LogLevel INFO


#### CIS Controls:
Version 7
6.2 Activate audit logging
Ensure that local logging has been enabled on all systems and networking devices.
6.3 Enable Detailed Logging
Enable system logging to include detailed information such as an event source, date,
user, timestamp, source addresses, destination addresses, and other useful elements.

## 5.2.6 Ensure SSH X11 forwarding is disabled

#### Profile Applicability:
* Level 1 - Workstation
* Level 2 - Server

#### Description:
The X11Forwarding parameter provides the ability to tunnel X11 traffic through the
connection to enable remote graphic connections.

#### Rationale:
Disable X11 forwarding unless there is an operational requirement to use X11 applications
directly. There is a small risk that the remote X11 servers of users who are logged in via
SSH with X11 forwarding could be compromised by other users on the X11 server. Note
that even if X11 forwarding is disabled, users can always install their own forwarders.

#### Audit:
Run the following command and verify that output matches:
<pre><code># sshd -T | grep x11forwarding
X11Forwarding no</code></pre>

#### Remediation:
Edit the <code>/etc/ssh/sshd_config</code> file to set the parameter as follows:
<pre><code>X11Forwarding no</code></pre>

#### CIS Controls:
Version 7
9.2 Ensure Only Approved Ports, Protocols and Services Are Running
Ensure that only network ports, protocols, and services listening on a system with
validated business needs, are running on each system.

## 5.2.7 Ensure SSH MaxAuthTries is set to 4 or less

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>MaxAuthTries</code> parameter specifies the maximum number of authentication attempts
permitted per connection. When the login failure count reaches half the number, error
messages will be written to the <code>syslog</code> file detailing the login failure.

#### Rationale:
Setting the <code>MaxAuthTries</code> parameter to a low number will minimize the risk of successful
brute force attacks to the SSH server. While the recommended setting is 4, set the number
based on site policy.

#### Audit:
Run the following command and verify that output <code>MaxAuthTries</code> is 4 or less:
<pre><code># sshd -T | grep maxauthtries
MaxAuthTries 4</code></pre>

#### Remediation:
Edit the <code>/etc/ssh/sshd_config</code> file to set the parameter as follows:
<pre><code>MaxAuthTries 4</code></pre>

#### Default Value:
MaxAuthTries 6

#### CIS Controls:
Version 7
16.13 Alert on Account Login Behavior Deviation
Alert when users deviate from normal login behavior, such as time-of-day, workstation
location and duration.

## 5.2.8 Ensure SSH IgnoreRhosts is enabled

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>IgnoreRhosts</code> parameter specifies that <code>.rhosts</code> and <code>.shosts</code> files will not be used in
<code>RhostsRSAAuthentication</code> or <code>HostbasedAuthentication</code>.

#### Rationale:
Setting this parameter forces users to enter a password when authenticating with ssh.

#### Audit:
Run the following command and verify that output matches:
<pre><code># sshd -T | grep ignorerhosts
IgnoreRhosts yes</code></pre>

#### Remediation:
Edit the <code>/etc/ssh/sshd_config</code> file to set the parameter as follows:
<pre><code>IgnoreRhosts yes</code></pre>

#### Default Value:
IgnoreRhosts yes

#### CIS Controls:
Version 7
9.2 Ensure Only Approved Ports, Protocols and Services Are Running
Ensure that only network ports, protocols, and services listening on a system with
validated business needs, are running on each system

## 5.2.9 Ensure SSH HostbasedAuthentication is disabled

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>HostbasedAuthentication</code> parameter specifies if authentication is allowed through
trusted hosts via the user of <code>.rhosts</code>, or <code>/etc/hosts.equiv</code>, along with successful public
key client host authentication. This option only applies to SSH Protocol Version 2.


#### Rationale:
Even though the <code>.rhosts</code> files are ineffective if support is disabled in <code>/etc/pam.conf</code>,
disabling the ability to use <code>.rhosts</code> files in SSH provides an additional layer of protection.

#### Audit:
Run the following command and verify that output matches:
<pre><code># sshd -T | grep hostbasedauthentication
HostbasedAuthentication no</code></pre>

#### Remediation:
Edit the <code>/etc/ssh/sshd_config</code> file to set the parameter as follows:
<pre><code>HostbasedAuthentication no</code></pre>

#### Default Value:
HostbasedAuthentication no

#### CIS Controls:
Version 7
16.3 Require Multi-factor Authentication
Require multi-factor authentication for all user accounts, on all systems, whether
managed onsite or by a third-party provider.

## 5.2.10 Ensure SSH root login is disabled

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation


#### Description:
The <code>PermitRootLogin</code> parameter specifies if the root user can log in using ssh. The default
is no.

#### Rationale:
Disallowing root logins over SSH requires system admins to authenticate using their own
individual account, then escalating to root via <code>sudo</code> or <code>su</code>. This in turn limits opportunity for
non-repudiation and provides a clear audit trail in the event of a security incident.

#### Audit:
Run the following command and verify that output matches:
<pre><code># sshd -T | grep permitrootlogin
PermitRootLogin no</code></pre>

#### Remediation:
Edit the <code>/etc/ssh/sshd_config</code> file to set the parameter as follows:
<pre><code>PermitRootLogin no</code></pre>

#### Default Value:
PermitRootLogin no

#### CIS Controls:
Version 7
4.3 Ensure the Use of Dedicated Administrative Accounts
Ensure that all users with administrative account access use a dedicated or secondary
account for elevated activities. This account should only be used for administrative
activities and not internet browsing, email, or similar activities.

## 5.2.11 Ensure SSH PermitEmptyPasswords is disabled

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>PermitEmptyPasswords</code> parameter specifies if the SSH server allows login to accounts
with empty password strings.

#### Rationale:
Disallowing remote shell access to accounts that have an empty password reduces the
probability of unauthorized access to the system

#### Audit:
Run the following command and verify that output matches:
<pre><code># sshd -T | grep permitemptypasswords
PermitEmptyPasswords no</code></pre>

#### Remediation:
Edit the <code>/etc/ssh/sshd_config</code> file to set the parameter as follows:
<pre><code>PermitEmptyPasswords no</code></pre>

#### Default Value:
PermitEmptyPasswords no

#### CIS Controls:
Version 7
16.3 Require Multi-factor Authentication
Require multi-factor authentication for all user accounts, on all systems, whether
managed onsite or by a third-party provider.
