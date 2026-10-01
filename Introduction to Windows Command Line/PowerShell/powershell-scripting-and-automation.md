
# PowerShell Scripting and Automation

---

## Overview

PowerShell allows us to automate administrative tasks by combining commands, functions, and scripts into reusable tools.

Instead of manually executing the same commands every time we investigate a Windows host, we can create a PowerShell module that performs those checks automatically.

In this section, we will learn how to:

- Understand the differences between scripts and modules.
- Identify common PowerShell file extensions.
- Create a module and its manifest.
- Write functions and work with variables.
- Document our code using comments and built-in help.
- Control which functions a module exports.
- Understand PowerShell scopes.
- Import and execute our completed module.

The HTB exercise involves creating a module named **`quick-recon`** that collects basic information about a Windows computer.

---

## 1. Scripts vs. Modules

PowerShell supports both individual scripts and reusable modules.

### Scripts

A script is a text file containing PowerShell commands, variables, and functions.

Scripts generally use the `.ps1` extension.

For example, we can execute a script located in our current directory:

```powershell
.\script.ps1
```

PowerShell executes the instructions contained in that file.

### Modules

A module is a reusable package that can contain functions, scripts, configuration files, and other supporting resources.

Unlike a standalone script, we normally import a module into our PowerShell session so that its exported commands become available for repeated use.

```powershell
Import-Module .\quick-recon.psm1
```

After importing the module, we can call its exported functions directly:

```powershell
Get-Recon
```

### Common PowerShell File Extensions

| Extension | Description |
|---|---|
| `.ps1` | An executable PowerShell script. |
| `.psm1` | A PowerShell script module containing reusable functions and commands. |
| `.psd1` | A PowerShell data file, commonly used as a module manifest. |

A module does not necessarily require all three file types. A single `.psm1` file can function as a module, while a manifest and supporting files provide additional organization and configuration.

---

## 2. Building Our Quick-Recon Module

### HTB Scenario

We frequently perform the same preliminary checks when administering Windows hosts.

To automate these tasks, we will create a module named `quick-recon`.

Our module should collect four pieces of information:

1. The computer's hostname.
2. Its IP configuration.
3. Basic Active Directory domain information.
4. The contents of `C:\Users\`.

The resulting information will be written to a file named `recon.txt` on the current user's Desktop.

### Module Structure

We will organize our module using the following structure:

```text
quick-recon/
├── quick-recon.psd1
└── quick-recon.psm1
```

The `.psd1` file will describe the module and its configuration.

The `.psm1` file will contain our PowerShell code, including the `Get-Recon` function.

---

## 3. Creating the Module Directory

PowerShell uses the `$env:PSModulePath` environment variable to identify directories in which it searches for modules.

We can inspect these locations using:

```powershell
$env:PSModulePath
```

For the HTB example, we create our module in the current user's Windows PowerShell module directory:

```powershell
New-Item `
    -Path "$HOME\Documents\WindowsPowerShell\Modules\quick-recon" `
    -ItemType Directory
```

Alternatively, after navigating to the appropriate parent directory:

```powershell
mkdir quick-recon
```

Placing a module in a recognized module directory allows PowerShell to discover it without requiring its complete path every time.

---

## 4. Creating a Module Manifest

A module manifest is a `.psd1` file that contains metadata and configuration information about a PowerShell module.

It uses a PowerShell hash table consisting of key-value pairs.

### What Does a Manifest Contain?

A manifest can define:

| Category | Examples |
|---|---|
| Metadata | Module name, version, author, and description. |
| Prerequisites | Required PowerShell version and dependent modules. |
| Processing directives | Root module and supporting files. |
| Export restrictions | Functions, cmdlets, variables, and aliases available to users. |

### New-ModuleManifest

We can generate a manifest using the `New-ModuleManifest` cmdlet:

```powershell
New-ModuleManifest `
    -Path "$HOME\Documents\WindowsPowerShell\Modules\quick-recon\quick-recon.psd1" `
    -PassThru
