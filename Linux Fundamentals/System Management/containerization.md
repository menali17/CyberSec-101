# Containerization

Containerization is the process of packaging applications inside isolated environments called:

```text
containers
```

A container contains the application together with the files, libraries, dependencies, and configuration it needs to run consistently.

Common container technologies include:

```text
Docker
Docker Compose
LXC
```

Containers are generally lighter than traditional virtual machines because they **share the host system's kernel** instead of running a complete operating system for every instance.

---

# Containers vs Virtual Machines

A useful mental model is:

```text
Virtual Machine
→ Own operating system + own kernel

Container
→ Isolated environment sharing the host kernel
```

Conceptually:

```text
VIRTUAL MACHINES

Hardware
   │
   ▼
Host OS
   │
   ▼
Hypervisor
   │
   ├── VM 1 → Guest OS → Application
   │
   └── VM 2 → Guest OS → Application
```

Compared with:

```text
CONTAINERS

Hardware
   │
   ▼
Host Linux Kernel
   │
   ├── Container 1 → Application
   ├── Container 2 → Application
   └── Container 3 → Application
```

Because containers share the kernel, they usually require fewer resources.

---

# Why Containers Are Useful

Containers provide:

```text
Isolation
Portability
Consistency
Resource efficiency
Scalability
Fast deployment
```

For example, an application can be packaged together with:

```text
Application code
Libraries
Dependencies
Configuration
```

and then run consistently across different environments.

This helps reduce the classic problem:

```text
"It works on our machine."
```

because the application environment is packaged together with the application.

---

# Container Security

Containers isolate applications from:

```text
The host system
Other containers
```

This can reduce the impact of compromised applications.

However:

> Containers do not provide the same isolation level as traditional virtual machines.

The material specifically mentions risks such as:

```text
Privilege escalation
Container escape
```

A vulnerable or badly configured container may allow an attacker to reach the host or other containers.

---

# Docker

Docker is an open-source platform used to create, deploy, and manage containers.

Docker uses:

```text
Images
Containers
Dockerfiles
Docker Engine
Registries
```

Docker is primarily application-focused.

A useful way to think about the relationship is:

```text
Dockerfile
    │
    ▼
Docker Image
    │
    ▼
Docker Container
```

---

# Docker Image vs Container

This distinction is fundamental.

## Image

A Docker image is a template containing everything required to create a container.

Conceptually:

```text
IMAGE
→ blueprint / template
```

## Container

A container is a running instance created from an image.

Conceptually:

```text
IMAGE
   │
   │ docker run
   ▼
CONTAINER
```

The material describes an image as a read-only template and a container as a running process created from that image.

---

# Docker Engine

The:

```text
Docker Engine
```

is responsible for building images and running/managing containers.

Conceptually:

```text
Docker CLI
    │
    ▼
Docker Engine
    │
    ├── Images
    │
    └── Containers
```

---

# Docker Hub

Docker images can be obtained from:

```text
Docker Hub
```

Docker Hub acts as a registry containing Docker images.

It has:

```text
Public images
Private images
Official images
Community images
```

Conceptually:

```text
Docker Hub
     │
     │ docker image
     ▼
Our system
     │
     ▼
Docker Engine
     │
     ▼
Container
```

---

# Dockerfile

A:

```text
Dockerfile
```

is a text file containing instructions used to build a Docker image.

The workflow is:

```text
Dockerfile
    │
    │ docker build
    ▼
Image
    │
    │ docker run
    ▼
Container
```

---

# Dockerfile Example

The material uses:

```dockerfile
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y \
        apache2 \
        openssh-server \
    && \
    rm -rf /var/lib/apt/lists/*

RUN useradd -m docker-user && \
    echo "docker-user:password" | chpasswd

RUN chown -R docker-user:docker-user /var/www/html && \
    chown -R docker-user:docker-user /var/run/apache2 && \
    chown -R docker-user:docker-user /var/log/apache2 && \
    chown -R docker-user:docker-user /var/lock/apache2 && \
    usermod -aG sudo docker-user && \
    echo "docker-user ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

EXPOSE 22 80

CMD service ssh start && /usr/sbin/apache2ctl -D FOREGROUND
```

This Dockerfile creates an Ubuntu-based environment with:

```text
Apache
SSH
docker-user
Ports 22 and 80
```

---

# `FROM`

The Dockerfile starts with:

```dockerfile
FROM ubuntu:22.04
```

This defines the:

```text
base image
```

So our new image starts from Ubuntu 22.04.

Conceptually:

```text
ubuntu:22.04
     │
     ▼
Our custom image
```

---

