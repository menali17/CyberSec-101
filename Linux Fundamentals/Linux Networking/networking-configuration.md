# Network Configuration

Network configuration is the process of managing how a Linux system communicates with other devices and networks.

For penetration testing, this includes understanding and configuring:

```text id="a7xknz"
Network interfaces
IP addresses
Netmasks
Default gateways
DNS servers
Routes
Access controls
Monitoring tools
Troubleshooting tools
```

A solid understanding of Linux networking helps us build testing environments, diagnose connectivity issues, manipulate traffic, and understand how systems communicate.

---

# Core Network Concepts

Important protocols and technologies introduced in this section include:

```text id="krdvin"
TCP/IP
→ Core Internet communication

DNS
→ Domain name resolution

DHCP
→ Dynamic IP address allocation

FTP
→ File transfer
```

We also need to understand network interfaces such as:

```text id="18f0hx"
Ethernet
Wireless
Loopback
```

---

# Network Interface

A network interface is the system's connection point to a network.

Examples include:

```text id="yb0ix9"
eth0
eth1
lo
```

Conceptually:

```text id="6dtfqz"
Linux Machine
     │
     ├── eth0 → Network A
     │
     ├── eth1 → Network B
     │
     └── lo   → Local machine
```

Each interface may have its own:

```text id="0kgc5t"
IP address
Netmask
MAC address
MTU
Status
```

---

# `ifconfig`

The material introduces:

```bash id="6em6z9"
ifconfig
```

to display information about network interfaces.

Example:

```bash id="ktltte"
cry0l1t3@htb:~$ ifconfig

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 178.62.32.126  netmask 255.255.192.0  broadcast 178.62.63.255
        inet6 fe80::88d9:faff:fecf:797a
        ether 8a:d9:fa:cf:79:7a
```

Important fields include:

```text id="yjzs3x"
eth0
→ interface name

UP
→ interface enabled

mtu 1500
→ maximum transmission unit

inet 178.62.32.126
→ IPv4 address

netmask 255.255.192.0
→ subnet mask

broadcast 178.62.63.255
→ broadcast address

ether 8a:d9:fa:cf:79:7a
→ MAC address
```

The material notes that `ifconfig` is deprecated on newer Linux systems and that `ip` provides more advanced functionality.

---

# `ip addr`

The modern command shown is:

```bash id="1qf7gu"
ip addr
```

Example:

```bash id="vynz7h"
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536
    inet 127.0.0.1/8 scope host lo

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    link/ether 8a:d9:fa:cf:79:7a
    inet 178.62.32.126/18 brd 178.62.63.255
```

This command displays similar information to `ifconfig`, including interface state and IP addressing.

---

# Loopback Interface

The interface:

```text id="2fh0ir"
lo
```

is the loopback interface.

It usually uses:

```text id="4b4apc"
127.0.0.1
```

This refers to:

```text id="fvz43u"
our own machine
```

Conceptually:

```text id="1m0kcp"
Application
    │
    ▼
127.0.0.1
    │
    ▼
Same machine
```

---

# Activating an Interface

The material shows two ways to enable `eth0`.

Using `ifconfig`:

```bash id="82ctzc"
sudo ifconfig eth0 up
```

Using `ip`:

```bash id="0pjxou"
sudo ip link set eth0 up
```

Both mean:

> Bring the `eth0` network interface up.

---

# Assigning an IP Address

Using `ifconfig`:

```bash id="9rpq2c"
sudo ifconfig eth0 192.168.1.2
```

Breaking it down:

```text id="6b69mg"
eth0
→ interface

192.168.1.2
→ IPv4 address assigned to it
```

Conceptually:

```text id="663o9o"
eth0
  │
  ▼
192.168.1.2
```

---

# Netmask

The material configures:

```bash id="t2qcls"
sudo ifconfig eth0 netmask 255.255.255.0
```

The netmask helps determine which part of an IPv4 address identifies:

```text id="w7jm67"
Network
vs
Host
```

