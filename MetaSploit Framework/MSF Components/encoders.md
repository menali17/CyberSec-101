
# Encoders — Metasploit Framework

## Overview

Encoders transform the representation of payloads while preserving their intended functionality.

Their primary purposes include:

- Avoiding bad characters.
- Adapting payload representations to exploitation constraints.
- Historically, changing byte patterns to complicate signature-based detection.

Encoding does not automatically make a payload undetectable or compatible with another CPU architecture.

## Bad Characters

Bad characters are bytes that interfere with payload delivery or execution.

Example:

`\x00` — Null byte.

In some C string operations, a null byte terminates the string and may truncate the payload.

Encoders can help avoid restricted bytes by transforming the payload and including decoding logic.

## Shikata Ga Nai

`x86/shikata_ga_nai`

Shikata Ga Nai is a polymorphic XOR-based encoder for x86 payloads.

Polymorphic encoding can produce different encoded representations while preserving the original functionality.

Modern security solutions can still detect encoded payloads.

## MSFvenom

MSFvenom combines functionality previously provided by:

- `msfpayload`
- `msfencode`

Useful commands:

```bash
msfvenom --help
msfvenom --list encoders
```

### Common Parameters

- `-a`: Architecture.
- `--platform`: Target platform.
- `-p`: Payload.
- `-b`: Bad characters.
- `-f`: Output format.
- `-e`: Encoder.
- `-i`: Encoding iterations.
- `-o`: Output file.

## Listing Compatible Encoders

Inside a selected Metasploit exploit:

```text
show encoders
```

Available encoders depend on the selected module, payload, and architecture.

## Encoding vs. Encryption

Encoding transforms data representation and may include a decoder stub.

Encryption protects information using cryptographic mechanisms.

Encoding alone does not provide confidentiality.

## Antivirus Detection

Repeated encoding does not guarantee antivirus evasion.

Modern defensive tools may use static analysis, behavioral detection, and other methods.

## Key Takeaways

- Encoders transform payload representations.
- Bad characters can disrupt exploitation.
- Shikata Ga Nai is an x86 polymorphic encoder.
- MSFvenom provides payload generation and encoding capabilities.
- Encoding is different from encryption.
- Encoding does not guarantee bypassing modern security controls.
