# Introduction

Microsoft's Active Directory is the backbone of the corporate world. It simplifies the management of devices and users within a corporate environment.

# Windows Domains

Managing computers individually works well in small environments, but this approach becomes difficult to maintain as the number of users and devices increases.

A **Windows Domain** provides a centralized way to manage users and computers within an organization's network.

Instead of configuring each machine separately, administrators can manage common network components through a central directory called **Active Directory (AD)**.

### Active Directory

**Active Directory (AD)** is Microsoft's directory service used to centralize the administration of users, computers, and other resources within a Windows domain.

This allows administrators to manage the network from a central location instead of configuring every computer independently.

### Advantages of Windows Domains

Using a Windows domain provides several advantages over managing computers individually.

-  Centralized Identity Management

    User accounts across the network can be managed centrally through **Active Directory**.

    Instead of creating and configuring users separately on each computer, administrators can manage their identities from a central location, reducing administrative effort and making the environment easier to scale.

- Security Policy Management

    Administrators can centrally configure **security policies** and apply them to users and computers across the domain.

    This allows organizations to maintain consistent security configurations without manually configuring each machine.

# Active Directory



The core of a Windows Domain is **Active Directory Domain Services (AD DS)**.

AD DS works as a directory that stores information about the different **objects** that exist within a domain.

Some common Active Directory objects include:

- Users
- Groups
- Computers
- Printers
- Network shares

These objects represent resources and entities that administrators can centrally organize and manage.

### Security Principals

Some Active Directory objects are classified as **security principals**.

A security principal is an entity that can be **authenticated** and can be assigned permissions or privileges over resources in the network.

Examples include:

- Users
- Computers
- Security Groups

Security principals are therefore important for controlling **who or what can access resources** within a domain.

---

### Users

**User objects** represent accounts that can authenticate within the domain and access resources according to their assigned permissions.

Users generally represent two types of entities:

#### People

User accounts commonly represent actual people within an organization, such as employees.

These accounts allow users to authenticate to the domain and access resources for which they have permission.

#### Services

User accounts can also represent **services** or applications.

For example, services such as IIS or MSSQL may run under dedicated accounts.

Service accounts should generally receive only the permissions required to perform their specific function, following the **Principle of Least Privilege**.

---

### Machines

When a computer joins an Active Directory domain, a corresponding **computer (machine) account** is automatically created in Active Directory.

Computer accounts are also **security principals**, meaning that computers themselves can authenticate to the domain and receive permissions.

Machine accounts follow a recognizable naming convention:

    <COMPUTER_NAME>$

For example, a computer named:

    DC01

will normally have the machine account:

    DC01$

The `$` suffix is commonly used to identify machine accounts.

Machine accounts have their own passwords, which are automatically managed and periodically changed by Windows. These credentials are normally used by the computer itself rather than by a human user.

---

### Security Groups

**Security Groups** simplify permission management by allowing administrators to assign permissions to a group rather than individually to every user or computer.

For example:

    Permission -> Security Group -> Users

Instead of granting access to a resource separately to several employees, an administrator can grant access to a security group and then add the required users to that group.

Members receive the permissions associated with the group.

Security groups can contain:

- Users
- Computers
- Other groups

Groups are also **security principals**, meaning they can be assigned permissions over domain resources.

This approach makes access control significantly easier to manage in larger environments.

---

### Important Default Security Groups

Active Directory domains contain several built-in groups with predefined roles and privileges.

| Security Group | Description |
|---|---|
| **Domain Admins** | Members have administrative privileges across the domain and can normally administer domain-joined computers, including Domain Controllers. |
| **Server Operators** | Members can perform administrative tasks on Domain Controllers but cannot modify administrative group memberships. |
| **Backup Operators** | Members can bypass normal file permissions when performing backup and restore operations. |
| **Account Operators** | Members can create and modify many types of accounts within the domain, subject to certain restrictions. |
| **Domain Users** | Contains domain user accounts by default. |
| **Domain Computers** | Contains computers that have joined the domain. |
| **Domain Controllers** | Contains the Domain Controllers within the domain. |

### Active Directory Users and Computers

**Active Directory Users and Computers (ADUC)** is a Microsoft Management Console (MMC) tool used to view and manage objects within an Active Directory domain.

Through ADUC, administrators can perform tasks such as:

- Creating, deleting, and modifying users
- Managing groups
- Managing computer accounts
- Organizing objects into Organizational Units
- Resetting user passwords

