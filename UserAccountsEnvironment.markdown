# User accounts & environment
This section provides guidance on setting up secure defaults for system and user accounts
and their environment.

## 5.5.1.1 Ensure password expiration is 365 days or less

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>PASS_MAX_DAYS</code> parameter in <code>/etc/login.defs</code allows an administrator to force
passwords to expire once they reach a defined age. It is recommended that the
<code>PASS_MAX_DAYS</code> parameter be set to less than or equal to 365 days.

#### Rationale:
The window of opportunity for an attacker to leverage compromised credentials or
successfully compromise credentials via an online brute force attack is limited by the age of
the password. Therefore, reducing the maximum age of a password also reduces an
attacker's window of opportunity.

#### Audit:
Run the following command and verify <code>PASS_MAX_DAYS</code> conforms to site policy (no more
than 365 days):

<pre><code># grep PASS_MAX_DAYS /etc/login.defs
PASS_MAX_DAYS 365
Run the following command and Review list of users and PASS_MAX_DAYS to verify that all
users' PASS_MAX_DAYS conforms to site policy (no more than 365 days):
# grep -E '^[^:]+:[^!*]' /etc/shadow | cut -d: -f1,5
<user>:<PASS_MAX_DAYS>
</code></pre>

#### Remediation:
Set the <code>PASS_MAX_DAYS</code> parameter to conform to site policy in <code>/etc/login.defs</code> :
<pre><code>PASS_MAX_DAYS 365</code></pre>
Modify user parameters for all users with a password set to match:
<pre><code># chage --maxdays 365 <user></code></pre>

#### Notes:
Note: A value of -1 will disable password expiration. Additionally the password expiration
must be greater than the minimum days between password changes or users will be unable
to change their password.

#### CIS Controls:
Version 7
4.4 Use Unique Passwords
Where multi-factor authentication is not supported (such as local administrator, root, or
service accounts), accounts will use passwords that are unique to that system.

## 5.5.1.2 
