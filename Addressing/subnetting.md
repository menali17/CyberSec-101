# Subnetting

## Overview

**Subnetting** is the process of dividing an IPv4 network into smaller networks called **subnets**.

A subnet is a logical segment of a network in which devices share the same network address.

When analyzing a subnet, we usually want to determine:

- `Network Address`
- `Broadcast Address`
- `First Host`
- `Last Host`
- `Number of Hosts`

---

## Network Part and Host Part

An IPv4 address is divided into two parts:

- **Network Part:** identifies the network.
- **Host Part:** identifies a device inside that network.

The **Subnet Mask**, or its CIDR notation, determines where this separation occurs.

Example:

```text
IPv4 Address: 192.168.12.160
Subnet Mask:  255.255.255.192
CIDR:         /26
```

An IPv4 address has `32 bits`.

Therefore:

```text
/26

26 bits → Network
 6 bits → Host
```

The `/26` subnet mask in binary is:

```text
11111111.11111111.11111111.11000000
```

And in decimal:

```text
255.255.255.192
```

The `1` bits represent the **Network Part**, while the `0` bits represent the **Host Part**.

```text
11111111.11111111.11111111.11 | 000000
              Network              Host
```

---

## Network Address

The **Network Address** identifies the subnet itself.

To find it, all bits in the **Host Part** are set to `0`.

For:

```text
192.168.12.160/26
```

The last octet (`160`) in binary is:

```text
160 = 10100000
```

With `/26`, it is divided as:

```text
10 | 100000
     └────── Host Part
```

Setting all Host bits to `0`:

```text
10 | 000000
```

Which is `128` in decimal.

Therefore:

```text
Network Address = 192.168.12.128
```

---

## Broadcast Address

The **Broadcast Address** is used to reach all hosts inside the subnet.

To find it, all bits in the **Host Part** are set to `1`.

```text
10 | 111111
```

This equals `191` in decimal.

Therefore:

```text
Broadcast Address = 192.168.12.191
```

---

## Host Range

A `/26` leaves `6 bits` for hosts.

The total number of addresses is:

```text
2^6 = 64
```

The Network Address and Broadcast Address are reserved, so:

```text
64 - 2 = 62 usable hosts
```

For `192.168.12.160/26`:

| Type | IPv4 Address |
|---|---|
| Network Address | `192.168.12.128` |
| First Host | `192.168.12.129` |
| Last Host | `192.168.12.190` |
| Broadcast Address | `192.168.12.191` |

---

## Finding the Subnet Range

A `/26` creates blocks containing `64 addresses`.

The last octet is therefore divided into:

```text
0   - 63
64  - 127
128 - 191
192 - 255
```

Our IP is:

```text
192.168.12.160
```

Since `160` is inside the range:

```text
128 - 191
```

we know:

```text
Network Address:   192.168.12.128
Broadcast Address: 192.168.12.191
```

---

## Subnetting Into Smaller Networks

A subnet can be divided into even smaller subnets.

For example, suppose we have:

```text
192.168.12.128/26
```

and need to divide it into `4 subnets`.

Since:

```text
2^2 = 4
```

we need `2 additional network bits`.

Therefore:

```text
/26 → /28
```

A `/28` leaves:

```text
32 - 28 = 4 Host bits
```

Each subnet contains:

```text
2^4 = 16 addresses
```

With:

```text
16 - 2 = 14 usable hosts
```

The original `/26` is divided into:

| Subnet | Network Address | First Host | Last Host | Broadcast |
|---|---|---|---|---|
| 1 | `192.168.12.128/28` | `192.168.12.129` | `192.168.12.142` | `192.168.12.143` |
| 2 | `192.168.12.144/28` | `192.168.12.145` | `192.168.12.158` | `192.168.12.159` |
| 3 | `192.168.12.160/28` | `192.168.12.161` | `192.168.12.174` | `192.168.12.175` |
| 4 | `192.168.12.176/28` | `192.168.12.177` | `192.168.12.190` | `192.168.12.191` |

---

## Mental Subnetting