ADUC also provides a hierarchical view of how objects are organized within the domain.

---

### Organizational Units (OUs)

**Organizational Units (OUs)** are container objects used to organize users, computers, and other objects within Active Directory.

They are commonly designed to reflect the organizational structure of a company.

For example:

    Company
    |
    +-- IT
    |   +-- Users
    |   +-- Computers
    |
    +-- Management
    |
    +-- Marketing
    |
    +-- Sales

This structure makes it easier to administer objects belonging to different parts of an organization.

One of the main purposes of OUs is to provide a logical structure for **applying policies and delegating administration**.

For example, computers belonging to the IT department may require different configurations from computers used by the Sales department.

An Active Directory object exists in **one location in the OU hierarchy at a time**.

> **Note:** OUs are not primarily designed to grant access to resources. Their main purpose is organizing objects and providing administrative and policy boundaries.

---

### Default Containers

Active Directory creates several containers and OUs by default.

Some important ones include:

| Container / OU | Purpose |
|---|---|
| **Builtin** | Contains built-in groups used by the domain |
| **Computers** | Default location for newly joined computer accounts |
| **Domain Controllers** | OU containing the Domain Controllers |
| **Users** | Default container for several domain users and groups |
| **Managed Service Accounts** | Contains managed accounts used by services |

Computers and users do not necessarily need to remain in their default containers. Administrators can organize them into appropriate OUs according to the organization's structure and administrative requirements.

---

### Organizational Units vs Security Groups

Although both **Organizational Units** and **Security Groups** can be used to organize objects conceptually, they serve different purposes.

#### Organizational Units

OUs are primarily used for:

- Organizing Active Directory objects
- Applying policies to sets of users and computers
- Delegating administrative responsibilities

An object occupies a single position within the domain's OU hierarchy.

For example:

    Company
    |
    +-- Sales
        |
        +-- Alice
        +-- Bob

Policies associated with the Sales organizational structure can then be applied to the appropriate objects.

#### Security Groups

Security Groups are primarily used to **assign permissions to resources**.

Instead of assigning permissions individually to each user, permissions can be granted to a group:

    Shared Folder
         |
      Permission
         |
    Sales Group
       /     \
    Alice    Bob

Users can belong to **multiple Security Groups simultaneously**, allowing them to receive permissions for different resources.

For example, the same user could belong to:

- Sales
- VPN Users
- Printer Access
- Finance Reports

without changing their position in the OU hierarchy.

---

### OU vs Security Group

| | Organizational Unit (OU) | Security Group |
|---|---|---|
| **Main purpose** | Organization and administration | Access control |
| **Used for policies** | Yes | Not primarily |
| **Used to grant resource permissions** | No | Yes |
| **Object location** | One position in the OU hierarchy | Can belong to multiple groups |
| **Typical example** | Sales department OU | Printer Access group |

A simple way to remember the distinction is:

    OU -> Where the object is organized
    Group -> What the object can access


# Managing Users in AD

### Managing Organizational Units

#### Accidental Deletion Protection

Organizational Units can contain many important Active Directory objects, including users, groups, computers, and even other OUs.

To reduce the risk of accidentally deleting these objects, OUs can be configured with **accidental deletion protection**.

In Active Directory Users and Computers (ADUC), this setting can be accessed by enabling:

    View -> Advanced Features

Then, through the OU's properties:

    OU -> Properties -> Object
       -> Protect object from accidental deletion

This protection helps prevent administrators from unintentionally deleting an OU.


---

### Delegation of Control

Active Directory administration does not require every administrative task to be performed by a **Domain Administrator**.

Through **delegation**, specific users or groups can be granted permission to perform particular administrative tasks over specific parts of Active Directory.

For example, members of an IT Help Desk team could be allowed to reset passwords for users in a particular OU without receiving administrative privileges over the entire domain.

A simplified model would be:

    Domain
    |
    +-- Sales OU
    |      |
    |      +-- Users
    |
    +-- IT Support
           |
           +-- Delegated permission:
               Reset passwords in Sales OU

This provides more granular administrative control and helps avoid giving users unnecessary privileges.

#### Delegating Permissions

In ADUC, permissions over an OU can be delegated using:

    Right-click OU
          |
          v
    Delegate Control
          |
          v
    Select user/group
          |
          v
    Select allowed tasks

Examples of tasks that can be delegated include:

- Resetting user passwords
- Creating or deleting certain objects
- Managing user accounts
- Modifying specific properties

