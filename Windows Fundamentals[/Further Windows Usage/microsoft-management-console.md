# Microsoft Management Console (MMC)

The **Microsoft Management Console (MMC)** is a Windows framework used to organize administrative tools into a single console.

MMC can be used to manage:

* Hardware
* Software
* Windows services
* Network components
* Local systems
* Remote systems

MMC itself does not provide the management functionality. Instead, administrative tools called **snap-ins** are added to the console.

---

# Snap-ins

A **snap-in** is an administrative tool that can be loaded into MMC.

Administrators can create customized consoles containing only the tools required for a particular task.

For example, a console could contain snap-ins for:

```text id="5drtxq"
Services
Computer Management
Event Viewer
Device Manager
Local Users and Groups
```

Snap-ins can be configured to manage either:

* The local computer
* Another computer on the network

This allows MMC to serve as a centralized interface for different Windows administrative tasks.

---

# Opening MMC

MMC can be opened by entering:

```cmd id="7ym7gn"
mmc
```

When MMC is opened without a predefined console, it initially contains an empty **Console Root**.

Administrative tools can then be added through:

```text id="9uqp7m"
File → Add or Remove Snap-ins
```

---

# Adding Snap-ins

From the **Add or Remove Snap-ins** window, we can select the administrative tools we want to include.

When adding certain snap-ins, Windows may ask whether the tool should manage:

```text id="89a1kg"
Local computer

or

Another computer on the network
```

Once added, the snap-ins appear in the left side of the MMC console and can be used directly from the customized interface.

---

# Saving MMC Consoles

A customized MMC configuration can be saved as:

```text id="p9m3wq"
.msc
```

For example:

```text id="ft8dr7"
management.msc
```

The saved file preserves the configured snap-ins so they do not need to be added manually every time MMC is opened.

By default, these consoles can be saved in the Windows Administrative Tools directory available through the Start menu.

---

# MMC and `.msc` Files

Several Windows administrative tools we have already encountered use the `.msc` format.

For example:

```cmd id="w3qx0v"
services.msc
```

The important relationship is:

```text id="a5p4cm"
MMC
 ↓
Management console framework

Snap-ins
 ↓
Administrative tools loaded into MMC

.msc
 ↓
Saved MMC console configuration
```

---

# Local and Remote Administration

One useful feature of MMC is that some snap-ins can manage remote Windows machines.

Instead of creating completely separate administrative environments, an administrator can configure a console containing the required snap-ins and point them toward local or remote systems.

This makes MMC useful for administering multiple Windows systems through a consistent graphical interface.

---

# Quick Reference

### Open MMC

```cmd id="bf6h9p"
mmc
```

### Add Administrative Tools

```text id="d1vq8s"
File → Add or Remove Snap-ins
```

### MMC Components

| Component        | Purpose                                   |
| ---------------- | ----------------------------------------- |
| **MMC**          | Framework that hosts administrative tools |
| **Snap-in**      | Administrative tool loaded into MMC       |
| **Console Root** | Root of the current MMC configuration     |
| **`.msc` file**  | Saved MMC console configuration           |

### Management Targets

```text id="g91c2x"
Local computer
Remote computer
```

---

## Key Takeaway

**Microsoft Management Console (MMC) is a Windows framework that allows administrative tools called snap-ins to be grouped into customized management consoles. Snap-ins can manage local or remote systems, and a customized console can be saved as an `.msc` file for later use. Tools such as `services.msc` are examples of Windows management consoles based on this infrastructure.**
