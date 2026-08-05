# Class 31 - AWS Networking (VPC)
class URL: https://youtu.be/JjMRlQjAFIY
## What is a VPC?

A **Virtual Private Cloud (VPC)** is a logically isolated virtual network in AWS where you can launch and manage your AWS resources securely.

- Every AWS account can create multiple VPCs.
- You define your own IP address range using CIDR.
- Resources inside a VPC can communicate with each other.
- You control inbound and outbound traffic using Route Tables, Security Groups, and NACLs.

### Benefits
- Network Isolation
- Secure communication
- Full control over IP addressing
- Connect to on-premises networks using VPN or Direct Connect
- High Availability using multiple Availability Zones

---

# CIDR Range (Classless Inter-Domain Routing)

CIDR defines the IP address range for a VPC or subnet.

Example:

```
10.0.0.0/16
```

Where:

- Network Address: `10.0.0.0`
- Prefix: `/16`

Available IPs:

```
10.0.0.1
to
10.0.255.254
```

Total IP Addresses:

```
2^(32-16)
=
65,536 IPs
```

### Common CIDR Blocks

| CIDR | Total IPs |
|------|-----------|
| /16 | 65,536 |
| /17 | 32,768 |
| /18 | 16,384 |
| /19 | 8,192 |
| /20 | 4,096 |
| /21 | 2,048 |
| /22 | 1,024 |
| /23 | 512 |
| /24 | 256 |
| /25 | 128 |
| /26 | 64 |
| /27 | 32 |
| /28 | 16 |

---

# Subnet

A subnet is a smaller network inside a VPC.

Example:

VPC

```
10.0.0.0/16
```

Subnets

```
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
10.0.4.0/24
```

Each subnet belongs to one Availability Zone (AZ).

---

# Public Subnet

A Public Subnet is a subnet that has access to the Internet.

Characteristics:

- Has a route to Internet Gateway (IGW)
- Resources can access the internet
- Can receive traffic from the internet
- EC2 instances may have Public IP or Elastic IP

Used for:

- Web Servers
- Bastion (Jump) Servers
- Load Balancers
- NAT Gateway

Example:

```
Public Subnet
10.0.1.0/24

Route:

0.0.0.0/0 --> Internet Gateway
```

---

# Private Subnet

A Private Subnet does NOT have direct internet access.

Characteristics:

- No route to Internet Gateway
- Instances do not have Public IP
- More secure
- Outbound internet access can be provided using a NAT Gateway

Used for:

- Database Servers
- Application Servers
- Internal APIs
- Backend Services

Example:

```
Private Subnet
10.0.2.0/24

No direct Internet Gateway route
```

---

# Internet Gateway (IGW)

An Internet Gateway connects your VPC to the public internet.

Functions:

- Enables internet access
- Allows inbound and outbound communication
- One IGW can be attached to one VPC
- Managed by AWS

Without an Internet Gateway:

```
No Internet Access
```

With an Internet Gateway:

```
EC2 <----> Internet
```

---

# Route Table

A Route Table tells AWS where network traffic should go.

Every subnet is associated with one Route Table.

Example Route Table:

| Destination | Target |
|------------|----------|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | Internet Gateway |

Explanation:

```
10.0.0.0/16

Internal VPC communication

0.0.0.0/0

All internet traffic
```

Private Route Table Example:

| Destination | Target |
|------------|---------|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | NAT Gateway |

---

# NAT Gateway

A NAT Gateway allows instances in a Private Subnet to access the internet **without allowing inbound internet connections**.

Example:

```
Private EC2

↓

NAT Gateway

↓

Internet
```

Important Points:

- Created inside a Public Subnet
- Requires an Elastic IP
- Highly Available within an Availability Zone
- Managed by AWS
- Used only for outbound internet access

Typical Use Cases:

- Download software updates
- Install packages
- Access external APIs
- Pull Docker images
- Access GitHub repositories

---

# Jump Server (Bastion Host)

A Jump Server (also called a Bastion Host) is an EC2 instance placed in a Public Subnet that is used to securely access EC2 instances in Private Subnets.

Instead of exposing private servers to the internet, administrators first connect to the Jump Server and then access the private servers.

### Advantages

- Improved security
- No Public IP required for private instances
- Centralized administrative access
- Reduces attack surface

Example Workflow:

```
Admin Laptop
      │
      ▼
Internet
      │
      ▼
Jump Server (Public Subnet)
      │
      ▼
Private EC2 Instance
```

---

# Sample VPC Architecture

```
                    Internet
                        │
                 Internet Gateway
                        │
        ---------------------------------
        |                               |
   Public Route Table              Private Route Table
        |                               |
        |                               |
 ------------------               -------------------
 |                |               |                 |
 | Public Subnet  |               | Private Subnet  |
 | 10.0.1.0/24    |               | 10.0.2.0/24     |
 |                |               |                 |
 | Jump Server    |               | App Server      |
 | NAT Gateway    |               | Database        |
 ------------------               -------------------
         │
         │
    Elastic IP
```

---

# Communication Flow

### Public EC2 Internet Access

```
EC2
↓

Public Subnet

↓

Route Table

↓

Internet Gateway

↓

Internet
```

---

### Private EC2 Internet Access

```
Private EC2

↓

Private Route Table

↓

NAT Gateway

↓

Internet Gateway

↓

Internet
```

---

### Administrator Access to Private Server

```
Laptop

↓

Internet

↓

Jump Server

↓

Private EC2
```

---

# Components Summary

| Component | Purpose |
|------------|---------|
| VPC | Creates an isolated virtual network |
| CIDR | Defines the IP address range |
| Subnet | Divides the VPC into smaller networks |
| Public Subnet | Internet-facing resources |
| Private Subnet | Internal resources without direct internet access |
| Internet Gateway | Enables internet connectivity for the VPC |
| Route Table | Determines how network traffic is routed |
| NAT Gateway | Provides outbound internet access for private instances |
| Jump Server | Securely accesses private instances from the internet |

---

# Interview Questions

### 1. What is a VPC?
A logically isolated virtual network in AWS where you launch AWS resources securely.

### 2. What is CIDR?
CIDR defines the IP address range of a VPC or subnet.

### 3. What is the difference between a Public and Private Subnet?

**Public Subnet**
- Has Internet Gateway route
- Can have Public IP
- Internet accessible

**Private Subnet**
- No direct Internet Gateway route
- No Public IP
- More secure
- Uses NAT Gateway for outbound internet

### 4. Why do we use an Internet Gateway?
To allow communication between the VPC and the internet.

### 5. What is the purpose of a Route Table?
It defines where network traffic should be routed.

### 6. Why is a NAT Gateway required?
To allow instances in private subnets to access the internet for outbound traffic while preventing inbound internet access.

### 7. Where should a NAT Gateway be placed?
Inside a Public Subnet with an attached Elastic IP.

### 8. What is a Jump Server?
A secure EC2 instance in a Public Subnet used to connect to EC2 instances in Private Subnets.

### 9. Can a Private EC2 instance have internet access?
Yes, through a NAT Gateway, but only for outbound connections.

### 10. Can an Internet Gateway be attached to multiple VPCs?
No. An Internet Gateway can be attached to only one VPC at a time.