Delegation should follow the **Principle of Least Privilege**, meaning that users should receive only the permissions required to perform their responsibilities.

---

### Resetting an Active Directory User Password with PowerShell

If an administrator or delegated user has the appropriate permissions, an Active Directory user's password can be reset through PowerShell.

    Set-ADAccountPassword <USERNAME> -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose

The command:

- `Set-ADAccountPassword` modifies the account password.
- `<USERNAME>` identifies the target account.
- `-Reset` indicates an administrative password reset.
- `Read-Host -AsSecureString` securely prompts for the new password.
- `-Verbose` displays additional information about the operation.

After an administrative password reset, the user can be required to choose a new password during their next login:

    Set-ADUser -ChangePasswordAtLogon $true -Identity <USERNAME> -Verbose

This prevents the temporary password chosen by the administrator or Help Desk operator from remaining as the user's permanent password.

---

### Why Delegation Matters

Without delegation, an organization might need to give broad administrative privileges to employees who only need to perform a small number of administrative tasks.

For example:

    Bad approach:

    Help Desk
        |
        v
    Domain Admin privileges
        |
        +-- Reset passwords
        +-- Manage critical objects
        +-- Administer domain computers
        +-- Modify privileged accounts
        +-- Other unnecessary privileges

A better approach is:

    Help Desk
        |
        v
    Delegated Permissions
        |
        v
    Specific OU
        |
        +-- Reset passwords

Delegation therefore provides **granular administrative control** and supports the **Principle of Least Privilege** by avoiding unnecessary privileged access.

### Key Concept

**Delegation of Control** allows administrators to grant specific administrative permissions over particular Active Directory objects or OUs without giving the delegated user full administrative privileges over the domain.

In short:

    Domain Admin
        -> Broad administrative privileges

    Delegated User
        -> Specific privileges
        -> Specific scope
        -> Only what is necessary


# Managing Computers in AD

When a computer joins an Active Directory domain, a corresponding **computer account** is created.

By default, domain-joined computers, except for Domain Controllers, are placed in the built-in **Computers container**.

Keeping every computer in the same container is usually not ideal. Different types of devices have different purposes and security requirements, so they may need different configurations and policies.

A common approach is to organize computers according to their role within the organization.

### Common Types of Domain Computers

#### Workstations

**Workstations** are computers used by regular users to perform their daily activities.

Examples include:

- Desktop computers
- Employee laptops
- Office PCs

Users commonly authenticate to the domain through these machines to access organizational resources and perform their work.

Privileged accounts should generally **not be used on regular workstations**, since these machines have a larger attack surface due to activities such as web browsing, email usage, and running user applications.

Compromising a workstation where a highly privileged account has been used could potentially expose privileged credentials.

---

#### Servers

**Servers** are computers responsible for providing services or resources to users and other systems.

Examples may include:

- Web servers
- File servers
- Database servers
- Application servers

Because servers have different purposes and security requirements from regular workstations, they should generally be managed separately.

---

#### Domain Controllers

**Domain Controllers (DCs)** provide the services required to operate and manage an Active Directory domain.

They are among the most security-sensitive systems in an Active Directory environment because they handle critical domain information and authentication-related data.

Domain Controllers are automatically placed in the:

    Domain Controllers OU

Because compromising a Domain Controller can potentially compromise the entire domain, access to these systems should be highly restricted.

---

### Organizing Computers with OUs

Computers can be separated into **Organizational Units (OUs)** according to their purpose.

A simple structure could look like:

    thm.local
    |
    +-- Workstations
    |   |
    |   +-- PC01
    |   +-- PC02
    |   +-- LAPTOP01
    |
    +-- Servers
    |   |
    |   +-- SERVER01
    |   +-- SERVER02
    |
    +-- Domain Controllers
        |
        +-- DC01

There is no universal OU structure that every organization must follow. The structure should reflect the organization's administrative and security requirements.

Separating computers by role makes it easier to apply different configurations and policies to each type of system.

For example:

    Workstations OU
         |
         +-- Workstation-specific policies

    Servers OU
         |
         +-- Server-specific policies

    Domain Controllers OU
         |
         +-- Highly restrictive DC policies

This becomes especially important when using **Group Policies**, which can apply different configurations to computers depending on their location within the Active Directory hierarchy.

### Key Concept

Computer organization in Active Directory should reflect the different **roles and security requirements** of the devices.

A useful starting point is:

    Workstations -> End-user computers
    Servers      -> Systems providing services
    DCs          -> Systems managing the domain

