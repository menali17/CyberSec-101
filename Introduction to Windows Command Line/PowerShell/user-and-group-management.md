
# User and Group Management

---

## Overview

Managing users and groups is an essential part of Windows system administration. User accounts determine who can access a system, while groups simplify the assignment of permissions to multiple users.

During penetration testing, enumerating users and groups helps us understand the environment, identify accounts with excessive privileges, and discover potential privilege escalation opportunities.

In this section, we will learn how to:

- Identify different types of Windows user accounts.
- Understand the difference between local and domain accounts.
- Enumerate and manage local users and groups with PowerShell.
- Install and use the Active Directory PowerShell module.
- Enumerate, create, and modify domain users.

---

## 1. Windows User Accounts

Windows user accounts allow individuals, applications, and system components to access resources according to their assigned permissions.

We commonly encounter four types of accounts:

| Account type | Description |
|---|---|
| Local users | Accounts created and managed on an individual Windows host. |
| Domain users | Accounts centrally managed through Active Directory. |
| Service accounts | Accounts used by applications and services to execute tasks. |
| Built-in accounts | Accounts created by Windows for predefined administrative or system purposes. |

### Built-in Accounts

Windows includes several predefined local accounts.

| Account | Purpose |
|---|---|
| `Administrator` | Built-in account for administering the local computer. |
| `DefaultAccount` | System-managed account used for certain Windows components. |
| `Guest` | Provides limited guest access and is disabled by default. |
| `WDAGUtilityAccount` | System-managed account associated with Windows Defender Application Guard. |

During enumeration, we should identify which accounts exist, whether they are enabled, and what privileges they possess.

---

## 2. Local Users vs. Domain Users

### Local Users

Local accounts are created and managed on an individual Windows computer.

Their identities and permissions are primarily associated with that host.

For example, a local account named `JLawrence` could be represented as:

```text
DESKTOP-01\JLawrence
```

### Domain Users

Domain accounts are centrally managed through **Active Directory (AD)**.

Active Directory is a directory service that provides centralized management of users, computers, groups, and other resources in Windows enterprise environments.

A domain account could be represented as:

```text
GREENHORN\MTanaka
```

Domain users can be granted access to resources across multiple domain-joined computers, depending on their permissions and applicable policies.

| Feature | Local user | Domain user |
|---|---|---|
| Account management | Individual Windows host | Active Directory |
| Authentication | Local computer | Domain infrastructure |
| Resource access | Primarily local resources | Authorized resources across the domain |
| Centralized administration | No | Yes |

### Why Active Directory Matters

Active Directory allows administrators to centrally manage:

- User and computer accounts.
- Security groups and permissions.
- Group Policies.
- Network resources.
- Relationships between domains.

For pentesters, understanding these relationships is important when investigating excessive permissions, potential privilege escalation paths, and lateral movement opportunities.

---

## 3. Understanding Windows Groups

Groups organize accounts and simplify permission management.

Instead of assigning permissions individually to every user, we can grant permissions to a group and add the appropriate users to it.

For example, members of the `Remote Desktop Users` group may be granted permission to establish Remote Desktop sessions, subject to other system policies.

### Enumerating Local Groups

We can list the local groups on a Windows host using:

```powershell
Get-LocalGroup
```

Common groups include:

| Group | Purpose |
|---|---|
| `Administrators` | Provides local administrative privileges. |
| `Users` | Contains accounts with standard user permissions. |
| `Guests` | Provides restricted guest privileges. |
| `Backup Operators` | Grants privileges related to backup and restoration. |
| `Remote Desktop Users` | Grants permissions associated with Remote Desktop access. |
| `Event Log Readers` | Allows members to read certain Windows event logs. |
| `Remote Management Users` | Grants access to supported remote management functionality. |

Group membership is especially important because an account's effective privileges may extend beyond its individual permissions.

---

## 4. Managing Local Users

PowerShell provides built-in cmdlets for enumerating, creating, and modifying local accounts.

### Enumerating Local Users

We can list local accounts using:

```powershell
Get-LocalUser
```

Example output:

```text
Name                Enabled  Description
----                -------  -----------
Administrator       False    Built-in administrator
DefaultAccount      False    System-managed account
JLawrence           True
Guest               False    Built-in guest account
WDAGUtilityAccount  False    System-managed account
```

The `Enabled` property indicates whether an account is currently enabled.

### Creating a Local User

We can create a local account using `New-LocalUser`.