```

The `-Path` parameter specifies where the manifest will be created.

The `-PassThru` parameter displays the generated manifest content in our console.

### Example Manifest

A simplified version of our manifest could look like this:

```powershell
@{
    RootModule        = 'quick-recon.psm1'
    ModuleVersion     = '1.0'
    GUID              = '0a062bb1-8a1b-4bdb-86ed-5adbe1071d2f'
    Author            = 'MTanaka'
    CompanyName       = 'Greenhorn.Corp.'
    Description       = 'Performs basic host reconnaissance.'

    FunctionsToExport = @('Get-Recon')
    CmdletsToExport   = @()
    VariablesToExport = @()
    AliasesToExport   = @()
}
```

Several properties are especially important:

- `RootModule` identifies the main module file.
- `ModuleVersion` specifies the module's version.
- `GUID` provides a unique module identifier.
- `FunctionsToExport` identifies the functions available when we import the module.

**Important:** If we set `FunctionsToExport = @()`, the manifest does not export any functions. We therefore need to include `Get-Recon` in this array for our completed module.

---

## 5. Creating the Module File

The `.psm1` file will contain our actual PowerShell instructions.

We can create it using `New-Item`:

```powershell
New-Item `
    -Path "$HOME\Documents\WindowsPowerShell\Modules\quick-recon\quick-recon.psm1" `
    -ItemType File
```

Now we can start writing our module.

---

## 6. Importing Dependencies

Our module will collect information about the computer's Active Directory domain.

To accomplish this, the HTB exercise uses the `Get-ADDomain` cmdlet from the ActiveDirectory module.

We therefore begin our script with:

```powershell
Import-Module ActiveDirectory
```

This loads the dependency before we attempt to execute its commands.

The ActiveDirectory module must already be installed and available on the computer for this operation to succeed.

---

## 7. Working with Variables

PowerShell variables store values and objects that we can reuse throughout our scripts.

A variable begins with the `$` character, and we use `=` to assign its value.

For example:

```powershell
$Hostname = $env:ComputerName
```

Here, `$env:ComputerName` retrieves the computer's hostname from an environment variable.

The HTB module uses four variables to collect its information:

```powershell
$Hostname = $env:ComputerName
$IP = ipconfig
$Domain = Get-ADDomain
$Users = Get-ChildItem C:\Users\
```

| Variable | Command | Information collected |
|---|---|---|
| `$Hostname` | `$env:ComputerName` | Computer hostname. |
| `$IP` | `ipconfig` | Network configuration. |
| `$Domain` | `Get-ADDomain` | Active Directory domain information. |
| `$Users` | `Get-ChildItem C:\Users\` | User profile directories. |

When we assign a command's output to a variable, we can use that information later without executing the command again.

### A Note About C:\Users

Listing `C:\Users\` helps us identify local user profile directories.

However, the existence of a directory does not necessarily prove that the corresponding user has logged in recently or that the account still exists.

---

## 8. Creating Our First Function

A function is a named block of PowerShell instructions that we can execute whenever necessary.

The basic syntax is:

```powershell
function Function-Name {
    # Commands
}
```

For our module, we will create a function called `Get-Recon`.

```powershell
function Get-Recon {

    $Hostname = $env:ComputerName

    $IP = ipconfig

    $Domain = Get-ADDomain

    $Users = Get-ChildItem C:\Users\

}
```

Every time we call `Get-Recon`, PowerShell executes the instructions inside its script block.

This eliminates the need to manually enter the same four commands during every administrative session.

---

## 9. Writing Our Results to a File

Collecting the information is only the first part of the exercise.

We also need to save the results in a readable format.

The HTB material uses `New-Item` to create a file and `Add-Content` to populate it.

### Creating the Output File

```powershell
New-Item ~\Desktop\recon.txt -ItemType File
```

The `~` symbol represents the current user's home directory.

In the HTB environment, the resulting file is created on the user's Desktop.

### Formatting the Output

We can combine the collected information with descriptive headings:

```powershell
$Vars = @(
    "***---Hostname Info---***"
    $Hostname
    "***---Domain Info---***"
    $Domain
    "***---IP Info---***"
    $IP
    "***---Users---***"
    $Users
)
```

Here, `$Vars` contains our collected information and the headings that separate each section.

### Writing to the File

We can use `Add-Content` to append the results:

```powershell
Add-Content ~\Desktop\recon.txt $Vars
```

### Combining Everything into a Function

```powershell
Import-Module ActiveDirectory

