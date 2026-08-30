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