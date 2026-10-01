
# Working With Scheduled Tasks

---

## What Are Scheduled Tasks?

Windows Task Scheduler allows us to automate the execution of programs, scripts, and administrative tasks based on predefined conditions called **triggers**.

Instead of manually executing a command every time an action is required, we can configure Windows to execute it automatically when a specific condition is met.

Common triggers include:

- At system startup.
- When a user logs on.
- At a specific time or on a recurring schedule.
- When a particular system event occurs.
- When the computer becomes idle.

### Relevance to Penetration Testing

Scheduled tasks can also be abused as a **persistence mechanism**, allowing attackers to maintain access to a compromised host.

For example, an attacker with sufficient permissions could create a task that establishes a connection to their command-and-control infrastructure whenever the computer restarts.

Depending on the account configured to execute the task, a misconfigured scheduled task could also present a privilege escalation opportunity.

---

## Managing Scheduled Tasks With Schtasks

The `schtasks` utility allows us to create, query, modify, execute, and delete scheduled tasks through CMD.

We can inspect its available functionality using:

```cmd
schtasks /?
```

### Querying Scheduled Tasks

We can list the existing scheduled tasks on our system using:

```cmd
schtasks /query
```

For more detailed information, we can execute:

```cmd
schtasks /query /v /fo list
```

Common parameters:

| Parameter | Description |
|---|---|
| `/query` | Displays existing scheduled tasks. |
| `/v` | Displays detailed information. |
| `/fo` | Specifies the output format: TABLE, LIST, or CSV. |
| `/nh` | Removes column headers from TABLE or CSV output. |
| `/s` | Specifies a remote host. |
| `/u` | Specifies the account used to connect to a remote host. |
| `/p` | Specifies the password for remote authentication. |

The detailed output may include the task name, next execution time, current status, executable path, trigger, and account used to execute the task.

**Note:** Our ability to enumerate scheduled tasks depends on our current permissions.

---

## Creating Scheduled Tasks

We can create a scheduled task using the `/create` parameter.

The four primary components required to create a task are:

| Parameter | Description |
|---|---|
| `/create` | Creates a new scheduled task. |
| `/sc` | Specifies the schedule or trigger type. |
| `/tn` | Defines the task name. |
| `/tr` | Specifies the program, script, or command to execute. |

Additional parameters:

| Parameter | Description |
|---|---|
| `/mo` | Modifies the recurrence interval. |
| `/ru` | Specifies the account that executes the task. |
| `/rp` | Specifies the password of that account. |
| `/rl` | Specifies the task's run level: LIMITED or HIGHEST. |
| `/z` | Deletes the task after its final scheduled execution. |

### Example: Creating a Task

We can create a scheduled task that performs a network connectivity check whenever Windows starts:

```cmd
schtasks /create /sc ONSTART /tn "Network Check" /tr "cmd.exe /c ping 8.8.8.8"
```

In this example:

- `/create` instructs Windows to create a task.
- `/sc ONSTART` sets system startup as the trigger.
- `/tn "Network Check"` assigns a name to the task.
- `/tr` specifies the command that Windows executes.

### Scheduled Tasks and Persistence

The HTB module demonstrates how the same functionality can be used for persistence.

In its example, a scheduled task is configured to execute Ncat when Windows starts, initiating an outbound connection to a remote host.

This illustrates how an attacker could use a scheduled task to regain access after losing an existing session.

The permissions associated with the task determine which resources its executable can access.

---

## Modifying Scheduled Tasks

The `/change` parameter allows us to modify an existing task.

For example, we can change the program executed by our previously created task:

```cmd
schtasks /change /tn "Network Check" /tr "cmd.exe /c hostname"
```

Common parameters include:

| Parameter | Description |
|---|---|
| `/change` | Modifies an existing scheduled task. |
| `/tn` | Identifies the task to modify. |
| `/tr` | Changes the program or command executed. |
| `/enable` | Enables a scheduled task. |
| `/disable` | Disables a scheduled task. |
| `/ru` | Changes the account used to execute the task. |
| `/rp` | Specifies the password for the task's execution account. |

We can verify our changes by querying the task:

```cmd
schtasks /query /tn "Network Check" /v /fo list
```

**Important:** Modifying the account used to execute a task requires appropriate permissions. Changing the configured account does not automatically provide us with that account's privileges.

---

## Running Scheduled Tasks

We can manually execute an existing task without waiting for its scheduled trigger using:

```cmd
schtasks /run /tn "Network Check"
```

This is useful for verifying whether a scheduled task executes its configured action correctly.

The task must be enabled, and its execution remains subject to its security configuration.

---

## Deleting Scheduled Tasks

We can delete an existing scheduled task using:

```cmd
schtasks /delete /tn "Network Check"
```

Windows normally requests confirmation before deleting the task.

We can suppress this confirmation using `/f`:

```cmd
schtasks /delete /tn "Network Check" /f
```

Deleting a scheduled task removes its registration from Task Scheduler. It does not automatically delete the scripts or executables associated with it.

---

## Key Takeaways

- Windows Task Scheduler automates the execution of programs and scripts through predefined triggers.
- `schtasks /query` enumerates existing scheduled tasks.
- `schtasks /create` creates new tasks using a schedule, name, and execution action.
- `schtasks /change` modifies existing tasks.
- `schtasks /run` immediately triggers an existing task.
- `schtasks /delete` removes a task.
- Scheduled tasks can be abused for persistence and, when permissions are misconfigured, privilege escalation.
- Enumerating scheduled tasks helps us identify automated processes, execution privileges, and potential security misconfigurations.