For example:

```text id="5ljv72"
IP:
192.168.1.2

Netmask:
255.255.255.0
```

corresponds conceptually to the network:

```text id="p6o1c3"
192.168.1.0/24
```

---

# Default Gateway

The default gateway is the router used when traffic needs to leave the local network.

The material uses:

```bash id="4njpw8"
sudo route add default gw 192.168.1.1 eth0
```

Breaking it down:

```text id="injmw0"
route
→ routing configuration

add default
→ add default route

gw 192.168.1.1
→ gateway

eth0
→ interface
```

Conceptually:

```text id="hd2udl"
Our machine
192.168.1.2
      │
      ▼
Default Gateway
192.168.1.1
      │
      ▼
Other networks / Internet
```

The material explains the default gateway as the router used for destinations outside the local network.

---

# DNS

`DNS` stands for:

```text id="26o2eg"
Domain Name System
```

DNS translates names such as:

```text id="04ni0m"
example.com
```

into IP addresses.

Conceptually:

```text id="ki9fgy"
example.com
     │
     ▼
DNS Server
     │
     ▼
93.184.216.34
```

Without working DNS, we may still be able to communicate directly with IP addresses but fail when using hostnames.

---

# `/etc/resolv.conf`

The material introduces:

```text id="ayvo87"
/etc/resolv.conf
```

for DNS information.

It is edited with:

```bash id="myu78r"
sudo vim /etc/resolv.conf
```

Example:

```text id="l7vjym"
nameserver 8.8.8.8
nameserver 8.8.4.4
```

These specify DNS servers.

The material warns that direct changes to `/etc/resolv.conf` may be overwritten by services such as NetworkManager or `systemd-resolved`.

---

# Persistent Network Configuration

The material uses:

```text id="u10u9k"
/etc/network/interfaces
```

to demonstrate persistent interface configuration.

Example:

```text id="bctyz6"
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```

---

# Reading the Configuration

```text id="cazrx7"
auto eth0
→ automatically configure eth0
```

```text id="tvrff8"
iface eth0 inet static
→ use a static IPv4 configuration
```

```text id="x4v12c"
address 192.168.1.2
→ IP address
```

```text id="w3uop0"
netmask 255.255.255.0
→ subnet mask
```

```text id="7cl8uh"
gateway 192.168.1.1
→ default gateway
```

```text id="exxxez"
dns-nameservers 8.8.8.8 8.8.4.4
→ DNS servers
```

---

# Restarting Networking

After making configuration changes, the material uses:

```bash id="mt4d0n"
sudo systemctl restart networking
```

This connects directly with the previous Service Management section.

```text id="3krvld"
Configuration changed
       │
       ▼
Restart networking service
       │
       ▼
Configuration applied
```

---

# Basic Network Configuration Mental Model

A host normally needs several pieces of information:

```text id="w0c9pr"
IP Address
      │
      ├── Who are we?

Netmask
      │
      ├── What is our local network?

Gateway
      │
      ├── Where do we send non-local traffic?

DNS
      │
      └── How do we resolve names?
```

Example:

```text id="22k5fr"
IP
192.168.1.2

Netmask
255.255.255.0

Gateway
192.168.1.1

DNS
8.8.8.8
```

---

# Network Access Control

`NAC` stands for:

```text id="uodl2d"
Network Access Control
```

The material describes NAC as controlling which authorized and compliant devices are allowed network access.

Three models are introduced:

```text id="mb5iqy"
DAC
MAC
RBAC
```

---

# DAC

`DAC` stands for:

```text id="r4grze"
Discretionary Access Control
```

In DAC, the owner of a resource decides who can access it.

Conceptually:

```text id="lx55mm"
Resource Owner
     │
     ├── User A → allowed
     ├── User B → denied
     └── User C → read-only
```

The resource owner controls the permissions.

---

# MAC

`MAC` stands for:

```text id="l3mec0"
Mandatory Access Control
```

