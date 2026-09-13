# Reverse Engineering

## Overview

**Reverse Engineering** is the process of analyzing software, systems, or applications to understand how they work internally.

Instead of starting with source code and building a program, we start with the final product and work backward.

```text
Forward Engineering:
Requirements
    ↓
Source Code
    ↓
Compiled Program

Reverse Engineering:
Compiled Program
    ↓
Analysis
    ↓
Understand Internal Logic
```

This is especially useful when:

* Source code is unavailable
* Documentation is missing
* Security mechanisms need to be understood
* Vulnerabilities need to be identified
* Custom exploitation techniques need to be developed

---

# Required Foundations

Reverse engineering requires knowledge across several technical areas.

Important foundations include:

* Programming languages
* Assembly language
* Computer architecture
* Operating system internals
* Memory management
* Software design
* Data structures and algorithms
* Networking protocols
* APIs

Common programming languages include:

```text
C / C++
Java
Swift
Kotlin
```

---

# Computer Architecture and Assembly

Compiled programs ultimately execute as **machine code**.

Conceptually:

```text
Source Code
    ↓
Compiler
    ↓
Machine Code
    ↓
CPU Execution
```

Machine code contains instructions that the processor executes directly.

Because of this, understanding:

```text
Assembly Language
+
CPU Architecture
```

is fundamental for low-level reverse engineering.

---

# Memory Layout

Understanding memory is also important.

Two major memory areas are:

* Stack
* Heap

---

## Stack

The **stack** is commonly used for:

* Function calls
* Local variables
* Execution flow

Conceptually:

```text
Function Call
     ↓
Stack Frame
     ↓
Local Variables
     ↓
Return
```

Understanding the stack is especially important when analyzing low-level vulnerabilities and program execution.

---

## Heap

The **heap** is primarily used for:

```text
Dynamic Memory Allocation
```

Programs request and release memory from the heap during runtime.

```text
Program
   ↓
Request Memory
   ↓
Heap
   ↓
Dynamic Allocation
```

---

# Reverse Engineering Tools

There are three important categories of tools:

1. Disassemblers
2. Debuggers
3. Decompilers

---

# Disassemblers

A **disassembler** converts machine code into assembly instructions.

```text
Machine Code
     ↓
Disassembler
     ↓
Assembly
```

Common tools include:

* IDA Pro
* Ghidra
* Radare2

Disassembly gives us a low-level representation of what the processor executes.

---

# Debuggers

A **debugger** allows us to observe program execution in real time.

Common tools include:

* GDB
* WinDbg
* x64dbg

Debuggers allow us to:

* Set breakpoints
* Step through instructions
* Inspect registers
* Inspect memory
* Observe function calls
* Analyze runtime behavior

Conceptually:

```text
Run Program
     ↓
Pause at Breakpoint
     ↓
Inspect State
     ↓
Continue Execution
```

---

# Decompilers

A **decompiler** attempts to reconstruct higher-level source code from compiled code.

```text
Compiled Program
       ↓
Decompiler
       ↓
Approximate High-Level Code
```

Examples include:

* DNSpy
* ILSpy
* JADX

Decompiled code is not always identical to the original source code, but it can make analysis much easier.

---

# Disassembler vs Debugger vs Decompiler

A simple distinction is:

```text
Disassembler
→ Machine Code → Assembly

Debugger
→ Observe Program While Running

Decompiler
→ Compiled Code → Approximate Source Code
```

This distinction is very important.

---

# Static Analysis

**Static Analysis** means analyzing a program without executing it.

We may inspect:

* Functions
* Variables
* Strings
* Program structure
* Control flow
* Embedded resources

Conceptually:

```text
Program
   ↓
Inspect Without Running
   ↓
Understand Structure
```

Static analysis is useful for getting a broad understanding of the application.

---

# Dynamic Analysis

**Dynamic Analysis** means running the program and observing its behavior.

We may analyze:

* Memory usage
* Function calls
* Program flow
* Runtime values
* Security checks
* Encryption behavior

Conceptually:

```text
Run Program
    ↓
Observe Execution
    ↓
Inspect Behavior
```

Dynamic analysis is particularly useful for understanding behavior that may be difficult to determine statically.

---

# Static vs Dynamic Analysis

```text
Static Analysis
→ Analyze without execution

Dynamic Analysis
→ Analyze during execution
```

The two approaches complement each other.

```text
Static
   +
Dynamic
   ↓
Better Understanding
```

---