Knowing powers of two makes subnetting much faster.

| CIDR | Host Bits | Total Addresses |
|---|---:|---:|
| `/24` | 8 | 256 |
| `/25` | 7 | 128 |
| `/26` | 6 | 64 |
| `/27` | 5 | 32 |
| `/28` | 4 | 16 |
| `/29` | 3 | 8 |
| `/30` | 2 | 4 |

A useful pattern to remember is:

```text
/24 → 256
/25 → 128
/26 → 64
/27 → 32
/28 → 16
/29 → 8
/30 → 4
```

Each time the CIDR increases by `1`, the size of the subnet is divided by `2`.

### Example: /25

For:

```text
192.168.1.1/25
```

There are:

```text
32 - 25 = 7 Host bits

2^7 = 128 addresses
```

Therefore, the last octet is divided into two ranges:

```text
0   - 127
128 - 255
```

First subnet:

```text
Network:    192.168.1.0
First Host: 192.168.1.1
Last Host:  192.168.1.126
Broadcast:  192.168.1.127
```

Second subnet:

```text
Network:    192.168.1.128
First Host: 192.168.1.129
Last Host:  192.168.1.254
Broadcast:  192.168.1.255
```

## CIDR to Subnet Mask Conversion

CIDR notation indicates how many bits of an IPv4 address belong to the **network portion**.

An IPv4 address contains **32 bits**, divided into four octets of 8 bits each.

```text
8 bits      8 bits      8 bits      8 bits
--------    --------    --------    --------
1st octet   2nd octet   3rd octet   4th octet

/8          /16         /24         /32
```

Each complete octet containing network bits is represented as `255`.

To calculate a partially filled octet, use the following binary values:

```text
128  64  32  16  8  4  2  1
```

The values corresponding to the network bits are added together.

---

### Example: /28

A `/28` prefix means that **28 bits belong to the network**.

The first 24 bits fill three complete octets:

```text
255.255.255.?
```

There are 4 remaining network bits:

```text
28 - 24 = 4
```

Therefore, the last octet contains four `1` bits:

```text
128  64  32  16  8  4  2  1
 1    1   1   1   0  0  0  0
```

Adding the values:

```text
128 + 64 + 32 + 16 = 240
```

Therefore:

```text
/28 = 255.255.255.240
```

---

### Example: /19

A `/19` prefix means that **19 bits belong to the network**.

The first 16 bits fill two complete octets:

```text
255.255.?.0
```

There are 3 remaining network bits:

```text
19 - 16 = 3
```

Therefore, the third octet contains three `1` bits:

```text
128  64  32  16  8  4  2  1
 1    1   1   0   0  0  0  0
```

Adding the values:

```text
128 + 64 + 32 = 224
```

Therefore:

```text
/19 = 255.255.224.0
```

---

### Quick Reference

| CIDR Range | Partial Octet |
|---|---|
| `/1 - /8` | 1st octet |
| `/9 - /16` | 2nd octet |
| `/17 - /24` | 3rd octet |
| `/25 - /32` | 4th octet |

---

### Key Idea

The same process can be used for any CIDR prefix:

1. Identify how many complete 8-bit octets exist.
2. Subtract those bits from the CIDR prefix.
3. Place the remaining network bits in the next octet.
4. Add the corresponding values: `128, 64, 32, 16, 8, 4, 2, 1`.
5. Complete octets before it become `255`.
6. Octets after it become `0`.

For example:

```text
/21

21 - 16 = 5

128 + 64 + 32 + 16 + 8 = 248

/21 = 255.255.248.0
```

## Finding the Broadcast Address

The **broadcast address** is the last IP address of a subnet. It is used to send traffic to all hosts within that subnet.

To calculate the broadcast address, we first need to determine the number of addresses available in the subnet.

### Step 1 - Find the Number of Host Bits

An IPv4 address contains **32 bits**.

Subtract the CIDR prefix from 32:

```text
Host Bits = 32 - CIDR
```

For example, with `/26`:

```text
32 - 26 = 6 host bits
```

