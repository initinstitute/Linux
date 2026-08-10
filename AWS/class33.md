# Class 33 – AWS Networking
class url: https://youtu.be/TZfgbAnoCmg
## Topics Covered

1. Bastion Host / Jump Server
2. NAT Gateway
3. VPC Peering
4. AWS Transit Gateway
5. VPC Endpoints
6. VPN
7. Types of VPN

---

# 1. Bastion Host / Jump Server

## What is a Bastion Host?

A **Bastion Host** is a specially configured EC2 instance used to securely connect to resources located in a **private subnet**.

It is also called a:

* Jump Server
* Jump Host
* Bastion Server

The private EC2 instances do not need to have public IP addresses.

### Example Architecture

```text
                   Internet
                       |
                       |
                  Internet Gateway
                       |
                Public Subnet
                       |
                Bastion Host
                Public IP
                       |
                 SSH Connection
                       |
                Private Subnet
                       |
              -----------------
              |               |
          Private EC2      Private EC2
```

## Why do we use a Bastion Host?

Suppose we have:

```text
Public Subnet
    |
    |-- Bastion Host
    |
Private Subnet
    |
    |-- Application Server
    |-- Database Server
```

The application and database servers should not be directly accessible from the internet.

Instead:

```text
Your Laptop
     |
     | SSH
     v
Bastion Host
     |
     | SSH
     v
Private EC2
```

## Bastion Host Requirements

Typically, the Bastion Host:

* Is deployed in a public subnet.
* Has a public IP or Elastic IP.
* Has a route to an Internet Gateway.
* Allows SSH from trusted source IPs.
* Can communicate with private EC2 instances.
* Should have strict security group rules.

## Security Group Example

### Bastion Security Group

```text
Inbound:

SSH
Port: 22
Source: Your-Public-IP/32
```

Avoid:

```text
SSH
Port: 22
Source: 0.0.0.0/0
```

because this allows SSH access from anywhere.

### Private EC2 Security Group

Allow SSH only from the Bastion Host security group:

```text
SSH
Port: 22
Source: Bastion-SG
```

This is better than allowing SSH from the entire internet.

## Connecting Through Bastion Host

First connect to Bastion:

```bash
ssh -i key.pem ubuntu@<bastion-public-ip>
```

Then from Bastion connect to private EC2:

```bash
ssh -i key.pem ubuntu@<private-ip>
```

## Bastion Host vs NAT Gateway

| Bastion Host                             | NAT Gateway                       |
| ---------------------------------------- | --------------------------------- |
| Used for administrative access           | Used for outbound internet access |
| Allows SSH/RDP access to private servers | Does not provide inbound SSH/RDP  |
| Usually an EC2 instance                  | AWS managed service               |
| Has a public IP                          | Uses Elastic IP for public NAT    |
| Requires OS maintenance                  | AWS manages infrastructure        |
| Can act as a jump server                 | Cannot be used as a jump server   |

---

# 2. NAT Gateway

## What is NAT?

NAT stands for:

**Network Address Translation**

A NAT Gateway allows resources in a **private subnet** to access the internet without allowing the internet to initiate connections to those resources.

### Example

```text
                  Internet
                     |
                     |
              Internet Gateway
                     |
                Public Subnet
                     |
                NAT Gateway
                     |
                Private Subnet
                     |
                 Private EC2
```

The private EC2 can initiate:

```text
Private EC2 ---> NAT Gateway ---> Internet
```

But the internet cannot directly initiate:

```text
Internet ---> Private EC2
```

---

# 3. NAT Gateway Architecture

A NAT Gateway should normally be created in a **public subnet**.

```text
VPC
|
+-- Public Subnet
|      |
|      +-- NAT Gateway
|      |
|      +-- Internet Gateway route
|
+-- Private Subnet
       |
       +-- EC2
```

## Public Subnet Route Table

The public subnet needs:

```text
0.0.0.0/0 ---> Internet Gateway
```

## Private Subnet Route Table

The private subnet needs:

```text
0.0.0.0/0 ---> NAT Gateway
```

### Traffic Flow

```text
Private EC2
     |
     v
Private Route Table
     |
     v
NAT Gateway
     |
     v
Internet Gateway
     |
     v
Internet
```

