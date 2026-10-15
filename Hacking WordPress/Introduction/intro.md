# WordPress Overview

WordPress is an open-source **Content Management System (CMS)** used to create and manage websites.

It can be used for:

- Blogs
- Forums
- E-commerce
- Project management
- Document management
- Other types of websites

WordPress is highly customizable and has a large ecosystem of:

- Themes
- Plugins

---

## Themes and Plugins

### Themes

Themes mainly control the visual appearance and layout of a WordPress website.

```text
Themes → Design / Appearance
```

### Plugins

Plugins add new functionality to the website.

Examples:

- WPForms → Contact forms
- MonsterInsights → Google Analytics integration
- Constant Contact → Email marketing

```text
Plugins → Additional functionality
```

---

## Security Considerations

WordPress is highly extensible, but this also increases its attack surface.

A WordPress website may be vulnerable even if the WordPress core itself is updated.

Possible vulnerable components include:

- Plugins
- Themes
- Misconfigurations
- User accounts

Example:

```text
Updated WordPress
+
Vulnerable Plugin
=
Vulnerable Website
```

Third-party plugins and themes are especially important during enumeration because they may introduce vulnerabilities.

---

## WordPress Technology Stack

WordPress is written in:

```text
PHP
```

A common WordPress environment uses:

```text
Web Server → Apache
Application → PHP / WordPress
Database → MySQL
```

Simplified architecture:

```text
User
  ↓
Apache
  ↓
PHP / WordPress
  ↓
MySQL
```

---

# What is a CMS?

A **CMS — Content Management System** allows users to create and manage websites without needing to manually write all of the underlying code.

The CMS handles much of the website infrastructure so users can focus on:

- Content
- Design
- Presentation
- Media

---

## WYSIWYG

Many CMS platforms provide a:

```text
WYSIWYG
```

which stands for:

```text
What You See Is What You Get
```

This allows users to edit website content similarly to using a word processor.

Users can:

- Write text
- Format content
- Upload images
- Upload videos
- Manage media

without directly editing the website code.

---

# CMS Components

A CMS is composed of two main components:

```text
CMA
CDA
```

---

## CMA — Content Management Application

The **CMA** is the interface used to create and manage content.

Example:

```text
Login to WordPress
        ↓
Create a post
        ↓
Add title
        ↓
Add image
        ↓
Publish
```

The CMA is the part used by administrators and content creators.

```text
CMA = Manage Content
```

---

## CDA — Content Delivery Application

The **CDA** is the backend responsible for taking the content entered through the CMA and assembling it into the website shown to visitors.

Example:

```text
Admin uses CMA
      ↓
Creates content
      ↓
CDA processes the content
      ↓
Website is displayed
```

```text
CDA = Deliver Content
```

---

# CMS Features

A good CMS should provide features such as:

- Extensibility
- User management
- Media management
- Version control
- Security settings
- Regular updates and upgrades

---

## Extensibility

The CMS should allow additional functionality and design elements to be added without directly modifying the website code.

In WordPress, this is mainly done through:

```text
Plugins
Themes
```

---

## User Management

A CMS should provide fine-grained control over:

- Users
- Roles
- Permissions
- Access levels

---

## Media Management

Users should be able to easily upload and manage:

- Images
- Videos
- Other media files

without directly interacting with the web server.

---

## Security and Maintenance

When choosing or evaluating a CMS, important factors include:

- Active maintenance
- Regular updates
- Security updates
- Built-in security settings

---

# Main Takeaways

- WordPress is a CMS.
- WordPress is written in PHP.
- It commonly runs with Apache and MySQL.
- Themes mainly control appearance.
- Plugins add functionality.
- Third-party plugins and themes can introduce vulnerabilities.
- A CMS allows users to manage websites without writing everything from scratch.
- CMA is responsible for content management.
- CDA is responsible for delivering the content.
- WordPress extensibility increases its attack surface.

```text
CMA = Manage
CDA = Deliver
```

```text
Themes  = Appearance
Plugins = Functionality
```