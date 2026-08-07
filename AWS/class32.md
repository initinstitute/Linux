# Class 32 – CIDR Range

class url: https://youtu.be/d2GFK7Ykfps
## 1. What is CIDR?

**CIDR** stands for **Classless Inter-Domain Routing**.

CIDR is a method used to define the **IP address range** of a network.

CIDR notation looks like:

```text
10.0.0.0/16
```

or

```text
192.168.1.0/24
```

The `/16` and `/24` represent the **prefix length**.

---

# 2. IPv4 Address

An IPv4 address contains **32 bits**.

Example:

```text
192.168.1.10
```

Each section is called an **octet**.

```text
192     .     168     .     1     .     10
 8 bits      8 bits       8 bits       8 bits

              Total = 32 bits
```

Therefore:

```text
IPv4 = 32 bits
```

---

# 3. CIDR Notation

CIDR has two parts:

```text
10.0.0.0/16
│       │
│       └── Prefix length
│
└────────── Network address
```

The `/16` means:

```text
First 16 bits = Network portion
Remaining 16 bits = Host portion
```

---

# 4. Network Bits and Host Bits

For:

```text
10.0.0.0/16
```

There are:

```text
Network bits = 16
Host bits    = 32 - 16 = 16
```

Number of addresses:

```text
2^16 = 65,536
```

So:

```text
10.0.0.0/16
```

contains **65,536 IPv4 addresses**.

---

# 5. Common CIDR Ranges

| CIDR  | Total IP Addresses |
| ----- | -----------------: |
| `/8`  |         16,777,216 |
| `/16` |             65,536 |
| `/20` |              4,096 |
| `/21` |              2,048 |
| `/22` |              1,024 |
| `/23` |                512 |
| `/24` |                256 |
| `/25` |                128 |
| `/26` |                 64 |
| `/27` |                 32 |
| `/28` |                 16 |
| `/29` |                  8 |
| `/30` |                  4 |
| `/31` |                  2 |
| `/32` |                  1 |

Formula:

```text
Number of IP addresses = 2^(32 - prefix)
```

For `/24`:

```text
2^(32 - 24)

= 2^8

= 256
```

---

# 6. Understanding /24

Consider:

```text
192.168.1.0/24
```

There are:

```text
Network bits = 24
Host bits = 8
```

Therefore:

```text
2^8 = 256 addresses
```

Range:

```text
192.168.1.0
        to
192.168.1.255
```

---

# 7. Understanding /16

Consider:

```text
10.0.0.0/16
```

There are:

```text
Network bits = 16
Host bits = 16
```

Therefore:

```text
2^16 = 65,536 addresses
```

Range:

```text
10.0.0.0
    to
10.0.255.255
```

---

# 8. Understanding /8

Example:

```text
10.0.0.0/8
```

Network bits:

```text
8
```

Host bits:

```text
32 - 8 = 24
```

Total addresses:

```text
2^24 = 16,777,216
```

Range:

```text
10.0.0.0
    to
10.255.255.255
```

---

# 9. CIDR and AWS VPC

When creating an AWS VPC, we specify a CIDR block.

Example:

```text
VPC CIDR:
10.0.0.0/16
```

This provides:

```text
10.0.0.0 - 10.0.255.255
```

addresses.

Inside this VPC, we can create smaller subnet ranges.

Example:

```text
VPC
10.0.0.0/16
│
├── Public Subnet
│   10.0.1.0/24
│
├── Public Subnet
│   10.0.2.0/24
│
├── Private Subnet
│   10.0.3.0/24
│
└── Private Subnet
    10.0.4.0/24
```

---

# 10. VPC CIDR vs Subnet CIDR

### VPC

```text
10.0.0.0/16
```

Large network range.

### Subnet

```text
10.0.1.0/24
```

Smaller network range inside the VPC.

```text
VPC: 10.0.0.0/16
          |
          +--- Subnet: 10.0.1.0/24
          |
          +--- Subnet: 10.0.2.0/24
          |
          +--- Subnet: 10.0.3.0/24
```

The subnet CIDR must be a valid range within the VPC CIDR.

---

# 11. Example AWS VPC Design

Suppose we create:

```text
VPC CIDR:
10.0.0.0/16
```

We can divide it into subnets:

```text
Public Subnet 1:
10.0.1.0/24

Public Subnet 2:
10.0.2.0/24

Private Subnet 1:
10.0.3.0/24

Private Subnet 2:
10.0.4.0/24
```

Architecture:

```text
                 VPC
             10.0.0.0/16
                   |
        +----------+----------+
        |                     |
   Public Subnets        Private Subnets
        |                     |
   10.0.1.0/24          10.0.3.0/24
   10.0.2.0/24          10.0.4.0/24
```

---

# 12. CIDR `/24` Example

```text
192.168.10.0/24
```

Range:

```text
192.168.10.0
        |
        |
192.168.10.255
```

Total:

```text
256 addresses
```

In a normal network:

```text
Network Address:
192.168.10.0

Usable Host Range:
192.168.10.1 - 192.168.10.254

Broadcast Address:
192.168.10.255
```

---

# 13. AWS Subnet Reserved IP Addresses

AWS reserves **5 IP addresses in every subnet**.

For:

```text
10.0.1.0/24
```

AWS reserves:

```text
10.0.1.0
10.0.1.1
10.0.1.2
10.0.1.3
10.0.1.255
```

Therefore:

```text
Total addresses = 256
AWS reserved    = 5

Usable = 251
```

### Important

AWS reserves these addresses for every IPv4 subnet:

```text
First address
Second address
Third address
Fourth address
Last address
```

---

# 14. CIDR `/25`

Example:

```text
10.0.1.0/25
```

Host bits:

```text
32 - 25 = 7
```

Total:

```text
2^7 = 128
```

Range:

```text
10.0.1.0 - 10.0.1.127
```

AWS usable IPv4 addresses:

```text
128 - 5 = 123
```

---

# 15. CIDR `/26`

Example:

```text
10.0.1.0/26
```

Host bits:

```text
32 - 26 = 6
```

Total:

```text
2^6 = 64
```

Range:

```text
10.0.1.0 - 10.0.1.63
```

AWS usable IPv4 addresses:

```text
64 - 5 = 59
```

---

# 16. CIDR `/27`

Example:

```text
10.0.1.0/27
```

Host bits:

```text
32 - 27 = 5
```

Total:

```text
2^5 = 32
```

Range:

```text
10.0.1.0 - 10.0.1.31
```

AWS usable:

```text
32 - 5 = 27
```

---

# 17. CIDR `/28`

Example:

```text
10.0.1.0/28
```

Host bits:

```text
32 - 28 = 4
```

Total:

```text
2^4 = 16
```

AWS usable:

```text
16 - 5 = 11
```

Range:

```text
10.0.1.0 - 10.0.1.15
```

---

# 18. Subnetting

Subnetting means dividing a large network into smaller networks.

Example:

```text
10.0.0.0/16
```

can be divided into:

```text
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
10.0.4.0/24
```

This helps us organize resources.

Example:

```text
VPC
10.0.0.0/16
│
├── Public
│   └── 10.0.1.0/24
│
├── Public
│   └── 10.0.2.0/24
│
├── Private
│   └── 10.0.3.0/24
│
└── Private
    └── 10.0.4.0/24
```

---

# 19. Public and Private Subnets Do Not Depend on CIDR

A common misunderstanding is:

> `/24` means public subnet and `/16` means private subnet.

This is **wrong**.

CIDR determines the **IP address range**.

Whether a subnet is public or private depends primarily on its **route table**.

### Public subnet

```text
0.0.0.0/0
      |
      v
Internet Gateway
```

### Private subnet

```text
0.0.0.0/0
      |
      v
NAT Gateway
```

or there may be no internet route at all.

---

# 20. Private IP CIDR Ranges

The commonly used private IPv4 ranges are:

```text
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

### 10.0.0.0/8

```text
10.0.0.0 - 10.255.255.255
```

### 172.16.0.0/12

```text
172.16.0.0 - 172.31.255.255
```

### 192.168.0.0/16

```text
192.168.0.0 - 192.168.255.255
```

These ranges are commonly used for internal/private networks.

---

# 21. Common AWS CIDR Design

A common VPC design is:

```text
VPC:
10.0.0.0/16
```

Then:

```text
Availability Zone A

Public:
10.0.1.0/24

Private:
10.0.11.0/24
```

and:

```text
Availability Zone B

Public:
10.0.2.0/24

Private:
10.0.12.0/24
```

Example:

```text
                 VPC
             10.0.0.0/16
                    |
        +-----------+-----------+
        |                       |
       AZ-A                    AZ-B
        |                       |
   +----+----+             +----+----+
   |         |             |         |
Public    Private        Public    Private
10.0.1/24 10.0.11/24    10.0.2/24 10.0.12/24
```

---

# 22. CIDR Calculation Formula

### Number of IP addresses

```text
2^(32 - CIDR prefix)
```

Example:

```text
/24

2^(32 - 24)

= 2^8

= 256
```

Example:

```text
/20

2^(32 - 20)

= 2^12

= 4096
```

---

# 23. Quick CIDR Table

| CIDR  | Host Bits | Total IPs | AWS Usable IPv4* |
| ----- | --------: | --------: | ---------------: |
| `/16` |        16 |    65,536 |           65,531 |
| `/20` |        12 |     4,096 |            4,091 |
| `/21` |        11 |     2,048 |            2,043 |
| `/22` |        10 |     1,024 |            1,019 |
| `/23` |         9 |       512 |              507 |
| `/24` |         8 |       256 |              251 |
| `/25` |         7 |       128 |              123 |
| `/26` |         6 |        64 |               59 |
| `/27` |         5 |        32 |               27 |
| `/28` |         4 |        16 |               11 |

*For standard AWS IPv4 subnets, AWS reserves 5 addresses.

---

# 24. Important Rules

1. IPv4 contains **32 bits**.
2. CIDR tells us how many bits belong to the network portion.
3. Remaining bits are used for hosts.
4. Smaller prefix (`/16`) = larger network.
5. Larger prefix (`/28`) = smaller network.
6. Subnet CIDR must fit inside the VPC CIDR.
7. Subnets within the same VPC cannot have overlapping CIDR ranges.
8. Public/private status is determined by routing, not by the CIDR number.
9. AWS reserves 5 IPv4 addresses in each subnet.
10. Private IPv4 ranges include `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`.

---

# 25. Easy Way to Remember

```text
CIDR = Network Size

/16  -> Large Network
/20  -> Smaller
/24  -> Common Subnet
/26  -> Smaller
/28  -> Very Small
```

```text
Prefix increases
      |
      v
Network becomes smaller

/16 -> /20 -> /24 -> /28
Large              Small
```

### AWS Example

```text
VPC
10.0.0.0/16
    |
    +-- Public Subnet
    |   10.0.1.0/24
    |
    +-- Public Subnet
    |   10.0.2.0/24
    |
    +-- Private Subnet
    |   10.0.3.0/24
    |
    +-- Private Subnet
        10.0.4.0/24
```

**Key point:**

```text
CIDR       -> Defines IP address range

Route Table -> Determines public/private connectivity
```
