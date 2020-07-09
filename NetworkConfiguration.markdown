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
