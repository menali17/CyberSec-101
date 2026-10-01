
# Environment Variables

---

## What Are Environment Variables?

**Environment variables** are named values used by Windows, applications, and scripts to store configuration information and reference system resources.

They allow us to access information such as system directories, user profiles, executable locations, and network settings without manually specifying their values.

In Windows CMD, environment variables are referenced using the following syntax:

```cmd
%VARIABLE_NAME%
```

For example:

```cmd
echo %WINDIR%
```

Output:

```text
C:\Windows
```

Windows environment variable names are case-insensitive. By convention, they are usually written in uppercase.

---

## Environment Variable Scope

Scope determines where an environment variable is available and which processes or users can access it.

Windows organizes environment variables into three primary scopes.

| Scope | Description | Registry Location |
|---|---|---|
| System (Machine) | Variables configured for the entire operating system and generally available to all users. | `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment` |
| User | Variables configured for a particular user account. | `HKEY_CURRENT_USER\Environment` |
| Process | Variables available within a running process and typically inherited by its child processes. | Stored in process memory. |

### System Variables

System variables are configured at the operating system level.

For example, two different users can access the same Windows installation directory using:

```cmd
echo %WINDIR%
```

Both users would typically receive:

```text
C:\Windows
```

Modifying persistent system environment variables generally requires administrative privileges.

### User Variables

User variables are associated with a particular Windows account.

They allow different users to maintain their own environment configurations without modifying variables belonging to other users.

For example, a user may configure a custom environment variable that is loaded into their environment when they sign in.

### Process Variables

Process variables exist within the environment of a particular running process.

For example:

```cmd
set SECRET=HTB{5UP3r_53Cr37_V4r14813}
```

We can retrieve the value using:

```cmd
echo %SECRET%
```

Output:

```text
HTB{5UP3r_53Cr37_V4r14813}
```

The variable is available within our current CMD session and can be inherited by child processes.

However, it is not automatically available to unrelated CMD sessions or other users.

Once we close our CMD session, the variable will no longer be available unless it has also been configured persistently.

**Security consideration:** Environment variables should not be treated as secure storage for passwords or other sensitive information. Their values may be exposed through process inspection, scripts, logs, or diagnostic tools.

---

## Viewing Environment Variables

CMD provides two primary commands for inspecting environment variables: `set` and `echo`.

### Set

Executing `set` without arguments displays all environment variables available to our current CMD process.

```cmd
set
```

We can also inspect a particular variable by specifying its name:

```cmd
set SYSTEMROOT
```

Example output:

```text
SystemRoot=C:\Windows
```

Unlike `echo`, the `set` command expects a variable name without surrounding percentage signs when searching for variables.

### Echo

The `echo` command allows us to display the value stored in an environment variable.

```cmd
echo %PATH%
```

This displays the directories stored in our current `PATH` variable.

We can also inspect individual system variables:

```cmd
echo %SYSTEMROOT%
echo %USERPROFILE%
echo %LOGONSERVER%
```

---

## Managing Environment Variables

Windows provides two commands for creating and modifying environment variables: `set` and `setx`.

The main difference is whether our changes are temporary or persistent.

| Command | Description |
|---|---|
| `set` | Creates, modifies, or removes environment variables within the current CMD process. |
| `setx` | Creates or modifies persistent environment variables for future sessions. |

### Creating Variables with Set

Suppose we want to store the IP address of a Domain Controller in an environment variable.

We can create a temporary variable using:

```cmd
set DCIP=172.16.5.2
```

We can verify that the variable was created:

```cmd
echo %DCIP%
```

Output:

```text
172.16.5.2
```

The variable remains available within our current CMD session.

However, closing the session removes this temporary definition.

### Creating Persistent Variables with Setx

We can use `setx` to persist environment variables.

**Syntax:**

```cmd
setx <variable_name> <value>
```

For example:

```cmd
setx DCIP 172.16.5.2
```

Output:

```text
SUCCESS: Specified value was saved.
```

By default, `setx` creates or modifies a persistent user environment variable.

We can use `/M` to modify a system environment variable when we have the necessary administrative privileges.

```cmd
setx DCIP 172.16.5.2 /M
```

**Important:** Changes made with `setx` do not automatically update our current CMD process. We generally need to open a new CMD session to access the updated value.

### Editing Variables

We can update an existing variable by assigning it a new value.

For example, suppose the IP address of our Domain Controller changes.

Using `set`:

```cmd
set DCIP=172.16.5.5
```

Using `setx`:

```cmd
setx DCIP 172.16.5.5
```

The first command changes the variable in our current process, while the second persists the new value for future sessions.

### Removing Variables

We can remove a temporary environment variable by assigning it an empty value:

```cmd
set DCIP=
```

We can verify that the variable is no longer defined:

```cmd
set DCIP
```

Expected output:

```text
Environment variable DCIP not defined
```

For a persistent variable, the module demonstrates clearing its value using:

```cmd
setx DCIP ""
```

This saves an empty value for future sessions rather than deleting the variable's registry entry entirely.

To completely remove a persistent variable, we would need to delete its corresponding registry value or use another appropriate management method.

---

## Important Environment Variables

Several built-in Windows environment variables are especially useful during system administration and penetration testing.

| Variable | Description |
|---|---|
| `%PATH%` | Contains directories Windows searches for executable files. |
| `%OS%` | Identifies the current operating system family. |
| `%SYSTEMROOT%` | Contains the Windows installation directory, commonly `C:\Windows`. |
| `%LOGONSERVER%` | Identifies the server that authenticated the current logon session. |
| `%USERPROFILE%` | Contains the current user's profile directory. |
| `%ProgramFiles%` | Contains the default installation directory for applications, typically 64-bit applications on 64-bit Windows. |
| `%ProgramFiles(x86)%` | Contains the default installation directory for 32-bit applications on 64-bit Windows. |

### PATH

The `PATH` environment variable specifies directories Windows searches when we execute commands without providing their complete paths.

```cmd
echo %PATH%
```

For example, we can execute:

```cmd
ipconfig
```

Instead of specifying the complete path to `ipconfig.exe`, provided its directory is available through the command search path.

### SYSTEMROOT

The `%SYSTEMROOT%` variable identifies the Windows installation directory.

```cmd
echo %SYSTEMROOT%
```

We can use it to reference important Windows directories without hardcoding their paths.

For example:

```cmd
dir %SYSTEMROOT%\System32
```

### USERPROFILE

The `%USERPROFILE%` variable identifies the current user's profile directory.

```cmd
echo %USERPROFILE%
```

Example output:

```text
C:\Users\htb
```

This can help us navigate directly to the current user's files:

```cmd
cd %USERPROFILE%\Documents
```

### LOGONSERVER

The `%LOGONSERVER%` variable identifies the server associated with the current logon session.

```cmd
echo %LOGONSERVER%
```

In a domain environment, its value may identify the Domain Controller that authenticated our session.

This information can help us understand the authentication environment, although it does not independently establish domain membership.

---

## Relevance to Penetration Testing

Environment variables can provide useful information during Windows host enumeration.

We can inspect them to identify system directories, user profiles, application locations, and details about the current logon environment.

For example:

```cmd
set
```

This displays the environment variables available to our current process, allowing us to identify potentially relevant configuration information.

We should pay particular attention to custom environment variables because applications and administrators sometimes use them to store application settings, network addresses, and other operational information.

**Key takeaway:** `set` manages temporary environment variables within our current CMD session, while `setx` persists changes for future sessions. Understanding environment variable scope allows us to manage Windows configurations and gather valuable information during host enumeration.
