# 4 Logging and Auditing

The items in this section describe how to configure logging, log monitoring, and auditing,
using tools included in most distributions. In addition to the local log files created by the steps in this section, it is also recommended
that sites collect copies of their system logs on a secure, centralized log server via an
encrypted connection. Not only does centralized logging help sites correlate events that
may be occurring on multiple systems, but having a second copy of the system log
information may be critical after a system compromise where the attacker has modified the
local log files on the affected system(s). If a log correlation system is deployed, configure it
to process the logs described in this section.

It is important that all logs described in this section be monitored on a regular basis and
correlated to determine trends. A seemingly innocuous entry in one log could be more
significant when compared to an entry in another log.

**Note on log file permissions:** There really isn't a "one size fits all" solution to the
permissions on log files. Many sites utilize group permissions so that administrators who
are in a defined security group, such as "wheel" do not have to elevate privileges to root in
order to read log files. Also, if a third party log aggregation tool is used, it may need to have
group permissions to read the log files, which is preferable to having it run setuid to root.
Therefore, there are two remediation and audit steps for log file permissions. One is for
systems that do not have a secured group method implemented that only permits root to
read the log files (<code>root:operator 600</code>). The other is for sites that do have such a setup and are
designated as <code>root:securegrp 640</code> where securegrp is the defined security group (in some
cases wheel).



## 4.1.1 Ensure auditing is enabled
The capturing of system events provides system administrators with information to allow
them to determine if unauthorized access to their system is occurring

## 4.1.1.1 Ensure auditd is installed

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:
auditd is the userspace component to the FreeBSD Auditing System. It's responsible for
writing audit records to the disk

#### Rationale:
The capturing of system events provides system administrators with information to allow
them to determine if unauthorized access to their system is occurring.

#### Audit:
Run the following command and verify auditd is installed:

# UPDATE THIS

#### Remediation:
Run the following command to Install auditd
<pre><code># pkg install auditd</code></pre>

#### CIS Controls:
Version 7

6.2 Activate audit logging
Ensure that local logging has been enabled on all systems and networking devices.

6.3 Enable Detailed Logging
Enable system logging to include detailed information such as an event source, date,
user, timestamp, source addresses, destination addresses, and other useful elements.

## 4.1.1.2 Ensure auditd service is enabled

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:
Turn on the <code>auditd</code> daemon to record system events.

#### Rationale:
The capturing of system events provides system administrators with information to allow
them to determine if unauthorized access to their system is occurring.

#### Audit:
Run the following command to verify auditd is enabled:
<pre><code># service auditd status
auditd is running as pid [PID].</code></pre>

Verify that <code>auditd</code> is running.

#### Remediation:
Add the following line <code>/etc/rc.conf</code>
<pre><code>auditd_enable="YES"</code></pre>
Run the following command to enable auditd:
<pre><code># service auditd start</code></pre>

#### Notes:
Additional methods of enabling a service exist. Consult your distribution documentation for
appropriate methods.

#### CIS Controls:
Version 7

6.2 Activate audit logging
Ensure that local logging has been enabled on all systems and networking devices.

6.3 Enable Detailed Logging
Enable system logging to include detailed information such as an event source, date,
user, timestamp, source addresses, destination addresses, and other useful elements.

## 4.1.2 Configure Data Retention

When auditing, it is important to carefully configure the storage requirements for audit
logs. By default, auditd will max out the log files at 2MB and retain only 4 copies of them.
Older versions will be deleted. It is possible on a system that the 20 MBs of audit logs may
fill up the system causing loss of audit data. While the recommendations here provide
guidance, check your site policy for audit storage requirements.

## 4.1.2.1 Ensure audit log storage size is configured (Scored)

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:
Configure the maximum size of the audit log file. Once the log reaches the maximum size, it
will be rotated and a new log file will be started.

#### Rationale:
It is important that an appropriate size is determined for log files so that they do not impact
the system and audit data is not lost.

#### Audit:
Run the following command and ensure output is in compliance with site policy:
<pre><code># grep filesz: /etc/security/audit_control
filesz: [bytes]</code></pre>

#### Remediation:
Set the following parameter in <code>/etc/security/audit_control</code> in accordance with site policy:
<pre><code>filesz: [bytes]</code></pre>

#### Notes:

For convenience, the log size may be	expressed with suffix letters: B (Bytes), K (Kilobytes), M (Megabytes), or G (Gigabytes). For example, 2M is	the same as 2097152.

Other methods of log rotation may be appropriate based on site policy.

#### CIS Controls:
Version 7

6.4 Ensure adequate storage for logs

Ensure that all systems that store logs have adequate storage space for the logs
generated.

## 4.1.2.2 Ensure audit logs are not automatically deleted

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:
The <code>expire-after</code> setting determines whent he audit log file will expire and be removed. This may be after a time period	has passed since the file was last written to	or when	the aggregate of all the trail files have reached a specified size or a combination of both. If no expire-after parameter is	given then audit log files will	not expire and be	removed	by the audit control system.

