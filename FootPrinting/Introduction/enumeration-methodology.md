# Enumeration Methodology

---

## Overview

Enumeration is a dynamic process, but having a standardized methodology helps us organize our investigations and avoid overlooking important information.

The HTB enumeration methodology consists of **three levels** and **six layers**.

### Three Enumeration Levels

1. **Infrastructure-based Enumeration**
2. **Host-based Enumeration**
3. **OS-based Enumeration**

Each level allows us to investigate different aspects of the target, from its external infrastructure to its internal operating system configuration.

The methodology is flexible and can be adapted depending on the target environment.

---

## The Six Enumeration Layers

### Layer 1 — Internet Presence

We identify the organization's presence on the Internet and discover potential targets.

**Information to collect:**
- Domains and subdomains
- Virtual hosts (vHosts)
- Autonomous System Numbers (ASN)
- Netblocks and IP addresses
- Cloud instances
- Security measures

**Goal:** Identify all possible target systems and interfaces within the authorized scope.

### Layer 2 — Gateway

We investigate how the target's infrastructure is protected and how its systems are connected.

**Information to collect:**
- Firewalls
- DMZ (Demilitarized Zone)
- IDS/IPS
- EDR (Endpoint Detection and Response)
- Proxies
- NAC (Network Access Control)
- Network segmentation
- VPNs
- Cloudflare

**Goal:** Understand the security measures protecting the target and the restrictions we may encounter.

### Layer 3 — Accessible Services

We investigate the services and interfaces available on the target.

Each service has a specific purpose, configuration, and functionality that we need to understand.

**Information to collect:**
- Service type
- Functionality
- Configuration
- Open ports
- Service versions
- Interfaces

**Goal:** Understand how the target's services work and how we can interact with them.

**Note:** This is the main focus of the current HTB module.

### Layer 4 — Processes

We investigate the internal processes and operations associated with the services.

Every process performs tasks involving data from a source to a destination.

**Information to collect:**
- Process IDs (PIDs)
- Processed data
- Tasks
- Sources
- Destinations

**Goal:** Understand how processes operate and identify dependencies between them.

### Layer 5 — Privileges

We investigate the permissions and privileges assigned to users, groups, and services.

Misconfigured privileges may allow actions that administrators did not intend.

**Information to collect:**
- Users
- Groups
- Permissions
- Restrictions
- Environment

**Goal:** Identify what actions are possible with the available privileges and understand their limitations.

### Layer 6 — OS Setup

We investigate the operating system and its internal configuration after obtaining the necessary access.

**Information to collect:**
- Operating system type
- Patch level
- Network configuration
- OS environment
- Configuration files
- Sensitive files

**Goal:** Understand how the system is configured and managed, and identify potentially sensitive internal information.

---

## Enumeration as a Labyrinth

We can visualize penetration testing as a labyrinth consisting of multiple layers.

Each layer represents an obstacle that we need to understand and potentially overcome.

- Vulnerabilities or misconfigurations represent possible gaps.
- Not every discovered vulnerability provides a useful path forward.
- We should investigate different possibilities instead of forcing a single approach.
- Time limitations prevent us from guaranteeing that every vulnerability has been discovered.

**The objective is to identify effective paths through the infrastructure rather than blindly attempting exploitation.**

---

## Enumeration Methodology in Practice

A methodology is a structured approach to achieving an objective, **not a fixed step-by-step guide**.

Our enumeration methodology defines what we should investigate, while the techniques and tools we use may change depending on the environment.

### Methodology vs. Cheat Sheet

- **Methodology:** Defines our investigation approach and the information we want to obtain.
- **Cheat Sheet:** Provides specific commands, tools, and techniques that help us collect that information.

For example, different tools can enumerate a web server and produce different results, but our underlying objective remains the same.

The methodology remains consistent while our tools and techniques evolve.

---

## Key Takeaways

- Enumeration follows a structured but flexible methodology.
- The methodology contains **3 levels and 6 layers**.
- We investigate the target from its external infrastructure toward its internal configuration.
- Not every vulnerability leads to a useful attack path.
- The first two layers primarily apply to external infrastructure and differ in internal network assessments.
- **Accessible Services** is the main focus of this module.
- A methodology defines our objectives, while a cheat sheet provides the commands to achieve them.

**Understanding what we are investigating is more important than simply knowing which tools to execute.**