---

# 4. NAT Gateway vs Internet Gateway

| Feature                        | Internet Gateway                   | NAT Gateway                                |
| ------------------------------ | ---------------------------------- | ------------------------------------------ |
| Managed by AWS                 | Yes                                | Yes                                        |
| Used by public subnet          | Yes                                | NAT Gateway is placed in public subnet     |
| Private subnet internet access | No, not by itself                  | Yes                                        |
| Allows inbound connections     | Can, depending on routing/security | No unsolicited inbound                     |
| Public IPv4 required           | Resources need public addressing   | NAT Gateway uses Elastic IP for public NAT |
| Main purpose                   | Internet connectivity for VPC      | Outbound internet from private resources   |

---

# 5. VPC Peering

## What is VPC Peering?

**VPC Peering** allows two VPCs to communicate with each other using private IP addresses.

Example:

```text
VPC-A
CIDR: 10.0.0.0/16
     |
     |
 VPC Peering
     |
     |
VPC-B
CIDR: 20.0.0.0/16
```

Resources in VPC-A can communicate with resources in VPC-B using private IP addresses.

---

# 6. VPC Peering Architecture

```text
VPC A
10.0.0.0/16
|
|-- EC2
|
+-------- VPC Peering --------+
                              |
                              |
                         VPC B
                         20.0.0.0/16
                              |
                              |-- EC2
```

For communication to work, **route tables must be configured on both sides**.

### VPC-A Route Table

```text
Destination: 20.0.0.0/16
Target: VPC Peering Connection
```

### VPC-B Route Table

```text
Destination: 10.0.0.0/16
Target: VPC Peering Connection
```

Security groups and network ACLs must also allow the required traffic.

---

# 7. Important Characteristics of VPC Peering

### Private Communication

Communication occurs using private IP addresses.

### No Transitive Routing

This is one of the most important concepts.

Suppose:

```text
VPC-A <----> VPC-B <----> VPC-C
```

Even though:

```text
A <-> B
B <-> C
```

VPC-A cannot automatically communicate with VPC-C through VPC-B.

```text
A ---> B ---> C
```

is **not automatically supported** through VPC peering.

This is called the **no-transitive-routing limitation**.

### CIDR Ranges Should Not Overlap

For normal VPC peering, VPC CIDR ranges should not overlap.

Example:

```text
VPC-A = 10.0.0.0/16
VPC-B = 10.0.0.0/16
```

This is not a suitable addressing design for VPC peering.

Better:

```text
VPC-A = 10.0.0.0/16
VPC-B = 20.0.0.0/16
```

---

# 8. VPC Peering Use Cases

VPC Peering can be used for:

* Application VPC to database VPC
* Development VPC to shared-services VPC
* Production VPC to monitoring VPC
* Communication between VPCs in the same AWS Region
* Communication between VPCs in different AWS Regions

---

# 9. VPC Peering Limitations

Important limitations include:

* No transitive routing.
* Route tables must be configured.
* Security groups must allow communication.
* Network ACLs must allow communication.
* Overlapping CIDRs are problematic.
* Managing many VPC-to-VPC connections becomes difficult.

For example:

```text
VPC-A <----> VPC-B
VPC-A <----> VPC-C
VPC-A <----> VPC-D
VPC-B <----> VPC-C
VPC-B <----> VPC-D
VPC-C <----> VPC-D
```

As the number of VPCs increases, managing individual peering connections becomes complicated.

This is where **Transit Gateway** becomes useful.

---

# 10. AWS Transit Gateway

## What is Transit Gateway?

**AWS Transit Gateway (TGW)** is a centralized network hub used to connect:

* Multiple VPCs
* VPN connections
* Other supported network attachments

Instead of creating many individual connections, VPCs can connect to a central Transit Gateway.

### Architecture

```text
             VPC-A
               |
               |
             \ |
               v
        +----------------+
        | Transit Gateway|
        +----------------+
          /      |      \
         /       |       \
       VPC-B    VPC-C    VPC-D
```

---

# 11. Transit Gateway vs VPC Peering

### VPC Peering

```text
VPC-A <------> VPC-B
```

For many VPCs, connections become difficult to manage.

### Transit Gateway

