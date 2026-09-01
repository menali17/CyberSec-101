# Remote Desktop Protocols in Linux

Remote desktop protocols allow us to access and control a graphical desktop on another system over a network.

They are useful for tasks such as:

```text id="e5v25n"
Remote administration
Troubleshooting
Software installation
System maintenance
Graphical access to remote hosts
```

Two common protocols are:

```text id="2wg0ak"
RDP
→ Primarily associated with Windows

VNC
→ Commonly used with Linux and cross-platform systems
```

---

# RDP

`RDP` stands for:

```text id="gtt8dh"
Remote Desktop Protocol
```

It is primarily used in Windows environments.

RDP allows us to interact with a remote graphical desktop almost as if we were physically using that machine.

Conceptually:

```text id="zug0hp"
Our Machine
    │
    │ RDP
    ▼
Remote Windows Desktop
```

---

# VNC

`VNC` stands for:

```text id="s9lnae"
Virtual Network Computing
```

It provides remote graphical access and is especially common on Linux systems.

Conceptually:

```text id="d7g4uu"
Our Machine
    │
    │ VNC
    ▼
Remote Linux Desktop
```

Unlike SSH, which usually gives us a terminal:

```text id="r8j7e0"
SSH
→ remote command-line shell

VNC
→ remote graphical desktop
```

---

# X Window System

Linux graphical environments commonly rely on the:

```text id="kcnxjv"
X Window System
```

also known as:

```text id="3r0iud"
X
X11
```

The X Window System allows graphical applications to display windows and interact with a graphical environment.

The material explains that the X server is involved in communication between the Linux GUI and the operating system.

---

# X Server

The:

```text id="wb2zmd"
X Server
```

is responsible for handling the graphical display side of X11.

Conceptually:

```text id="f91c74"
Application
    │
    ▼
X11
    │
    ▼
X Server
    │
    ▼
Display
```

One important characteristic of X11 is:

```text id="1u4m7p"
network transparency
```

This means an application can run on one machine while its graphical output appears on another machine.

---

# X11 Remote Application Model

Suppose Firefox runs on a remote Linux server.

With X11 forwarding:

```text id="bf8e43"
REMOTE SERVER
Firefox process
      │
      │ X11
      ▼
OUR MACHINE
X Server
      │
      ▼
Firefox window appears locally
```

The application executes remotely, but its interface is displayed locally.

---

# X11 vs VNC/RDP

The material draws an important distinction.

With VNC or RDP:

```text id="ahr8dj"
Remote machine
    │
    ├── renders graphical desktop
    │
    ▼
Graphical output sent over network
    │
    ▼
Our machine
```

With X11:

```text id="r9ha3p"
Remote machine
    │
    ├── runs application
    │
    ▼
X11 instructions
    │
    ▼
Our local X Server
    │
    ▼
Graphical window rendered locally
```

This is one of the major conceptual differences in the section.

---

# X11 Ports

The material associates X11 with TCP ports around:

```text id="a7s99f"
6000+
```

It describes:

```text id="5s0bdb"
Display :0
→ TCP/6000
```

and subsequent displays using higher ports.

Conceptually:

```text id="h14saa"
Display :0 → 6000
Display :1 → 6001
Display :2 → 6002
...
```

---

# X11 Security

The material emphasizes that X11 communication is not encrypted by default.

This means exposed X11 services can present security risks.

Potential consequences mentioned include:

```text id="auar8e"
Reading graphical contents
Capturing sensitive information
Interacting with another user's graphical session
Potential exploitation of X server vulnerabilities
```

---

# X11 over SSH

A safer way to use remote X applications is:

```text id="m1xs2j"
X11 forwarding through SSH
```

First, the SSH server configuration must allow:

```text id="72tr1t"
X11Forwarding yes
```

The material checks this with:

```bash id="bcwndp"
cat /etc/ssh/sshd_config | grep X11Forwarding
```

Output:

```bash id="i42mxt"
X11Forwarding yes
```

---

# `ssh -X`

The material starts a remote Firefox instance using:

