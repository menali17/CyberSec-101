# Network Services

Network services allow Linux systems to communicate with other computers, transfer files, provide remote access, share resources, and expose applications over a network.

Understanding these services is important for both administration and cybersecurity because misconfigured or insecure network services can expose credentials, data, or entire systems.

A good example is FTP: if credentials are transmitted without encryption, they may be captured in plain text.

This section focuses on:

```text
SSH
NFS
Web Servers
VPN
```

---

# SSH

`SSH` stands for:

```text
Secure Shell
```

SSH is a network protocol used to securely communicate with remote systems.

It provides:

```text
Encrypted remote access
Remote command execution
Secure file transfer
Encrypted communication
```

A corresponding SSH server must be running on the remote Linux machine before we can connect to it.

---

# OpenSSH

A commonly used SSH implementation is:

```text
OpenSSH
```

OpenSSH is free and open source.

It allows administrators to:

```text
Manage remote systems
Execute remote commands
Transfer files
Create encrypted remote sessions
```

---

# Installing OpenSSH

We can install the SSH server using:

```bash
sudo apt install openssh-server -y
```

---

# Checking SSH Status

After installation, we can check whether the service is running with:

```bash
systemctl status ssh
```

Example:

```bash
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded
     Active: active (running)
     Main PID: 7740 (sshd)
```

The important part is:

```text
Active: active (running)
```

This means the SSH server is currently running.

---

# Connecting with SSH

The syntax is:

```bash
ssh <user>@<IP>
```

Example:

```bash
ssh cry0l1t3@10.129.17.122
```

The first time we connect, SSH may ask us to verify the host key:

```bash
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```

Then it may request the user's password.

Conceptually:

```text
Our machine
    │
    │ encrypted SSH connection
    ▼
Remote Linux host
    │
    ▼
Remote shell
```

---

# SSH Configuration

The OpenSSH server configuration file is:

```text
/etc/ssh/sshd_config
```

This file can control settings such as:

```text
Password authentication
Key authentication
Maximum connections
Host key configuration
Other SSH server behavior
```

Changes should be made carefully because a bad configuration can affect remote access.

---

# SSH Security Importance

Because SSH encrypts communication, commands and credentials are protected from passive interception.

SSH can also be used for:

```text
Remote administration
File transfer
Tunneling
Port forwarding
```

---

# NFS

`NFS` stands for:

```text
Network File System
```

NFS allows us to access files located on remote systems as though they were part of our local filesystem.

Conceptually:

```text
Remote server
    │
    │ NFS share
    ▼
Local mount point
    │
    ▼
Files appear locally
```

NFS is commonly used for centralized file storage and file sharing across networks.

---

# Installing NFS

We can install the NFS server using:

```bash
sudo apt install nfs-kernel-server -y
```

---

# Checking NFS Status

We can check its service status using:

```bash
systemctl status nfs-kernel-server
```

Example:

```bash
● nfs-server.service - NFS server and services
     Loaded: loaded
     Active: active (exited)
```

---

# NFS Configuration

NFS shares are configured using:

```text
/etc/exports
```

This file determines:

```text
Which directories are shared
Which hosts can access them
What permissions are granted
```

---

# Important NFS Permissions

| Option           | Meaning                             |
| ---------------- | ----------------------------------- |
| `rw`             | Read and write                      |
| `ro`             | Read-only                           |
| `no_root_squash` | Remote root retains root privileges |
| `root_squash`    | Remote root is restricted           |
| `sync`           | Write changes synchronously         |
| `async`          | Write asynchronously                |

These permissions are important from a security perspective because weak export configurations can expose sensitive files or excessive privileges.

---

# `root_squash`

The option:

```text
root_squash
```

reduces the privileges of the root user connecting from a client.

Conceptually:

```text
Remote root
    │
    ▼
NFS server
    │
    ▼
Treated as restricted user
```

---

# `no_root_squash`

The option:

```text
no_root_squash
```

does the opposite.

It allows root on the client to retain elevated privileges when accessing the share.

This can be security-sensitive because a misconfigured share may expose privileged operations to a remote root user.