Separating these systems into appropriate OUs enables more granular administration and policy enforcement.

# Group Policies

Organizing users and computers into **Organizational Units (OUs)** allows administrators to apply different configurations and security requirements to different parts of an Active Directory environment.

Windows provides centralized policy management through **Group Policy Objects (GPOs)**.

### Group Policy Objects (GPOs)

A **Group Policy Object (GPO)** is a collection of configuration and security settings that can be applied to users and computers in an Active Directory domain.

GPOs allow administrators to centrally enforce configurations instead of manually configuring every machine.

Examples include:

- Password requirements
- Account lockout policies
- Screen lock settings
- Control Panel restrictions
- Windows security settings
- Desktop configurations
- Software-related policies

A simplified model is:

    Active Directory
          |
          +-- Organizational Units
          |       |
          |       +-- Users
          |       +-- Computers
          |
          +-- Group Policy Objects
                  |
                  +-- Security Settings
                  +-- User Settings
                  +-- Computer Settings

The GPO is then **linked** to the location where its settings should apply.

---

### Group Policy Management

GPOs can be created and managed using the **Group Policy Management Console (GPMC)**.

The console provides a hierarchical view of the Active Directory domain and its OUs.

A typical workflow is:

    Create GPO
        |
        v
    Configure Settings
        |
        v
    Link GPO
        |
        v
    Domain / OU
        |
        v
    Users or Computers

Creating a GPO alone does not determine which objects receive its settings. The GPO must be **linked** to an appropriate domain, site, or OU.

---

### GPO Scope and Inheritance

A GPO affects the location where it is linked and, by default, its child OUs.

For example:

    thm.local
       |
       |  Default Domain Policy
       |
       +-- THM
           |
           +-- Sales
           +-- Marketing
           +-- IT

If a GPO is linked to `thm.local`, objects located in its child OUs can also receive that policy through **inheritance**.

Therefore, policies linked higher in the Active Directory hierarchy can affect many objects below them.

This makes the location where a GPO is linked an important administrative and security decision.

---

### Security Filtering

A GPO does not necessarily need to apply to every object within its scope.

**Security Filtering** can restrict which users or computers are allowed to apply a particular GPO.

By default, many GPOs use:

    Authenticated Users

This allows authenticated domain users and computers within the appropriate scope to process the policy.

Security Filtering can be changed when administrators need more granular control over which objects receive a policy.

---

### User Configuration vs Computer Configuration

Each GPO contains two main categories of settings:

#### Computer Configuration

Contains policies that apply to **computers**.

Examples include:

- Security settings
- System configurations
- Machine inactivity limits
- Computer-specific Windows settings

These policies affect the computer regardless of which user is currently using it.

#### User Configuration

Contains policies that apply to **users**.

Examples include:

- Desktop restrictions
- Control Panel restrictions
- User interface configurations
- User-specific Windows settings

A simplified distinction is:

    Computer Configuration
            |
            v
        "How should this machine behave?"

    User Configuration
            |
            v
        "How should this user's environment behave?"

---

### Default Domain Policies

Active Directory domains normally contain default GPOs.

Two important examples are:

#### Default Domain Policy

The **Default Domain Policy** is linked to the domain and commonly contains domain-wide settings such as:

- Password policies
- Account lockout policies
- Kerberos-related account policies

Because of its broad scope, changes to domain-wide policies can affect users and computers throughout the domain.

#### Default Domain Controllers Policy

The **Default Domain Controllers Policy** is linked specifically to the:

    Domain Controllers OU

It contains policies intended for Domain Controllers.

---

### Example: Minimum Password Length

Password requirements can be centrally configured through Group Policy.

A password policy can be found through a path similar to:

    Computer Configuration
        |
        +-- Policies
            |
            +-- Windows Settings
                |
                +-- Security Settings
                    |
                    +-- Account Policies
                        |
                        +-- Password Policy
                            |
                            +-- Minimum password length

For example, an organization could require passwords to contain at least:

    10 characters

Instead of configuring this requirement separately on every system, the appropriate domain policy centrally enforces the requirement.

---

## GPO Distribution

Group Policy information must be accessible to domain-joined computers so that they can retrieve and apply their policies.

An important component of this process is **SYSVOL**.

### SYSVOL

**SYSVOL** is a shared directory stored on Domain Controllers that contains domain-wide files required by Active Directory, including files associated with Group Policy.