In MAC, access rules are enforced by the security system rather than being freely decided by the resource owner.

The material describes the use of:

```text id="mki0pp"
Security labels
Security levels
Security clearances
```

Conceptually:

```text id="fne42i"
User / Process
Security Level
      │
      ▼
Security Policy
      │
      ▼
Resource
Security Level
```

Access is determined according to mandatory security rules.

---

# RBAC

`RBAC` stands for:

```text id="6tm3od"
Role-Based Access Control
```

Permissions are assigned to:

```text id="m6zxxm"
roles
```

rather than directly to every individual user.

For example:

```text id="2ee0et"
Admin Role
├── Read
├── Write
└── Configure

Analyst Role
├── Read
└── Analyze

Guest Role
└── Read limited resources
```

Then users receive roles:

```text id="l3lfnw"
Alice → Admin
Bob   → Analyst
Carol → Guest
```

This can simplify permission management in large organizations.

---

# DAC vs MAC vs RBAC

| Model | Access Based On               |
| ----- | ----------------------------- |
| DAC   | Resource owner's decisions    |
| MAC   | Mandatory security policies   |
| RBAC  | Assigned organizational roles |

The material summarizes these models as different approaches to access control.

---

# Network Monitoring

Network monitoring involves:

```text id="mjkswl"
Capturing traffic
Analyzing traffic
Interpreting traffic
Detecting anomalies
Identifying suspicious behavior
```

The material gives the security example of capturing credentials when an unencrypted FTP connection is used.

Tools mentioned include:

```text id="3wtqvh"
Wireshark
tshark
tcpdump
```

Earlier in the section, it also mentions tools such as:

```text id="7vo2of"
syslog
rsyslog
ss
lsof
ELK stack
```

---

# Network Troubleshooting

Troubleshooting means diagnosing and resolving network problems.

Common problems include:

```text id="ebf5qc"
No connectivity
Slow connection
Packet loss
DNS problems
Incorrect routes
Network errors
```

The material introduces several tools:

```text id="j5v9dh"
ping
traceroute
netstat
tcpdump
Wireshark
nmap
```

---

# Ping

`ping` tests connectivity between systems.

Basic syntax:

```bash id="6quc48"
ping <remote_host>
```

Example:

```bash id="lp7nbr"
ping 8.8.8.8
```

Output:

```bash id="wwo6it"
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=119 time=1.61 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=119 time=1.06 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=119 time=0.636 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=119 time=0.685 ms
```

---

# Understanding Ping Output

Consider:

```text id="idvpxp"
64 bytes from 8.8.8.8: icmp_seq=1 ttl=119 time=1.61 ms
```

Important fields:

```text id="pe2m52"
icmp_seq=1
→ packet sequence number

ttl=119
→ remaining Time To Live

time=1.61 ms
→ response time
```

At the end:

```bash id="h67c15"
4 packets transmitted, 4 received, 0% packet loss
```

means all four test packets received responses.

---

# Ping Mental Model

```text id="12l919"
Our Machine
    │
    │ ICMP Echo Request
    ▼
Remote Host
    │
    │ ICMP Echo Reply
    ▼
Our Machine
```

If replies arrive, connectivity exists at least at the level tested by the ping exchange.

---

# Traceroute

`traceroute` shows the path packets take toward a destination.

Example:

```bash id="o8lkkt"
traceroute www.inlanefreight.com
```

Example output:

```bash id="zu35pz"
traceroute to www.inlanefreight.com (134.209.24.248), 30 hops max

1  * * *
2  10.80.71.5       2.716 ms  2.700 ms  2.730 ms
3  * * *
4  10.80.68.175     7.147 ms  7.132 ms
```

---

# Hop

Each network device traversed on the route is generally shown as a:

```text id="y1bnif"
hop
```

Conceptually:

```text id="pqiiqg"
Our PC
   │
   ▼
Router 1
   │
   ▼
Router 2
   │
   ▼
Router 3
   │
   ▼
Destination
```

