# Introduction to Networks

Networks are fundamental to modern computing and cybersecurity. They allow devices to communicate, exchange data, and share resources locally or across large geographical distances.

This section introduces the basic concepts of computer networks, focusing primarily on **Local Area Networks (LANs)** and **Wide Area Networks (WANs)**.

---

## What is a Network?

A **network** is a group of interconnected devices capable of communicating and exchanging data.

Devices connected to a network are commonly called **nodes**. Examples include:

* Computers
* Smartphones
* Servers
* Printers
* IoT devices

Communication between these devices occurs through **links**, which may be wired or wireless.

### Core Concepts

| Concept          | Description                                                           |
| ---------------- | --------------------------------------------------------------------- |
| **Node**         | A device connected to a network.                                      |
| **Link**         | The communication path connecting nodes. It can be wired or wireless. |
| **Data Sharing** | Exchange of information between devices on the network.               |

A network therefore requires more than just connected devices: there must also be a communication medium that allows data to travel between them.

---

## Why Networks Matter

Networks enable devices and users to share information and resources efficiently.

Some common uses include:

| Function             | Description                                                                         |
| -------------------- | ----------------------------------------------------------------------------------- |
| **Resource Sharing** | Allows devices to share resources such as printers, storage, and services.          |
| **Communication**    | Enables services such as email, messaging, VoIP, and video calls.                   |
| **Data Access**      | Allows users and systems to access remote files, applications, and databases.       |
| **Collaboration**    | Enables users in different locations to work with shared resources and information. |

From a cybersecurity perspective, understanding networks is essential because communication between systems creates both functionality and potential attack surfaces.

---

## Types of Networks

Networks can be classified according to their geographical scope and purpose.

Two fundamental types are:

* **LAN — Local Area Network**
* **WAN — Wide Area Network**

### Local Area Network (LAN)

A **Local Area Network (LAN)** connects devices within a relatively small geographical area, such as:

* A home
* An office
* A school
* A laboratory

LANs are typically controlled by a single individual or organization.

Common characteristics include:

| Characteristic         | LAN                                         |
| ---------------------- | ------------------------------------------- |
| **Geographical Scope** | Small                                       |
| **Ownership**          | Usually a single organization or individual |
| **Speed**              | Generally high                              |
| **Media**              | Ethernet and Wi-Fi                          |
| **Management**         | Relatively simple                           |

A home network is a common example. Computers, smartphones, TVs, consoles, and other devices connect to a router through Ethernet or Wi-Fi and become part of the same LAN.

---

### Wide Area Network (WAN)

A **Wide Area Network (WAN)** connects networks across much larger geographical areas.

Instead of primarily connecting individual devices, WANs commonly provide connectivity between multiple LANs.

They can span:

* Cities
* States
* Countries
* Continents

Common characteristics include:

| Characteristic         | WAN                                                                    |
| ---------------------- | ---------------------------------------------------------------------- |
| **Geographical Scope** | Large                                                                  |
| **Ownership**          | Often distributed across multiple organizations or service providers   |
| **Speed**              | Generally lower than LANs due to distance and infrastructure           |
| **Media**              | Fiber optics, telecommunications infrastructure, satellite links, etc. |
| **Management**         | More complex                                                           |

The **Internet** is the largest example of a WAN, interconnecting networks throughout the world.

---

## LAN vs WAN

| Aspect         | LAN                 | WAN                     |
| -------------- | ------------------- | ----------------------- |
| **Coverage**   | Small/local area    | Large geographical area |
| **Ownership**  | Usually centralized | Often distributed       |
| **Speed**      | Generally higher    | Generally lower         |
| **Complexity** | Lower               | Higher                  |
| **Cost**       | Lower               | Higher                  |
| **Example**    | Home network        | Internet                |

The distinction is especially useful for understanding where network traffic is traveling: communication may remain inside the local network or leave the LAN to reach systems on external networks.

---

## How LANs and WANs Work Together

LANs are commonly connected to WANs to communicate with systems outside their local environment.

Devices communicate locally through the home network, while the router provides a path toward external networks.

The **Internet Service Provider (ISP)** provides connectivity between the customer's network and its broader network infrastructure.

Depending on the access technology, a **modem** or similar access device may be responsible for interfacing the customer's network equipment with the ISP's transmission infrastructure.

The same principle applies to organizations. A company may have separate LANs in different offices and use WAN connectivity to allow those locations to communicate and access shared services.

---

## Cybersecurity Perspective

Understanding the distinction between LAN and WAN becomes important when analyzing network security.

For example:

* What devices exist inside the local network?
* Which systems are reachable from other networks?
* Where does traffic enter or leave the LAN?
* Which devices control communication between networks?
* Which services are exposed externally?

Many cybersecurity activities—including **network enumeration, traffic analysis, firewall configuration, intrusion detection, and penetration testing**—depend on understanding these basic relationships.

---

## Key Takeaways

* A **network** is a collection of interconnected devices that communicate and exchange data.
* A connected device is commonly called a **node**.
* **Links** provide the communication paths between nodes.
* A **LAN** covers a relatively small geographical area.
* A **WAN** connects networks across larger geographical distances.
* WANs can interconnect multiple LANs.
* The **Internet** is the largest example of a WAN.
* An **ISP** provides connectivity between local networks and broader Internet infrastructure.
* Understanding how local and external networks interact is fundamental to cybersecurity.
