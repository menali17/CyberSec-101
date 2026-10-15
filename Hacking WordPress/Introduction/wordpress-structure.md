# WordPress Structure

A default WordPress installation on Linux commonly runs on a **LAMP stack**:

```text
Linux
Apache
MySQL
PHP
```

The WordPress files are commonly located in the webroot:

```text
/var/www/html
```

---

## Default WordPress File Structure

A default installation contains files and directories such as:

```text
index.php
license.txt
readme.html
wp-activate.php
wp-admin/
wp-blog-header.php
wp-comments-post.php
wp-config.php
wp-config-sample.php
wp-content/
wp-cron.php
wp-includes/
wp-links-opml.php
wp-load.php
wp-login.php
wp-mail.php
wp-settings.php
wp-signup.php
wp-trackback.php
xmlrpc.php
```

---

# Key WordPress Files

## index.php

```text
index.php
```

Acts as the WordPress homepage entry point.

---

## license.txt

```text
license.txt
```

May contain useful information, including details about the installed WordPress version.

---

## wp-activate.php

```text
wp-activate.php
```

Used during the email activation process when setting up a new WordPress site.

---

## wp-admin

```text
wp-admin/
```

Contains administrative components and backend dashboard functionality.

After authentication, users can manage the website according to their assigned permissions.

Possible login paths include:

```text
/wp-admin/login.php
/wp-admin/wp-login.php
/login.php
/wp-login.php
```

The login page can also be renamed.

---

## xmlrpc.php

```text
xmlrpc.php
```

Provides a WordPress communication mechanism using:

```text
HTTP → transport
XML  → encoding
```

This type of communication has largely been replaced by the WordPress REST API.

---

# WordPress Configuration File

## wp-config.php

```text
wp-config.php
```

Contains important WordPress configuration information.

It may include:

- Database name
- Database username
- Database password
- Database host
- Authentication keys
- Authentication salts
- Database table prefix
- Debug configuration

Example:

```php
define( 'DB_NAME', 'database_name_here' );
define( 'DB_USER', 'username_here' );
define( 'DB_PASSWORD', 'password_here' );
define( 'DB_HOST', 'localhost' );
```

---

## Authentication Keys and Salts

The configuration file also contains authentication keys and salts.

Examples:

```php
AUTH_KEY
SECURE_AUTH_KEY
LOGGED_IN_KEY
NONCE_KEY

AUTH_SALT
SECURE_AUTH_SALT
LOGGED_IN_SALT
NONCE_SALT
```

These values are used by WordPress for authentication-related security.

---

## Database Table Prefix

The default database table prefix is commonly:

```php
$table_prefix = 'wp_';
```

This means WordPress tables may have names such as:

```text
wp_users
wp_posts
wp_options
```

---

## Debug Mode

WordPress debugging can be controlled using:

```php
define( 'WP_DEBUG', false );
```

Possible values:

```text
false → debugging disabled
true  → debugging enabled
```

Debug mode can be useful for troubleshooting.

---

# Key WordPress Directories

## wp-content

```text
wp-content/
```

This directory stores important website-specific content.

Common subdirectories include:

```text
wp-content/
├── plugins/
├── themes/
└── uploads/
```

### plugins

```text
wp-content/plugins/
```

Contains installed WordPress plugins.

Plugins may introduce:

- Additional functionality
- Misconfigurations
- Vulnerabilities

---

### themes

```text
wp-content/themes/
```

Contains installed WordPress themes.

Themes control the appearance and design of the website.

Themes may also contain vulnerable or outdated code.

---

### uploads

```text
wp-content/uploads/
```

Usually contains files uploaded through WordPress.

Examples:

- Images
- Documents
- Media files

These files should be carefully enumerated because they may contain sensitive data or reveal misconfigurations.

---

## wp-includes

```text
wp-includes/
```

Contains many of the core WordPress files.

Examples include:

- JavaScript files
- Fonts
- Certificates
- Widgets
- Core PHP files

Example files:

```text
theme.php
update.php
user.php
vars.php
version.php
widgets.php
wp-db.php
wp-diff.php
```

---

# Important Structure

A simplified WordPress structure can be remembered as:

```text
WordPress
├── wp-admin/
│   └── Administrative functionality
│
├── wp-content/
│   ├── plugins/
│   ├── themes/
│   └── uploads/
│
├── wp-includes/
│   └── WordPress core files
│
└── wp-config.php
    └── Sensitive configuration
```

---

# Main Takeaways

- WordPress commonly runs on a LAMP stack.
- The default webroot may be `/var/www/html`.
- `wp-admin` contains administrative functionality.
- `wp-config.php` contains sensitive configuration information.
- `wp-content` stores plugins, themes, and uploads.
- `wp-includes` contains WordPress core files.
- `xmlrpc.php` provides XML-based communication over HTTP.
- Plugins and themes are especially important during enumeration.
- `wp-config.php` is one of the most sensitive files in a WordPress installation.

```text
wp-admin     → Administration
wp-content   → Plugins, themes, uploads
wp-includes  → Core files
wp-config.php → Sensitive configuration
```