---

### Step 2 - Calculate the Block Size

The total number of addresses in the subnet is:

```text
2^(Host Bits)
```

For `/26`:

```text
2^6 = 64 addresses
```

Therefore, each `/26` subnet contains **64 total IP addresses**.

---

### Step 3 - Find the Broadcast Address

The broadcast address is the **last address of the subnet**.

A useful formula is:

```text
Broadcast = Network Address + Block Size - 1
```

For example:

```text
192.168.10.64/26
```

We already know:

```text
Block Size = 64
Network Address = 192.168.10.64
```

Therefore:

```text
64 + 64 - 1 = 127
```

So:

```text
Network Address:   192.168.10.64
First Host:        192.168.10.65
Last Host:         192.168.10.126
Broadcast Address: 192.168.10.127
```

---

### Understanding the `-1`

The network address itself counts as the **first address** of the block.

For a block containing 64 addresses starting at `.64`:

```text
1st address  = .64
2nd address  = .65
3rd address  = .66
...
64th address = .127
```

This is why:

```text
64 + 64 - 1 = 127
```

---

### Another Example

Consider:

```text
10.200.20.0/27
```

First, calculate the host bits:

```text
32 - 27 = 5
```

Then calculate the block size:

```text
2^5 = 32 addresses
```

The network starts at `.0`, so:

```text
0 + 32 - 1 = 31
```

Therefore:

```text
Network Address:   10.200.20.0
First Host:        10.200.20.1
Last Host:         10.200.20.30
Broadcast Address: 10.200.20.31
```

---

### Quick Method

```text
1. Host Bits  = 32 - CIDR
2. Block Size = 2^(Host Bits)
3. Broadcast  = Network Address + Block Size - 1
```

Example:

```text
192.168.10.64/26

32 - 26 = 6
2^6 = 64
64 + 64 - 1 = 127

Broadcast = 192.168.10.127
```

> The broadcast address is always the last address of the subnet, immediately before the next subnet begins.

---

## Splitting a Network into Smaller Subnets

Subnetting can also be used to divide an existing network into multiple smaller subnets.

To do this, we borrow bits from the **host portion** of the address and use them to identify the new subnets.

### Step 1 - Determine How Many Bits Are Needed

The number of subnet bits can be calculated using:

```text
2^n = Number of Subnets
```

Where `n` is the number of bits that need to be borrowed.

For example, if we need **4 subnets**:

```text
2^2 = 4
```

Therefore, we need to borrow **2 bits**.

---

### Step 2 - Calculate the New CIDR Prefix

Consider the following network:

```text
10.200.20.0/27
```

We need to divide it into **4 subnets**.

Since 4 subnets require 2 additional bits:

```text
Original CIDR: /27
Borrowed bits: 2

27 + 2 = 29
```

Therefore, each new subnet will use:

```text
/29
```

---

### Step 3 - Calculate the New Block Size

An IPv4 address contains 32 bits.

With `/29`:

```text
32 - 29 = 3 host bits
```

The number of addresses in each subnet is:

```text
2^3 = 8 addresses
```

Therefore, each subnet contains **8 total IP addresses**.

This also means that each new network address increases by 8.

---

### Step 4 - List the New Subnets

Starting from `10.200.20.0`, increase the network address by 8 for each subnet:

| Subnet | Network Address | CIDR |
|---|---|---|
| 1st | `10.200.20.0` | `/29` |
| 2nd | `10.200.20.8` | `/29` |
| 3rd | `10.200.20.16` | `/29` |
| 4th | `10.200.20.24` | `/29` |

Therefore, the network address of the **3rd subnet** is:

```text
10.200.20.16
```

---

### Quick Method

To divide a network into smaller subnets:

```text
1. Find how many bits are required:
   2^n = Number of Subnets

2. Add those bits to the original CIDR:
   New CIDR = Original CIDR + n

3. Calculate the remaining host bits:
   Host Bits = 32 - New CIDR

4. Calculate the block size:
   Block Size = 2^(Host Bits)

5. Increase the network address by the block size
   to find each new subnet.
```

