
# All About Cmdlets and Modules

---

## Cmdlets

A **cmdlet** is a PowerShell command designed to perform a specific operation on objects.

Unlike ordinary PowerShell functions, traditional compiled cmdlets are written in languages such as C# and compiled for execution within PowerShell.

Cmdlets generally follow the **Verb-Noun** naming convention:

```powershell
Test-WSMan
Get-Process
Get-ChildItem
Import-Module
```

For example, `Test-WSMan` uses `Test` as its verb and `WSMan` as its noun.

We can discover available cmdlets, functions, aliases, and applications using:

```powershell
Get-Command
```

To retrieve documentation for a particular cmdlet, we can use:

```powershell
Get-Help Test-WSMan
```

The `Get-Member` cmdlet serves a different purpose: it allows us to inspect the properties and methods of objects returned by PowerShell commands.

---

## PowerShell Modules

A **PowerShell module** is a reusable collection of PowerShell functionality that can be distributed, installed, and imported into a session.

Modules make PowerShell extensible by allowing us to introduce additional commands and capabilities without modifying the PowerShell installation itself.

A module may contain:

- Cmdlets
- PowerShell functions
- Scripts
- Compiled assemblies
- Module manifests and help files

### PowerSploit

The HTB module uses **PowerSploit** as an example of a PowerShell module collection.

PowerSploit contains tools developed for penetration testing Windows environments, including Active Directory enumeration and privilege escalation assessments.

One of its components is PowerView, which provides functions for gathering information about Active Directory environments.

The project was already archived when the HTB material was written, but it remains a useful example for understanding how PowerShell modules are structured.

### Module File Types

Two important file extensions are `.psd1` and `.psm1`.

| Extension | Purpose |
|---|---|
| `.psd1` | Module manifest containing metadata and configuration. |
| `.psm1` | Script module containing PowerShell code. |

#### Module Manifest (.psd1)

A module manifest describes a PowerShell module and its configuration.

It may contain:

- Module name and version.
- GUID and author information.
- PowerShell compatibility requirements.
- Required modules and assemblies.
- Exported functions and cmdlets.
- Additional metadata.

For example, `PowerSploit.psd1` describes the PowerSploit module.

#### Script Module (.psm1)

A `.psm1` file contains PowerShell code that defines or imports the module's functionality.

The HTB material presents the following example from PowerSploit:

```powershell
Get-ChildItem $PSScriptRoot |
    ? { $_.PSIsContainer -and !('Tests','docs' -contains $_.Name) } |
    % { Import-Module $_.FullName -DisableNameChecking }
```

This command performs three main operations:

1. `Get-ChildItem` retrieves the contents of the module's directory.
2. `Where-Object`, represented by `?`, filters the results to include directories other than `Tests` and `docs`.
3. `ForEach-Object`, represented by `%`, imports each remaining module.

`$PSScriptRoot` is an automatic variable containing the directory of the currently executing script.

---

## Working with PowerShell Modules

### Listing Loaded Modules

We can use `Get-Module` to identify which modules are currently loaded into our PowerShell session.

```powershell
Get-Module
```

The output includes information such as the module name, version, type, and exported commands.

### Listing Available Modules

To identify modules installed in locations available to PowerShell, including modules that have not yet been imported, we can use:

```powershell
Get-Module -ListAvailable
```

This distinction is important:

| Command | Description |
|---|---|
| `Get-Module` | Displays modules loaded into the current session. |
| `Get-Module -ListAvailable` | Displays modules discoverable in the configured module paths. |

### Importing Modules

The `Import-Module` cmdlet loads a module into our current PowerShell session.

For example:

```powershell
Import-Module .\PowerSploit.psd1
```

After successfully importing PowerSploit, we can access its exported functions.

The HTB material demonstrates this by executing:

```powershell
Get-NetLocalgroup
```

This PowerView function enumerates local groups on a Windows host.

Before importing the module, PowerShell may not recognize the function because it is not available in our current session.

### PSModulePath

PowerShell uses the `PSModulePath` environment variable to identify directories in which it searches for modules.

We can inspect it using:

```powershell
$env:PSModulePath
```

Typical Windows module locations include:

```text
C:\Users\<user>\Documents\WindowsPowerShell\Modules

C:\Program Files\WindowsPowerShell\Modules

C:\Windows\System32\WindowsPowerShell\v1.0\Modules
```

Modules installed in these directories can generally be discovered by PowerShell without explicitly specifying their complete paths.

Modules stored elsewhere may need to be imported using their complete or relative file paths.

---

## PowerShell Execution Policy

PowerShell provides an execution policy mechanism that controls the conditions under which scripts and certain configuration files can run.

The execution policy is intended to help prevent accidental execution of untrusted scripts. **It is not a security boundary.**

### Checking the Execution Policy

We can inspect the effective execution policy using:

```powershell
Get-ExecutionPolicy
```

For example:

```text
Restricted
```

The `Restricted` policy prevents PowerShell script files from executing.

