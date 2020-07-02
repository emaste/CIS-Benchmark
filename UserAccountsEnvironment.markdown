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
Set the <code>passwordtime</code> parameter to conform to site policy in <code>/etc/login.conf</code> :
<pre><code>:passwordtime=365d</code></pre>
Modify user parameters for all users with a password set to match:
<pre><code># pw usermod -e 365 [user]</code></pre>

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
Run the following command and verify <code>warnpassword</code> conforms to site policy (No less than
7 days) and is enabled:

<pre><code># grep warnpassword /etc/login.conf
:warnpassword=7d:\</code></pre>


#### Remediation:
Set the <code>warnpassword</code> parameter to 7 in <code>/etc/login.conf</code> and ensure it is enabled :
<pre><code>warnpassword=7d</code></pre>

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
