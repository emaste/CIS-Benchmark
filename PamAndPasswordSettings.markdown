# 5.4 PAM and Password Settings

## 5.4.1 Ensure password creation requirements are configured

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>pam_passwdqc.so</code> module checks the strength of passwords. It performs checks such
as making sure a password is not a dictionary word, it is a certain length, contains a mix of
characters (e.g. alphabet, numeric, other) and more. The following are definitions of the
<code>pam_passwdqc.so</code> options.

* <code>min=N0,N1,N2,N3,N4</code>

   <code>(min=disabled,24,12,8,7)</code> The minimum allowed password lengths for
	 different kinds of	passwords/passphrases.	The keyword disabled
	 can be used to disallow passwords of a given kind regardless of
	 their length.  Each subsequent number is required to be no	larger
	 than the preceding	one.

   <code>N0</code>	is used	for passwords consisting of characters from one	char-
	 acter class only.	The character classes are: digits, lower-case
	 letters, upper-case letters, and other characters.	 There is also
	 a special class for non-ASCII characters which could not be clas-
	 sified, but are assumed to	be non-digits.

   <code>N1</code>	is used	for passwords consisting of characters from two	char-
	 acter classes, which do not meet the requirements for a
	 passphrase.

	 <code>N2</code>	is used	for passphrases.  A passphrase must consist of suffi-
	 cient words.

	 <code>N3</code>	and <code>N4</code> are used	for passwords consisting of characters from
	 three and four character classes, respectively.

	 When calculating the number of character classes, upper-case let-
	 ters used as the first character and digits used as the last
	 character of a password are not counted.


* <code>try_first_pass</code>

   Retrieve the password from a previous stacked PAM module. If
   not available, then prompt the user for a password.

* <code>retry=3</code> 
    Allow 3 tries before sending back a failure.

Strong passwords protect systems from being hacked through brute force methods. To ensure at least 4 classes of characters are used to enforce complex passwords.

#### Audit:
Verify password creation requirements conform to organization policy:

Run the following command and verify that retry conforms to organization policy.
<pre><code># grep pam_passwdqc.so /etc/pam.d/passwd</code></pre>
Output should be similar to:

<pre><code>password        requisite       pam_passwdqc.so         min=disabled,disabled,disabled,disabled,14 enforce=everyone</code></pre>


#### Remediation:
Edit the file <code>/etc/pam.d/passwd</code> and add or modify the following line for to ensur
password length and complexity conform to site policy

<pre><code>password        requisite       pam_passwdqc.so         min=disabled,disabled,disabled,disabled,14 enforce=everyone</code></pre>


#### Notes:
all default authselect profiles have pam_pwquality enabled with the expectation that
options will be specified in pwquality.conf

#### CIS Controls:
Version 7

4.4 Use Unique Passwords
Where multi-factor authentication is not supported (such as local administrator, root, or
service accounts), accounts will use passwords that are unique to that system

## 5.4.2 Ensure password hashing algorithm is SHA-512

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The commands below ensure password encryption is in <code>sha512</code> (a much stronger
hashing algorithm). All existing accounts will need to perform a password change to
upgrade the stored hashes to the new algorithm.

#### Rationale:
The <code>SHA-512</code> algorithm provides much stronger hashing than MD5, thus providing
additional protection to the system by increasing the level of effort for an attacker to
successfully determine passwords.

Note that these change only apply to accounts configured on the local system.

#### Audit:
Verify password hashing algorithm is <code>sha512</code>. This setting is configured with the
<code>passwd_format</code> <code>sha512</code> option found in <code>/etc/login.conf</code>

Run the following command:
pre><code># grep -E '^\s*password\s+sufficient\s+pam_unix.so\s+.*sha512\s*.*$'
/etc/pam.d/password-auth /etc/pam.d/system-auth</code></pre>

The output should be similar to:

<pre><code>:passwd_format=sha512:\</code></pre>

#### Remediation:
Set password hashing algorithm to sha512. Modify or enable the <code>passwd_format</code> lines in the <code>/etc/login.conf</code>file for each user group.

<pre><code>:passwd_format=sha512:\</code></pre>

Than run the following command:

<pre><code># cap_mkdb /etc/login.conf</code></pre>

#### Notes:

Additional module options may be set, recommendation only covers those listed here.
If it is determined that the password algorithm being used is not <code>SHA-512</code>, once it is
changed, it is recommended that all user ID's be immediately expired and forced to change
their passwords on next login. To accomplish that, the following commands can be used.
Any system accounts that need to be expired should be carefully done separately by the
system administrator to prevent any potential problems.

<pre><code># awk -F: '( $3<'"$(awk '/^\s*UID_MIN/{print $2}' /etc/login.conf)"' && $1 !=
"nfsnobody" ) { print $1 }' /etc/passwd | xargs -n 1 chage -d 0</code></pre>

#### CIS Controls:
Version 7

16.4 Encrypt or Hash all Authentication Credentials
Encrypt or hash with a salt all authentication credentials when stored