```bash id="ywkn8s"
ssh -X htb-student@10.129.23.11 /usr/bin/firefox
```

Breaking this down:

```text id="st0qcw"
ssh
→ establish SSH connection

-X
→ enable X11 forwarding

htb-student@10.129.23.11
→ remote account and host

/usr/bin/firefox
→ application to execute remotely
```

Conceptually:

```text id="nrf9in"
Firefox process
runs remotely
      │
      │ SSH-encrypted X11
      ▼
Firefox window
appears locally
```

---

# Why SSH Helps X11

Without SSH:

```text id="xk8v8u"
X11
→ unencrypted communication
```

With SSH forwarding:

```text id="bywdl1"
X11
     │
     ▼
SSH tunnel
     │
     ▼
Encrypted transport
```

So SSH protects the X11 communication while it crosses the network.

---

# XDMCP

`XDMCP` stands for:

```text id="0um7ci"
X Display Manager Control Protocol
```

It is used for managing remote X graphical sessions on Unix/Linux systems.

The material states that XDMCP uses:

```text id="3dukgx"
UDP/177
```

---

# XDMCP Purpose

XDMCP can redirect an entire graphical environment such as:

```text id="vprxhf"
GNOME
KDE
```

to a remote client.

Conceptually:

```text id="1kzet9"
Linux Server
      │
      │ XDMCP
      ▼
Remote graphical session
      │
      ▼
Client machine
```

---

# XDMCP Security

The material explicitly describes XDMCP as:

```text id="1tnid9"
insecure
```

One example risk mentioned is:

```text id="hxz2hm"
Man-in-the-middle attack
```

An attacker positioned between the client and server could potentially intercept or manipulate the connection.

---

# VNC

`VNC` uses the:

```text id="i7qshk"
RFB protocol
```

and allows us to remotely view and control a graphical desktop.

The material describes it as one of the most common graphical remote-access methods for Linux hosts.

---

# VNC Mental Model

```text id="3umjda"
REMOTE LINUX HOST
      │
      │ VNC / RFB
      ▼
OUR VNC VIEWER
      │
      ▼
Remote graphical desktop
```

We can interact with:

```text id="37kd5u"
Mouse
Keyboard
Desktop
Applications
```

as though we were using the remote computer directly.

---

# VNC Server Models

The material describes two common approaches.

## Shared Existing Desktop

The VNC server exposes the actual graphical desktop already running on the machine.

Conceptually:

```text id="fs202e"
Physical desktop
      │
      └── same session ──► VNC client
```

Both local and remote users may interact with the same session.

---

## Virtual VNC Session

A server can instead create a separate graphical session.

Conceptually:

```text id="g7g5f4"
Linux Host
   │
   ├── Local desktop
   │
   └── Virtual VNC desktop :1
```

This behaves more like a terminal-server session.

---

# VNC Ports

Traditionally:

```text id="wg7yah"
Display :0
→ TCP/5900
```

Additional displays generally use:

```text id="uhthf3"
:1 → 5901
:2 → 5902
:3 → 5903
```

A useful rule is:

```text id="quyek9"
VNC port = 5900 + display number
```

So:

```text id="xt0e47"
:1
→ 5901
```

The material uses exactly this relationship later when listing the TigerVNC session.

---

# Common VNC Implementations

The material mentions:

```text id="gitpxa"
TigerVNC
TightVNC
RealVNC
UltraVNC
```

Different implementations provide different features, authentication methods, and security options.

---

# Installing TigerVNC

The example uses:

```bash id="wuv2oe"
sudo apt install xfce4 xfce4-goodies tigervnc-standalone-server -y
```

This installs:

```text id="2v6ly3"
XFCE4 desktop environment
TigerVNC server
Supporting XFCE packages
```

---

# Creating the VNC Password

The material uses:

```bash id="pwm4vm"
vncpasswd
```

Example interaction:

```bash id="24pa06"
Password: ******
Verify: ******
Would you like to enter a view-only password (y/n)? n
```

This password is used to authenticate VNC clients.

---

# `.vnc` Directory

TigerVNC creates:

```text id="3gqvvr"
~/.vnc
```