function Get-Recon {

    $Hostname = $env:ComputerName
    $IP = ipconfig
    $Domain = Get-ADDomain
    $Users = Get-ChildItem C:\Users\

    New-Item ~\Desktop\recon.txt -ItemType File

    $Vars = @(
        "***---Hostname Info---***"
        $Hostname
        "***---Domain Info---***"
        $Domain
        "***---IP Info---***"
        $IP
        "***---Users---***"
        $Users
    )

    Add-Content ~\Desktop\recon.txt $Vars
}
```

**Practical improvement:** The original exercise uses `New-Item` followed by `Add-Content`. If we execute this function multiple times, the output file may already exist. We can use `Set-Content` instead when we want to create or overwrite the report on every execution.

---

## 10. Adding Comments

Comments make our code easier to understand and maintain.

PowerShell supports both single-line and multiline comments.

### Single-Line Comments

We use `#` for a single-line comment:

```powershell
# Collect the hostname.
$Hostname = $env:ComputerName
```

### Multiline Comments

For longer explanations, we can use `<#` and `#>`:

```powershell
<#
This function collects basic information
about the local Windows computer.

The resulting report is saved to a file.
#>
```

Comments are ignored during the normal execution of our script.

They are especially useful when writing modules that other administrators or security analysts may eventually need to maintain.

---

## 11. Creating Comment-Based Help

PowerShell allows us to include documentation directly inside our scripts and functions.

This functionality is called **comment-based help**.

Unlike ordinary comments, comment-based help uses specific keywords that PowerShell recognizes.

### Common Help Keywords

| Keyword | Purpose |
|---|---|
| `.SYNOPSIS` | A short description of the command. |
| `.DESCRIPTION` | A more detailed explanation. |
| `.EXAMPLE` | An example of how to use the command. |
| `.NOTES` | Additional information. |

### Adding Help to Get-Recon

```powershell
<#
.SYNOPSIS
Collects basic information about a Windows host.

.DESCRIPTION
Retrieves the computer name, IP configuration,
Active Directory domain information, and
the contents of C:\Users\.

The results are saved to recon.txt on the
current user's Desktop.

.EXAMPLE
Get-Recon

.NOTES
This version collects information only
from the local computer.
#>

function Get-Recon {
    # Function code
}
```

We can place this help block immediately above our function.

### Reading the Help

After importing our module, we can access its documentation using:

```powershell
Get-Help Get-Recon
```

To view its examples:

```powershell
Get-Help Get-Recon -Examples
```

This makes our custom function behave more like PowerShell's built-in commands.

---

## 12. Exporting Module Functions

PowerShell modules can contain functions intended for public use alongside internal helper functions.

We can use `Export-ModuleMember` to control which functions and other members become available when our module is imported.

### Exporting Get-Recon

For example:

```powershell
Export-ModuleMember -Function Get-Recon
```

This makes `Get-Recon` available outside the module.

Other functions that are not exported can remain internal to the module.

### Exporting Multiple Members

The HTB material also demonstrates exporting a specific function and variable:

```powershell
Export-ModuleMember -Function Get-Recon -Variable Hostname
```

However, the `$Hostname` variable in our example is defined inside the `Get-Recon` function. It is therefore local to that function rather than a module-level variable.

To export a variable in this way, we would need to define it in the appropriate module scope.

### Relationship with the Manifest

If our module has a manifest, its export settings must also permit the members we want to expose.

For example:

```powershell
FunctionsToExport = @('Get-Recon')
```

This works alongside our `.psm1` file's export configuration.

---

## 13. Understanding PowerShell Scope

**Scope** determines where variables, functions, and other PowerShell objects can be accessed.

It helps us organize code and avoid accidentally modifying unrelated variables.

The HTB module introduces three important scope concepts.

| Scope | Description |
|---|---|
| Global | Available throughout the current PowerShell session, subject to scope rules. |
| Local | Refers to the current scope in which a command is executing. |
| Script | Provides a scope associated with a running script. |

### Global Scope

Variables created at the top level of an interactive PowerShell session are generally available in its global scope.

For example:

```powershell
$Global:Example = "Hello"
```

The explicit `Global:` qualifier identifies where the variable should be created.

### Script Scope

We can explicitly create a variable in script scope:

```powershell
$Script:Example = "Hello"
```

This is useful when multiple functions within the same script need to access shared information.

### Local Scope

Functions generally create their own local scope when executed.

For example:

```powershell
function Test-Scope {
    $Message = "Hello from the function"
}
```

The `$Message` variable belongs to the function's local scope.

After the function finishes, that local variable is not normally available to the rest of our PowerShell session.

### Why Does Scope Matter?

Consider our `Get-Recon` function:

```powershell
function Get-Recon {
    $Hostname = $env:ComputerName
}
```

