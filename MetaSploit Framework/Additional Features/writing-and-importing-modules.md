
# Writing and Importing Modules — Metasploit Framework

## 1. Introduction

Metasploit Framework supports custom modules, allowing us to extend its functionality beyond the modules included in the default installation.

We can:

- Update Metasploit to obtain newly integrated modules.
- Download and import existing third-party modules.
- Adapt standalone exploit scripts into Metasploit-compatible modules.
- Develop our own modules using Ruby.

There are three important concepts:

| Concept | Description |
|---|---|
| Importing | Adding an existing Metasploit-compatible module |
| Porting | Converting an independent script into a compatible Metasploit module |
| Writing | Developing a new module using the Metasploit API |

**Key point:** Not every exploit script can be imported directly into Metasploit.

---

## 2. Finding Metasploit Modules

### Exploit Database

**ExploitDB** is a repository of publicly disclosed exploits and proof-of-concept (PoC) code.

Website: https://www.exploit-db.com/

It provides a **Metasploit Framework (MSF)** tag, which helps us identify exploits available in Metasploit module format.

https://www.exploit-db.com/?tag=3

### Searching for Existing Modules

Before importing an exploit, we should check whether it is already installed.

Example:

```bash id="41xa47"
msfconsole
search nagios
```

In the lesson, Metasploit already contains several Nagios-related modules but does not list the specific `statuswml.cgi` exploit we want.

This may mean:

- Our Metasploit installation does not contain the module.
- The module has not been integrated into the official framework.
- The module is only available through a third-party repository.

We can search for an external implementation.

---

## 3. SearchSploit

**SearchSploit** is a command-line utility used to search the local ExploitDB database.

It allows us to find publicly available exploit references without manually browsing the website.

### Basic Search

```bash id="r18m8n"
searchsploit nagios3
```

The lesson shows the following results:

| Exploit | Path |
|---|---|
| Nagios3 history.cgi Host Command Execution | `linux/remote/24159.rb` |
| Nagios3 history.cgi Remote Command Execution | `multiple/remote/24084.py` |
| Nagios3 statuswml.cgi Ping Command Execution | `cgi/webapps/16908.rb` |
| Nagios3 statuswml.cgi Command Injection | `unix/webapps/9861.rb` |

### Filtering Results

We can search exploit titles and exclude Python files:

```bash id="fv6s8u"
searchsploit -t Nagios3 --exclude=".py"
```

Explanation:

- `-t`: Search within exploit titles.
- `--exclude=".py"`: Exclude matching paths containing `.py`.

This helps us focus on other file types, including Ruby scripts.

### Understanding File Extensions

| Extension | Language |
|---|---|
| `.rb` | Ruby |
| `.py` | Python |
| `.php` | PHP |
| `.pl` | Perl |

Metasploit modules are commonly written in Ruby.

However, **not every Ruby script is a valid Metasploit module**.

A compatible module must follow the classes and interfaces expected by the framework.

---

## 4. Metasploit Directory Structure

Metasploit stores its framework components in a structured directory hierarchy.

### Framework Installation Directory

On the lesson's Linux installation:

```bash id="gqw933"
ls /usr/share/metasploit-framework/
```

Important directories include:

| Directory | Purpose |
|---|---|
| `modules/` | Exploit, auxiliary, post-exploitation, and payload modules |
| `plugins/` | Framework plugins |
| `scripts/` | Supporting scripts |
| `data/` | Framework data files |
| `lib/` | Libraries and internal framework code |
| `documentation/` | Documentation |

### User Configuration Directory

Metasploit also uses a hidden directory in the user's home folder:

```bash id="nrjk5t"
ls ~/.msf4/
```

Example:

```text id="8jnu2v"
history
local
logos
logs
loot
modules
plugins
store
```

The `~/.msf4/modules/` directory can be used to store custom modules without modifying the system-wide framework installation.

### Module Categories

Metasploit organizes modules by functionality.

Example:

```text id="q7dz7v"
modules/
├── exploits/
│   ├── linux/
│   ├── windows/
│   └── unix/
├── auxiliary/
├── post/
├── payloads/
└── encoders/
```

A module's location determines its category and module path.

For example:

```text id="d0k56a"
modules/exploits/unix/webapp/example.rb
```

Corresponds to:

```text id="4fo1j2"
exploit/unix/webapp/example
```

---

## 5. Importing an Existing Module

The lesson uses a Nagios3 command injection module from ExploitDB.

ExploitDB entry:

https://www.exploit-db.com/exploits/9861

### Step 1 — Obtain the Module

The example assumes that `9861.rb` has already been downloaded into our `Downloads` directory.

### Step 2 — Copy the Module

The lesson places the Ruby module in the appropriate Metasploit category:

```bash id="njqpig"
cp ~/Downloads/9861.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rb
```

This is a system-wide location and may require appropriate filesystem permissions.

Alternatively, custom modules can be organized inside our user directory.

Example:

```bash id="55gqje"
mkdir -p ~/.msf4/modules/exploits/unix/webapp/
```

Then:

```bash id="0n6gs7"
cp ~/Downloads/9861.rb ~/.msf4/modules/exploits/unix/webapp/nagios3_command_injection.rb
```

### Module Naming Conventions

The lesson recommends lowercase snake_case filenames.

Valid examples:

```text id="w3g5j5"
nagios3_command_injection.rb
our_module_here.rb
```

Avoid names containing spaces or hyphens.

### Step 3 — Load the Module

We can provide a custom module directory when launching Metasploit:

```bash id="cyecsg"
msfconsole -m /usr/share/metasploit-framework/modules/
```

Alternatively, inside an existing Metasploit console:

```bash id="yrti00"
loadpath /usr/share/metasploit-framework/modules/
```

Or reload available modules:

```bash id="9b6y5t"
reload_all
```

### Step 4 — Verify the Module

Search for the module:

```bash id="3wb4q6"
search nagios3_command_injection
```

Select it:

```bash id="k6cm44"
use exploit/unix/webapp/nagios3_command_injection
```

Inspect its options:

```bash id="jrbw98"
show options
```

If Metasploit recognizes the module, we can examine its configuration and supported targets.

**Important:** Successfully loading a module does not guarantee that the exploit will work against a target.

---

## 6. Porting Exploit Scripts

**Porting** means adapting an exploit written as a standalone script so that it works within the Metasploit Framework.

For example, we may have an exploit written in:

- Python
- PHP
- Perl
- Ruby

A standalone exploit cannot necessarily be executed directly as a Metasploit module.

Instead, we must adapt its implementation to use Metasploit's Ruby API.

### General Porting Process

1. Find an existing exploit implementation.
2. Identify the underlying vulnerability and exploit behavior.
3. Find an existing Metasploit module with similar functionality.
4. Use that module as a starting template.
5. Adjust its classes, mixins, metadata, and options.
6. Adapt the exploit logic to the framework.
7. Test module loading and functionality in an authorized environment.

### Why Use an Existing Module?

Starting from an existing implementation reduces the amount of framework boilerplate we must write.

It also provides examples of:

- Module inheritance
- Metadata declarations
- Option registration
- HTTP communication
- Payload integration
- Error handling

---

## 7. Metasploit Modules and Ruby

Metasploit's exploit modules are implemented using Ruby.

A typical remote exploit module starts with:

```ruby id="rz46ee"
class MetasploitModule < Msf::Exploit::Remote
```

Explanation:

| Component | Meaning |
|---|---|
| `class` | Declares a Ruby class |
| `MetasploitModule` | Class name recognized by Metasploit |
| `<` | Inheritance operator |
| `Msf::Exploit::Remote` | Parent class for remote exploit modules |

Through inheritance, the module obtains access to functionality provided by the framework.

### Understanding Mixins

**Mixins** are Ruby modules that provide reusable functionality.

We can include them in a class using the `include` keyword.

Example from the lesson:

```ruby id="gx9msm"
include Msf::Exploit::Remote::HttpClient
include Msf::Exploit::PhpEXE
include Msf::Exploit::FileDropper
include Msf::Auxiliary::Report
```

| Mixin | Purpose |
|---|---|
| `HttpClient` | Provides HTTP client functionality |
| `PhpEXE` | Provides PHP payload generation functionality |
| `FileDropper` | Supports tracking and cleanup of deployed files |
| `Report` | Provides methods for recording information in the Metasploit database |

We only need to include functionality relevant to our module.

For example, a module that does not upload files might not require `FileDropper`.

**Key point:** Inheritance defines the module's base behavior, while mixins provide additional reusable capabilities.

---

## 8. Writing Module Metadata

Metasploit modules contain descriptive information about their functionality.

This information is commonly configured inside the `initialize` method.

Example structure:

```ruby id="8svu0x"
def initialize(info = {})
  super(update_info(info,
    'Name' => 'Example Module',
    'Description' => %q{
      Description of the module.
    },
    'License' => MSF_LICENSE,
    'Author' => ['Researcher'],
    'Platform' => 'php',
    'Arch' => ARCH_PHP
  ))
end
```

### Important Metadata Fields

| Field | Description |
|---|---|
| `Name` | Name of the module |
| `Description` | Explanation of its functionality |
| `License` | Module license |
| `Author` | Researchers and module developers |
| `References` | CVEs, advisories, and external references |
| `Platform` | Target platform |
| `Arch` | Target architecture |
| `Targets` | Supported target configurations |
| `Privileged` | Whether the payload requires elevated privileges |
| `DisclosureDate` | Vulnerability disclosure date |
| `DefaultTarget` | Default target index |

### Module Reliability and Side Effects

The lesson also introduces a `Notes` section.

Example:

```ruby id="6eczhp"
'Notes' => {
  'SideEffects' => [IOC_IN_LOGS],
  'Reliability' => [REPEATABLE_SESSION],
  'Stability' => [CRASH_SAFE]
}
```

These fields describe expected operational characteristics.

- **IOC_IN_LOGS:** The activity may leave indicators in logs.
- **REPEATABLE_SESSION:** The exploit is expected to support repeatable session establishment.
- **CRASH_SAFE:** The exploit is classified as having low expected crash risk.

These are module classifications, not guarantees.

---

## 9. Registering Module Options

A Metasploit module may require configuration values before execution.

These are defined using `register_options`.

Example from the Bludit module:

```ruby id="qdba2o"
register_options(
  [
    OptString.new('TARGETURI', [
      true,
      'The base path for Bludit',
      '/'
    ]),
    OptString.new('BLUDITUSER', [
      true,
      'The username for Bludit'
    ]),
    OptString.new('BLUDITPASS', [
      true,
      'The password for Bludit'
    ])
  ]
)
```

### Understanding OptString

The general structure is:

```ruby id="h3xh14"
OptString.new('OPTION_NAME', [
  required,
  'Description',
  default_value
])
```

- **OPTION_NAME:** Name displayed in module options.
- **required:** Whether the option is mandatory.
- **Description:** Explanation of the setting.
- **default_value:** Optional default value.

These options appear when we execute:

```bash id="n1zcmz"
show options
```

### OptPath

The lesson also introduces `OptPath` for a filesystem path.

Example:

```ruby id="eyh3u9"
OptPath.new('PASSWORDS', [
  true,
  'The list of passwords',
  File.join(
    Msf::Config.data_directory,
    'wordlists',
    'passwords.txt'
  )
])
```

Here, the option expects a file path rather than a normal string setting.

`Msf::Config.data_directory` refers to Metasploit's configured data directory.

---

## 10. Bludit Example

The lesson uses **Bludit CMS** to illustrate how an existing module can serve as a template for adapting another exploit.

### Existing Module

```text id="h3d6ej"
bludit_upload_images_exec.rb
```

This module provides an existing example of a Bludit-related exploit.