```text
             VPC-A
               |
             VPC-B
               |
        +--------------+
        | Transit GW   |
        +--------------+
          |     |     |
        VPC-C VPC-D  VPN
```

Transit Gateway provides a centralized networking model.

---

# 12. Transit Gateway Use Case

Suppose an organization has:

```text
Production VPC
Development VPC
Testing VPC
Security VPC
Shared Services VPC
On-Premises Network
```

Instead of creating many peering connections:

```text
Production <--> Development
Production <--> Testing
Production <--> Security
Development <--> Testing
Development <--> Security
...
```

we can use:

```text
                 Production
                     |
Development ---- Transit Gateway ---- Testing
                     |
                 Security VPC
                     |
                Shared Services
                     |
                  VPN/On-Prem
```

This makes network management easier.

---

# 13. Transit Gateway Components

Important concepts include:

### Transit Gateway

The central networking hub.

### Transit Gateway Attachment

A VPC, VPN, or other supported resource connects to the Transit Gateway using an attachment.

### Transit Gateway Route Table

Controls how traffic is routed between attachments.

Example:

```text
Destination: 10.20.0.0/16
Target: VPC-B attachment
```

---

# 14. VPC Endpoints

## What is a VPC Endpoint?

A **VPC Endpoint** allows resources inside a VPC to privately access supported AWS services without requiring internet access.

Example:

```text
Private EC2
    |
    v
VPC Endpoint
    |
    v
Amazon S3
```

The EC2 instance does not need to go through:

```text
NAT Gateway
Internet Gateway
Public Internet
```

for supported endpoint-based AWS service access.

---

# 15. Why VPC Endpoints?

Consider a private EC2:

```text
Private EC2
     |
     v
NAT Gateway
     |
     v
Internet Gateway
     |
     v
AWS Service
```

This can create NAT Gateway processing costs and adds an internet path.

With a VPC Endpoint:

```text
Private EC2
     |
     v
VPC Endpoint
     |
     v
AWS Service
```

This provides private connectivity for supported AWS services.

---

# 16. Types of VPC Endpoints

There are mainly two endpoint types:

1. Gateway Endpoint
2. Interface Endpoint

---

# 17. Gateway Endpoint

Gateway endpoints are used for:

* Amazon S3
* Amazon DynamoDB

Example:

```text
Private EC2
    |
    v
Route Table
    |
    v
Gateway Endpoint
    |
    v
S3
```

A Gateway Endpoint is associated with route tables.

Example route:

```text
Destination: S3 Prefix List
Target: vpce-xxxxxxxx
```

---

# 18. Interface Endpoint

An Interface Endpoint uses:

**AWS PrivateLink**

It creates an **Elastic Network Interface (ENI)** with private IP addresses inside your subnet.

Example:

```text
Private EC2
     |
     v
Private IP
     |
     v
Interface Endpoint ENI
     |
     v
AWS Service
```

Interface endpoints can be used for many AWS services.

Examples include services such as:

* AWS Systems Manager
* Amazon CloudWatch
* AWS Secrets Manager
* Amazon ECR
* AWS STS
* Many other supported AWS services

---

# 19. Gateway Endpoint vs Interface Endpoint

| Feature           | Gateway Endpoint                | Interface Endpoint                         |
| ----------------- | ------------------------------- | ------------------------------------------ |
| Technology        | Gateway                         | AWS PrivateLink                            |
| Main examples     | S3, DynamoDB                    | Many AWS services                          |
| Uses ENI          | No                              | Yes                                        |
| Private IP        | Not represented by endpoint ENI | Yes                                        |
| Route table entry | Yes                             | Traffic typically resolves to endpoint ENI |
| Security Group    | No endpoint SG                  | Yes                                        |
| Cost model        | No hourly endpoint charge       | Hourly + data processing charges can apply |

---

# 20. VPN

## What is VPN?

VPN stands for:

**Virtual Private Network**

A VPN creates a secure connection between two networks or between a user/device and a network.

Example:

```text
Office Network
      |
      |
   Internet
      |
      |
     VPN
      |
      |
     AWS
```

VPN traffic is encrypted while traversing the public internet.

---

# 21. AWS Site-to-Site VPN

