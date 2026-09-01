# Task Scheduling

Task scheduling allows us to **automate commands, scripts, and processes so they run at specific times or regular intervals**.

Instead of manually starting the same task repeatedly, Linux can execute it automatically according to a schedule.

Common use cases include:

```text id="m6vw23"
Software updates
Script execution
Database maintenance
Backups
Notifications
```

From a cybersecurity perspective, scheduled tasks are important because they can be used both legitimately and maliciously.

For example:

```text id="4p1x9h"
Administrator
→ schedules backups

Attacker
→ schedules a persistence script
```

Because of this, scheduled tasks are relevant during system auditing and penetration testing.

---

# Main Scheduling Tools

This section introduces two main approaches:

```text id="k9v2af"
systemd timers

cron
```

Both automate tasks, but they are configured differently.

---

# Systemd Timers

`systemd` can schedule scripts and processes using **timer units**.

The basic workflow is:

```text id="z7q4wk"
1. Create timer
2. Create service
3. Reload systemd
4. Start timer
5. Enable timer
```

The timer defines:

> **When should the task run?**

The service defines:

> **What should run?**

---

# Step 1 — Create the Timer

The material first creates a directory and timer file:

```bash id="f5n2xr"
menali@htb[/htb]$ sudo mkdir /etc/systemd/system/mytimer.timer.d
menali@htb[/htb]$ sudo vim /etc/systemd/system/mytimer.timer
```

The timer configuration is:

```text id="c3m7vp"
[Unit]
Description=My Timer

[Timer]
OnBootSec=3min
OnUnitActiveSec=1hour

[Install]
WantedBy=timers.target
```

---

# Timer Sections

The timer contains three main sections:

```text id="h8q1md"
[Unit]

[Timer]

[Install]
```

## `[Unit]`

Used for general information about the timer.

Example:

```text id="p2w6kn"
[Unit]
Description=My Timer
```

This gives the timer a description.

---

## `[Timer]`

Defines **when the timer runs**.

Example:

```text id="r9m4zc"
[Timer]
OnBootSec=3min
OnUnitActiveSec=1hour
```

---

# `OnBootSec`

```text id="v5q3np"
OnBootSec=3min
```

means:

> Run the timer three minutes after boot.

Conceptually:

```text id="x1m8wr"
System boots
    │
    ▼
Wait 3 minutes
    │
    ▼
Run task
```

This is useful when we want something to run after startup.

---

# `OnUnitActiveSec`

```text id="b7p2kv"
OnUnitActiveSec=1hour
```

means the task should run repeatedly at an interval of:

```text id="q4n9xm"
1 hour
```

Conceptually:

```text id="g6w1vr"
Run
 │
 │ 1 hour
 ▼
Run
 │
 │ 1 hour
 ▼
Run
```

So:

```text id="f8m3qc"
OnBootSec
→ delay after system boot

OnUnitActiveSec
→ recurring interval
```

---

# `[Install]`

The timer uses:

```text id="n2k7wp"
[Install]
WantedBy=timers.target
```

This specifies the systemd target associated with enabling the timer.

For this section, the important idea is that it allows the timer to be integrated into systemd's timer management.

---

# Step 2 — Create the Service

The timer decides **when** something happens.

Now we need a service that defines **what actually happens**.

Create:

```bash id="m9x4pr"
menali@htb[/htb]$ sudo vim /etc/systemd/system/mytimer.service
```

The service configuration is:

```text id="w5q8nc"
[Unit]
Description=My Service

[Service]
ExecStart=/full/path/to/my/script.sh

[Install]
WantedBy=multi-user.target
```

---

# `ExecStart`

The most important line here is:

```text id="r1n6vk"
ExecStart=/full/path/to/my/script.sh
```

This tells systemd which command or script it should execute.

Conceptually:

```text id="c7m2wp"
Timer
  │
  │ schedule reached
  ▼
Service
  │
  ▼
ExecStart
  │
  ▼
script.sh
```

---

# `multi-user.target`

The example uses:

```text id="p4q9xn"
WantedBy=multi-user.target
```

The material describes this as a systemd target used during normal multi-user system operation.

At this stage, the important idea is:

```text id="h6v1mr"
timer
→ controls scheduling

service
→ controls execution
```

---

# Step 3 — Reload systemd

After creating or modifying unit files, systemd needs to read the configuration again.

We do this using:

```bash id="k3w8qp"
sudo systemctl daemon-reload
```

Conceptually:

```text id="x9m5nc"
We modify unit files
        │
        ▼
systemctl daemon-reload
        │
        ▼
systemd reads changes
```

---

# Step 4 — Start the Timer

We can start the timer using:

```bash id="t7q2mv"
sudo systemctl start mytimer.timer
```

This activates it for the current session.

---

# Step 5 — Enable the Timer

To configure the timer to activate automatically:

```bash id="d1n6wr"
sudo systemctl enable mytimer.timer
```

Recall the distinction from service management:

```text id="m4p8xq"
start
→ activate now

enable
→ activate automatically after boot
```

So the complete setup is:

```bash id="v9k3qn"
sudo systemctl daemon-reload
sudo systemctl start mytimer.timer
sudo systemctl enable mytimer.timer
```

---

# Systemd Timer Mental Model

The easiest way to understand a systemd scheduled task is:

```text id="q2w7mr"
mytimer.timer
      │
      │ decides WHEN
      ▼
mytimer.service
      │
      │ decides WHAT
      ▼
script.sh
```

For example:

```text id="f5m9xp"
Every hour
    │
    ▼
mytimer.timer
    │
    ▼
mytimer.service
    │
    ▼
backup.sh
```

---

# Cron

`cron` is another scheduling system commonly used in Linux.

Instead of creating separate timer and service units, Cron stores scheduled tasks inside a:

```text id="n8q4vk"
crontab
```

The cron daemon reads those entries and executes commands at the configured times.

Conceptually:

```text id="b3m7wp"
crontab
   │
   ▼
cron daemon
   │
   ▼
Checks schedule
   │
   ▼
Runs command/script
```

---

# Cron Schedule Structure

A cron entry has five time fields followed by the command.

```text id="r6p1xn"
minute hour day-of-month month day-of-week command
```

Or visually:

```text id="k9w4mc"
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week
│ │ │ └──── Month
│ │ └────── Day of month
│ └──────── Hour
└────────── Minute
```

---

# Cron Fields

| Field        | Range  |
| ------------ | ------ |
| Minute       | `0-59` |
| Hour         | `0-23` |
| Day of month | `1-31` |
| Month        | `1-12` |
| Day of week  | `0-7`  |

The command or script path appears at the end.

---

# Wildcard `*`

The:

```text id="p2x7nr"
*
```

means that the field can match **any value**.

For example:

```text id="c6m1vq"
* * * * *
```

has no restriction in any of the five time fields.

The material mainly uses `*` to indicate fields that do not need to be restricted for that schedule.

---

# Example 1 — Every Six Hours

```text id="f8q3mw"
0 */6 * * * /path/to/update_software.sh
```

Breaking it down:

```text id="z5n9pk"
0
│
└── minute 0

*/6
│
└── every sixth hour

*
│
└── any day of month

*
│
└── any month

*
│
└── any day of week
```

So the task runs once every six hours.

The command is:

```text id="r4m8xq"
/path/to/update_software.sh
```

---

# Example 2 — First Day of Every Month

```text id="n7w2kc"
0 0 1 * * /path/to/scripts/run_scripts.sh
```

Breaking it down:

```text id="v1q6mp"
0 → minute 0

0 → hour 0

1 → first day of month

* → every month

* → any day of week
```

Therefore:

> Run the script at midnight on the first day of every month.

---

# Example 3 — Sunday at Midnight

```text id="k5p9xr"
0 0 * * 0 /path/to/scripts/clean_database.sh
```

Breaking it down:

```text id="d3m7vn"
0 → minute 0

0 → hour 0

* → any day of month

* → any month

0 → Sunday
```

Therefore:

> Run every Sunday at midnight.

---

# Example 4 — Sunday Using `7`

The material also shows:

```text id="q8w1mk"
0 0 * * 7 /path/to/scripts/backup.sh
```

Here:

```text id="p4n6vc"
7
```

also represents Sunday according to the schedule format shown in the material.

So both examples schedule a Sunday task.

---

# Reading Cron Expressions

The most important skill is learning to read them from left to right.

Consider:

```text id="m2r7xq"
0 0 * * 0 /backup.sh
```

Read:

```text id="c9p4wn"
Minute
0
↓
Hour
0
↓
Any day of month
↓
Any month
↓
Sunday
↓
Run /backup.sh
```

Therefore:

> Run `/backup.sh` every Sunday at midnight.

---

# Cron Example Overview

The material gives:

```text id="r5q8vm"
# System Update
0 */6 * * * /path/to/update_software.sh

# Execute Scripts
0 0 1 * * /path/to/scripts/run_scripts.sh

# Cleanup DB
0 0 * * 0 /path/to/scripts/clean_database.sh

# Backups
0 0 * * 7 /path/to/scripts/backup.sh
```

Meaning:

| Schedule      | Meaning                              |
| ------------- | ------------------------------------ |
| `0 */6 * * *` | Every six hours                      |
| `0 0 1 * *`   | First day of every month at midnight |
| `0 0 * * 0`   | Every Sunday at midnight             |
| `0 0 * * 7`   | Every Sunday at midnight             |

---

# Cron Mental Model

A useful way to think about Cron is:

```text id="x1v6pn"
WHEN WHEN WHEN WHEN WHEN → WHAT
```

For example:

```text id="w4m9qr"
0 0 * * 0 /backup.sh
│ │ │ │ │     │
│ │ │ │ │     └── WHAT
│ │ │ │ └──────── WHEN
│ │ │ └────────── WHEN
│ │ └──────────── WHEN
│ └────────────── WHEN
└──────────────── WHEN
```

So Cron combines the schedule and command in one entry.

---

# Systemd vs Cron

Both tools automate tasks, but their configuration differs.

### Systemd

Systemd separates scheduling from execution:

```text id="k7n2wc"
.timer
│
└── WHEN

.service
│
└── WHAT
```

We normally create:

```text id="p9q4mv"
mytimer.timer

mytimer.service
```

and activate them through:

```bash id="f3m8xn"
systemctl
```

---

### Cron

Cron usually places the schedule and command together:

```text id="v6w1kr"
0 0 * * 0 /backup.sh
```

which contains:

```text id="c2q7mp"
WHEN + WHAT
```

inside the same crontab entry.

---

# Cybersecurity Relevance

Scheduled tasks are particularly important during cybersecurity investigations and penetration testing.

A legitimate administrator may schedule:

```text id="x8m4qn"
Backups
Updates
Maintenance
Monitoring
```

But scheduled execution can also be abused.

For example:

```text id="r1p6wv"
Attacker compromises system
        │
        ▼
Creates malicious script
        │
        ▼
Adds scheduled task
        │
        ▼
Script executes repeatedly
        │
        ▼
Persistence
```

Because of this, examining scheduled tasks can reveal unauthorized persistence mechanisms.

---

# Important Commands

Reload systemd after changing unit files:

```bash id="m5q9xr"
sudo systemctl daemon-reload
```

Start a timer:

```bash id="c7w2pn"
sudo systemctl start mytimer.timer
```

Enable a timer:

```bash id="v4n8mk"
sudo systemctl enable mytimer.timer
```

---

# Quick Reference

| Concept           | Purpose                          |
| ----------------- | -------------------------------- |
| Task scheduling   | Automatically execute tasks      |
| `systemd timer`   | Schedule systemd tasks           |
| `.timer`          | Defines when a systemd task runs |
| `.service`        | Defines what systemd executes    |
| `OnBootSec`       | Run after a delay from boot      |
| `OnUnitActiveSec` | Run repeatedly after an interval |
| `daemon-reload`   | Reload systemd configuration     |
| `cron`            | Schedule recurring tasks         |
| `crontab`         | Stores cron schedules            |

---

# Cron Quick Reference

```text id="q3m7vp"
MIN HOUR DOM MONTH DOW COMMAND
```

Where:

```text id="k6n1wr"
MIN   = Minute
HOUR  = Hour
DOM   = Day of month
MONTH = Month
DOW   = Day of week
```

Example:

```text id="t9p4xq"
0 0 * * 0 /backup.sh
```

means:

```text id="f2m8vn"
Every Sunday at midnight
```

---

# What to Remember First

For now, the main concepts to remember are:

```text id="w5q1mc"
Systemd:
.timer   → WHEN
.service → WHAT
```

and:

```text id="n7p3xr"
Cron:

minute hour day month weekday command
```

The most important cron structure is:

```text id="v9m4qk"
* * * * * command
```

with the fields:

```text id="c6w2pn"
minute
hour
day of month
month
day of week
```

---

## Key Takeaway

**Linux task scheduling allows us to automate commands and scripts. Systemd timers separate the schedule (`.timer`) from the action (`.service`), while Cron stores the schedule and command together in a crontab entry. For cybersecurity, scheduled tasks are important because they can represent legitimate automation or be abused as persistence mechanisms.**