The lesson then discusses adapting a separate proof of concept:

**Bludit 3.9.2 — Authentication Bruteforce Mitigation Bypass**

Reference: `CVE-2019-17240`

### Vulnerability Concept

The affected Bludit versions incorrectly trust client-controlled HTTP headers when identifying the user's IP address.

Relevant headers include:

```http id="70ocaj"
X-Forwarded-For
Client-IP
```

These headers can be used legitimately by reverse proxies, but applications must validate which upstream systems are trusted to supply them.

When untrusted clients can influence the identity used by login-attempt protections, IP-based rate limiting or account protections may be bypassed.

### Adapting the Module

The lesson demonstrates changes to:

1. Module name and description.
2. CVE and reference information.
3. Included mixins.
4. Supported targets.
5. Registered configuration options.
6. The original script's implementation.

The original code includes functionality for:

- Obtaining a CSRF token.
- Sending HTTP requests.
- Checking authentication responses.
- Reading candidate values from a wordlist.

The goal of porting is to integrate suitable script behavior with Metasploit's module architecture.

### Important Implementation Limitation

The final code shown in the lesson should be treated as an **illustrative porting example**, not a verified, fully functional Metasploit module.

It retains standalone-script structures and does not demonstrate a complete framework-compatible exploit entry point.

Additional adaptation and testing would be necessary.

---

## 11. Important Concepts

### PoC (Proof of Concept)

A **Proof of Concept** demonstrates that a vulnerability or technical behavior can exist or be reproduced.

A PoC is not necessarily production-ready or compatible with other frameworks.

### Boilerplate

**Boilerplate code** is a reusable starting structure that contains common declarations and configuration.

We can adapt an existing Metasploit module instead of implementing every framework integration from scratch.

### API (Application Programming Interface)

The Metasploit API defines classes, methods, and interfaces used by modules to interact with framework functionality.

Official documentation:

https://docs.metasploit.com/api/

### Ruby Mixins

Mixins allow classes to reuse methods from other Ruby modules without relying exclusively on class inheritance.

### Exploit vs. Module

An **exploit** is code or a technique used to take advantage of a vulnerability.

A **Metasploit module** is a component integrated into the framework that follows its structure and interface requirements.

Not every exploit is a Metasploit module.

---

## 12. Command Cheat Sheet

| Command | Purpose |
|---|---|
| `search nagios` | Search installed Metasploit modules |
| `searchsploit nagios3` | Search ExploitDB locally |
| `searchsploit -t Nagios3` | Search exploit titles |
| `searchsploit --exclude=".py" Nagios3` | Exclude Python files from matching results |
| `ls /usr/share/metasploit-framework/` | Inspect the framework directory |
| `ls ~/.msf4/` | Inspect user-specific Metasploit files |
| `mkdir -p <path>` | Create a directory hierarchy |
| `cp <source> <destination>` | Copy a module file |
| `msfconsole -m <directory>` | Start Metasploit using an additional module path |
| `loadpath <directory>` | Load modules from a directory |
| `reload_all` | Reload available modules |
| `use <module>` | Select a module |
| `show options` | Display module configuration options |
| `info` | Display information about the selected module |

---

## 13. Key Takeaways

1. We can extend Metasploit by importing or writing custom modules.
2. ExploitDB and SearchSploit help us locate public exploit implementations.
3. Ruby files are not necessarily Metasploit-compatible modules.
4. Modules must be placed in an appropriate directory hierarchy.
5. We can use `reload_all` or `loadpath` to make additional modules available.
6. Porting requires adapting standalone code to Metasploit's Ruby API.
7. Existing modules can serve as boilerplate for new development.
8. Mixins provide reusable functionality such as HTTP communication and reporting.
9. `initialize` defines module metadata and configuration.
10. `register_options` defines settings displayed through `show options`.
11. The Bludit example illustrates module structure and porting, rather than proving that the supplied final script is fully integrated.
12. Imported third-party modules should be inspected before execution, since they run code in our Metasploit environment.