---

# Creating an NFS Share

The material creates:

```bash
mkdir nfs_sharing
```

Then adds an entry to:

```text
/etc/exports
```

Example:

```bash
echo '/home/cry0l1t3/nfs_sharing hostname(rw,sync,no_root_squash)' >> /etc/exports
```

The resulting entry is:

```text
/home/cry0l1t3/nfs_sharing hostname(rw,sync,no_root_squash)
```

Breaking it down:

```text
/home/cry0l1t3/nfs_sharing
→ shared directory

hostname
→ allowed client

rw
→ read/write

sync
→ synchronous writes

no_root_squash
→ client root keeps privileges
```

---

# Mounting an NFS Share

Before using a remote NFS share locally, we mount it.

First, create a local mount point:

```bash
mkdir ~/target_nfs
```

Then mount the remote share:

```bash
mount 10.129.12.17:/home/john/dev_scripts ~/target_nfs
```

The structure is:

```text
REMOTE_IP:/REMOTE_PATH  LOCAL_PATH
```

So:

```bash
10.129.12.17:/home/john/dev_scripts
```

is the remote NFS share.

And:

```bash
~/target_nfs
```

is where it appears locally.

---

# Mounted NFS Share

After mounting, the files can be accessed locally.

Example:

```bash
tree ~/target_nfs
```

Output:

```bash
target_nfs/
├── css.css
├── html.html
├── javascript.js
├── php.php
└── xml.xml
```

Even though the files physically exist on another system, they appear under our local mount point.

---

# Web Servers

A web server provides data and applications to clients over the network.

Web servers commonly use:

```text
HTTP
HTTPS
```

Clients such as browsers send requests, and the server returns data such as:

```text
HTML
CSS
JavaScript
Files
Applications
```

Common Linux web servers include:

```text
Apache
Nginx
Lighttpd
Caddy
```

Web servers are especially relevant in penetration testing because web applications are frequent security assessment targets.

---

# Installing Apache

We can install Apache using:

```bash
sudo apt install apache2 -y
```

---

# Apache Configuration

The main Apache configuration file introduced in the material is:

```text
/etc/apache2/apache2.conf
```

Example:

```text
<Directory /var/www/html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
</Directory>
```

---

# `/var/www/html`

A common default web directory is:

```text
/var/www/html
```

Files stored here can be served by Apache.

Conceptually:

```text
/var/www/html/file.txt
        │
        ▼
Apache
        │
        ▼
HTTP request
        │
        ▼
Remote client
```

This can also be useful for file transfer during security testing.

---

# Apache Directory Options

The example contains:

```text
Options Indexes FollowSymLinks
```

which enables the options shown in the configuration.

It also contains:

```text
AllowOverride All
```

which allows directory-level configuration overrides.

And:

```text
Require all granted
```

which grants access to users according to the configuration shown in the material.

---

# `.htaccess`

Apache can also use:

```text
.htaccess
```

files.

These allow directory-level configuration without modifying the global Apache configuration.

The material mentions features such as:

```text
Access control
mod_rewrite
mod_security
mod_ssl
```

---

# Python Web Server

For temporary file transfer or simple testing, Python can create a basic HTTP server.

First, Python 3 can be installed with:

```bash
sudo apt install python3 -y
```

Then we can start a web server using:

```bash
python3 -m http.server
```

By default, it listens on:

```text
TCP/8000
```

and serves the directory we are currently in.

---

# Python Web Server Example

Suppose we are in:

```text
/home/htb/files
```

and run:

```bash
python3 -m http.server
```

Then conceptually:

```text
/home/htb/files
      │
      ▼
Python HTTP Server
      │
      ▼
TCP/8000
      │
      ▼
Remote client
```

---

# Serving Another Directory

We can explicitly select a directory:

```bash
python3 -m http.server --directory /home/cry0l1t3/target_files
```

This exposes:

```text
/home/cry0l1t3/target_files
```

through the Python web server.

---

# Using Another Port

We can also specify a custom port.

Example:

```bash
python3 -m http.server 443
```

Instead of:

```text
8000
```