#### Rationale:
In high security contexts, the benefits of maintaining a long audit history exceed the cost of
storing the audit history.

#### Audit:
Run the following command and verify output matches:
<pre><code># grep expire-after: /etc/security/audit_control</code></pre>
There should be no output.

#### Remediation:
Remove the following parameter in <code>/etc/security/audit_control</code>:
<pre><code>expire-after: [time]</code></pre>

#### CIS Controls:
Version 7

6.4 Ensure adequate storage for logs

Ensure that all systems that store logs have adequate storage space for the logs
generated.

## 4.1.2.3 Ensure system is disabled when audit logs are full

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:
The auditd daemon can be configured to halt the system when the audit logs are full.

#### Rationale:
In high security contexts, the risk of detecting unauthorized access or nonrepudiation
exceeds the benefit of the system's availability.

#### Audit:
Run the following command and verify output matches:
<pre><code># grep policy: /etc/security/audit_control
policy:ahlt</code></pre>
Other policy flags may be in use, ensure that <code>ahlt</code> is included.

#### Remediation:
Set the following parameters in <code>/etc/audit/auditd_control</code>:
<pre><code>policy:ahlt</code></pre>

## 4.1.3 Ensure login and logout events are collected

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:
Monitor login and logout events. The parameters below track changes to files associated
with login/logout events. The file /var/log/faillog tracks failed events from login. The
file /var/log/lastlog maintain records of the last time a user successfully logged in.

#### Rationale:
Monitoring login/logout events could provide a system administrator with information
associated with brute force attacks against user logins.

#### Audit:
Run the following command and verify output matches:
<pre><code># grep flags: /etc/security/audit_control
flags:lo</code></pre>
Other policy flags may be in use, ensure that <code>lo</code> is included.

#### Remediation:
Set the following parameters in <code>/etc/audit/auditd_control</code>:
<pre><code>policy:lo</code></pre>

#### Notes:
Reloading the auditd config to set active settings may require a system reboot.

#### CIS Controls:

Version 7

4.9 Log and Alert on Unsuccessful Administrative Account Login
Configure systems to issue a log entry and alert on unsuccessful logins to an
administrative account.

16.13 Alert on Account Login Behavior Deviation
Alert when users deviate from normal login behavior, such as time-of-day, workstation
location and duration.

## 4.1.4 Ensure events that modify the system's network environment are collected

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:
Record changes to network environment files or system calls. The below parameters
monitor Audit events related to network actions such as <code>connect(2)</code> and <code>accept(2)</code>.

#### Rationale:
Monitoring <code>connect(2)</code> and <code>accept(2)</code> will identify potential unauthorized changes
to host and domainname of a system. The changing could potentially break
security parameters that are set based on those actions. 

#### Audit:
Run the following command and verify output matches:
<pre><code># grep flags: /etc/security/audit_control
flags:nt</code></pre>
Other policy flags may be in use, ensure that <code>nt</code> is included.


#### Remediation:
Set the following parameters in <code>/etc/audit/auditd_control</code>:
<pre><code>policy:nt</code></pre>

#### Notes:
Reloading the auditd config to set active settings may require a system reboot.

#### CIS Controls:
Version 7

5.5 Implement Automated Configuration Monitoring Systems

Utilize a Security Content Automation Protocol (SCAP) compliant configuration
monitoring system to verify all security configuration elements, catalog approved
exceptions, and alert when unauthorized changes occur.

##  4.1.5 Ensure discretionary access control permission modification events are collected

#### Profile Applicability:
* Level 2 - Server
* Level 2 - Workstation

#### Description:
Audit events where file attribute modification occurs, such as by <code>chown(8)</code>, <code>chflags(1)</code>, and <code>flock(2)</code>. This will monitor changes to file permissions, attributes, ownership and group. The parameters in this section track changes for system calls that affect file permissions and attributes. 

#### Rationale:
Monitoring for changes in file attributes could alert a system administrator to activity that
could indicate intruder activity or policy violation.

#### Audit:
Run the following command and verify output matches:
<pre><code># grep flags: /etc/security/audit_control
flags:fm</code></pre>
Other policy flags may be in use, ensure that <code>fm</code> is included.

#### Remediation:
Set the following parameters in <code>/etc/audit/auditd_control</code>:
<pre><code>policy:fm</code></pre>


#### Notes:
Reloading the auditd config to set active settings may require a system reboot.

#### CIS Controls:

Version 7

5.5 Implement Automated Configuration Monitoring Systems

Utilize a Security Content Automation Protocol (SCAP) compliant configuration
monitoring system to verify all security configuration elements, catalog approved
exceptions, and alert when unauthorized changes occur.