For example:

```text
10.200.20.0/27 → 4 subnets

2^2 = 4
/27 + 2 = /29

32 - 29 = 3
2^3 = 8 addresses per subnet

Subnet addresses:

1st → .0
2nd → .8
3rd → .16
4th → .24

3rd subnet = 10.200.20.16
```

### Key Idea

When splitting a network, borrowing more bits for the network portion creates **more subnets but fewer addresses per subnet**.

```text
More network bits
       ↓
More subnets
       ↓
Fewer host bits
       ↓
Fewer addresses per subnet
```
---

## Finding the Broadcast Address After Subnetting

After splitting a network into smaller subnets, we may need to determine the **broadcast address** of a specific subnet.

The broadcast address is always the **last IP address of the subnet**, immediately before the network address of the next subnet.

### Example

Consider the following network:

```text
10.200.20.0/27
```

We want to split it into **4 subnets** and find the **broadcast address of the 2nd subnet**.

---

### Step 1 - Determine the Number of Borrowed Bits

We need 4 subnets:

```text
2^2 = 4
```

Therefore, we need to borrow **2 bits** from the host portion.

The original CIDR is `/27`:

```text
/27 + 2 = /29
```

Each new subnet will therefore use `/29`.

---

### Step 2 - Calculate the Block Size

An IPv4 address contains 32 bits.

For `/29`:

```text
32 - 29 = 3 host bits
```

The number of addresses in each subnet is:

```text
2^3 = 8 addresses
```

Therefore, each subnet has a block size of **8 addresses**.

---

### Step 3 - Identify the Subnet Ranges

Starting from `.0`, each subnet increases by 8:

| Subnet | Network Address | Broadcast Address |
|---|---|---|
| 1st | `10.200.20.0` | `10.200.20.7` |
| 2nd | `10.200.20.8` | `10.200.20.15` |
| 3rd | `10.200.20.16` | `10.200.20.23` |
| 4th | `10.200.20.24` | `10.200.20.31` |

The **2nd subnet** therefore covers:

```text
10.200.20.8 - 10.200.20.15
```

Breaking it down:

```text
Network Address:   10.200.20.8
First Host:        10.200.20.9
Last Host:         10.200.20.14
Broadcast Address: 10.200.20.15
```

Therefore:

```text
Broadcast Address = 10.200.20.15
```

---

### Quick Method

Once the network addresses of the subnets are known, the broadcast address can be found easily:

```text
Broadcast = Next Subnet Network Address - 1
```

For the 2nd subnet:

```text
2nd subnet starts → 10.200.20.8

3rd subnet starts → 10.200.20.16

16 - 1 = 15
```

Therefore:

```text
Broadcast = 10.200.20.15
```

### Key Idea

When the block size is known, subnet ranges become easier to identify.

For a block size of 8:

```text
Network addresses:

.0
.8
.16
.24
```

The broadcast address is always one address before the next network:

```text
Network     Broadcast
.0      →   .7
.8      →   .15
.16     →   .23
.24     →   .31
```

> Network addresses mark the beginning of each subnet, while broadcast addresses mark the end.

## Key Takeaways

- **Subnetting** divides a network into smaller networks.
- An IPv4 address contains `32 bits`.
- The **CIDR** determines how many bits belong to the Network Part.
- The remaining bits belong to the Host Part.
- A larger CIDR means a smaller subnet.
- **Network Address:** all Host bits are `0`.
- **Broadcast Address:** all Host bits are `1`.
- Total addresses can be calculated with `2^(Host Bits)`.
- Usable hosts in the examples above can be calculated with `2^(Host Bits) - 2`.
- If two hosts belong to the same subnet, traffic can remain inside that subnet.
- If they belong to different subnets, the traffic must be routed through a gateway.

### Quick Reference

```text
/24 → 256 addresses
/25 → 128 addresses
/26 →  64 addresses
/27 →  32 addresses
/28 →  16 addresses
/29 →   8 addresses
/30 →   4 addresses
```