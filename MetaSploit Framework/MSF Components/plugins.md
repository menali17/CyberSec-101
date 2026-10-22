
# Plugins — Metasploit Framework

## Overview

Plugins extend the functionality of the Metasploit Framework by adding commands, integrations, and automation features.

Plugins can interact with the Framework through its APIs.

## Modules vs. Plugins

- **Modules:** Perform specific security assessment tasks, such as scanning or exploitation.
- **Plugins:** Extend Metasploit with additional functionality, integrations, or automation.

## Plugin Directory

```bash
ls /usr/share/metasploit-framework/plugins/
```

Metasploit plugins are commonly implemented as Ruby (`.rb`) files.

## Loading Plugins

Example — Nessus:

```text
load nessus
nessus_help
```

- `load`: Loads a plugin into MSFconsole.
- `nessus_help`: Displays commands provided by the Nessus plugin.

Loading an integration plugin does not automatically connect to the external service.

## Installing Plugins

Third-party plugins may be installed by placing compatible Ruby files in the appropriate plugin directory.

Always review the source and compatibility of third-party plugins before installation.

## Mixins

Mixins are a Ruby mechanism for sharing functionality between classes without requiring direct inheritance.

Ruby implements mixins using modules and `include`.

Example:

```ruby
module Logger
  def log(message)
    puts message
  end
end

class Scanner
  include Logger
end
```

## Key Takeaways

- Plugins extend Metasploit functionality.
- Modules perform specific assessment tasks.
- Plugins can introduce new MSFconsole commands.
- Third-party plugins should be reviewed before installation.
- Mixins provide reusable functionality in Ruby.
