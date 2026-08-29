# Internet Architecture

**Internet Architecture** describes how systems, services, and network resources are organized and how they communicate.

Different architectures solve different problems and involve trade-offs related to:

* Scalability
* Performance
* Security
* Manageability
* Cost
* Reliability

The main architectures covered in this section are:

* **Peer-to-Peer (P2P)**
* **Client-Server**
* **Hybrid**
* **Cloud**
* **Software-Defined Networking (SDN)**

In real-world environments, these models can also be combined to create more complex architectures.

---

# Peer-to-Peer (P2P) Architecture

In a **Peer-to-Peer (P2P)** architecture, nodes communicate directly with each other.

Each peer can act as both:

* **Client**
* **Server**

Instead of depending entirely on a centralized server, peers can directly share resources such as:

* Files
* Storage
* Processing power
* Bandwidth

Conceptually:

```text
      Peer A
      ↙    ↘
 Peer B ─── Peer C
      ↘    ↙
      Peer D
```

Every participating system can potentially provide and consume resources.

---

## Decentralized Communication

P2P networks may be:

### Fully Decentralized

There is no central server controlling communication.

```text
Peer ─── Peer
 │         │
 │         │
Peer ─── Peer
```

### Partially Centralized

A central service may coordinate certain operations while the actual resources remain distributed among peers.

```text
         Coordinator
        /     |     \
       /      |      \
    Peer ─── Peer ─── Peer
```

The central system might help peers discover each other without necessarily hosting the shared data itself.

---

## P2P Example

A common example is file sharing.

Instead of:

```text
Client ──► Central Server
```

files can be transferred directly between participating peers.

**BitTorrent** is an example of this model.

A peer possessing a complete file and making it available to others is commonly called a:

**Seeder**

Multiple peers can participate in distributing the same content.

---

## P2P Advantages

| Advantage             | Description                                                               |
| --------------------- | ------------------------------------------------------------------------- |
| **Scalability**       | Additional peers can contribute additional resources.                     |
| **Resilience**        | The network may continue functioning when individual peers disconnect.    |
| **Cost Distribution** | Storage, processing, and bandwidth can be distributed among participants. |

---

## P2P Disadvantages

| Disadvantage              | Description                                                           |
| ------------------------- | --------------------------------------------------------------------- |
| **Management Complexity** | Centralized administration and policy enforcement are more difficult. |
| **Reliability**           | Resources may become unavailable if the necessary peers disconnect.   |
| **Security Challenges**   | Every participating peer can introduce additional security concerns.  |

---

# Client-Server Architecture

The **Client-Server** architecture separates systems into two primary roles:

* **Clients request resources or services**
* **Servers provide resources or services**

```text
Client
   │
   │ Request
   ▼
Server
   │
   │ Response
   ▼
Client
```

This is one of the most common architectures used on the Internet.

For example, when accessing a website:

```text
Browser
   │
   │ HTTP Request
   ▼
Web Server
   │
   │ HTTP Response
   ▼
Browser
```

The server centrally hosts the service or resource while multiple clients can access it.

---

# Tiered Architectures

Client-server systems can be further organized into **tiers**.

A tier represents a logical separation of responsibilities within an application architecture.

---

## Single-Tier Architecture

In a **single-tier architecture**, the major components exist on the same machine.

This can include:

```text
┌───────────────────────┐
│       Machine         │
│                       │
│ Client                │
│ Application / Server  │
│ Database              │
└───────────────────────┘
```

This architecture is simple but has significant limitations regarding:

* Scalability
* Security
* Distribution

As a result, it is rarely suitable for large applications.

---

# Two-Tier Architecture

A **two-tier architecture** separates the environment into:

1. Client
2. Server

The client commonly handles presentation while the server handles data.

```text
┌────────────┐        ┌────────────┐
│   Client   │───────►│   Server   │
│            │◄───────│            │
│Presentation│        │    Data    │
└────────────┘        └────────────┘
```

A common example is a desktop application communicating directly with a database server.

It is important not to confuse this with a normal web application.

A browser typically communicates with a **web server**, rather than directly accessing the underlying database.

---

# Three-Tier Architecture

A **three-tier architecture** introduces an application layer between the client and the database.

The three tiers are commonly:

1. Presentation
2. Application / Business Logic
3. Data

```text
┌──────────────┐
│    Client    │
│ Presentation │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Application  │
│    Server    │
│Business Logic│
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Database   │
│    Server    │
└──────────────┘
```

This separation provides greater flexibility because each component can be developed and maintained independently.

It can also improve:

* Scalability
* Maintainability
* Security
* Resource distribution

