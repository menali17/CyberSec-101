
# Physical Security Testing

## Overview

**Physical Security Penetration Testing** evaluates whether an organization's physical security controls can prevent unauthorized access to facilities, systems, and sensitive assets.

The goal is to identify weaknesses in:

* Physical barriers
* Access controls
* Security procedures
* Employee behavior
* Surveillance
* Restricted areas

Conceptually:

```text
Physical Security Controls
        ↓
Attempted Bypass
        ↓
Unauthorized Access Possible?
        ↓
Real-World Impact
```

---

# Main Areas of Physical Security

Physical security testing may evaluate:

* Building perimeters
* Fences
* Gates
* Doors
* Windows
* Security checkpoints
* Restricted areas
* Sensitive asset storage
* Surveillance systems

The objective is to determine whether these controls provide effective protection.

---

# Perimeter Security

The outer perimeter is often the first layer of defense.

We may evaluate:

* Fences
* Walls
* Gates
* Lighting
* Cameras
* Blind spots
* Intrusion detection systems

Conceptually:

```text
Outside
  ↓
Perimeter
  ↓
Access Controls
  ↓
Internal Areas
```

Weaknesses at the perimeter may provide opportunities for unauthorized entry.

---

# Surveillance

Security cameras are an important physical control.

We may assess:

* Camera placement
* Coverage
* Blind spots
* Visibility
* Monitoring effectiveness

A security system may appear complete while still containing areas with little or no surveillance.

---

# Access Control Systems

Physical access controls may include:

* Key cards
* Biometric readers
* PIN pads
* Mechanical locks
* Visitor management systems

The assessment examines both:

```text
Technical Security
        +
Operational Implementation
```

A strong access control technology can still fail if procedures are poorly followed.

---

# Tailgating

**Tailgating** occurs when an unauthorized person enters a restricted area by following an authorized person through an access-controlled entrance.

Conceptually:

```text
Authorized Employee
        ↓
Access Granted
        ↓
Unauthorized Person Follows
```

This tests whether personnel actively enforce access control procedures.

---

# Security Personnel

Security guards and reception staff are part of the organization's security controls.

Testing may evaluate whether they:

* Verify credentials
* Challenge suspicious behavior
* Follow access policies
* Follow visitor procedures
* Escalate incidents correctly

Human procedures are often just as important as technical controls.

---

# Physical Security Reconnaissance

Physical security assessments usually begin with information gathering.

This may include:

* OSINT
* Public information
* Satellite imagery
* Social media
* Publicly available building information

Conceptually:

```text
Public Information
      ↓
OSINT
      ↓
Facility Knowledge
      ↓
Testing Plan
```

---

# On-Site Observation

Authorized testers may also observe the facility directly.

Information may include:

* Camera locations
* Guard patrol patterns
* Employee behavior
* Entry and exit patterns
* Security procedures

Observations may occur at different times because security conditions can change throughout the day.

---

# Physical Security Testing Workflow

A simplified process is:

```text
Authorization
     ↓
OSINT
     ↓
Facility Observation
     ↓
Identify Physical Controls
     ↓
Identify Weaknesses
     ↓
Authorized Testing
     ↓
Document Results
     ↓
Report Findings
```

---

# Social Engineering

Social engineering is often an important part of physical security testing.

Testers may simulate legitimate roles such as:

* Delivery personnel
* Maintenance workers
* Visitors

The goal is to evaluate whether employees properly verify identity and follow security procedures.

```text
Social Engineering Attempt
        ↓
Employee Response
        ↓
Security Procedure Followed?
```

The purpose is to identify training and procedural weaknesses.

---

# Lock Security

Physical assessments may include evaluating locks and their implementation.

This can involve examining:

* Lock type
* Installation quality
* Physical resistance
* Bypass risk

Any lock manipulation must remain within explicit authorization and should be performed by qualified professionals.

---

# Electronic Access Systems

Modern physical security frequently relies on electronic systems.

Examples include:

* RFID cards
* Access control panels
* Electronic door systems

The assessment may evaluate whether these systems are vulnerable to misuse or unauthorized duplication.

---

# Human and Technical Security

Physical security depends on both technology and people.

```text
Physical Security
      =
Technology
      +
Procedures
      +
People
```

For example:

```text
Secure Door
   +
Strong Access Card
   +
Poor Employee Verification
   =
Possible Security Failure
```

---

# Legal Authorization

Physical penetration testing requires explicit written authorization.

We must know:

* Which locations are in scope
* Which techniques are permitted
* Which areas are prohibited
* Testing timeframes
* Emergency contacts

Without proper authorization:

```text
No Authorization
      ↓
No Physical Testing
```

---

# Get Out of Jail Free Letter

During physical assessments, testers should carry written proof that the engagement is authorized.

This documentation should contain:

* Client authorization
* Scope
* Testing dates and times
* Emergency contacts
* Engagement details

If confronted by security personnel or law enforcement, the document can confirm that the activity is part of an authorized security assessment.

---

# Safety

Physical security testing must not create unnecessary risk to:

* People
* Property
* Business operations

The principle remains:

```text
Do No Harm
```

Physical testing requires particularly careful planning because mistakes can involve real people and physical environments.

---

# Documentation

Both successful and unsuccessful testing attempts should be documented.

Useful information may include:

* Time
* Location
* Technique attempted
* Security control encountered
* Result
* Evidence

Conceptually:

```text
Attempt
  ↓
Result
  ↓
Evidence
  ↓
Finding
```

---

# Common Physical Security Weaknesses

Examples may include:

* Poor perimeter security
* Camera blind spots
* Weak visitor procedures
* Tailgating opportunities
* Improperly secured doors
* Weak identity verification
* Poor security awareness
* Weak electronic access control implementation

---

# Key Takeaways

* Physical security testing evaluates barriers, controls, procedures, and personnel.
* Perimeter security is the first physical layer of defense.
* Cameras may still leave blind spots.
* Access control systems include cards, biometrics, PINs, and locks.
* Human behavior can undermine otherwise strong technical controls.
* Tailgating tests whether access procedures are properly enforced.
* OSINT and on-site observation can support physical security reconnaissance.
* Social engineering is often part of physical security testing.
* Electronic access systems may introduce additional weaknesses.
* Physical testing requires explicit written authorization.
* A Get Out of Jail Free letter provides evidence of authorization during the engagement.
* Safety and the principle of **Do No Harm** remain essential.

---

# Pentesting Mindset

When assessing physical security, we should ask:

```text
How is the facility protected?

Where are the entry points?

Which areas are restricted?

How is identity verified?

Do employees challenge unauthorized access?

Are there camera blind spots?

Can authorized access be abused?

Are visitor procedures actually enforced?

Which physical systems depend on human behavior?

What are we explicitly authorized to test?
```

A useful mental model is:

```text
Perimeter
   ↓
Entry Point
   ↓
Access Control
   ↓
Personnel
   ↓
Restricted Area
   ↓
Sensitive Asset
```

Physical security testing examines whether any weakness along this path could allow unauthorized access.
