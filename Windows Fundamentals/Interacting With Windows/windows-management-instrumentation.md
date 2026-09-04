# Windows Management Instrumentation (WMI)

**Windows Management Instrumentation (WMI)** is a Windows management framework that provides tools for monitoring, querying, and managing systems.

Its goal is to provide a common way to manage devices, applications, and system components across Windows environments.

WMI has been included with Windows since Windows 2000.

---

# WMI Components

WMI is made up of several components:

| Component              | Description                                                                                                           |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------- |
| **WMI Service**        | Runs automatically at boot and acts as an intermediary between providers, the repository, and management applications |
| **Managed Objects**    | Logical or physical components that can be managed through WMI                                                        |
| **WMI Providers**      | Collect and expose information about specific managed objects                                                         |
| **Classes**            | Structures used to represent and expose WMI data                                                                      |
| **Methods**            | Actions associated with classes, such as starting or stopping processes                                               |
| **WMI Repository**     | Database that stores WMI-related static information                                                                   |
| **CIM Object Manager** | Requests information from providers and returns it to the requesting application                                      |
| **WMI API**            | Allows applications to interact with WMI                                                                              |
| **WMI Consumer**       | Sends queries to WMI objects through the CIM Object Manager                                                           |

A useful simplified model is:

```text id="7ws1ae"
Application / PowerShell / WMIC
            ↓
      WMI Consumer
            ↓
     CIM Object Manager
            ↓
       WMI Providers
            ↓
      Managed Objects
```

---

# WMI Uses

WMI can be used for many administrative tasks, including:

* Retrieving information from local or remote systems
* Configuring security settings
* Managing users and groups
* Changing system properties
* Executing code
* Scheduling processes
* Configuring logging

This makes WMI useful both for system administration and cybersecurity work.

---

# WMIC

**WMIC**, or **Windows Management Instrumentation Command-Line**, provides command-line access to WMI.

It can be started interactively with:

```cmd id="csmdvy"
wmic
```

or used directly with commands.

For example, to retrieve the computer name:

```cmd id="3pqh80"
wmic computersystem get name
```

Help can be displayed with:

```cmd id="v9s6ci"
C:\htb> wmic /?
```

Output begins with:

```cmd id="8mk8xm"
WMIC is deprecated.

[global switches] <command>

The following global switches are available:
/NAMESPACE
/ROLE
/NODE
/IMPLEVEL
/AUTHLEVEL
/LOCALE
/PRIVILEGES
/TRACE
/RECORD
/INTERACTIVE
/FAILFAST
/USER
/PASSWORD
/OUTPUT
/APPEND
/AGGREGATE
/AUTHORITY
/?
```

The important point here is that **WMIC is deprecated**, but it is still useful to understand because it can be encountered on Windows systems and appears in older administrative and security workflows.

---

# Querying the Operating System with WMIC

We can retrieve operating system information with:

```cmd id="f1yzw9"
C:\htb> wmic os list brief
```

Output:

```cmd id="dsgd9v"
BuildNumber  Organization  RegisteredUser  SerialNumber             SystemDirectory      Version
19041                      Owner           00123-00123-00123-AAOEM  C:\Windows\system32  10.0.19041
```

The command uses:

```text id="oftm5p"
os      → WMI alias for operating system information
list    → display the data
brief   → display only the main properties
```

WMIC works with aliases together with verbs, adverbs, and switches.

---

# WMI with PowerShell

PowerShell can interact directly with WMI using:

```powershell id="0xrc4g"
Get-WmiObject
```

`Get-WmiObject` retrieves instances of WMI classes or information about available WMI classes.

It can query both:

* Local machines
* Remote machines

---

# WMI Classes

WMI organizes information into **classes**.

For example:

```text id="g4k6s2"
Win32_OperatingSystem
```

represents operating system information.

Other classes can represent:

* Processes
* Services
* Users
* Hardware
* Files
* Network configuration

A class defines the type of object and the information or actions associated with it.

---

# Querying Operating System Information

For example:

```powershell id="no2zmc"
PS C:\htb> Get-WmiObject -Class Win32_OperatingSystem |
    select SystemDirectory,BuildNumber,SerialNumber,Version |
    ft
```

Output:

```powershell id="ki03b4"
SystemDirectory     BuildNumber SerialNumber            Version
---------------     ----------- ------------            -------
C:\Windows\system32 19041       00123-00123-00123-AAOEM 10.0.19041
```