---

# N-Tier Architecture

An **N-tier architecture** extends the same concept beyond three tiers.

```text
Client
   │
   ▼
Web Tier
   │
   ▼
Application Tier
   │
   ▼
Service Tier
   │
   ▼
Data Tier
```

Different tiers can perform specialized functions.

This approach is commonly useful for complex systems requiring distributed deployment and greater scalability.

However, additional tiers also introduce greater:

* Deployment complexity
* Configuration complexity
* Security requirements
* Communication overhead

---

# Client-Server Advantages

| Advantage               | Description                                                |
| ----------------------- | ---------------------------------------------------------- |
| **Centralized Control** | Services and resources can be centrally managed.           |
| **Security**            | Centralized security policies can be applied.              |
| **Performance**         | Dedicated servers can be optimized for specific workloads. |

---

# Client-Server Disadvantages

| Disadvantage                | Description                                                                |
| --------------------------- | -------------------------------------------------------------------------- |
| **Single Point of Failure** | Failure of a central server may make the service unavailable.              |
| **Cost and Maintenance**    | Servers require infrastructure, operation, and administration.             |
| **Network Congestion**      | Large numbers of clients can generate significant traffic and server load. |

---

# Hybrid Architecture

A **Hybrid Architecture** combines characteristics of:

* Client-Server
* Peer-to-Peer

A central server may handle tasks such as:

* Authentication
* Coordination
* Directory services
* Session management

while data can be exchanged directly between peers.

Conceptually:

```text
              Central Server
             /      |      \
            /       |       \
         Peer A   Peer B   Peer C
            \       ↕       /
             ─── P2P Data ──
```

---

## Hybrid Example

A simplified video-conferencing system could use centralized infrastructure for:

```text
Authentication
Session Coordination
Access Control
```

while some media communication occurs directly between participating devices.

```text
       Central Server
        /         \
 Authentication  Coordination
      /             \
     ▼               ▼
 User A ◄══════════► User B
       Audio / Video
```

This reduces some workload on centralized infrastructure while retaining centralized control for specific operations.

---

## Hybrid Advantages

| Advantage      | Description                                                                             |
| -------------- | --------------------------------------------------------------------------------------- |
| **Efficiency** | Some workloads can be distributed among peers.                                          |
| **Control**    | Centralized systems can still manage functions such as authentication and coordination. |

## Hybrid Disadvantages

| Disadvantage                          | Description                                                                                           |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **Complex Implementation**            | Both centralized and distributed components must be managed.                                          |
| **Potential Single Point of Failure** | Failure of the coordinating infrastructure may disrupt peer discovery or other centralized functions. |

---

# Cloud Architecture

**Cloud Architecture** uses computing infrastructure hosted and managed by third-party providers.

Resources can include:

* Servers
* Storage
* Applications
* Computing capacity

These resources are accessed over a network, typically the Internet.

The underlying physical infrastructure is managed by the cloud provider rather than directly by the customer.

---

## SaaS Example

Services such as cloud-hosted applications can follow the:

**Software as a Service (SaaS)**

model.

Conceptually:

```text
User
 │
 │ Internet
 ▼
Cloud Application
 │
 ├── Compute
 ├── Storage
 └── Infrastructure
```

The user consumes the application without directly managing the underlying hardware.

---

# Cloud Characteristics

Five important characteristics are introduced for cloud environments.

## 1. On-Demand Self-Service

Users can provision and manage resources when required without direct manual intervention from the provider.

---

## 2. Broad Network Access

Cloud resources can be accessed through networks from different types of devices.

---

## 3. Resource Pooling

Computing resources are shared and dynamically allocated among customers.

---

## 4. Rapid Elasticity

Resources can be scaled up or down according to demand.

```text
Low Demand

[ Server ]

       ↓

High Demand

[ Server ]
[ Server ]
[ Server ]
[ Server ]
```

---

## 5. Measured Service

Resource usage can be monitored and measured.

This enables consumption-based models where customers pay according to resource usage.

---

# Cloud Advantages

| Advantage                              | Description                                                     |
| -------------------------------------- | --------------------------------------------------------------- |
| **Scalability**                        | Resources can be added or removed according to demand.          |
| **Reduced Infrastructure Maintenance** | The provider manages the underlying hardware.                   |
| **Flexibility**                        | Services can be accessed remotely through network connectivity. |

---

# Cloud Disadvantages