The default local path is:

    C:\Windows\SYSVOL\sysvol\

Domain clients can access SYSVOL over the network to retrieve policy-related information.

In environments with multiple Domain Controllers, SYSVOL content is replicated between the DCs so that clients can retrieve consistent domain policy information.

---

### Updating Group Policies

Group Policies are refreshed automatically at regular intervals.

When testing or deploying a new configuration, administrators may not want to wait for the normal refresh cycle.

A computer can be instructed to immediately refresh its Group Policy configuration with:

    gpupdate /force

This forces Windows to reprocess applicable Group Policies.

It is particularly useful when:

- Testing a new GPO
- Troubleshooting policy application
- Verifying administrative changes
- Deploying configurations that need to take effect immediately

---

## Practical GPO Examples

### Restricting Control Panel Access

Suppose an organization wants regular employees to be unable to modify system settings through the Control Panel while allowing IT personnel to retain access.

A GPO could contain a **User Configuration** policy that prohibits access to:

    Control Panel
    PC Settings

The GPO could then be linked only to OUs containing users who should receive the restriction.

For example:

    THM
    |
    +-- IT ---------------------- No restriction
    |
    +-- Marketing --+
    |               |
    +-- Management -+--> Restrict Control Panel Access GPO
    |               |
    +-- Sales -------+

Because the restriction applies to the **user's environment**, it belongs under User Configuration.

---

### Automatically Locking Computers

Another useful security policy is automatically locking computers after a period of inactivity.

For example:

    Inactivity Limit: 5 minutes

Because this configuration controls the behavior of the computer, it can be implemented through **Computer Configuration**.

If the policy should apply to all domain computers, the GPO can be linked higher in the domain hierarchy.

For example:

    thm.local
        |
        |  Auto Lock Screen GPO
        |
        +-- Workstations
        |
        +-- Servers
        |
        +-- Domain Controllers

The child OUs inherit the policy from the domain.

This is more efficient than separately linking the same GPO to every computer OU when the desired configuration is identical across all of them.

---

### Why GPOs Matter for Security

GPOs provide a centralized mechanism for establishing a **security baseline** across an Active Directory environment.

Instead of relying on users or administrators to configure each machine correctly, security requirements can be centrally defined and enforced.

For example:

    Without GPO

    PC01 -> manually configured
    PC02 -> manually configured
    PC03 -> forgotten
    PC04 -> configured differently

    With GPO

                 Security GPO
                     |
             +-------+-------+
             |       |       |
            PC01    PC02    PC03
             |       |       |
             +-- consistent configuration

This reduces configuration inconsistencies and makes security policies easier to manage at scale.

---

### Key Concepts

| Concept | Description |
|---|---|
| **GPO** | Collection of configuration and security settings |
| **GPO Link** | Associates a GPO with a domain, site, or OU |
| **Inheritance** | Allows child OUs to receive policies linked higher in the hierarchy |
| **Security Filtering** | Controls which users or computers can apply a GPO |
| **Computer Configuration** | Policies affecting computers |
| **User Configuration** | Policies affecting users |
| **SYSVOL** | Domain share containing files required for Group Policy and other domain functions |
| **gpupdate /force** | Forces the local system to refresh and reprocess Group Policy |


In short:

    OU  -> organizes objects
    GPO -> defines configurations
    Link -> defines where the GPO applies


# Authentication Methods

In a Windows domain, authentication is centralized through the **Domain Controllers (DCs)**.

When a user attempts to access a network service using domain credentials, the environment needs to verify the user's identity before granting access.

Two important authentication protocols found in Windows domains are:

- **Kerberos** — the default authentication protocol in modern Active Directory environments.
- **NTLM / NetNTLM** — an older authentication mechanism still supported mainly for compatibility.

---

## Kerberos Authentication

**Kerberos** is the primary authentication protocol used by modern Windows domains.

Instead of repeatedly sending credentials whenever a user accesses a network service, Kerberos uses a **ticket-based authentication system**.

A ticket acts as proof that an identity has already been authenticated.

The main components involved are:

| Component | Description |
|---|---|
| **KDC** | Key Distribution Center responsible for issuing Kerberos tickets |
| **TGT** | Ticket Granting Ticket used to request additional tickets |
| **TGS / Service Ticket** | Ticket used to authenticate to a specific service |
| **SPN** | Service Principal Name identifying a particular service instance |
| **krbtgt** | Special domain account whose key is used to protect TGTs |