# `RUN`

`RUN` executes commands while the image is being built.

For example:

```dockerfile
RUN apt-get update
```

or:

```dockerfile
RUN useradd -m docker-user
```

These commands modify the image during the build process.

---

# `EXPOSE`

The Dockerfile contains:

```dockerfile
EXPOSE 22 80
```

This documents the ports used by the application inside the container.

Here:

```text
22
→ SSH

80
→ HTTP
```

Port exposure alone does not perform the host-to-container port mapping shown later with `docker run -p`.

---

# `CMD`

The Dockerfile uses:

```dockerfile
CMD service ssh start && /usr/sbin/apache2ctl -D FOREGROUND
```

This specifies what should run when a container is started from the image.

In this case:

```text
Start SSH
+
Run Apache in foreground
```

---

# Building a Docker Image

After creating the Dockerfile, we build the image using:

```bash
docker build -t FS_docker .
```

Breaking it down:

```text
docker build
→ build an image

-t FS_docker
→ assign the tag/name FS_docker

.
→ use the current directory as the build context
```

The current directory should contain the Dockerfile in this example.

---

# Build Process

Conceptually:

```text
Current Directory
      │
      ├── Dockerfile
      └── other build files
             │
             ▼
       docker build
             │
             ▼
        FS_docker
          IMAGE
```

If a Dockerfile step fails, the build cannot successfully complete.

---

# Running a Docker Container

The general syntax shown is:

```bash
docker run -p <host port>:<docker port> -d <docker container name>
```

The example is:

```bash
docker run -p 8022:22 -p 8080:80 -d FS_docker
```

This starts a new container from:

```text
FS_docker
```

---

# `-d`

The option:

```text
-d
```

runs the container in:

```text
detached mode
```

This means the container runs in the background.

So:

```bash
docker run -d FS_docker
```

means roughly:

> Start a container from `FS_docker` and keep it running in the background.

---

# Port Mapping

One of the most important Docker concepts in this section is:

```text
HOST_PORT:CONTAINER_PORT
```

For example:

```bash
-p 8022:22
```

means:

```text
Host port 8022
      │
      ▼
Container port 22
```

Since port `22` is SSH:

```text
Our machine :8022
      │
      ▼
Container :22 SSH
```

---

# HTTP Port Mapping

The same command contains:

```bash
-p 8080:80
```

meaning:

```text
Host port 8080
      │
      ▼
Container port 80
```

Since port `80` is HTTP:

```text
http://HOST:8080
        │
        ▼
Container HTTP :80
```

The material explicitly maps host ports `8022` and `8080` to container ports `22` and `80`.

---

# Port Mapping Mental Model

The whole setup looks like:

```text
HOST

Port 8022
   │
   └──────────► Container :22
                 SSH

Port 8080
   │
   └──────────► Container :80
                 Apache
```

This lets us access services inside an isolated container through host ports.

---

# Docker Management

Docker provides commands for managing running and stopped containers.

| Command          | Purpose                   |
| ---------------- | ------------------------- |
| `docker ps`      | List running containers   |
| `docker stop`    | Stop a container          |
| `docker start`   | Start a stopped container |
| `docker restart` | Restart a container       |
| `docker rm`      | Remove a container        |
| `docker rmi`     | Remove an image           |
| `docker logs`    | Display container logs    |

---

# `docker ps`

We can list running containers using:

```bash
docker ps
```

A useful mental model is:

```text
ps
→ processes

docker ps
→ running Docker containers
```

---

# `docker stop`

To stop a running container:

```bash
docker stop <container>
```

---

# `docker start`

To restart a previously stopped container:

```bash
docker start <container>
```

---

# `docker restart`

To stop and start a running container:

```bash
docker restart <container>
```

---

# `docker rm`

To remove a container:

```bash
docker rm <container>
```

This removes the container, not necessarily the image from which it was created.

---

# `docker rmi`

To remove an image:

```bash
docker rmi <image>
```

The `i` can help us remember:

```text
rmi
→ remove image
```

---

# `docker logs`

To inspect container logs:

```bash
docker logs <container>
```

This is useful when:

```text
Application fails
Service crashes
We need debugging information
We want to inspect application activity
```

---

# Container Data and Persistence

The material emphasizes that modifications made inside a running container are not automatically written back into the original image.

Think:

```text
IMAGE
  │
  ▼
CONTAINER
  │
  ├── file modified
  ├── package installed
  └── configuration changed

Original IMAGE
→ unchanged
```

If we want a reusable updated image, the material recommends building a new image through a Dockerfile.

---

# Stateless Containers

The material describes Docker containers as:

```text
stateless by design
```

and recommends using:

```text
volumes
```

to persist data outside the container.

Conceptually:

```text
Container
   │
   │ application data
   ▼
Volume
   │
   ▼
Persistent storage
```

If the container is replaced, data stored in the external volume can remain available.

---

# Container Orchestration

When many containers need to work together, management becomes more complex.

The material mentions:

```text
Docker Compose
Kubernetes
```

These tools help:

```text
Manage containers
Connect containers
Scale applications
Coordinate services
```

---

# LXC

`LXC` stands for:

```text
Linux Containers
```

LXC is another Linux containerization technology.

Like Docker, LXC allows multiple isolated environments to run on the same Linux host.

It relies on Linux kernel technologies including:

```text
cgroups
namespaces
```

Containers share the host kernel, making them lighter than full virtual machines.

---

# Docker vs LXC

The material presents Docker and LXC as different approaches to containerization.

A simplified distinction is:

```text
Docker
→ application-focused

LXC
→ system/environment-focused
```

---

# Docker Approach

Docker commonly packages:

```text
Application
Dependencies
Libraries
Configuration
```

into portable images.

It is particularly common in:

```text
DevOps
Microservices
Application deployment
```

---

# LXC Approach

LXC is closer to creating:

```text
a lightweight isolated Linux environment
```

It can behave more like a lightweight virtual machine.

Conceptually:

```text
Docker
→ "Run this application."

LXC
→ "Run this isolated Linux environment."
```

This is the main distinction emphasized by the material.

---

# Docker vs LXC Comparison

| Category    | Docker                 | LXC                                 |
| ----------- | ---------------------- | ----------------------------------- |
| Focus       | Applications           | Linux environments                  |
| Images      | Standard Docker images | More manual environment setup       |
| Portability | High                   | More host-dependent                 |
| Ease of use | Generally easier       | More Linux administration knowledge |
| Typical use | Apps / microservices   | System-level containers             |

---

# Installing LXC

On Ubuntu:

```bash
sudo apt install lxc -y
```

---

# Creating an LXC Container

The material uses:

```bash
sudo lxc-create -n linuxcontainer -t ubuntu
```

Breaking it down:

```text
lxc-create
→ create container

-n linuxcontainer
→ container name

-t ubuntu
→ use Ubuntu template
```

So:

```text
Container name
→ linuxcontainer

Base/template
→ Ubuntu
```

---

# Managing LXC Containers

Important commands include:

| Command                      | Purpose              |
| ---------------------------- | -------------------- |
| `lxc-ls`                     | List containers      |
| `lxc-stop -n <container>`    | Stop container       |
| `lxc-start -n <container>`   | Start container      |
| `lxc-restart -n <container>` | Restart container    |
| `lxc-attach -n <container>`  | Connect to container |

The material also introduces `lxc-config` for storage, network, and security configuration.

---

# Connecting to an LXC Container

We can connect to a container using:

```bash
lxc-attach -n <container>
```

Example:

```bash
lxc-attach -n linuxcontainer
```

Conceptually:

```text
Host terminal
     │
     ▼
lxc-attach
     │
     ▼
Container shell
```

---

# Why Containers Matter in Cybersecurity

Containers allow us to create controlled testing environments.

Examples include:

```text
Testing specific software versions
Running vulnerable applications
Reproducing complex dependencies
Testing web servers
Analyzing exploits
Creating isolated labs
```

The material also mentions testing exploits or malware in controlled container environments.

However, containers must still be configured securely because the isolation is not absolute.

---

# cgroups

`cgroups` stands for:

```text
control groups
```

They control how much of the host's resources a container can consume.

Examples include:

```text
CPU
Memory
Disk-related resources
```

Conceptually:

```text
Host Resources
     │
     ├── Container A → limited resources
     │
     └── Container B → limited resources
```

---

# CPU Limit Example

The material uses:

```text
lxc.cgroup.cpu.shares = 512
```

The material compares this with the default value:

```text
1024
```

and presents `512` as providing a smaller relative CPU share.

---

# Memory Limit Example

The material uses:

```text
lxc.cgroup.memory.limit_in_bytes = 512M
```

This defines a maximum memory allocation of:

```text
512 MB
```

for the container.

---

# Applying LXC Configuration Changes

After modifying the LXC configuration, the material restarts the service:

```bash
sudo systemctl restart lxc.service
```

This connects with the previous **Service Management** section:

```text
Configuration changed
       │
       ▼
systemctl restart
       │
       ▼
Service reloads through restart
```

---

# Namespaces

`namespaces` are Linux kernel features that provide isolation between processes and resources.

