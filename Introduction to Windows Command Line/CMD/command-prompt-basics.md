
# Command Prompt Basics

---

## CMD.exe

The **Command Prompt (`cmd.exe`)** is the traditional Windows command-line interpreter, originally derived from the DOS `COMMAND.COM` interpreter.

It allows us to interact directly with the operating system by executing commands, running batch scripts, managing files, and performing administrative tasks without relying on a graphical interface.

Although PowerShell provides more advanced scripting capabilities, CMD remains useful for system administration and penetration testing.

From a security perspective, understanding CMD is important because PowerShell may be restricted or blocked by security mechanisms such as AppLocker, while CMD may still be accessible.

---

## Accessing CMD

We can access Windows hosts through two primary methods: **local access** and **remote access**.

### Local Access

Local access involves interacting directly with a machine, either physically or through a virtual machine's console. It does not require a network connection.

We can open CMD using either of the following methods:

- Press `Win + R`, type `cmd`, and press Enter.
- Execute `C:\Windows\System32\cmd.exe`.

Once opened, the Command Prompt displays our current working directory:

```cmd
C:\Users\htb>
```

We can execute commands directly from this prompt.

### Remote Access

Remote access allows us to interact with a Windows host over a network without requiring physical access.

Common remote access technologies include:

| Technology | Description |
|---|---|
| SSH | Provides encrypted remote command-line access. |
| RDP | Provides graphical remote desktop access. |
| WinRM | Enables remote Windows management and PowerShell remoting. |
| PsExec | Allows us to execute processes and commands on remote Windows hosts. |
| Telnet | Provides unencrypted remote terminal access and is not recommended. |

Remote access requires network connectivity, an accessible service, and appropriate authentication or authorization.

**Security considerations:**

Remote administration tools can also create security risks. If an attacker obtains valid credentials or exploits a misconfigured remote service, they may gain unauthorized access to additional systems.

During penetration testing, we may assess these services to identify exposed administrative interfaces, insecure configurations, and opportunities for lateral movement.

---

## Basic Usage

CMD follows a straightforward command-and-response model: we enter a command, the operating system executes it, and the results are displayed in the terminal.

### Listing Directory Contents

We can use the `dir` command to list the files and subdirectories in our current working directory.

```cmd
C:\Users\htb\Desktop> dir
```

Example output:

```text
Directory of C:\Users\htb\Desktop

06/11/2021  11:59 PM    <DIR>          .
06/11/2021  11:59 PM    <DIR>          ..
06/11/2021  11:57 PM                 0 file1.txt
06/11/2021  11:57 PM                 0 file2.txt
06/11/2021  11:57 PM                 0 file3.txt
06/11/2021  11:57 PM                 0 super-secret-sauce.txt
06/11/2021  11:59 PM                 0 write-secrets.ps1
```

The output provides information about the directory's contents, including file names, sizes, modification timestamps, and subdirectories.

Two special directory entries are worth remembering:

- `.` represents the current directory.
- `..` represents the parent directory.

We can also list the contents of a specific directory by providing its path:

```cmd
dir C:\Users\htb\Documents
```

To include hidden and system files, we can use:

```cmd
dir /a
```

---

## Case Study: Windows Recovery

Windows Recovery Environment (WinRE) provides troubleshooting tools, including access to a Command Prompt, that can be useful when a system cannot boot normally.

However, recovery environments can also introduce security risks when unauthorized individuals have physical access to a machine.

### Sticky Keys Authentication Bypass

A historical Windows 7 attack demonstrates how an attacker with physical access and the ability to modify an unencrypted system drive could bypass Windows authentication.

The technique involves replacing the legitimate Sticky Keys executable, `sethc.exe`, with `cmd.exe`.

Normally, pressing `Shift` five times on the Windows login screen launches Sticky Keys.

If the executable has been replaced, the same action launches a Command Prompt instead. In the vulnerable configuration described in the module, this process runs with `NT AUTHORITY\SYSTEM` privileges.

**Security implications:**

- Physical access can enable attacks against unprotected system drives.
- Offline modification of system executables can compromise operating-system integrity.
- Full-disk encryption, such as BitLocker, helps prevent unauthorized offline modification of protected volumes.

This historical example illustrates why protecting physical access and system integrity is essential, even when an operating system requires authentication.

---

## Key Takeaways

- CMD is a built-in Windows command interpreter that remains useful for administration and penetration testing.
- We can access CMD locally or through appropriately configured remote access technologies.
- The `dir` command allows us to enumerate files and directories.
- CMD may remain available in environments where PowerShell is restricted.
- Misconfigured remote administration services and unprotected physical access can introduce significant security risks.