inside the user's home directory.

The material then creates:

```text id="iz8d4w"
~/.vnc/xstartup
~/.vnc/config
```

---

# `xstartup`

The file:

```text id="xo309p"
~/.vnc/xstartup
```

controls how the graphical session starts.

The material uses:

```bash id="6784ml"
#!/bin/bash
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
/usr/bin/startxfce4
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
x-window-manager &
```

An important line is:

```bash id="cg95yf"
/usr/bin/startxfce4
```

which starts the XFCE graphical environment.

---

# VNC Configuration

The material creates:

```text id="odgk6h"
~/.vnc/config
```

with:

```text id="qufn8x"
geometry=1920x1080
dpi=96
```

These settings control aspects of the graphical session.

For example:

```text id="7xvuxk"
geometry=1920x1080
→ desktop resolution
```

---

# Making `xstartup` Executable

The file must be executable:

```bash id="quwb8a"
chmod +x ~/.vnc/xstartup
```

This connects directly with the earlier Linux permissions section:

```text id="9ielgm"
chmod
→ change permissions

+x
→ add execute permission
```

---

# Starting the VNC Server

The material uses:

```bash id="4o15kt"
vncserver
```

Output:

```bash id="v58bzz"
New 'linux:1 (htb-student)' desktop at :1 on machine linux

Starting applications specified in /home/htb-student/.vnc/xstartup
Log file is /home/htb-student/.vnc/linux:1.log
```

The important part is:

```text id="vf736l"
:1
```

which is the VNC/X display number.

---

# Listing VNC Sessions

We can list sessions with:

```bash id="o4v1yi"
vncserver -list
```

Output:

```bash id="g6cg35"
TigerVNC server sessions:

X DISPLAY #     RFB PORT #      PROCESS ID
:1              5901            79746
```

This directly shows:

```text id="pgqjv5"
Display
:1

VNC port
5901

PID
79746
```

---

# VNC over SSH

The material improves VNC security by tunneling the VNC connection through SSH.

Command:

```bash id="dhmp6x"
ssh -L 5901:127.0.0.1:5901 -N -f -l htb-student 10.129.14.130
```

This command is more advanced, so the most important concept for now is:

```text id="rf6e2h"
VNC traffic
     │
     ▼
SSH tunnel
     │
     ▼
Remote VNC server
```

The details of tunneling are covered later in HTB.

---

# `-L`

The key option is:

```text id="vzgc0r"
-L
```

which creates local port forwarding.

Here:

```text id="4jmf34"
5901:127.0.0.1:5901
```

can be understood as:

```text id="b81js0"
Our localhost:5901
       │
       │ SSH tunnel
       ▼
Remote localhost:5901
```

---

# Other SSH Options in the Tunnel

The material uses:

```text id="ih8ppj"
-N
→ do not execute a remote command
```

```text id="zncvdm"
-f
→ send SSH to the background
```

```text id="j7b61k"
-l htb-student
→ specify login user
```

So:

```bash id="fbfjb1"
ssh -L 5901:127.0.0.1:5901 -N -f -l htb-student 10.129.14.130
```

creates an SSH tunnel in the background without starting a remote interactive shell.

---

# Connecting Through the Tunnel

After creating the tunnel, the material uses:

```bash id="rd3wbu"
xtightvncviewer localhost:5901
```

Notice:

```text id="3cax5h"
localhost:5901
```

We connect to our own local port.

But SSH forwards that traffic to:

```text id="ab2edw"
remote 127.0.0.1:5901
```

Conceptually:

```text id="74tcgy"
VNC Viewer
localhost:5901
      │
      ▼
SSH Tunnel
      │
      ▼
Remote Host
127.0.0.1:5901
      │
      ▼
VNC Server
```

---

# Successful VNC Connection

The material shows:

```bash id="khz6ya"
Connected to RFB server, using protocol version 3.8
Performing standard VNC authentication

Password: ******

Authentication successful
Desktop name "linux:1 (htb-student)"
```

This confirms:

```text id="8s5d4e"
Network connection
+
VNC authentication
+
Remote graphical session
```

