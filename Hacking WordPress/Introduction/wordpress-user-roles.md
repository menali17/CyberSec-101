# WordPress User Roles

A standard WordPress installation contains five main user roles:

```text
Administrator
Editor
Author
Contributor
Subscriber
```

These roles define what each user is allowed to do inside the WordPress website.

---

## Administrator

The **Administrator** has access to administrative features of the website.

Capabilities include:

- Adding users
- Deleting users
- Creating posts
- Deleting posts
- Editing source code
- Managing administrative functionality

```text
Administrator = Highest privileges
```

Administrator access is usually the most valuable role when attempting to obtain code execution on the server.

---

## Editor

The **Editor** can:

- Publish posts
- Manage posts
- Edit posts created by other users

Editors do not have full administrative control.

However, they may have access to plugins or functionality that lower-privileged users cannot access.

---

## Author

The **Author** can:

- Create their own posts
- Publish their own posts
- Manage their own posts

They cannot normally manage the posts of other users.

```text
Author → Own posts only
```

---

## Contributor

The **Contributor** can:

- Write their own posts
- Manage their own posts

However, they cannot publish them.

```text
Contributor → Write but not publish
```

---

## Subscriber

The **Subscriber** is the most limited standard WordPress role.

Subscribers can generally:

- Browse posts
- Edit their own profile

```text
Subscriber = Basic user
```

---

# Role Hierarchy

A simplified privilege hierarchy is:

```text
Administrator
     ↓
Editor
     ↓
Author
     ↓
Contributor
     ↓
Subscriber
```

Higher roles have more permissions and access to more WordPress functionality.

---

# Security Perspective

Administrator access is usually needed to obtain direct code execution through WordPress functionality.

However:

```text
Editor
Author
```

may still be valuable if they have access to vulnerable plugins or other functionality unavailable to normal users.

---

# Main Takeaways

- WordPress has five standard user roles.
- Administrator has the highest privileges.
- Editor can manage and publish posts, including posts from other users.
- Author can publish and manage their own posts.
- Contributor can write posts but cannot publish them.
- Subscriber has very limited permissions.
- Higher-privileged roles may provide access to more attack surface.
- Administrator access is usually the most direct path toward code execution.

```text
Administrator → Full control
Editor        → Manage posts
Author        → Own published posts
Contributor   → Own unpublished posts
Subscriber    → Profile/basic access
```