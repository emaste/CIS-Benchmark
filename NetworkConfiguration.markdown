# 3 Network Configuration

This section provides guidance on for securing the network configuration of the system
through kernel parameters, access list control, and firewall settings.

# 3.1 Network Parameters
The following network parameters are intended for use if the system is to act as a host
only. A system is considered host only if the system has a single interface, or has multiple
interfaces but will not be configured as a router.

## 3.1.1 Ensure IP forwarding is disabled

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
The <code>gateway_enable</code> flag is used to tell the
system whether it can forward packets or not.

#### Rationale:
Setting the flags to <code>NO</code> ensures that a system with multiple interfaces (for example, a hard
proxy), will never be able to forward packets, and therefore, never serve as a router.

#### Audit:
Run the following command and verify there is either no output, or the output matches:
<pre><code># grep gateway_enable /etc/rc.conf
gateway_enable="NO"</code></pre>

#### Remediation:
Edit <code>/etc/rc.conf</code> and add or ammend the line:
<pre><code>gateway_enable="NO"</code></pre>

#### CIS Controls:

Version 7

5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software

# 3.2 Network Parameters

The following network parameters are intended for use on both host only and router
systems. A system acts as a router if it has at least two interfaces and is configured to
perform routing functions.

## 3.2.1 Ensure source routed packets are not accepted

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
In networking, source routing allows a sender to partially or fully specify the route packets
take through a network. In contrast, non-source routed packets travel a path determined
by routers in the network. In some cases, systems may not be routable or reachable from
some locations (e.g. private addresses vs. Internet routable), and so source routed packets
would need to be used.

##### Rationale:
Setting <code>net.inet.ip.accept_sourceroute</code> to 0 disables the system from accepting
source routed packets. Assume this system was capable of routing packets to Internet
routable addresses on one interface and private addresses on another interface. Assume
that the private addresses were not routable to the Internet routable addresses and vice
versa. Under normal routing circumstances, an attacker from the Internet routable
addresses could not use the system as a way to reach the private address systems. If,
however, source routed packets were allowed, they could be used to gain access to the
private address systems as the route could be specified, rather than rely on routing
protocols that did not allow this routing.

#### Audit:
Run the following commands and verify output matches:

<pre><code># sysctl net.inet.ip.accept_sourceroute
net.inet.ip.accept_sourceroute: 0</code></pre>

<pre><code># grep "net\.inet\.ip\.accept_sourceroute" /etc/sysctl.conf
net.inet.ip.accept_sourceroute=0</code></pre>


#### Remediation:
Set the following parameters in <code>/etc/sysctl.conf</code>:
<pre><code>net.inet.ip.accept_sourceroute=0</code></pre>

Run the following commands to set the active kernel parameters:

<pre><code># sysctl -w net.inet.ip.accept_sourceroute=0</code></pre>


#### CIS Controls:

Version 7

5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized
operating systems and software.

## 3.2.2 Ensure ICMP redirects are not accepted (Scored)

#### Profile Applicability:

* Level 1 - Server
* Level 1 - Workstation

#### Description:
ICMP redirect messages are packets that convey routing information and tell your host (acting as a router) to send packets via an alternate path. It is a way of allowing an outside routing devce to update your system routing tables. By setting the <code>net.inet.icmp.drop_redirect</code> sysctl to 1, the system will not accept any ICMP redirect messages, and therefore, won’t allow outsiders to update the system’s routing tables.

#### Rationale:
Attackers could use bogus ICMP redirect messages to maliciously alter the system routing tables and get them to send packets to incorrect networks and allow your system packets to be captured.

#### Audit:
Run the following commands and verify output matches:

<pre><code># sysctl net.inet.icmp.drop_redirect
net.inet.icmp.drop_redirect: 1</code></pre>

#### Remediation:
Set the following parameters in <code>/etc/sysctl.conf</code>:

<pre><code>net.inet.icmp.drop_redirect=1</code></pre>
Run the following commands to set the active kernel parameters:

<pre><code># sysctl net.inet.icmp.drop_redirect=1</code></pre>

#### CIS Controls:

Version 7

5.1 Establish Secure Configurations
Maintain documented, standard security configuration standards for all authorized operating systems and software.

## 3.2.3 Ensure broadcast ICMP requests are ignored (Scored)

#### Profile Applicability:
* Level 1 - Server
* Level 1 - Workstation

#### Description:
Setting <code>net.inet.icmp.bmcastecho</code> to 0 will cause the system to ignore all
ICMP echo and timestamp requests to broadcast and multicast addresses.

#### Rationale:
Accepting ICMP echo and timestamp requests with broadcast or multicast destinations for
your network could be used to trick your host into starting (or participating) in a Smurf
attack. A Smurf attack relies on an attacker sending large amounts of ICMP broadcast
messages with a spoofed source address. All hosts receiving this message and responding
would send echo-reply messages back to the spoofed address, which is probably not
routable. If many hosts respond to the packets, the amount of traffic on the network could
be significantly multiplied.

#### Audit:
Run the following commands and verify output matches:
<pre><code># sysctl net.inet.icmp.bmcastecho
net.inet.icmp.bmcastecho: 0</code><pre>


#### Remediation:
Set the following parameters in <code>/etc/sysctl.conf</code>:

<pre><code>net.inet.icmp.bmcastecho=0</code></pre>
Run the following commands to set the active kernel parameters:

<pre><code># sysctl net.inet.icmp.bmcastecho=0</code></pre>


#### CIS Controls:
Version 7

5.1 Establish Secure Configurations

Maintain documented, standard security configuration standards for all authorized
operating systems and software.

## 3.2.4 