# Malware Analysis

Reverse engineering is commonly used for:

```text
Malware Analysis
```

We may investigate:

* What the malware does
* Which files it modifies
* Which systems it contacts
* How it maintains persistence
* Which techniques it uses
* How detection can be improved

Conceptually:

```text
Malware Sample
      ↓
Static / Dynamic Analysis
      ↓
Understand Behavior
```

---

# Authentication Bypass

Reverse engineering may reveal weaknesses in authentication logic.

Examples include:

* Hardcoded credentials
* Weak validation
* Client-side authentication checks
* Incorrect security logic

Conceptually:

```text
Authentication Logic
        ↓
Reverse Engineering
        ↓
Weak Validation Found
        ↓
Potential Bypass
```

---

# Protocol Analysis

Some applications use custom communication protocols.

Reverse engineering can help us understand:

* Message formats
* Commands
* Authentication
* Data structures
* Communication logic

Conceptually:

```text
Unknown Protocol
      ↓
Analyze Traffic / Code
      ↓
Understand Protocol
      ↓
Create Custom Testing Tools
```

---

# Mobile Reverse Engineering

Reverse engineering is particularly useful in mobile security testing.

For Android, we may analyze:

```text
APK
 ↓
JADX
 ↓
Java-like Code
```

For mobile environments, we should also understand:

* Android architecture
* iOS architecture
* Application sandboxing
* Code signing
* Encryption
* Platform security mechanisms

---

# Anti-Reverse Engineering Techniques

Modern applications may include protections designed to make analysis harder.

Examples include:

* Code obfuscation
* Packed executables
* Anti-debugging techniques

---

## Code Obfuscation

**Obfuscation** makes code intentionally harder to understand.

For example:

```text
Readable Code
    ↓
Obfuscation
    ↓
Hard-to-Understand Code
```

The program still works, but analysis becomes more difficult.

---

## Packed Executables

A **packer** can compress or transform executable code so that the original code becomes harder to inspect directly.

Conceptually:

```text
Original Binary
      ↓
Packing
      ↓
Protected Binary
```

The program may unpack itself during runtime.

---

## Anti-Debugging

Applications may attempt to detect whether they are running inside a debugger.

```text
Program
   ↓
Debugger Detected?
   ↓
Modify / Stop Behavior
```

Understanding anti-debugging mechanisms is important during advanced reverse engineering.

---

# Platform Differences

Different platforms require different approaches.

Examples include:

```text
Desktop
Mobile
Embedded Systems
```

Each environment may use different:

* Architectures
* File formats
* Security mechanisms
* Tools
* Protections

---

# Reverse Engineering Workflow

A simplified workflow is:

```text
Target Binary / Application
          ↓
Identify Platform
          ↓
Static Analysis
          ↓
Strings / Functions / Structure
          ↓
Disassembly / Decompilation
          ↓
Dynamic Analysis
          ↓
Debugger / Runtime Observation
          ↓
Understand Logic
          ↓
Identify Security Weaknesses
```

---

# Key Takeaways

* Reverse engineering analyzes a finished product to understand how it works.
* It is useful when source code or documentation is unavailable.
* Programming knowledge helps us understand application logic.
* Assembly and computer architecture are fundamental for low-level analysis.
* The stack manages function calls and local variables.
* The heap is used for dynamic memory allocation.
* Disassemblers convert machine code into assembly.
* Debuggers allow us to observe execution in real time.
* Decompilers attempt to reconstruct high-level source code.
* Static analysis examines software without running it.
* Dynamic analysis examines software during execution.
* Reverse engineering is useful for malware analysis, authentication bypass, and protocol analysis.
* Modern software may use obfuscation, packing, and anti-debugging techniques.
* Different platforms require different tools and knowledge.

---

# Pentesting Mindset

When reverse engineering software, we should ask:

```text
What platform is this application built for?

Which language was probably used?

Can we decompile or disassemble it?

Which functions look interesting?

Are there hardcoded secrets?

How is authentication implemented?

What happens when the program runs?

Which values exist only at runtime?

Does the application use obfuscation?

Does it detect debugging?

Does it communicate with external services?

Can we understand or reproduce its protocol?
```

A useful mental model is:

```text
Binary / Application
        ↓
Static Analysis
        ↓
Understand Structure
        ↓
Dynamic Analysis
        ↓
Understand Behavior
        ↓
Security Weaknesses
```

Reverse engineering is essentially about turning **unknown implementation into understandable logic**.
