# User accounts & environment
This section provides guidance on setting up secure defaults for system and user accounts
and their environment.

## 5.5.1.1 Ensure password expiration is 365 days or less

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>passwordtime</code> parameter in <code>/etc/login.conf</code> allows an administrator to force
passwords to expire once they reach a defined age. It is recommended that the
<code>passwordtime</code> parameter be set to less than or equal to 365 days.

#### Rationale:
The window of opportunity for an attacker to leverage compromised credentials or
successfully compromise credentials via an online brute force attack is limited by the age of
the password. Therefore, reducing the maximum age of a password also reduces an
attacker's window of opportunity.

#### Audit:
Run the following command and verify <code>passwordtime</code> conforms to site policy (no more
than 365 days):

<pre><code># grep passwordtime /etc/login.conf
:passwordtime=365d:\</code></pre>

Run the following command and Review list of users and <code>passwordtime</code> to verify that all
users' <code>passwordtime</code> conforms to site policy (no more than 365 days):

<pre><code># grep -E '^[^:]+:[^!*]' /etc/master.passwd | cut -d: -f1,5
[user]:[passwordtime]
</code></pre>

#### Remediation:
Set the <code>passwordtime</code> parameter for each user group to conform to site policy in <code>/etc/login.conf</code> :
<pre><code>:passwordtime=365d</code></pre>
Modify user parameters for all users with a password set to match:
<pre><code># pw usermod -e 365 [user]</code></pre>
After the change, run the following command:
<pre><code># cap_mkdb /etc/login.conf</code></pre>

#### Notes:
Note: A value of -1 will disable password expiration. Additionally the password expiration
must be greater than the minimum days between password changes or users will be unable
to change their password.

#### CIS Controls:
Version 7

4.4 Use Unique Passwords
Where multi-factor authentication is not supported (such as local administrator, root, or
service accounts), accounts will use passwords that are unique to that system.

## 5.5.1.2 Ensure password expiration warning days is 7 or more

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>warnpassword</code> parameter in <code>/etc/login.conf</code> allows an administrator to notify users
that their password will expire in a defined number of days. It is recommended that the
<code>warnpassword</code> parameter be set to 7 or more days.

#### Rationale:
Providing an advance warning that a password will be expiring gives users time to think of
a secure password. Users caught unaware may choose a simple password or write it down
where it may be discovered.

#### Audit:
Run the following command and verify <code>warnpassword</code> for each user group conforms to site policy (No less than
7 days) and is enabled:

<pre><code># grep warnpassword /etc/login.conf
:warnpassword=7d:\</code></pre>


#### Remediation:
Set the <code>warnpassword</code> parameter to 7 in <code>/etc/login.conf</code> and ensure it is enabled for each user group :
<pre><code>warnpassword=7d</code></pre>

Then run the following command:
<pre><code># cap_mkdb /etc/login.conf</code></pre>


#### CIS Controls:
Version 7

4.4 Use Unique Passwords
Where multi-factor authentication is not supported (such as local administrator, root, or
service accounts), accounts will use passwords that are unique to that system.

## 5.5.2 Ensure default user shell timeout is 900 seconds or less

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The default <code>TMOUT</code> determines the shell timeout for users. The <code>TMOUT</code> value is measured in
seconds.

#### Rationale:
Having no timeout value associated with a shell could allow an unauthorized user access to
another user's shell session (e.g. user walks away from their computer and doesn't lock the
screen). Setting a timeout value at least reduces the risk of this happening.

#### Audit:
Run the following commands and verify the returned TMOUT line is 900 or less

<pre><code># grep "^TMOUT" /etc/profile
readonly TMOUT=900 ; export TMOUT
</code></pre>

#### Remediation:
Edit the <code>/etc/profile</code> file (and the appropriate
files for any other shell supported on your system) and add or edit any umask parameters
as follows:
<pre><code>readonly TMOUT=900 ; export TMOUT</code></pre>
Note that setting the value to readonly prevents unwanted modification during runtime.

#### Notes:
The audit and remediation in this recommendation only applies to the <code>sh</code>. If other shells
are supported on the system, it is recommended that their configuration files also are
checked. Other methods of setting a timeout exist for other shells not covered here.
Ensure that the timeout conforms to your local policy.

#### CIS Controls:
Version 7

16.11 Lock Workstation Sessions After Inactivity
Automatically lock workstation sessions after a standard period of inactivity.