Traceroute attempts to reveal those intermediate points.

---

# `* * *` in Traceroute

The output:

```text id="2us0xm"
* * *
```

means that the probe did not receive the expected response for that hop.

The material notes possible reasons such as:

```text id="ebktyy"
Device not responding
ICMP filtering
Packet loss
Network problems
```

It does not automatically mean that the entire route is broken.

---

# TTL

Traceroute works by sending packets with increasing:

```text id="40ei5h"
TTL
```

which stands for:

```text id="ym96at"
Time To Live
```

Each routing hop reduces the TTL.

Conceptually:

```text id="3qpqag"
TTL 1
→ expires at first router

TTL 2
→ expires at second router

TTL 3
→ expires at third router
```

This allows traceroute to discover intermediate devices.

---

# Netstat

`netstat` displays information about network connections and listening services.

The material uses:

```bash id="batt7e"
netstat -a
```

Example:

```bash id="0lzwsu"
Active Internet connections (servers and established)

Proto Recv-Q Send-Q Local Address       Foreign Address    State
tcp        0      0 localhost:5901      0.0.0.0:*          LISTEN
tcp        0      0 0.0.0.0:http       0.0.0.0:*          LISTEN
tcp        0      0 0.0.0.0:ssh        0.0.0.0:*          LISTEN
```

---

# LISTEN

The state:

```text id="nxehhi"
LISTEN
```

means a service is waiting for incoming connections.

For example:

```text id="q09j8u"
0.0.0.0:ssh
LISTEN
```

means an SSH service is listening for incoming connections.

The material also shows HTTP and VNC services in the listening state.

---

# `0.0.0.0`

When a service is bound to:

```text id="hav5xk"
0.0.0.0
```

it generally indicates listening on all IPv4 interfaces available to that service.

Compare conceptually:

```text id="vv5oh1"
127.0.0.1:80
→ local loopback only

0.0.0.0:80
→ all IPv4 interfaces
```

This distinction is very important during service enumeration.

---

# Common Network Problems

The material lists issues such as:

```text id="vxfism"
Network connectivity problems
DNS resolution problems
Packet loss
Performance problems
```

Potential causes include:

```text id="zg6ztf"
Incorrect firewall configuration
Incorrect router configuration
Damaged cables
Incorrect network settings
Hardware failures
DNS failures
Incorrect DNS entries
Network congestion
Outdated hardware
Unpatched software
Missing security controls
```

---

# Troubleshooting Mental Model

When something does not work, we can reason progressively.

```text id="ytjv8b"
1. Does the interface exist and have an IP?
        │
        ▼
   ip addr

2. Can we reach another IP?
        │
        ▼
   ping

3. What path does traffic take?
        │
        ▼
   traceroute

4. Are expected services listening?
        │
        ▼
   netstat / ss

5. Does DNS work?
        │
        ▼
   DNS-related checks

6. What traffic is actually happening?
        │
        ▼
   tcpdump / Wireshark
```

---

# Hardening

The material introduces three Linux security mechanisms:

```text id="1id1i5"
SELinux
AppArmor
TCP Wrappers
```

Their shared purpose is reducing unauthorized access and improving system security.

---

# SELinux

`SELinux` stands for:

```text id="8huf6l"
Security-Enhanced Linux
```

It is described as a:

```text id="47zj27"
Mandatory Access Control (MAC)
```

system integrated with Linux.

SELinux policies define what processes and files are allowed to do.

Conceptually:

```text id="aq0had"
Process
   │
   ▼
SELinux Policy
   │
   ├── allowed → Resource
   │
   └── denied  → Blocked
```

The material emphasizes strong, fine-grained controls, but also notes that SELinux can be complex to configure.

---

# AppArmor

`AppArmor` is also described as a:

```text id="519irx"
Mandatory Access Control system
```

It uses:

```text id="19pcmg"
application profiles
```

to control which resources applications may access.

Conceptually:

```text id="j1tjf9"
Application
    │
    ▼
AppArmor Profile
    │
    ├── allowed resources
    └── denied resources
```

The material presents AppArmor as easier to configure than SELinux, though potentially less granular.

---

# TCP Wrappers

The material describes TCP Wrappers as controlling access to network services based on:

```text id="2rx342"
client IP addresses
```

Conceptually:

```text id="cc17ax"
Incoming Connection
       │
       ▼
Access Rules
       │
       ├── allowed IP → service
       │
       └── denied IP  → blocked
```

It provides simpler network-level access restrictions compared with SELinux and AppArmor.

---

# SELinux vs AppArmor vs TCP Wrappers

| Technology   | Main Focus                                   |
| ------------ | -------------------------------------------- |
| SELinux      | Fine-grained mandatory system access control |
| AppArmor     | Profile-based mandatory application control  |
| TCP Wrappers | Network-service access based on IP           |

---

# Complete Network Configuration Mental Model

```text id="3j0ex9"
NETWORK INTERFACE
      │
      ├── IP Address
      ├── Netmask
      ├── Gateway
      └── DNS
           │
           ▼
      CONNECTIVITY
           │
           ├── ping
           ├── traceroute
           └── routing
           │
           ▼
       SERVICES
           │
           ├── netstat
           ├── ss
           └── lsof
           │
           ▼
       MONITORING
           │
           ├── tcpdump
           ├── Wireshark
           └── logs
           │
           ▼
        SECURITY
           │
           ├── SELinux
           ├── AppArmor
           └── access controls
```

---

# Quick Reference

| Command / Concept         | Purpose                                   |
| ------------------------- | ----------------------------------------- |
| `ifconfig`                | View/configure interfaces                 |
| `ip addr`                 | View interfaces and IP addresses          |
| `ip link set eth0 up`     | Activate interface                        |
| `route`                   | Configure/view routes                     |
| `/etc/resolv.conf`        | DNS resolver configuration                |
| `/etc/network/interfaces` | Interface configuration shown in material |
| `ping`                    | Test connectivity                         |
| `traceroute`              | Trace packet route                        |
| `netstat`                 | View network connections                  |
| `ss`                      | Socket/network statistics                 |
| `tcpdump`                 | Capture network traffic                   |
| SELinux                   | Mandatory access control                  |
| AppArmor                  | Profile-based mandatory access control    |
| TCP Wrappers              | IP-based network-service access control   |

---

# Commands to Remember First

Show interfaces:

```bash id="knbokc"
ip addr
```

Activate interface:

```bash id="ezi6sn"
sudo ip link set eth0 up
```

Test connectivity:

```bash id="4vde5g"
ping 8.8.8.8
```

Trace route:

```bash id="1jmu3u"
traceroute www.inlanefreight.com
```

View network connections:

```bash id="ksecc4"
netstat -a
```

View DNS configuration:

```bash id="4p94c0"
cat /etc/resolv.conf
```

---

# What to Remember First

The most important network configuration model is:

```text id="enb68o"
IP
→ Our address

Netmask
→ Our local network

Gateway
→ Way out of our local network

DNS
→ Convert names into IP addresses
```

For troubleshooting:

```text id="b9sawo"
ip addr
→ What interfaces/IPs do we have?

ping
→ Can we reach it?

traceroute
→ What path are packets taking?

netstat / ss
→ What connections/services exist?
```

And for access control:

```text id="bpqoom"
DAC
→ owner controls access

MAC
→ policy controls access

RBAC
→ role controls access
```

---

## Key Takeaway

**Linux network configuration revolves around correctly configuring interfaces, IP addresses, netmasks, gateways, DNS, and routes. Tools such as `ip`, `ping`, `traceroute`, and `netstat` help us inspect and troubleshoot connectivity, while monitoring and access-control mechanisms help us analyze and secure the system. For penetration testing, understanding how traffic moves from an interface through routing, name resolution, and network services is fundamental for both enumeration and troubleshooting.**