The HTB example creates an account without a password:

```powershell
New-LocalUser -Name "JLawrence" -NoPassword
```

This creates a local account named `JLawrence`.

**Security consideration:** Accounts without passwords are insecure. Windows may also restrict their ability to log on, depending on local security policies.

### Modifying a Local User

We can modify existing accounts with `Set-LocalUser`.

For example, we can securely enter a new password:

```powershell
$Password = Read-Host -AsSecureString
```

Then assign the password and update the account description:

```powershell
Set-LocalUser -Name "JLawrence" `
    -Password $Password `
    -Description "CEO EagleFang"
```

`Read-Host -AsSecureString` prevents the entered password from appearing as ordinary plaintext in the terminal.

The backtick (`) is PowerShell's line-continuation character.

### Local User Commands

| Cmdlet | Purpose |
|---|---|
| `Get-LocalUser` | Enumerates local accounts. |
| `New-LocalUser` | Creates a local account. |
| `Set-LocalUser` | Modifies an existing local account. |

---

## 5. Managing Local Groups

After enumerating local users, we can investigate their group memberships.

### Listing Groups

```powershell
Get-LocalGroup
```

### Enumerating Group Members

To identify the members of a particular group, we can use `Get-LocalGroupMember`.

For example:

```powershell
Get-LocalGroupMember -Name "Users"
```

Example output:

```text
ObjectClass  Name                     PrincipalSource
-----------  ----                     ---------------
User         DESKTOP-01\JLawrence     Local
Group        NT AUTHORITY\INTERACTIVE Unknown
```

This allows us to identify accounts and other principals associated with the selected group.

### Adding Users to a Group

With appropriate administrative permissions, we can add an account to a local group.

For example:

```powershell
Add-LocalGroupMember `
    -Group "Remote Desktop Users" `
    -Member "JLawrence"
```

We can verify the membership afterward:

```powershell
Get-LocalGroupMember -Name "Remote Desktop Users"
```

### Local Group Commands

| Cmdlet | Purpose |
|---|---|
| `Get-LocalGroup` | Lists local groups. |
| `Get-LocalGroupMember` | Lists the members of a specified group. |
| `Add-LocalGroupMember` | Adds an account or group to a local group. |

**Pentesting relevance:** Enumerating local group memberships allows us to identify accounts with administrative privileges, remote access permissions, or other elevated capabilities.

---

## 6. Managing Domain Users and Groups

Local account management uses the built-in `LocalAccounts` commands.

Managing Active Directory accounts requires the **ActiveDirectory PowerShell module**, which provides cmdlets such as `Get-ADUser`, `New-ADUser`, and `Set-ADUser`.

### Installing the Active Directory Module

On supported Windows systems, we can install the official Active Directory management tools through Remote Server Administration Tools (RSAT).

The HTB module demonstrates installing all available RSAT capabilities:

```powershell
Get-WindowsCapability -Name RSAT* -Online |
    Add-WindowsCapability -Online
```

After installation, we can verify that the module is available:

```powershell
Get-Module -Name ActiveDirectory -ListAvailable
```

If necessary, we can explicitly import it:

```powershell
Import-Module ActiveDirectory
```

The official Active Directory module provides commands for administering and enumerating directory objects, subject to our account permissions and connectivity to the domain.

---

## 7. Enumerating Active Directory Users

### Get-ADUser

The `Get-ADUser` cmdlet retrieves information about Active Directory user accounts.

To enumerate all domain users within the command's search scope:

```powershell
Get-ADUser -Filter *
```

The wildcard `*` matches all users.

On large enterprise networks, this command can return a substantial amount of information.

### Querying a Specific User

We can retrieve a particular account using the `-Identity` parameter:

```powershell
Get-ADUser -Identity TSilver
```

The `-Identity` parameter supports identifiers such as:

- Distinguished Name (DN).
- GUID.
- Security Identifier (SID).
- `sAMAccountName`.

### Understanding AD User Attributes

Example output:

```text
DistinguishedName : CN=TSilver,CN=Users,DC=greenhorn,DC=corp
Enabled           : True
Name              : TSilver
ObjectClass       : user
ObjectGUID        : a19a6c8a-000a-4cbf-aa14-0c7fca643c37
SamAccountName    : TSilver
SID               : S-1-5-21-...
```

| Attribute | Description |
|---|---|
| `DistinguishedName` | Identifies the object's location within the directory. |
| `Enabled` | Indicates whether the account is enabled. |
| `ObjectClass` | Identifies the type of directory object. |
| `ObjectGUID` | Unique identifier assigned to the directory object. |
| `SamAccountName` | Account name commonly used for Windows domain authentication. |
| `SID` | Security identifier assigned to the account. |

### Filtering Users by Attributes

We can also search for accounts whose attributes match specific conditions.

For example:

```powershell
Get-ADUser -Filter {
    EmailAddress -like '*greenhorn.corp'
}
```

This searches for users whose email addresses match the specified pattern.

The `-like` operator performs wildcard-based comparisons.

This approach is useful when we want to locate particular users without enumerating every account.

---

## 8. Creating Active Directory Users

The `New-ADUser` cmdlet allows administrators to create domain accounts.

The HTB example creates an account for Mori Tanaka:

```powershell
New-ADUser `
    -Name "MTanaka" `
    -Surname "Tanaka" `
    -GivenName "Mori" `
    -Office "Security" `
    -OtherAttributes @{
        'title' = "Sensei"
        'mail' = "MTanaka@greenhorn.corp"
    } `
    -AccountPassword (Read-Host -AsSecureString "AccountPassword") `
    -Enabled $true
```

### Understanding the Parameters

| Parameter | Purpose |
|---|---|
| `-Name` | Specifies the new directory object's name. |
| `-Surname` | Specifies the user's surname. |
| `-GivenName` | Specifies the user's first name. |
| `-Office` | Specifies the user's office. |
| `-OtherAttributes` | Assigns additional directory attributes. |
| `-AccountPassword` | Specifies the account password. |
| `-Enabled $true` | Enables the account. |

For predictable account naming, administrators can also explicitly specify `-SamAccountName`.

### Verifying the Account

After creating an account, we can retrieve its attributes:

```powershell
Get-ADUser -Identity MTanaka -Properties * |
    Format-Table Name,Enabled,GivenName,Surname,Title,Office,Mail
```

This command demonstrates PowerShell's object-based pipeline.

`Get-ADUser` retrieves the account, and `Format-Table` displays the selected properties in a readable table.

---

## 9. Modifying Active Directory Users

The `Set-ADUser` cmdlet allows us to modify existing domain accounts.

For example, we can update an account's description:

```powershell
Set-ADUser -Identity MTanaka `
    -Description "Security Department"
```

We can verify the modification using:

```powershell
Get-ADUser -Identity MTanaka -Properties Description
```

PowerShell returns the account information, including its updated description.

### Local and Domain Account Commands

| Operation | Local account | Domain account |
|---|---|---|
| Enumerate users | `Get-LocalUser` | `Get-ADUser` |
| Create user | `New-LocalUser` | `New-ADUser` |
| Modify user | `Set-LocalUser` | `Set-ADUser` |
| Enumerate groups | `Get-LocalGroup` | `Get-ADGroup` |
| Enumerate group members | `Get-LocalGroupMember` | `Get-ADGroupMember` |
| Add group members | `Add-LocalGroupMember` | `Add-ADGroupMember` |

---

## 10. Why Enumerating Users and Groups Matters

User and group enumeration is a fundamental part of Windows penetration testing.

Accounts and groups may be misconfigured in ways that create security risks.

Examples include:

- Accounts with unnecessary administrative permissions.
- Users assigned to groups that grant excessive access.
- Accounts with weak or missing passwords.
- Unnecessary remote access permissions.
- Nested group memberships that grant unexpected privileges.

**Nested groups** are particularly important in Active Directory because users can inherit permissions indirectly through their group memberships.

Tools such as BloodHound can help visualize these relationships and identify potential privilege escalation paths.

---

## Key Takeaways

- Windows supports local, domain, service, and built-in accounts.
- Local accounts are managed on individual hosts, while domain accounts are centrally managed through Active Directory.
- Groups simplify access control by assigning permissions to multiple accounts.
- `Get-LocalUser` and `Get-LocalGroup` enumerate local accounts and groups.
- `Get-LocalGroupMember` reveals which accounts belong to a particular local group.
- The ActiveDirectory PowerShell module provides cmdlets for managing domain objects.
- `Get-ADUser -Filter *` retrieves domain users within the selected search scope.
- `New-ADUser` creates domain accounts, while `Set-ADUser` modifies existing accounts.
- Understanding user privileges and group memberships is essential for identifying misconfigurations, privilege escalation opportunities, and potential lateral movement paths.