In an Active Directory environment, the **KDC runs on the Domain Controllers**.

---

### Step 1 — Obtaining a Ticket Granting Ticket (TGT)

The first stage occurs when the user authenticates and requests a **Ticket Granting Ticket (TGT)** from the KDC.

Simplified:

    User
      |
      | Authentication request
      v
     KDC
      |
      | TGT + Session Key
      v
    User

The client proves knowledge of credentials to the KDC without simply transmitting the plaintext password.

If authentication succeeds, the KDC provides:

- A **Ticket Granting Ticket (TGT)**
- A **session key**

The TGT is protected using a key associated with the special:

    krbtgt

domain account.

The client cannot simply modify the contents of the TGT.

The important purpose of the TGT is that the user can now request access to additional domain services **without providing their password again for every service**.

Conceptually:

    Credentials
        |
        v
       KDC
        |
        v
       TGT
        |
        +----------------+
        |                |
        v                v
    File Server      Web Service
       ticket           ticket

The TGT is therefore sometimes informally described as a:

    "ticket used to request other tickets"

---

### Step 2 — Requesting a Service Ticket

Suppose the authenticated user now wants to access a network service such as:

- A file share
- A web application
- A Database
- Another network service

The client presents its TGT to the KDC and requests a ticket for that particular service.

    User
      |
      | TGT
      | Service request
      | SPN
      v
     KDC
      |
      | Service Ticket
      | Service Session Key
      v
    User

The requested service is identified using a **Service Principal Name (SPN)**.

An SPN identifies a specific service instance associated with an account in Active Directory.

The KDC then issues a **service ticket** for that service.

The service ticket is protected using a key associated with the account under which the target service operates.

This means that the intended service can validate the ticket.

---

### Step 3 — Authenticating to the Service

The client can now present the service ticket to the target service.

    User
      |
      | Service Ticket
      v
    Service
      |
      | Validate ticket
      v
    Access Granted

The service validates the ticket using credentials associated with its service account.

If the ticket is valid, the client can establish an authenticated session with the service.

The user's password does not need to be sent to that service.

---

### Kerberos Authentication Flow

The complete process can be simplified as:

    1. User -> KDC
       "I want to authenticate"

    2. KDC -> User
       TGT

    3. User -> KDC
       "Here is my TGT.
        I want to access SERVICE-X"

    4. KDC -> User
       Service Ticket for SERVICE-X

    5. User -> SERVICE-X
       "Here is my Service Ticket"

    6. SERVICE-X
       Validates ticket and grants access

Or even more simply:

    Credentials
        |
        v
       TGT
        |
        v
    Service Ticket
        |
        v
      Service

---

## Important Kerberos Components

### Key Distribution Center (KDC)

The **Key Distribution Center** is responsible for issuing Kerberos tickets.

In Active Directory environments, this service is provided by Domain Controllers.

Its responsibilities include issuing:

- Ticket Granting Tickets
- Service Tickets

---

### Ticket Granting Ticket (TGT)

A **TGT** proves that the user has successfully authenticated to the Kerberos infrastructure.

It is not normally used directly to access an application or network service.

Instead, it is used to request **service tickets**.

    TGT -> Request more tickets

---

### Service Ticket

A **service ticket** allows the client to authenticate to a particular service.

Unlike a TGT, it is intended for a specific service.

For example:

    TGT
     |
     +-- Service Ticket -> File Server
     |
     +-- Service Ticket -> Web Server
     |
     +-- Service Ticket -> Database

---

### Service Principal Name (SPN)

A **Service Principal Name (SPN)** identifies a service instance in the Kerberos environment.

When requesting a service ticket, the client tells the KDC which service it wants to access by specifying its SPN.

Conceptually:

    SPN -> "Which service do I want a ticket for?"

---

## NTLM / NetNTLM Authentication

**NTLM** is an older Windows authentication technology.

For network authentication, NTLM uses a **challenge-response mechanism** rather than the ticket-based approach used by Kerberos.

It remains available in many Windows environments for compatibility with systems or situations where Kerberos cannot be used.

---

### NTLM Challenge-Response

A simplified domain authentication process works as follows.

#### Step 1 — Authentication Request

The client requests access to a server:

    Client
      |
      | Authentication request
      v
    Server

---

#### Step 2 — Challenge

The server generates a challenge and sends it to the client:

    Client
      ^
      |
      | Random Challenge
      |
    Server

---

#### Step 3 — Response

