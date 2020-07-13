# 4 Logging and Auditing

The items in this section describe how to configure logging, log monitoring, and auditing,
using tools included in most distributions

### ADD MORE TO THIS SECTION ###


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

