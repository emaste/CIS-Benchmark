# 2 Services

## 2.1 inetd Services

inetd is a super-server daemon that provides internet services and passes connections to
configured services. While not commonly used inetd and any unneeded inetd based
services should be disabled if possible.

## 2.1.1 Ensure xinetd is not installed

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The eXtended InterNET Daemon ( <code>xinetd</code> ) is an open source super daemon that replaced
the original <code>inetd</code> daemon. The <code>xinetd</code> daemon listens for well known services and
dispatches the appropriate daemon to properly respond to service requests.

#### Rationale:
If there are no <code>xinetd</code> services required, it is recommended that the package be removed.

#### Audit:
Run the following command to verify <code>xinetd</code> is not installed:
<pre><code># pkg info xinetd
pkg: No package(s) matching xinetd</code></pre>

#### Remediation:
Run the following command to remove xinetd:
<pre><code># pkg delete xinetd</code></pre>

#### CIS Controls:
Version 7

9.2 Ensure Only Approved Ports, Protocols and Services Are Running
Ensure that only network ports, protocols, and services listening on a system with
validated business needs, are running on each system.

## 2.2 Special Purpose Services

This section describes services that are installed on systems that specifically need to run
these services. If any of these services are not required, it is recommended that they be
disabled or deleted from the system to reduce the potential attack surface.

## 2.2.1 Time Synchronization

It is recommended that physical systems and virtual guests lacking direct access to the
physical host's clock be configured to synchronize their time using NTP

## 2.2.1.1 Ensure time synchronization is in use

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
System time should be synchronized between all systems in an environment. This is
typically done by establishing an authoritative time server or set of servers and having all
systems synchronize their clocks to them.

#### Rationale:
Time synchronization is important to support time sensitive security mechanisms like
Kerberos and also ensures log files have consistent time records across the enterprise,
which aids in forensic investigations.

#### Audit:
On physical systems or virtual systems where host based time synchronization is not
available verify that NTP is installed.

Run the following command to verify that NTP is installed
# rpm -q chrony
chrony-<VERSION>
On virtual systems where host based time synchronization is available consult your
virtualization software documentation and verify that host based synchronization is in use.

#### Remediation:
On physical systems or virtual systems where host based time synchronization is not
available install chrony:
Run the folloing command to install chrony:
# dnf install chrony
On virtual systems where host based time synchronization is available consult your
virtualization software documentation and setup host based synchronization.

#### Notes:
systemd-timesyncd is part of systemd. Some versions of systemd have been compiled
without systemd-timesycnd. On these distributions, chrony or NTP should be used instead
of systemd-timesycnd.

#### CIS Controls:
Version 7

6.1 Utilize Three Synchronized Time Sources
Use at least three synchronized time sources from which all servers and network devices
retrieve time information on a regular basis so that timestamps in logs are consistent.
