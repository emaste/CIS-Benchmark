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

## 4.1.1.3 Ensure auditing for processes that start prior to auditd is enabled

Profile Applicability:
 Level 2 - Server
 Level 2 - Workstation
Description:
Configure grub2 so that processes that are capable of being audited can be audited even if
they start up prior to auditd startup.
Rationale:
Audit events need to be captured on processes that start up prior to auditd , so that
potential malicious activity cannot go undetected.
Audit:
Run the following command:
# grep -E 'kernelopts=(\S+\s+)*audit=1\b' /boot/grub2/grubenv
Output will include audit=1
275 | P a g e
Remediation:
Edit /etc/default/grub and add audit=1 to GRUB_CMDLINE_LINUX:
GRUB_CMDLINE_LINUX="audit=1"
Run the following command to update the grub2 configuration:
# grub2-mkconfig -o /boot/grub2/grub.cfg
Notes:
This recommendation is designed around the grub2 bootloader, if another bootloader is in
use in your environment enact equivalent settings.
CIS Controls:
Version 7
6.2 Activate audit logging
Ensure that local logging has been enabled on all systems and networking devices.
6.3 Enable Detailed Logging
Enable system logging to include detailed information such as an event source, date,
user, timestamp, source addresses, destination addresses, and other useful elements.