Here:

```text id="i4l4ai"
Get-WmiObject
        ↓
Query WMI

-Class Win32_OperatingSystem
        ↓
Select operating system objects

select ...
        ↓
Choose specific properties

ft
        ↓
Format the result as a table
```

---

# WMI Methods

WMI classes can also expose **methods**.

A method performs an action rather than simply returning information.

Examples may include:

* Starting a process
* Stopping a process
* Renaming a file
* Modifying system configuration

PowerShell provides:

```powershell id="24vi1f"
Invoke-WmiMethod
```

to invoke these methods.

---

# Example: Renaming a File

The following command invokes the `Rename` method of a WMI file object:

```powershell id="1kghfb"
PS C:\htb> Invoke-WmiMethod `
    -Path "CIM_DataFile.Name='C:\users\public\spns.csv'" `
    -Name Rename `
    -ArgumentList "C:\Users\Public\kerberoasted_users.csv"
```

Output:

```powershell id="wjdymf"
__GENUS          : 2
__CLASS          : __PARAMETERS
__SUPERCLASS     :
__DYNASTY        : __PARAMETERS
__RELPATH        :
__PROPERTY_COUNT : 1
__DERIVATION     : {}
__SERVER         :
__NAMESPACE      :
__PATH           :
ReturnValue      : 0
PSComputerName   :
```

The important field is:

```text id="sp2k1q"
ReturnValue : 0
```

A value of `0` indicates that the method completed successfully.

---

# Querying vs. Performing Actions

A useful distinction is:

```text id="go9f08"
Get-WmiObject
    ↓
Retrieve information

Invoke-WmiMethod
    ↓
Perform an action
```

For example:

```powershell id="na5iv0"
Get-WmiObject -Class Win32_OperatingSystem
```

asks WMI for information.

While:

```powershell id="ul2jbx"
Invoke-WmiMethod ...
```

asks WMI to execute a method associated with an object.

---

# WMI in Cybersecurity

WMI has legitimate administrative uses, but its ability to manage remote systems and execute actions also makes it relevant to cybersecurity.

For **blue teams**, WMI activity may be useful when:

* Monitoring administrative actions
* Investigating process creation
* Detecting suspicious remote management
* Examining system configuration

For **red teams**, WMI can be used for tasks such as:

* Enumeration
* Remote system interaction
* Code execution
* Lateral movement

Later modules explore these offensive uses in more depth.

---

# WMI, WMIC, and PowerShell

The three concepts are related but different:

| Component      | Purpose                                                                            |
| -------------- | ---------------------------------------------------------------------------------- |
| **WMI**        | The Windows management infrastructure itself                                       |
| **WMIC**       | Legacy command-line interface for interacting with WMI                             |
| **PowerShell** | Can interact with WMI using cmdlets such as `Get-WmiObject` and `Invoke-WmiMethod` |

A simple way to remember this is:

```text id="hs3c4r"
WMI
↓
Management infrastructure

WMIC
↓
Old command-line interface to WMI

PowerShell
↓
Modern way to interact with WMI
```

---

# Quick Reference

### Open WMIC

```cmd id="gp88bx"
wmic
```

### WMIC Help

```cmd id="1nztly"
wmic /?
```

### Get Hostname

```cmd id="1q4u0d"
wmic computersystem get name
```

### Get Operating System Information

```cmd id="aljjvn"
wmic os list brief
```

### Query WMI with PowerShell

```powershell id="s85dtv"
Get-WmiObject -Class Win32_OperatingSystem
```

### Select Specific Properties

```powershell id="8dey9g"
Get-WmiObject -Class Win32_OperatingSystem |
    select SystemDirectory,BuildNumber,SerialNumber,Version |
    ft
```

### Invoke a WMI Method

```powershell id="33ac24"
Invoke-WmiMethod -Path <object> -Name <method> -ArgumentList <arguments>
```

---

## Key Takeaway

**WMI is a Windows management framework used to query and control operating system components locally or remotely. It organizes manageable resources into classes, properties, and methods. WMIC provides a legacy command-line interface to WMI, while PowerShell can interact with it using commands such as `Get-WmiObject` and `Invoke-WmiMethod`. Because WMI can retrieve system information and execute actions, it is important for both Windows administration and cybersecurity operations.**