AWS Site-to-Site VPN is used to connect an on-premises network to an AWS VPC.

Example:

```text
On-Premises Data Center
        |
        |
Customer Gateway
        |
     Internet
        |
        |
Virtual Private Gateway
        |
       VPC
        |
      EC2
```

It creates an encrypted VPN connection between the on-premises network and AWS.

---

# 22. Important Site-to-Site VPN Components

## Customer Gateway

Represents the customer's VPN device or software VPN device.

Example:

```text
Company Firewall
       |
       v
Customer Gateway
```

## Virtual Private Gateway

A VPN gateway attached to a VPC.

```text
VPC
 |
 +-- Virtual Private Gateway
```

## Transit Gateway

A Transit Gateway can also be used as a VPN termination point for centralized connectivity.

---

# 23. AWS Client VPN

AWS Client VPN is used when individual users need secure access to AWS resources.

Example:

```text
Developer Laptop
       |
       | VPN
       v
AWS Client VPN
       |
       v
VPC
       |
       v
Private EC2
```

Use cases:

* Developers accessing private servers
* Administrators accessing internal applications
* Employees accessing private AWS resources

---

# 24. Types of VPN

VPNs can be broadly categorized based on their use case.

## 1. Site-to-Site VPN

Connects one network to another network.

```text
Office Network
      |
     VPN
      |
AWS VPC
```

Typical use:

```text
On-Premises <----VPN----> AWS
```

---

## 2. Client-to-Site VPN

Connects an individual user/device to a private network.

```text
Laptop
   |
  VPN
   |
AWS VPC
```

Example:

```text
Employee Laptop
       |
       v
Client VPN
       |
       v
Private Network
```

---

## 3. Remote Access VPN

Remote Access VPN allows users working remotely to securely access company resources.

```text
Remote Employee
       |
    Internet
       |
      VPN
       |
Company Network
```

---

## 4. Site-to-Site IPsec VPN

IPsec can be used to establish an encrypted tunnel between networks.

```text
Network A
    |
    | IPsec Tunnel
    |
Network B
```

AWS Site-to-Site VPN commonly uses IPsec VPN tunnels.

---

# 25. VPN vs VPC Peering vs Transit Gateway

| Feature                  | VPN                               | VPC Peering                          | Transit Gateway                      |
| ------------------------ | --------------------------------- | ------------------------------------ | ------------------------------------ |
| Main purpose             | Secure network connection         | Connect two VPCs                     | Connect many networks                |
| Encryption               | Yes, VPN tunnel                   | Traffic stays on AWS private network | Traffic stays on AWS private network |
| On-premises connectivity | Yes                               | No                                   | Yes                                  |
| VPC-to-VPC               | Possible through VPN architecture | Yes                                  | Yes                                  |
| Centralized hub          | No                                | No                                   | Yes                                  |
| Best for                 | On-prem to AWS / remote users     | Simple VPC-to-VPC                    | Large multi-VPC networks             |

---

# 26. Complete AWS Networking Architecture

A larger AWS environment can look like:

```text
                           Internet
                              |
                              |
                       Internet Gateway
                              |
                    +---------+---------+
                    |                   |
              Public Subnet        Public Subnet
                    |                   |
             Bastion Host          NAT Gateway
                                        |
                                        |
                                  Private Subnet
                                        |
                                      EC2
                                        |
                                        |
                                  VPC Endpoint
                                        |
                                        v
                                  AWS Services


          On-Premises Network
                  |
                  |
             VPN Connection
                  |
                  v
           Transit Gateway
            /      |       \
           /       |        \
        VPC-A    VPC-B     VPC-C
          |        |         |
        EC2      EC2       EC2
```

---

# 27. When to Use What?

## Use Bastion Host When:

You need administrative access to private EC2 instances.

```text
Admin ---> Bastion ---> Private EC2
```

---

## Use NAT Gateway When:

Private instances need outbound internet access.

```text
Private EC2 ---> NAT Gateway ---> Internet
```

Examples:

* Downloading packages
* Installing updates
* Accessing external APIs
* Downloading software

---

## Use VPC Peering When:

You have a small number of VPCs and need direct private communication.

```text
VPC-A <----> VPC-B
```

---

## Use Transit Gateway When:

You have many VPCs, VPNs, or networks and need centralized connectivity.

```text
VPCs
 |
Transit Gateway
 |
VPN
 |
On-Premises
```

---

## Use VPC Endpoint When:

Private resources need to access supported AWS services privately.

```text
Private EC2 ---> VPC Endpoint ---> AWS Service
```

---

## Use Site-to-Site VPN When:

You need to connect an on-premises network to AWS.

```text
On-Premises <---- VPN ----> AWS
```

---

## Use Client VPN When:

Individual users need secure remote access to private AWS resources.

```text
Developer Laptop
       |
   Client VPN
       |
      VPC
```

---

# 28. Quick Revision

```text
Bastion Host
     |
     +--> Secure administrative access to private servers

NAT Gateway
     |
     +--> Private subnet ---> Internet

VPC Peering
     |
     +--> VPC <----> VPC

Transit Gateway
     |
     +--> Many VPCs + VPNs ---> Centralized connectivity

VPC Endpoint
     |
     +--> Private resources ---> AWS services

Site-to-Site VPN
     |
     +--> On-Premises <----> AWS

Client VPN
     |
     +--> User/Laptop <----> AWS Private Resources
```

# 29. Important Interview Questions

### Q1. What is a Bastion Host?

A Bastion Host is an EC2 instance placed in a public subnet and used as a secure jump server to access private resources.

### Q2. Can a private EC2 access the internet?

Yes. A private EC2 can access the internet through a NAT Gateway, provided the route table, security group, and network configuration are correctly configured.

### Q3. Can the internet directly access an EC2 behind a NAT Gateway?

No. NAT Gateway provides outbound connectivity; it does not allow unsolicited inbound connections to private instances.

### Q4. What is VPC Peering?

VPC Peering provides private connectivity between two VPCs.

### Q5. What is the major limitation of VPC Peering?

It does not provide transitive routing.

### Q6. What is Transit Gateway?

Transit Gateway is a centralized network hub that connects multiple VPCs, VPN connections, and other supported network attachments.

### Q7. Why use Transit Gateway instead of VPC Peering?

Transit Gateway simplifies connectivity and routing when an organization has many VPCs and networks.

### Q8. What is a VPC Endpoint?

A VPC Endpoint provides private connectivity from a VPC to supported AWS services without requiring an internet gateway or NAT Gateway for that service access.

### Q9. What are the two major VPC Endpoint types?

```text
1. Gateway Endpoint
2. Interface Endpoint
```

### Q10. Which AWS services use Gateway Endpoints?

Primarily:

```text
S3
DynamoDB
```

### Q11. What is an Interface Endpoint?

An Interface Endpoint uses AWS PrivateLink and creates an ENI with private IP addresses in the VPC.

### Q12. What is a VPN?

VPN is an encrypted network connection that allows secure communication across an untrusted network such as the internet.

### Q13. What is Site-to-Site VPN?

It connects an on-premises network to an AWS network through encrypted VPN tunnels.

### Q14. What is Client VPN?

It allows individual users to establish secure VPN connections to access private resources.

---

# 30. Key Concepts to Remember

```text
Public Subnet
    |
    +--> Internet Gateway
    |
    +--> Bastion Host
    |
    +--> NAT Gateway


Private Subnet
    |
    +--> Private EC2
    |
    +--> NAT Gateway ---> Internet
    |
    +--> VPC Endpoint ---> AWS Services


Multiple VPCs
    |
    +--> VPC Peering
    |
    +--> Transit Gateway


On-Premises
    |
    +--> Site-to-Site VPN
    |
    +--> Transit Gateway


Individual User
    |
    +--> Client VPN
```

## One-Line Summary

| Service              | Purpose                                                |
| -------------------- | ------------------------------------------------------ |
| **Bastion Host**     | Securely access private servers                        |
| **NAT Gateway**      | Give private resources outbound internet access        |
| **VPC Peering**      | Privately connect two VPCs                             |
| **Transit Gateway**  | Centrally connect multiple VPCs and networks           |
| **VPC Endpoint**     | Privately access supported AWS services                |
| **Site-to-Site VPN** | Connect on-premises network to AWS                     |
| **Client VPN**       | Connect individual users securely to private resources |
