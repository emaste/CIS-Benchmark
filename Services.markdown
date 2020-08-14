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


## 2.2.1.1 Ensure NTP time synchronization is in use

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
available verify that NTP is configured and enabled.

Run the following command to verify that NTP is in use:

<pre><code># grep ntpd_enable /etc/rc.conf
ntpd_enable="YES"</code></pre>

#### Remediation:

Add the following line to <code>/etc/rc.conf</code> 

<pre><code>ntpd_enable="YES"</code></pre>

NTP can be started immediately with

<pre><code># service ntpd start</code></pre>

#### CIS Controls:
Version 7

6.1 Utilize Three Synchronized Time Sources
Use at least three synchronized time sources from which all servers and network devices
retrieve time information on a regular basis so that timestamps in logs are consistent.

## 2.2.2 SMTP




## 2.2.3 Ensure NFS is not enabled

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The Network File System (NFS) is one of the first and most widely distributed file systems
in the UNIX environment. It provides the ability for systems to mount file systems of other
servers through the network.

##### Rationale:
If the system does not export NFS shares, it is recommended that the NFS be disabled to
reduce the remote attack surface.

##### Audit:
Run the following command and verify there is no output:
<pre><code># grep nfs_client_enable /etc/rc.conf</code></pre>


#### Remediation:
Remove the following line from <code>/etc/rc.conf</code> to disable <code>nfs</code>:
<pre><code>nfs_client_enable="YES"</code></pre>

#### Notes:
Additional methods of disabling a service exist. Consult your distribution documentation
for appropriate methods.

#### CIS Controls:
Version 7

9.2 Ensure Only Approved Ports, Protocols and Services Are Running
Ensure that only network ports, protocols, and services listening on a system with
validated business needs, are running on each system.

## 2.2.4 Ensure RPC is not enabled

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The rpcbind service maps Remote Procedure Call (<code>RPC</code>) services to the ports on which they
listen. <code>RPC</code> processes notify rpcbind when they start, registering the ports they are
listening on and the <code>RPC</code> program numbers they expect to serve. The client system then
contacts rpcbind on the server with a particular <code>RPC</code> program number. The rpcbind service
redirects the client to the proper port number so it can communicate with the requested
service.

#### Rationale:
If the system does not require <code>rpc</code> based services, it is recommended that rpcbind be
disabled to reduce the remote attack surface.

#### Audit:
Run the following command and verify there is no output:
<pre><code># grep rpcbind_enable /etc/rc.conf</code></pre>

#### Remediation:
Remove the following line from <code>/etc/rc.conf</code> to disable <code>rcbind</code>:
<pre><code>nfs_client_enable="YES"</code></pre>

#### Impact:
Because <code>RPC</code>-based services rely on <code>rpcbind</code> to make all connections with incoming client
requests, <code>rpcbind</code> must be available before any of these services start

#### CIS Controls:
Version 7

9.2 Ensure Only Approved Ports, Protocols and Services Are Running
Ensure that only network ports, protocols, and services listening on a system with
validated business needs, are running on each system.

## 2.2.5 Ensure NIS Server is not enabled

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The Network Information Service (NIS) (formally known as Yellow Pages) is a client-server
directory service protocol for distributing system configuration files. The NIS server is a
collection of programs that allow for the distribution of configuration files.

#### Rationale:
The NIS service is inherently an insecure system that has been vulnerable to DOS attacks,
buffer overflows and has poor authentication for querying NIS maps. NIS generally has
been replaced by such protocols as Lightweight Directory Access Protocol (LDAP). It is
recommended that the service be disabled and other, more secure services be used

#### Audit:
Run the following command and verify there is no output:
<pre><code># grep nis_client_enable /etc/rc.conf</code></pre>

#### Remediation:
Remove the following line from <code>/etc/rc.conf</code> to disable <code>NIS</code>:
<pre><code>nis_client_enable="YES"</code></pre>


#### Notes:
Additional methods of disabling a service exist. Consult your distribution documentation
for appropriate methods.

#### CIS Controls:
Version 7

9.2 Ensure Only Approved Ports, Protocols and Services Are Running
Ensure that only network ports, protocols, and services listening on a system with
validated business needs, are running on each system.