---

# SSH vs X11 vs VNC

This distinction is useful.

| Technology     | Main Purpose                                               |
| -------------- | ---------------------------------------------------------- |
| SSH            | Remote command-line access                                 |
| X11 forwarding | Run remote graphical applications and display them locally |
| VNC            | Access/control an entire graphical desktop                 |
| RDP            | Remote graphical desktop, primarily Windows                |
| XDMCP          | Remote X graphical sessions                                |

---

# X11 vs VNC

## X11 Forwarding

```text id="qwt25l"
Remote application
      │
      ▼
Window displayed locally
```

Example:

```bash id="axv3z8"
ssh -X user@server firefox
```

We launch an individual graphical application.

---

## VNC

```text id="21l5f4"
Remote desktop/session
      │
      ▼
Displayed and controlled remotely
```

We access an entire graphical environment.

This gives us a very useful mental model:

```text id="kw7z5g"
SSH
→ remote terminal

X11
→ remote graphical application

VNC
→ remote graphical desktop
```

---

# Important Ports

| Protocol / Service |     Port |
| ------------------ | -------: |
| X11 display `:0`   | TCP/6000 |
| X11 display `:1`   | TCP/6001 |
| XDMCP              |  UDP/177 |
| VNC display `:0`   | TCP/5900 |
| VNC display `:1`   | TCP/5901 |
| VNC display `:2`   | TCP/5902 |

---

# Cybersecurity Relevance

During Linux enumeration, graphical remote-access services can reveal valuable attack surfaces.

The material particularly emphasizes looking for:

```text id="xkqmdq"
X11
VNC
XDMCP
```

because weak configurations may expose:

```text id="ol05qd"
Graphical sessions
Sensitive information
Remote access
Authentication interfaces
Potential privilege-escalation paths
```

X11 is especially relevant because its communication may be unencrypted unless protected with SSH.

---

# Quick Reference

| Command / Concept          | Purpose                            |
| -------------------------- | ---------------------------------- |
| `ssh -X`                   | SSH with X11 forwarding            |
| `X11Forwarding yes`        | Allow SSH X11 forwarding           |
| `vncpasswd`                | Configure VNC password             |
| `vncserver`                | Start VNC server                   |
| `vncserver -list`          | List VNC sessions                  |
| `chmod +x ~/.vnc/xstartup` | Make VNC startup script executable |
| `ssh -L`                   | Local SSH port forwarding          |
| `xtightvncviewer`          | VNC client used in material        |

---

# Commands to Remember First

Check X11 forwarding:

```bash id="ff7zii"
cat /etc/ssh/sshd_config | grep X11Forwarding
```

Run a graphical application through SSH:

```bash id="se65k6"
ssh -X user@host /usr/bin/firefox
```

Set a VNC password:

```bash id="6kwe9c"
vncpasswd
```

Start VNC:

```bash id="8zi5lh"
vncserver
```

List VNC sessions:

```bash id="qd0r5s"
vncserver -list
```

Connect to VNC:

```bash id="p8ixhz"
xtightvncviewer localhost:5901
```

---

# What to Remember First

The easiest way to organize this section is:

```text id="6v3cuv"
SSH
→ remote terminal

X11
→ remote application window

VNC
→ remote graphical desktop

RDP
→ Windows graphical desktop

XDMCP
→ remote X session
```

And remember the VNC display relationship:

```text id="q40wzh"
:0 → 5900
:1 → 5901
:2 → 5902
```

For X11:

```text id="7d1xuj"
:0 → 6000
:1 → 6001
```

Finally:

```text id="cvgwzu"
X11 / VNC traffic
      +
SSH tunneling
      =
safer remote graphical access
```

---

## Key Takeaway

**Linux supports several methods for remote graphical access. X11 can display remotely executed graphical applications on our local system, while VNC provides access to complete remote desktop sessions. RDP is primarily associated with Windows, while XDMCP manages remote X sessions. Because some of these protocols may expose sensitive graphical information or lack strong transport security by default, SSH forwarding and tunneling are important techniques for protecting remote graphical connections.**