The `$Hostname` variable is created inside the function's scope.

This allows us to collect and process information without unnecessarily creating variables in the caller's session.

---

## 14. Importing Our Completed Module

Once we finish writing the module, we can import it into PowerShell.

The module directory should contain:

```text
quick-recon/
├── quick-recon.psd1
└── quick-recon.psm1
```

### Importing the Module

We can import its manifest:

```powershell
Import-Module "$HOME\Documents\WindowsPowerShell\Modules\quick-recon\quick-recon.psd1"
```

Or import the `.psm1` file directly:

```powershell
Import-Module "$HOME\Documents\WindowsPowerShell\Modules\quick-recon\quick-recon.psm1"
```

When our module is located in an appropriate directory listed in `$env:PSModulePath`, we can also import it by name:

```powershell
Import-Module quick-recon
```

### Verifying the Import

We can inspect the modules loaded into the current session:

```powershell
Get-Module
```

To inspect our specific module:

```powershell
Get-Module quick-recon
```

The HTB example shows `Get-Recon` as the exported command associated with the imported module.

### Testing Our Function

Once imported, we can execute:

```powershell
Get-Recon
```

The function collects the specified information and saves it to `recon.txt`.

We can then inspect the report:

```powershell
Get-Content ~\Desktop\recon.txt
```

Finally, we can verify that our help documentation works:

```powershell
Get-Help Get-Recon
```

---

## 15. Complete Quick-Reference Example

The following brings together the principal concepts demonstrated in the HTB exercise.

**File: `quick-recon.psm1`**

```powershell
Import-Module ActiveDirectory

<#
.SYNOPSIS
Collects basic Windows host information.

.DESCRIPTION
Retrieves the hostname, IP configuration,
Active Directory domain information, and
user profile directories.

Saves the collected information to recon.txt
on the current user's Desktop.

.EXAMPLE
Get-Recon

.NOTES
This function performs local reconnaissance.
#>

function Get-Recon {

    # Collect the hostname.
    $Hostname = $env:ComputerName

    # Collect the IP configuration.
    $IP = ipconfig

    # Collect domain information.
    $Domain = Get-ADDomain

    # Enumerate user profile directories.
    $Users = Get-ChildItem C:\Users\

    # Define the report destination.
    $OutputFile = "$HOME\Desktop\recon.txt"

    # Organize the collected information.
    $Vars = @(
        "***---Hostname Info---***"
        $Hostname
        "***---Domain Info---***"
        $Domain
        "***---IP Info---***"
        $IP
        "***---Users---***"
        $Users
    )

    # Create or overwrite the report.
    $Vars | Out-String | Set-Content -Path $OutputFile

}

# Make the function available after importing.
Export-ModuleMember -Function Get-Recon
```

This version preserves the exercise's functionality while making repeated executions easier by overwriting the previous report rather than attempting to recreate an existing file.

The ActiveDirectory module and a valid domain environment are required for the `Get-ADDomain` command.

---

## Command Summary

| Command | Purpose |
|---|---|
| `$env:PSModulePath` | Displays the module search paths. |
| `New-ModuleManifest` | Generates a module manifest. |
| `New-Item` | Creates the module directory and files. |
| `Import-Module` | Loads a module into the current session. |
| `Get-Module` | Displays loaded modules. |
| `Get-ADDomain` | Retrieves Active Directory domain information. |
| `Get-ChildItem` | Enumerates files and directories. |
| `Add-Content` | Appends content to a file. |
| `Set-Content` | Writes or replaces file content. |
| `Export-ModuleMember` | Controls which module members are exported. |
| `Get-Help` | Displays documentation for commands and functions. |

---

## Key Takeaways

- PowerShell scripts use the `.ps1` extension, while script modules use `.psm1`.
- A `.psd1` manifest describes a module's metadata, dependencies, and exported members.
- `$env:PSModulePath` defines the directories PowerShell searches for modules.
- `New-ModuleManifest` generates a manifest for our module.
- Functions allow us to organize commands into reusable operations.
- Variables store command output and other information for later use.
- Comments document our code, while comment-based help integrates with `Get-Help`.
- `Export-ModuleMember` controls which functions and other members are publicly available.
- Scope determines where variables and functions can be accessed.
- `Import-Module` loads a module, and `Get-Module` allows us to verify that it is loaded.
- The `quick-recon` exercise demonstrates how several basic PowerShell commands can be combined into a reusable administrative tool.