| Disadvantage                | Description                                                                       |
| --------------------------- | --------------------------------------------------------------------------------- |
| **Vendor Lock-In**          | Migrating between providers can be complex.                                       |
| **Security / Compliance**   | Third-party infrastructure introduces data governance and privacy considerations. |
| **Connectivity Dependency** | Access to cloud services generally depends on reliable network connectivity.      |

---

# Software-Defined Networking (SDN)

**Software-Defined Networking (SDN)** separates network control from the devices responsible for forwarding traffic.

Two concepts are fundamental:

* **Control Plane**
* **Data Plane**

---

## Control Plane

The **control plane** determines how network traffic should be handled.

It makes decisions regarding:

* Routing
* Policies
* Traffic flows

---

## Data Plane

The **data plane** performs the actual forwarding of network traffic.

It executes the decisions established by the control plane.

---

# Traditional Networking vs SDN

In traditional networking devices, both planes typically exist within each individual device.

```text
Traditional Network

Router
├── Control Plane
└── Data Plane

Switch
├── Control Plane
└── Data Plane
```

With SDN, control can instead be centralized in software.

```text
             SDN Controller
             Control Plane
             /     |     \
            /      |      \
           ▼       ▼       ▼
        Switch   Switch   Router
          │        │        │
          └──── Data Plane ─┘
```

Network devices execute instructions received from the controller.

---

# Why SDN?

Separating network control from forwarding provides a more programmable network environment.

Administrators can dynamically modify:

* Routing policies
* Traffic flows
* Network configurations
* Resource allocation

Instead of manually configuring every network device independently, changes can be coordinated through software.

This is particularly useful in:

* Large enterprises
* Datacenters
* Cloud environments

---

# SDN Advantages

| Advantage                        | Description                                              |
| -------------------------------- | -------------------------------------------------------- |
| **Centralized Control**          | Network policies can be centrally managed.               |
| **Programmability & Automation** | Configuration changes can be automated through software. |
| **Scalability & Efficiency**     | Traffic and resources can be dynamically optimized.      |

---

# SDN Disadvantages

| Disadvantage                 | Description                                                                        |
| ---------------------------- | ---------------------------------------------------------------------------------- |
| **Controller Vulnerability** | Problems affecting the centralized controller can have significant network impact. |
| **Complex Implementation**   | SDN requires specialized technologies and skills.                                  |

---

# Architecture Comparison

| Architecture      | Centralization                         | Scalability                           | Management                           | Typical Use                    |
| ----------------- | -------------------------------------- | ------------------------------------- | ------------------------------------ | ------------------------------ |
| **P2P**           | Decentralized or partially centralized | High as peers contribute resources    | More complex                         | File sharing                   |
| **Client-Server** | Centralized                            | Moderate                              | Easier centralized management        | Websites and email             |
| **Hybrid**        | Partially centralized                  | Higher than traditional client-server | More complex                         | Messaging and conferencing     |
| **Cloud**         | Provider infrastructure                | High                                  | Infrastructure management outsourced | SaaS and cloud storage         |
| **SDN**           | Centralized control plane              | High                                  | Requires specialized tools           | Datacenters and large networks |

---

# Cybersecurity Perspective

Each architecture creates a different security model.

### P2P

Security is distributed across many individual nodes.

A compromised or malicious peer can represent a risk to other participants.

### Client-Server

Security controls can be centralized, but critical servers become high-value targets.

A failure or compromise of central infrastructure may affect many clients.

### Hybrid

Security must account for both centralized infrastructure and direct communication between peers.

### Cloud

Some infrastructure responsibilities are transferred to the provider, while customers still depend on correct configuration and appropriate management of their resources.

### SDN

Centralized control simplifies policy management, but the SDN controller becomes a particularly important security component.

Understanding the architecture therefore helps identify:

* Critical assets
* Trust relationships
* Potential single points of failure
* Communication paths
* Security boundaries

---

# Key Takeaways

* **P2P** allows nodes to act as both clients and servers and communicate directly.
* P2P can improve scalability and resilience but makes centralized management more difficult.
* **Client-Server** centralizes services and resources on servers accessed by clients.
* Client-server systems can use **single-tier, two-tier, three-tier, or N-tier** designs.
* A **three-tier architecture** commonly separates presentation, business logic, and data.
* **Hybrid architectures** combine centralized coordination with distributed communication.
* **Cloud Architecture** provides computing resources through provider-managed infrastructure.
* Cloud environments support concepts such as on-demand service, resource pooling, elasticity, and measured usage.
* **SDN** separates the control plane from the data plane.
* The SDN control plane determines how traffic should flow, while the data plane forwards the traffic.
* Different architectures involve different trade-offs in scalability, security, performance, cost, and manageability.