The client uses information derived from its credentials together with the challenge to calculate a response.

    Challenge
        +
    Credential-derived secret
        |
        v
    Challenge Response

The response is sent to the server.

The plaintext password itself is **not sent across the network**.

---

#### Step 4 — Domain Controller Verification

When domain credentials are being used, the server can ask the Domain Controller to validate the authentication attempt.

    Client
      |
      | Response
      v
    Server
      |
      | Challenge + Response
      v
    Domain Controller

The Domain Controller has the necessary account information to determine whether the response is valid.

---

#### Step 5 — Authentication Result

The Domain Controller verifies the response and returns the authentication result.

    Domain Controller
          |
          | Valid / Invalid
          v
        Server
          |
          v
        Client

If the expected response matches the client's response, authentication succeeds.

Otherwise, access is denied.

---

### NTLM Authentication Flow

The entire process can be summarized as:

    Client                Server                 DC
      |                     |                    |
      | Authentication ---> |                    |
      |                     |                    |
      | <--- Challenge ---- |                    |
      |                     |                    |
      | Response ---------> |                    |
      |                     | Challenge +        |
      |                     | Response --------->|
      |                     |                    |
      |                     |<--- Verification --|
      |                     |                    |
      |<-- Authentication --|                    |
      |      Result         |                    |

The important concept is:

    Password -> Never directly transmitted

Instead, the client proves knowledge of the necessary credential material by generating a response to the server's challenge.

---

### Domain Accounts vs Local Accounts

When authenticating with a **domain account**, the server may rely on a Domain Controller to validate the NTLM authentication.

    Domain Account
          |
          v
    Domain Controller
        validates

With a **local account**, the target Windows machine can validate the authentication itself because local account credential information is stored locally in the **Security Account Manager (SAM)**.

    Local Account
          |
          v
       Local SAM
        validates

---

## Kerberos vs NTLM

| | Kerberos | NTLM |
|---|---|---|
| **Authentication model** | Ticket-based | Challenge-response |
| **Status** | Default in modern AD | Legacy / compatibility |
| **Domain infrastructure** | Uses KDC | Domain authentication can involve DC validation |
| **Service authentication** | Uses service tickets | Uses challenge-response |
| **Password sent over network** | No | No |
| **Modern AD preference** | Preferred | Used when required or as fallback |

### Mental Model

A useful way to distinguish them is:

    Kerberos
       |
       v
    "Prove that you authenticated
     by presenting a valid ticket."

    NTLM
       |
       v
    "Prove that you know the credential
     by correctly responding to a challenge."

For Kerberos:

    Authenticate once
          |
          v
         TGT
          |
          v
    Request Service Ticket
          |
          v
    Access Service

For NTLM:

    Request Access
          |
          v
       Challenge
          |
          v
       Response
          |
          v
      Verification

# Trees, Forests and Trusts

A single Active Directory domain can be enough for a small or medium-sized organization.

However, as an organization grows, it may become useful to divide the environment into multiple domains to improve administration, delegation, policy management, and organizational separation.

---

## Trees

An **Active Directory Tree** is a collection of related domains that share a **contiguous DNS namespace**.

For example:

    thm.local
       |
       +-- uk.thm.local
       |
       +-- us.thm.local

In this example:

- `thm.local` is the root domain.
- `uk.thm.local` is a child domain.
- `us.thm.local` is another child domain.

Each child domain has its own Active Directory database, users, computers, policies, and Domain Controllers.

This allows different parts of an organization to be managed independently while still belonging to the same logical structure.

---

### Why Use Multiple Domains?

Multiple domains can help when different parts of an organization have distinct administrative or regulatory requirements.

For example:

    thm.local
       |
       +-- uk.thm.local
       |      |
       |      +-- UK users
       |      +-- UK computers
       |      +-- UK policies
       |
       +-- us.thm.local
              |
              +-- US users
              +-- US computers
              +-- US policies

The IT team responsible for the UK domain can manage UK resources without automatically having administrative control over the US domain.

This provides stronger administrative separation than simply creating additional OUs inside a single domain.

---

## Domain Admins and Enterprise Admins

Each domain has its own **Domain Admins** group.

Members of Domain Admins have administrative privileges within their respective domain.

For example:

    UK Domain Admin
        |
        v
    uk.thm.local

    US Domain Admin
        |
        v
    us.thm.local

An additional privileged group exists at the forest level:

    Enterprise Admins

**Enterprise Admins** have administrative capabilities across the domains in the forest.