To inspect the policies configured at every available scope, we can use:

```powershell
Get-ExecutionPolicy -List
```

### Execution Policy Scopes

PowerShell supports multiple execution policy scopes.

| Scope | Description |
|---|---|
| MachinePolicy | Policy configured through Group Policy for the computer. |
| UserPolicy | Policy configured through Group Policy for the user. |
| Process | Policy applied to the current PowerShell process. |
| CurrentUser | Policy configured for the current user. |
| LocalMachine | Policy configured for the local computer. |

These scopes have different precedence levels.

### Changing the Execution Policy

The `Set-ExecutionPolicy` cmdlet allows us to configure an execution policy when permitted by the system's higher-precedence policies.

The HTB material demonstrates changing the policy to allow a module to be imported.

For an isolated, authorized laboratory, we can apply a temporary policy to the current PowerShell process:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

This change lasts only for the current process and does not persist after the PowerShell session terminates.

We can verify the configuration using:

```powershell
Get-ExecutionPolicy -List
```

**Important:** An execution policy configured through Group Policy takes precedence over a process-level setting. We should restore any persistent configuration changes made during an assessment.

---

## Discovering Commands from Imported Modules

After importing a module, we may want to identify which functions and cmdlets it provides.

We can use `Get-Command` with the `-Module` parameter.

For example:

```powershell
Get-Command -Module PowerSploit
```

The HTB example includes functions such as:

```text
Find-InterestingFile
Find-LocalAdminAccess
Find-PathDLLHijack
Find-ProcessDLLHijack
Get-GPPPassword
```

These functions extend the commands available in our PowerShell session.

We can inspect the available documentation for individual commands using `Get-Help`, when documentation is provided.

---

## Finding and Installing Modules

PowerShell modules can be distributed through repositories such as PowerShell Gallery and GitHub.

### PowerShell Gallery

[PowerShell Gallery](https://www.powershellgallery.com/) is an online repository containing PowerShell scripts, modules, and other reusable resources.

The HTB material introduces the `PowerShellGet` module for interacting with this repository.

Common commands include:

| Command | Description |
|---|---|
| `Find-Module` | Searches for modules in registered repositories. |
| `Install-Module` | Installs a module. |
| `Get-InstalledModule` | Lists modules installed through PowerShellGet. |
| `Update-Module` | Updates an installed module. |
| `Uninstall-Module` | Uninstalls a module. |
| `Save-Module` | Downloads a module without installing it. |

### Finding Modules

We can search for a particular module using:

```powershell
Find-Module -Name AdminToolbox
```

AdminToolbox is a collection of modules designed to assist with Windows administration, Active Directory, Exchange, networking, and other administrative tasks.

### Installing Modules

Once we identify the module we want, we can install it using:

```powershell
Install-Module -Name AdminToolbox
```

Alternatively, we can combine module discovery and installation using a pipeline:

```powershell
Find-Module -Name AdminToolbox | Install-Module
```

This demonstrates PowerShell's object-based pipeline: the module information returned by `Find-Module` is passed directly to `Install-Module`.

Installation permissions depend on the installation scope. PowerShellGet also supports installing modules for the current user.

Modern PowerShell versions can automatically import installed modules when we execute one of their exported commands.

### GitHub Modules

PowerShell modules and scripts can also be distributed through GitHub.

Unlike modules installed in standard module directories, custom modules downloaded from GitHub may need to be imported using their file paths.

Before importing third-party modules, we should review their source code, dependencies, and origin.

---

## Tools to Be Aware Of

The HTB material introduces several PowerShell-related projects used for Windows administration and security assessments.

| Tool | Purpose |
|---|---|
| AdminToolbox | Provides administrative tools for Active Directory, Exchange, networking, and other Windows components. |
| ActiveDirectory | Provides PowerShell cmdlets for managing Active Directory users, groups, computers, and other directory objects. |
| Empire | A post-exploitation framework that includes situational awareness capabilities. |
| Inveigh | Supports network spoofing and man-in-the-middle security assessments. |
| BloodHound / SharpHound | Collects and analyzes Active Directory relationships to help identify attack paths. |
| PowerSploit / PowerView | Provides security assessment and Active Directory enumeration functionality. |

These tools illustrate how importing additional modules and scripts extends PowerShell beyond its built-in administrative capabilities.

---

## Key Takeaways

- Cmdlets are individual commands designed to manipulate PowerShell objects.
- Modules organize reusable cmdlets, functions, scripts, and related resources.
- `.psd1` files describe modules, while `.psm1` files contain PowerShell script-module code.
- `Get-Module` lists loaded modules, and `Get-Module -ListAvailable` identifies available modules.
- `Import-Module` loads module functionality into our current session.
- `PSModulePath` defines the directories PowerShell searches for modules.
- Execution policies affect script execution but are not security boundaries.
- `Get-Command -Module` identifies commands exported by a module.
- PowerShell Gallery and GitHub provide additional modules that extend PowerShell's capabilities for administration and security assessments.
