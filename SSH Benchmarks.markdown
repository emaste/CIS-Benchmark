# **SSH Benchmarks**

## 5.2.1 Secure sshd_config

### Profile Applicability:
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

<pre><code># stat /etc/ssh/sshd_config</code></pre>
  
<pre><code># Access: (0600/-rw-------) Uid: ( 0/ root) Gid: ( 0/ root)</code></pre>


#### Remediation:
Run the following commands to set ownership and permissions on /etc/ssh/sshd_config:
<pre><code># chown root:root /etc/ssh/sshd_config</code></pre>
<pre><code># chmod og-rwx /etc/ssh/sshd_config</code></pre>

#### CIS Controls:

Version 7
5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software
