# Subnetting and CIDR – Networking Fundamentals

## 1. What is Subnetting?

Subnetting is the process of dividing one large network into multiple smaller networks called **subnets**.

Organizations use subnetting to:
- Separate different departments.
- Organize IP address allocation.
- Reduce unnecessary broadcast traffic.
- Improve network management.
- Support network segmentation and security.

### Example

An organization has the network:

`192.168.1.0/24`

It wants to create separate networks for:
- HR
- Development
- IT
- Support

Instead of using one large network, we can divide it into four smaller subnets.

---

## 2. What is CIDR?

CIDR stands for **Classless Inter-Domain Routing**.

Example:

`192.168.1.0/24`

Here:
- `/24` represents the network portion.
- IPv4 contains 32 bits.
- Remaining 8 bits represent the host portion.

Formula:

```text
Host bits = 32 - CIDR prefix
```

For `/24`:

```text
32 - 24 = 8 host bits
```

---

## 3. Important Subnetting Formulas

### Total IP Addresses

```text
Total IP addresses = 2^(32 - CIDR)
```

### Traditionally Usable Host Addresses

```text
Usable hosts = Total IP addresses - 2
```

We subtract:
- 1 network address
- 1 broadcast address

### Number of Subnets

```text
Number of subnets = 2^n
```

Here, `n` represents the number of borrowed host bits.

### Block Size

```text
Block size = 256 - subnet mask value
```

Use the subnet mask value in the changing octet.

---

## 4. CIDR and Subnet Mask Reference

| CIDR | Subnet Mask | Total IPs | Usable Hosts |
|---|---|---:|---:|
| /16 | 255.255.0.0 | 65,536 | 65,534 |
| /23 | 255.255.254.0 | 512 | 510 |
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |

*Usable host counts above follow traditional IPv4 network and broadcast address rules.*

---

## 5. How to Create Subnets

### Practical Example

Original network:

`192.168.1.0/24`

Requirement: Create four equal-sized subnets.

**Step 1: Calculate borrowed bits**

Formula:

```text
Number of subnets = 2^n
```

We need four subnets:

```text
2^2 = 4
```

Therefore, we borrow 2 bits.

**Step 2: Calculate the new CIDR**

```text
Original CIDR = /24
Borrowed bits = 2

New CIDR = 24 + 2 = /26
```

**Step 3: Find the subnet mask**

```text
/26 = 255.255.255.192
```

**Step 4: Calculate block size**

```text
256 - 192 = 64
```

Each subnet contains 64 IP addresses.

**Step 5: Identify the four subnets**

| Subnet | Network Address | Broadcast Address |
|---|---|---|
| 1 | 192.168.1.0/26 | 192.168.1.63 |
| 2 | 192.168.1.64/26 | 192.168.1.127 |
| 3 | 192.168.1.128/26 | 192.168.1.191 |
| 4 | 192.168.1.192/26 | 192.168.1.255 |

Each subnet has:
- 64 total addresses.
- 62 traditionally usable host addresses.

---

## 6. Understanding Borrowed Bits

Each bit has two possible values: 0 and 1.

| Borrowed Bits | Calculation | Number of Subnets |
|---|---|---:|
| 1 | 2^1 | 2 |
| 2 | 2^2 | 4 |
| 3 | 2^3 | 8 |
| 4 | 2^4 | 16 |
| 5 | 2^5 | 32 |

Example:

If you need 8 subnets from a `/24` network:

```text
2^3 = 8

New CIDR = /24 + 3

New CIDR = /27
```

Remember: Borrowed bits determine the number of subnets. The remaining host bits determine the number of addresses per subnet.

---

## 7. Subnetting Based on Department Requirements

In a real organization, subnet sizes depend on the number of devices each department needs.

Example:

| Department | Required Devices | Suggested CIDR | Usable Hosts |
|---|---:|---|---:|
| Development | 100 | /25 | 126 |
| Support | 50 | /26 | 62 |
| HR | 20 | /27 | 30 |
| IT | 10 | /28 | 14 |

Different departments need different subnet sizes.

This leads to a networking technique called VLSM.

---

## 8. What is VLSM?

VLSM stands for **Variable Length Subnet Masking**.

VLSM allows us to create subnets of different sizes within one larger network.

Instead of allocating equal-sized subnets, we allocate IP addresses according to actual requirements.

### Practical VLSM Example

Original network:

`192.168.1.0/24`

Requirements:

- Development: 100 devices
- Support: 50 devices
- HR: 20 devices
- IT: 10 devices

We allocate the largest subnet first.

| Department | Network CIDR | Address Range | Broadcast |
|---|---|---|---|
| Development | 192.168.1.0/25 | .0 – .127 | .127 |
| Support | 192.168.1.128/26 | .128 – .191 | .191 |
| HR | 192.168.1.192/27 | .192 – .223 | .223 |
| IT | 192.168.1.224/28 | .224 – .239 | .239 |