the server attempts to listen on:

```text
443
```

---

# VPN

`VPN` stands for:

```text
Virtual Private Network
```

A VPN creates an encrypted tunnel between systems or networks.

Conceptually:

```text
Our machine
    │
    │ encrypted tunnel
    ▼
VPN server
    │
    ▼
Internal network
```

This allows us to communicate with remote network resources as though we were connected more directly to that network.

---

# Why VPNs Are Used

VPNs can provide:

```text
Remote access
Encrypted communication
Access to internal resources
Traffic protection
Network tunneling
```

Organizations often use them so employees can securely access internal resources remotely.

For penetration testing, VPNs commonly provide access to the internal network being assessed.

---

# OpenVPN

The material introduces:

```text
OpenVPN
```

as a popular open-source VPN solution.

It can provide:

```text
Encryption
Tunneling
Traffic routing
Remote access
```

---

# Installing OpenVPN

We can install it using:

```bash
sudo apt install openvpn -y
```

---

# OpenVPN Configuration

The server configuration file introduced is:

```text
/etc/openvpn/server.conf
```

This may contain configuration for:

```text
Encryption
Tunneling
Traffic shaping
Other VPN settings
```

---

# `.ovpn` Files

Clients commonly receive a configuration file with the extension:

```text
.ovpn
```

For example:

```text
internal.ovpn
```

This contains configuration information required to establish the VPN connection.

---

# Connecting to OpenVPN

The command shown in the material is:

```bash
sudo openvpn --config internal.ovpn
```

Breaking it down:

```text
sudo
│
└── Run with elevated privileges

openvpn
│
└── OpenVPN client

--config
│
└── Use configuration file

internal.ovpn
│
└── VPN configuration
```

Once the connection is established, we can communicate with hosts available through that internal network.

---

# Network Services Mental Model

A useful way to separate the services is:

```text
SSH
→ Remote shell / remote administration

NFS
→ Remote files appear locally

Web Server
→ Serve files and web applications

VPN
→ Connect networks through an encrypted tunnel
```

---

# Important Configuration Files

| Service | Configuration               |
| ------- | --------------------------- |
| SSH     | `/etc/ssh/sshd_config`      |
| NFS     | `/etc/exports`              |
| Apache  | `/etc/apache2/apache2.conf` |
| OpenVPN | `/etc/openvpn/server.conf`  |

---

# Important Commands

Install SSH:

```bash
sudo apt install openssh-server -y
```

Check SSH:

```bash
systemctl status ssh
```

Connect through SSH:

```bash
ssh user@IP
```

Install NFS:

```bash
sudo apt install nfs-kernel-server -y
```

Mount an NFS share:

```bash
mount IP:/remote/path /local/path
```

Install Apache:

```bash
sudo apt install apache2 -y
```

Start a temporary web server:

```bash
python3 -m http.server
```

Connect to a VPN:

```bash
sudo openvpn --config internal.ovpn
```

---

# Quick Reference

| Service            | Main Purpose                     |
| ------------------ | -------------------------------- |
| SSH                | Secure remote access             |
| NFS                | Network file sharing             |
| Apache             | Web hosting                      |
| Python HTTP Server | Simple temporary web/file server |
| VPN                | Secure remote network access     |

---

# What to Remember First

The most important concepts from this section are:

```text
SSH
→ encrypted remote access
```

```text
NFS
→ mount remote filesystems
```

```text
Web Server
→ serve files/content through HTTP
```

```text
VPN
→ encrypted tunnel to another network
```

Also remember:

```bash
ssh user@IP
```

```bash
systemctl status ssh
```

```bash
mount IP:/share /local/path
```

```bash
python3 -m http.server
```

```bash
sudo openvpn --config file.ovpn
```

---

## Key Takeaway

**Linux network services provide different ways for systems to communicate. SSH provides secure remote access, NFS exposes remote filesystems, web servers deliver files and applications over HTTP, and VPNs create encrypted network tunnels. For cybersecurity, understanding how these services work and how they are configured helps us identify insecure configurations, exposed data, and potential attack surfaces.**