The material describes isolation of areas including:

```text
Processes
Networks
File systems
```

---

# PID Namespace

Containers can have isolated process ID spaces.

Conceptually:

```text
HOST

PID 100
PID 200
PID 300

──────── isolation ────────

CONTAINER

PID 1
PID 2
PID 3
```

The processes inside the container operate within their own process namespace.

---

# Network Namespace

A container can have its own:

```text
Network interfaces
Routing tables
Firewall rules
```

Conceptually:

```text
HOST NETWORK
     │
     │ isolation
     ▼
CONTAINER NETWORK
```

---

# Mount Namespace

Containers can also have isolated file-system views.

The material describes containers as having their own root filesystem view.

Conceptually:

```text
HOST

/
├── home
├── etc
└── var

        isolation

CONTAINER

/
├── home
├── etc
└── var
```

Changes within the container's filesystem view do not automatically mean equivalent changes to the host filesystem.

---

# Namespaces vs cgroups

This distinction is very useful.

```text
Namespaces
→ What can the container SEE?

cgroups
→ How much can the container USE?
```

For example:

```text
Namespaces
→ isolated processes
→ isolated network
→ isolated filesystem

cgroups
→ CPU limits
→ memory limits
→ resource control
```

Together they are major Linux technologies underlying container isolation.

---

# Container Security

Important container security measures mentioned in the material include:

```text
Restrict access
Limit resources
Isolate containers from the host
Use mandatory access controls
Keep containers updated
Disable unnecessary services
Use strong authentication
```

Because the host kernel is shared, container security depends heavily on correct isolation and configuration.

---

# Complete Docker Workflow

The basic Docker workflow is:

```text
Dockerfile
    │
    ▼
docker build
    │
    ▼
IMAGE
    │
    ▼
docker run
    │
    ▼
CONTAINER
    │
    ├── docker ps
    ├── docker logs
    ├── docker stop
    └── docker restart
```

---

# Complete Container Mental Model

```text
                HOST LINUX
                    │
                    │ shared kernel
                    ▼
        ┌──────────────────────┐
        │     Container A      │
        │                      │
        │ Application          │
        │ Libraries            │
        │ Configuration        │
        └──────────────────────┘

        ┌──────────────────────┐
        │     Container B      │
        │                      │
        │ Application          │
        │ Libraries            │
        │ Configuration        │
        └──────────────────────┘

Namespaces
→ isolation

cgroups
→ resource control
```

---

# Quick Reference

| Concept        | Purpose                                 |
| -------------- | --------------------------------------- |
| Container      | Isolated runtime environment            |
| Image          | Template used to create containers      |
| Dockerfile     | Instructions for building an image      |
| Docker Engine  | Builds and runs Docker containers       |
| Docker Hub     | Registry for Docker images              |
| `docker build` | Build image                             |
| `docker run`   | Start container from image              |
| `docker ps`    | List running containers                 |
| `docker logs`  | Inspect logs                            |
| `docker stop`  | Stop container                          |
| `docker rm`    | Remove container                        |
| `docker rmi`   | Remove image                            |
| LXC            | Linux system-level container technology |
| namespace      | Resource isolation                      |
| cgroup         | Resource control                        |

---

# Commands to Remember First

Build an image:

```bash
docker build -t FS_docker .
```

Run a container:

```bash
docker run -p 8022:22 -p 8080:80 -d FS_docker
```

List containers:

```bash
docker ps
```

Stop:

```bash
docker stop <container>
```

View logs:

```bash
docker logs <container>
```

Install LXC:

```bash
sudo apt install lxc -y
```

Create an LXC container:

```bash
sudo lxc-create -n linuxcontainer -t ubuntu
```

Connect to LXC:

```bash
lxc-attach -n linuxcontainer
```

---

# What to Remember First

The most important relationships are:

```text
Dockerfile
→ recipe

Image
→ template

Container
→ running instance
```

And:

```text
docker build
→ Dockerfile → Image

docker run
→ Image → Container
```

For isolation:

```text
Namespaces
→ isolate resources

cgroups
→ limit resources
```

And the fundamental difference from a VM:

```text
VM
→ own guest OS/kernel

Container
→ shares host kernel
```

---

## Key Takeaway

**Containers provide lightweight isolated environments by sharing the Linux host kernel while separating applications through mechanisms such as namespaces and cgroups. Docker focuses primarily on portable application containers built from images and Dockerfiles, while LXC focuses more on isolated Linux environments. Containers are efficient and highly useful for development, deployment, and cybersecurity testing, but they do not provide perfect isolation and can become security risks when misconfigured.**