The remaining range `.240 – .255` is available for future subnet allocation.

**Important:** Every subnet must have a valid network boundary and must not overlap with another subnet.

---

## 9. Understanding Octet Changes

IPv4 addresses contain four octets.

Example:

`192.168.10.77`

| Octet | Value |
|---|---:|
| First | 192 |
| Second | 168 |
| Third | 10 |
| Fourth | 77 |

The changing octet depends on the subnet mask.

### Case 1: Fourth Octet Changes

Example:

`192.168.10.77/26`

Subnet mask:

`255.255.255.192`

Block size:

```text
256 - 192 = 64
```

Network boundaries:

```text
0, 64, 128, 192
```

Network address:

`192.168.10.64`

Broadcast address:

`192.168.10.127`

### Case 2: Third Octet Changes

Example:

`192.168.5.20/23`

Subnet mask:

`255.255.254.0`

Block size:

```text
256 - 254 = 2
```

Third-octet boundaries:

```text
0, 2, 4, 6, 8, 10...
```

Since 5 belongs to the block 4–5:

Network address:

`192.168.4.0`

Broadcast address:

`192.168.5.255`

**Important rule:** When the third octet is the changing octet, the fourth octet of the network address is zero.

When the second octet is the changing octet, the third and fourth octets of the network address are zero.

These rules apply to network addresses, not every address in the subnet.

---

## 10. Finding Network and Broadcast Addresses

Example:

`192.168.10.140/26`

Step 1: Subnet mask:

`255.255.255.192`

Step 2: Block size:

```text
256 - 192 = 64
```

Step 3: Subnet boundaries:

```text
0, 64, 128, 192
```

Step 4: Identify the subnet containing 140.

The address 140 falls between 128 and 191.

Therefore:

| Address Type | IP Address |
|---|---|
| Network Address | 192.168.10.128 |
| First Usable Host | 192.168.10.129 |
| Last Usable Host | 192.168.10.190 |
| Broadcast Address | 192.168.10.191 |

---

## 11. Subnetting in AWS VPC

Subnetting is important when designing AWS cloud infrastructure.

Example:

VPC CIDR:

`192.168.1.0/24`

Possible subnet architecture:

| Subnet | CIDR | Purpose |
|---|---|---|
| Public Subnet | 192.168.1.0/26 | Load Balancer |
| Private Subnet | 192.168.1.64/26 | Application Servers |
| Private Subnet | 192.168.1.128/26 | Database Servers |
| Reserved Subnet | 192.168.1.192/26 | Future Resources |

### AWS Networking Concepts to Learn

- VPC
- Public and private subnets
- Availability Zones
- Route Tables
- Internet Gateway
- NAT Gateway
- Security Groups
- Network ACLs
- VPC Peering
- Transit Gateway

### AWS-Specific Address Reservation

AWS reserves five IPv4 addresses in every subnet.

For example:

A `/26` subnet contains 64 total IP addresses.

```text
64 - 5 = 59 usable AWS IPv4 addresses
```

This differs from the traditional IPv4 usable-host calculation of 62.

Subnetting alone does not provide security isolation. You must also configure appropriate routing and network security controls.

---

## 12. Important Rules to Remember

1. IPv4 contains 32 bits.
2. Higher CIDR prefix means fewer IP addresses.
3. Borrowed bits determine the number of subnets.
4. Remaining host bits determine subnet size.
5. Block size determines subnet boundaries.
6. Network address is the first address in a subnet.
7. Broadcast address is the last address in a traditional IPv4 subnet.
8. First usable host = Network address + 1.
9. Last usable host = Broadcast address - 1.
10. VLSM allows different subnet sizes.
11. AWS reserves five IPv4 addresses in each subnet.

---

## 13. Practice Questions

Use these questions to test your understanding.

| Question | Topic |
|---|---|
| 1 | How many total IPs are available in `/26`? |
| 2 | How many traditionally usable hosts are available in `/27`? |
| 3 | What is the subnet mask for `/28`? |
| 4 | How many subnets can be created by borrowing 3 bits? |
| 5 | What is the network address of `192.168.1.75/26`? |
| 6 | What is the broadcast address of `192.168.10.140/26`? |
| 7 | Find the network address of `10.0.5.77/27`. |
| 8 | What CIDR is needed for 8 equal subnets from `/24`? |
| 9 | What is VLSM? |
| 10 | How many AWS-usable IPv4 addresses are available in a `/27` subnet? |

---

## Learning Objective

By completing these topics, you should be able to:

- Calculate CIDR and subnet masks.
- Divide networks into equal-sized subnets.
- Calculate borrowed bits.
- Identify changing octets.
- Find network and broadcast addresses.
- Perform VLSM subnet allocation.
- Design basic AWS VPC subnet architectures.

**Next learning milestone:** Practice subnetting with different CIDR prefixes, including `/20`, `/21`, `/22`, and `/23`, before moving to AWS VPC subnet design and routing.
