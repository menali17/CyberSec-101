# Social Engineering

## Overview

**Social Engineering** targets the human element of security.

Instead of exploiting software vulnerabilities, it exploits:

* Human behavior
* Trust
* Emotions
* Habits
* Decision-making

The goal may be to obtain:

* Sensitive information
* Credentials
* System access
* Network access
* Physical access

Conceptually:

```text
Technical Attack
→ Exploits systems

Social Engineering
→ Exploits human behavior
```

---

# Psychological Principles

Social engineering commonly relies on several psychological triggers:

* Authority
* Urgency
* Fear
* Curiosity
* Trust

These principles can influence people into making decisions they would normally question.

---

## Authority

People are more likely to comply with someone they believe has authority.

Examples include impersonating:

* Executives
* IT staff
* Security personnel
* Managers

Conceptually:

```text
Perceived Authority
      ↓
Reduced Questioning
      ↓
Compliance
```

---

## Urgency

Urgency creates pressure and reduces the time available for careful thinking.

Example:

```text
"Your account will be disabled immediately."
```

This may cause someone to act before verifying the request.

---

## Fear

Fear can reduce critical thinking.

An attacker may create a scenario involving:

* Account suspension
* Security incidents
* Job-related consequences
* Financial loss

The victim may react quickly instead of verifying the situation.

---

## Curiosity

Curiosity can encourage people to interact with suspicious content.

Examples include:

* Unknown attachments
* Suspicious links
* USB devices

---

## Trust

Social engineers may build or abuse trust to obtain information or access.

Conceptually:

```text
Trust
  ↓
Reduced Suspicion
  ↓
Information / Access
```

---

# Common Social Engineering Techniques

## Phishing

**Phishing** uses deceptive messages that appear to come from legitimate sources.

The objective may be to make the victim:

* Reveal credentials
* Click a malicious link
* Open an attachment
* Provide sensitive information

Conceptually:

```text
Fake Message
    ↓
Victim Trusts It
    ↓
Performs Action
```

---

# Spear Phishing

**Spear Phishing** is a more targeted form of phishing.

Instead of sending generic messages, the attacker researches a specific individual or group.

```text
OSINT
  ↓
Target Information
  ↓
Personalized Message
  ↓
More Convincing Attack
```

The personalization can make the attack appear more legitimate.

---

# Pretexting

**Pretexting** involves creating a fabricated scenario to obtain information or access.

Example:

```text
"I am from IT.
We need your credentials for maintenance."
```

The attacker creates a believable context, or **pretext**, to justify the request.

Successful pretexting often requires good research and preparation.

---

# Baiting

**Baiting** exploits curiosity.

The attacker provides something that encourages the victim to interact with it.

The module gives the example of:

```text
Unknown USB Device
      ↓
Victim Becomes Curious
      ↓
Victim Uses Device
```

This can create an opportunity for compromise.

---

# Physical Social Engineering

Social engineering can also be used to gain physical access.

Examples include:

* Tailgating
* Impersonating delivery personnel
* Pretending to be a new employee
* Claiming to have forgotten an access card

Conceptually:

```text
Social Engineering
       ↓
Bypass Human Control
       ↓
Physical Access
```

---

# Tailgating

**Tailgating** occurs when an unauthorized person follows an authorized employee through a restricted entrance.

```text
Authorized Employee
       ↓
Door Opens
       ↓
Unauthorized Person Follows
```

The technique tests whether employees enforce physical access policies.

---

# Reconnaissance

A social engineering assessment usually begins with detailed reconnaissance.

The objective is to understand:

* Organizational structure
* Employees
* Internal processes
* Security practices
* Business context

A major source of information is:

```text
OSINT — Open Source Intelligence
```

---

# OSINT Sources

Useful public sources may include:

* Social media
* Company websites
* Professional networking sites
* Public records
* Industry publications

Conceptually:

```text
Public Information
      ↓
OSINT
      ↓
Target Knowledge
      ↓
More Convincing Scenario
```

---

# Developing Attack Scenarios

After gathering information, we can create realistic scenarios based on:

* Organizational weaknesses
* Security objectives
* Employee roles
* Realistic threats

A good scenario should be:

* Realistic
* Relevant
* Authorized
* Controlled

Conceptually:

```text
Reconnaissance
      ↓
Target Understanding
      ↓
Attack Scenario
      ↓
Controlled Test
```

---

# Documentation and Authorization

Before any social engineering test begins, activities must be clearly documented and explicitly authorized.

Important elements include:

* Scope
* Targets
* Allowed techniques
* Testing periods
* Restrictions
* Emergency procedures

```text
No Written Authorization
        ↓
No Social Engineering Test
```

This is especially important because social engineering directly involves real people.

---

# Ethical Considerations

Social engineering requires particularly strict ethical controls.

The test should avoid:

* Psychological harm
* Humiliation
* Harassment
* Unnecessary stress
* Privacy violations
* Damage to workplace relationships

The objective is to improve security awareness, not to embarrass employees.

---

# Psychological Impact

Social engineering manipulates emotions and behavior.

Poorly designed tests may create:

* Stress
* Fear
* Embarrassment
* Loss of trust

Therefore, scenarios must be carefully designed.

The core principle remains:

```text
Do No Harm
```

---

# Privacy

Social engineering may involve personal information.

We must protect:

* Employee data
* Personal details
* Credentials
* Information discovered during OSINT

Information should only be used as permitted by the engagement.

---

# Reveal Identity When Necessary

If a situation becomes dangerous or could cause harm, the tester should be prepared to immediately reveal that the activity is part of an authorized security assessment.

```text
Situation Becomes Unsafe
        ↓
Stop Testing
        ↓
Reveal Identity
        ↓
Prevent Harm
```

---

# Social Engineering Assessment Flow

A simplified process is:

```text
Authorization
     ↓
OSINT
     ↓
Target Research
     ↓
Scenario Development
     ↓
Controlled Execution
     ↓
Observe Response
     ↓
Document Results
     ↓
Improve Awareness
```

---

# Human Security Controls

Social engineering tests can evaluate whether employees:

* Verify requests
* Question suspicious messages
* Protect sensitive information
* Follow access procedures
* Report suspicious behavior

Conceptually:

```text
Suspicious Request
       ↓
Employee Response
       ↓
Security Procedure Followed?
```

---

# Key Takeaways

* Social engineering targets human behavior instead of technical vulnerabilities.
* Common psychological triggers include authority, urgency, fear, curiosity, and trust.
* Phishing uses deceptive messages to manipulate victims.
* Spear phishing targets specific individuals using personalized information.
* Pretexting uses fabricated scenarios.
* Baiting exploits curiosity.
* Physical social engineering may be used to gain unauthorized facility access.
* OSINT is important for researching targets.
* Realistic attack scenarios are built from reconnaissance.
* Written authorization is mandatory.
* Social engineering can create psychological and privacy risks.
* Tests must remain ethical, controlled, and professional.
* The principle **Do No Harm** is especially important.

---

# Pentesting Mindset

When planning a social engineering assessment, we should ask:

```text
What human behavior are we testing?

What information is publicly available?

Is the scenario realistic?

Is it explicitly authorized?

Could it cause psychological or professional harm?

Are we protecting personal information?

When should we stop the test?

How will the results improve security awareness?
```

A useful mental model is:

```text
Human Psychology
      +
OSINT
      +
Realistic Scenario
      +
Authorization
      +
Ethics
      =
Social Engineering Assessment
```

The goal is not to prove that employees can be tricked.

The goal is to identify weaknesses in human security controls and help the organization improve them.