A simplified comparison is:

| Group | Administrative Scope |
|---|---|
| **Domain Admins** | A specific domain |
| **Enterprise Admins** | Entire forest |

Because Enterprise Admins have extremely broad privileges, membership in this group should be highly restricted.

---

## Forests

An **Active Directory Forest** is the highest-level logical structure in Active Directory.

A forest can contain one or more **domain trees**, including trees that use different DNS namespaces.

For example, suppose two companies have their own Active Directory environments:

    thm.local
       |
       +-- uk.thm.local
       +-- us.thm.local

and:

    mht.local
       |
       +-- eu.mht.local
       +-- asia.mht.local

If these domain trees become part of the same Active Directory forest, the structure could conceptually look like:

    Forest
      |
      +-- thm.local
      |      |
      |      +-- uk.thm.local
      |      +-- us.thm.local
      |
      +-- mht.local
             |
             +-- eu.mht.local
             +-- asia.mht.local

The trees do not need to share the same DNS namespace.

Therefore:

    Tree
      -> Domains sharing a related namespace

    Forest
      -> One or more domain trees

---

## Trust Relationships

Multiple domains often need to allow users from one domain to access resources located in another domain.

This is made possible through **trust relationships**.

A trust relationship establishes a mechanism that allows authentication information from one domain to be accepted by another domain.

However, a trust does **not automatically grant access to resources**.

It only makes cross-domain authorization possible.

Actual access still depends on permissions assigned to users and groups.

---

### One-Way Trust

In a **one-way trust**, one domain trusts another domain.

If:

    Domain AAA trusts Domain BBB

then identities from:

    Domain BBB

can potentially be authorized to access resources in:

    Domain AAA

The important detail is that the **trust direction is opposite to the access direction**.

Conceptually:

    AAA ---- trusts ----> BBB

    AAA <--- access ----- BBB users

In other words:

    AAA trusts BBB
           |
           v
    AAA accepts authentication
    originating from BBB

This allows administrators in AAA to assign permissions on AAA resources to identities from BBB.

---

### Two-Way Trust

A **two-way trust** means that both domains trust each other.

For example:

    Domain AAA <------> Domain BBB

This allows:

    AAA users -> potentially access BBB resources

and:

    BBB users -> potentially access AAA resources

provided that the appropriate permissions are explicitly granted.

Domains created within the same Active Directory tree or forest typically have automatic trust relationships between them.

---

## Trust Does Not Mean Access

A trust relationship should not be interpreted as:

    "Everyone in the other domain can access everything."

Instead, it means:

    "Users from the trusted domain can now be considered
     when assigning permissions."

For example:

    THM UK User
          |
          | authentication accepted through trust
          v
    MHT EU Domain
          |
          | permission check
          v
    Shared Folder
          |
          +-- Allowed -> access granted
          |
          +-- Not Allowed -> access denied

Authentication and authorization are therefore still separate concepts.

The trust enables the cross-domain authentication relationship, while permissions determine what the authenticated identity can actually access.

---

## Active Directory Hierarchy

A simplified hierarchy can be represented as:

    Forest
      |
      +-- Tree
      |    |
      |    +-- Domain
      |    |
      |    +-- Child Domain
      |
      +-- Tree
           |
           +-- Domain
           |
           +-- Child Domain

Within each domain:

    Domain
      |
      +-- OUs
      |    |
      |    +-- Users
      |    +-- Computers
      |
      +-- Groups
      +-- GPOs
      +-- Domain Controllers

Trust relationships allow identities from different domains to interact across these boundaries when appropriate.

---

## Tree vs Forest vs Trust

| Concept | Purpose |
|---|---|
| **Domain** | Administrative and authentication boundary containing AD objects |
| **Tree** | Collection of domains sharing a contiguous DNS namespace |
| **Forest** | Collection of one or more domain trees |
| **Trust** | Relationship allowing identities from one domain to be recognized by another |

### Mental Model

A useful way to remember these concepts is:

    Domain
       |
       | Multiple related domains
       v
     Tree
       |
       | Multiple trees
       v
    Forest

While:

    Trust
      |
      v
    Connects authentication relationships
    between domains

For example:

    Forest
    |
    +-- thm.local
    |     |
    |     +-- uk.thm.local
    |     +-- us.thm.local
    |
    +-- mht.local
          |
          +-- eu.mht.local
          +-- asia.mht.local

Trust relationships allow users from these domains to be authorized to access resources across domain boundaries.