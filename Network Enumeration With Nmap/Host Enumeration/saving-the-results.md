# Saving the Results

---

## Different Formats

We should save our scan results so we can review findings, compare scanning methods, and document our work without repeating every scan.

Nmap supports three main output formats:

| Format | Option | Typical filename | Purpose |
|---|---|---|---|
| **Normal** | `-oN` | `target.nmap` | Human-readable text. |
| **Grepable** | `-oG` | `target.gnmap` | Compact text suitable for filtering. |
| **XML** | `-oX` | `target.xml` | Structured data for tools and reporting. |

To save one format, we specify its option and the complete filename:

```bash
sudo nmap 10.129.2.28 -p- -oN target.nmap
```

To save all three formats at once, we use **`-oA`** followed by a filename prefix:

```bash
sudo nmap 10.129.2.28 -p- -oA target
```

| Argument | Description |
|---|---|
| `10.129.2.28` | The target IP address. |
| `-p-` | Scans TCP ports 1 through 65535 in this command. |
| `-oA target` | Saves all three formats using `target` as the prefix. |

Nmap creates:

```text
target.nmap
target.gnmap
target.xml
```

**With `-oA`, we provide a prefix rather than a filename extension.** Nmap adds the extensions automatically.

Unless we specify another directory, the files are saved in our current working directory.

We can list them with:

```bash
ls target.*
```

Saving the results does not change the scanning technique. It controls how the findings are recorded.

We should use distinct prefixes for scans we want to preserve separately, because reusing the same filenames normally overwrites existing results.

### Normal Output

We can read the normal output directly:

```bash
cat target.nmap
```

A shortened example:

```text
Nmap scan report for 10.129.2.28
Host is up.

PORT   STATE SERVICE
22/tcp open  ssh
25/tcp open  smtp
80/tcp open  http
```

This format resembles the terminal output and is convenient for reviewing findings manually.

It also records scan information such as the command used and the start and completion times.

### Grepable Output

```bash
cat target.gnmap
```

A shortened example:

```text
Host: 10.129.2.28 () Status: Up
Host: 10.129.2.28 () Ports: 22/open/tcp//ssh///, 25/open/tcp//smtp///, 80/open/tcp//http///
```

This format places information into compact lines that we can filter with text-processing tools.

For example:

```bash
grep "22/open/tcp" target.gnmap
```

This selects lines containing an open TCP port 22 entry.

An entry such as:

```text
22/open/tcp//ssh///
```

Includes the port number, state, protocol, and associated service name. Empty fields account for the consecutive slashes.

### XML Output

```bash
cat target.xml
```

XML represents findings using elements and attributes. A shortened excerpt looks like this:

```xml
<port protocol="tcp" portid="22">
  <state state="open" reason="syn-ack" reason_ttl="64"/>
  <service name="ssh" method="table" conf="3"/>
</port>
```

We can interpret this as:

| Field | Meaning |
|---|---|
| `protocol="tcp"` | The transport protocol is TCP. |
| `portid="22"` | The scanned port is 22. |
| `state="open"` | Nmap classified the port as open. |
| `reason="syn-ack"` | A SYN-ACK response supported that classification. |
| `reason_ttl="64"` | The response had a TTL of 64. |
| `name="ssh"` | The associated service name is SSH. |
| `method="table"` | The name came from a port mapping rather than service probing. |

XML is useful when we want software to process the results or generate a formatted report.

The supplied examples contain inconsistent port counts and timestamps. We should treat them as format illustrations rather than matching records of one complete scan.

---

## Style Sheets

We can transform XML results into an **HTML report** for viewing in a browser.

An **XSL stylesheet** defines how the XML data should be presented. Nmap's XML output normally references an Nmap stylesheet.

We can use `xsltproc` to perform the transformation:

```bash
xsltproc target.xml -o target.html
```

| Component | Purpose |
|---|---|
| `xsltproc` | Applies an XSLT stylesheet to XML data. |
| `target.xml` | The saved Nmap XML input. |
| `-o target.html` | Writes the generated HTML to this file. |

This command relies on the stylesheet referenced by the XML being accessible.

We can then open `target.html` in a browser to inspect the formatted results.

**This conversion does not run another scan.** It presents the data already stored in `target.xml` in a more readable layout.