## 5.5.3 Ensure default group for the root account is GID 0

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>pw</code> command can be used to specify which group the root user belongs to. This
affects permissions of files that are created by the root user.

#### Rationale:
Using <code>GID 0</code> for the <code>root</code> account helps prevent <code>root</code> -owned files from accidentally
becoming accessible to non-privileged users.

#### Audit:
Run the following command and verify the result is 0 :
<pre><code># grep "^root:" /etc/passwd | cut -f4 -d:
0</code></pre>

#### Remediation:
Run the following command to set the root user default group to GID 0 :
<pre><code># pw usermod root -g 0</code></pre>

##### CIS Controls:
Version 7

5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.

## 5.5.4 Ensure default user umask is 027 or more restrictive

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The default <code>umask</code> determines the permissions of files created by users. The user creating
the file has the discretion of making their files and directories readable by others via the
chmod command. Users who wish to allow their files and directories to be readable by
others by default may choose a different default umask by inserting the umask command
into the standard shell configuration files ( <code>.profile</code> and <code>.cshrc</code>) in their home
directories.

#### Rationale:
Setting a very secure default value for <code>umask</code> ensures that users make a conscious choice
about their file permissions. A default <code>umask</code> setting of <code>077</code> causes files and directories
created by users to not be readable by any other user on the system. A <code>umask</code> of <code>027</code> would
make files and directories readable by users in the same Unix group, while a <code>umask</code> of <code>022</code>
would make files readable by every user on the system.

#### Audit:
Run the following commands and verify all umask lines returned are 027 or more
restrictive.
<pre><code># grep "umask" /etc/profile ~/.cshrc /etc/profile
umask 027</code></pre>

#### Remediation:
Edit the <code>/etc/profile</code> and <code>~/.cshrc files</code> (and the appropriate
files for any other shell supported on your system) and add or edit any <code>umask</code> parameters
as follows:
<pre><code>umask 027</code></pre>

#### Notes:
The audit and remediation in this recommendation apply to <code>sh</code> and <code>tcsh</code>. If other shells
are supported on the system, it is recommended that their configuration files also are
checked.

Other methods of setting a default user <code>umask</code> exist however the shell configuration files
are the last run and will override other settings if they exist therefor our recommendation
is to configure in the shell configuration files. If other methods are in use in your
environment they should be audited and the shell configs should be verified to not
override.

#### CIS Controls:
Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.

13 Data Protection
Data Protection

## 5.5.5 Ensure root login is restricted to system console

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The file /etc/ttys contains a list of valid terminals that may be logged into directly by root.

#### Rationale:
Since the system console has special properties to handle emergency situations, it is
important to ensure that the console is in a physically secure location and that
unauthorized consoles have not been defined.

#### Audit:
<pre><code># cat /etc/ttys</code></pre>

#### Remediation:
Remove entries for any consoles that are not in a physically secure location. This can be done by 
changing the status of those consoles from secure to insecure.

#### CIS Controls:
Version 7

5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software

## 5.5.6 Ensure access to the su command is restricted

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>su</code> command allows a user to run a command or shell as another user. Normally, the <code>su</code> command can be executed by any user. By uncommenting the
<code>pam_wheel.so</code> statement in <code>/etc/pam.d/su</code> , the <code>su</code> command will only allow users in the
wheel group to execute <code>su</code> .

#### Rationale:
Restricting the use of <code>su</code>, provides system administrators better
control of the escalation of user privileges to execute privileged commands. 

#### Audit:
Run the following command and verify output includes matching line:
<pre><code># grep pam_group.so /etc/pam.d/su
auth requisite pam_group.so no_warn group=wheel root_only fail_safe ruser</code></pre>

Run the following command and verify users in wheel group match site policy. If no users
are listed, only root will have access to <code>su</code>.
<pre><code># grep wheel /etc/group
wheel:x:0:root,[user list]</code></pre>

#### Remediation:
Add the following line to the <code>/etc/pam.d/su</code> file:
<pre><code>auth requisite pam_group.so no_warn group=wheel root_only fail_safe ruser</code></pre>

Create a comma separated list of users in the wheel statement in the <code>/etc/group</code> file:
<pre><code>wheel:x:<GID>:root,[user list]</code></pre>
Example:
<pre><code>wheel:x:10:root,user1,user2,user3</code></pre>

#### CIS Controls:
Version 7

5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.
