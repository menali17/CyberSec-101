# Cloud Resources

---

## Overview

Cloud resources are widely used by organizations to host applications, store files, and manage infrastructure.

Major cloud providers include:

- **AWS (Amazon Web Services):** S3 Buckets
- **Microsoft Azure:** Blob Storage
- **Google Cloud Platform (GCP):** Cloud Storage

Cloud providers secure their underlying infrastructure, but organizations are still responsible for properly configuring access permissions and their resources.

Misconfigurations may expose sensitive information to unauthorized users.

---

## Cloud Resource Discovery

During passive reconnaissance, we can identify cloud resources associated with a target organization.

### Google Dorking

Google Dorking uses advanced search operators to discover publicly indexed information.

Example:

```text
intext:"company" inurl:amazonaws.com
```

- `intext:` Searches for specific terms in page content.
- `inurl:` Searches for terms within URLs.

We can use these operators to identify publicly indexed files hosted on cloud platforms.

### Website Source Code

Web applications may reference cloud storage resources directly in their source code.

By inspecting HTML, JavaScript, and CSS references, we may discover cloud storage endpoints.

### Third-Party Services

- **domain.glass:** Provides information about domains and their infrastructure.
- **GrayHatWarfare:** Helps discover cloud storage resources and publicly indexed files.

---

## Cloud Misconfigurations

Cloud misconfigurations occur when resources are configured with inappropriate security settings.

One common example is an S3 bucket or Azure Blob container that unintentionally allows public access.

Potentially exposed information includes:

- Documents and internal files
- Backups
- Application source code
- Credentials
- SSH private keys

Publicly accessible resources are not necessarily vulnerable; the security issue arises when access is broader than intended.

---

## Exposed SSH Keys

SSH authentication can use public and private key pairs.

- `id_rsa`: Private key that must remain protected.
- `id_rsa.pub`: Public key that can be shared.

If a private SSH key is accidentally exposed through cloud storage, unauthorized individuals may attempt to use it to authenticate to systems that trust the corresponding public key.

Successful authentication still depends on the system's configuration and any additional security controls.

---

## Key Takeaways

- Cloud resources are part of an organization's external attack surface.
- Misconfigured access permissions can expose sensitive files.
- We can discover cloud resources through Google Dorking, website source code, and third-party services.
- DNS records may also reveal cloud infrastructure.
- Exposed SSH private keys can create serious security risks.
- We should identify and understand cloud resources during passive reconnaissance before proceeding with authorized active testing.

**Cloud security depends not only on the cloud provider but also on how organizations configure and protect their resources.**