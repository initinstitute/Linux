# Class 38 - AWS RDS and DynamoDB
class Link: https://youtu.be/Db4MZghe89s
## 1. Amazon RDS

**Amazon RDS (Relational Database Service)** is a managed AWS service used to create, operate, and scale relational databases.

Instead of installing and maintaining a database manually on an EC2 instance, AWS manages many administrative tasks such as:

* Database installation
* Patching
* Backups
* Storage management
* Monitoring
* High availability

### Supported Database Engines

Amazon RDS supports popular relational database engines such as:

* MySQL
* PostgreSQL
* MariaDB
* Oracle
* Microsoft SQL Server
* Amazon Aurora

---

## 2. RDS Architecture

Basic architecture:

```text
             Internet
                |
                v
          Application
             Server
             EC2
                |
                v
          Private Subnet
                |
                v
          +-----------+
          |    RDS    |
          | Database  |
          +-----------+
```

Normally, RDS should be placed in a **private subnet** so that the database is not directly accessible from the internet.

---

## 3. Creating an RDS Database

Basic steps:

1. Open the AWS Management Console.
2. Search for **RDS**.
3. Click **Create database**.
4. Select the database engine.
5. Select the required version.
6. Choose the template:

   * Production
   * Dev/Test
   * Free tier, if available
7. Provide:

   * DB instance identifier
   * Master username
   * Master password
8. Select the instance class.
9. Configure storage.
10. Configure the VPC and subnet.
11. Configure security groups.
12. Configure backup settings.
13. Click **Create database**.

---

## 4. RDS Endpoint

After creating an RDS instance, AWS provides a database endpoint.

Example:

```text
mydb.xxxxxxxxxxxx.ap-south-1.rds.amazonaws.com
```

Applications use this endpoint to connect to the database.

Example PostgreSQL connection:

```bash
psql -h mydb.xxxxxxxxxxxx.ap-south-1.rds.amazonaws.com \
     -U postgres \
     -d mydatabase
```

---

## 5. RDS Security Group

The RDS security group controls which systems can connect to the database.

Example for PostgreSQL:

```text
Type: PostgreSQL
Port: 5432
Source: Application Server Security Group
```

For MySQL:

```text
Type: MySQL/Aurora
Port: 3306
Source: Application Server Security Group
```

### Important

Avoid allowing:

```text
0.0.0.0/0
```

for database ports in production.

Prefer allowing access only from the application server's security group.

---

## 6. RDS Storage

RDS supports different storage options depending on the database configuration.

Common options include:

* General Purpose SSD
* Provisioned IOPS SSD

Storage can be configured according to application requirements.

---

## 7. RDS Backups

RDS provides automated backups.

Backups can be used to:

* Recover from accidental data deletion
* Restore a database
* Perform point-in-time recovery

RDS also supports **manual snapshots**.

### Snapshot

A snapshot is a point-in-time backup of the database.

Example:

```text
RDS Database
     |
     v
Create Snapshot
     |
     v
RDS Snapshot
```

A snapshot can later be used to create a new RDS database.

---

## 8. Multi-AZ

**Multi-AZ (Availability Zone)** is used for high availability.

Basic architecture:

```text
             RDS
              |
       +------+------+
       |             |
       v             v
    AZ-1           AZ-2
   Primary        Standby
```

The standby database is maintained in another Availability Zone.

If the primary database has a failure, RDS can perform automatic failover.

### Benefits

* High availability
* Automatic failover
* Improved reliability

---

## 9. Read Replica

A **Read Replica** is a copy of the database used mainly for read operations.

```text
              Application
                  |
                  v
             RDS Primary
              /       \
             /         \
            v           v
      Read Replica   Read Replica
```

Read replicas can help:

* Handle read-heavy workloads
* Improve read performance
* Reduce load on the primary database

### Multi-AZ vs Read Replica

| Feature            | Multi-AZ                  | Read Replica            |
| ------------------ | ------------------------- | ----------------------- |
| Main purpose       | High availability         | Read scaling            |
| Automatic failover | Yes                       | Not the primary purpose |
| Used for reads     | No                        | Yes                     |
| Primary use        | Disaster/failure recovery | Performance             |

---

# 10. Amazon DynamoDB

**Amazon DynamoDB** is a fully managed **NoSQL database** service provided by AWS.

It is designed for:

* High performance
* Low latency
* Large-scale applications
* Serverless applications

DynamoDB does not use traditional relational tables with rows and columns in the same way as databases such as MySQL or PostgreSQL.

---

## 11. RDS vs DynamoDB

### RDS

RDS is a **relational database**.

Example:

```text
MySQL
PostgreSQL
Oracle
SQL Server
```

Data is organized into tables with rows and columns.

### DynamoDB

DynamoDB is a **NoSQL key-value/document database**.

It stores items using keys and attributes.

---

## 12. DynamoDB Basic Structure

```text
DynamoDB
   |
   +---- Table
          |
          +---- Item
          |
          +---- Item
          |
          +---- Item
```

For example, a `Users` table:

```text
UserId     Name       Age
------     ----       ---
101        Rahul      25
102        Ravi       30
103        Priya      28
```

Each record is called an **Item**.

The individual fields are called **Attributes**.

---

## 13. Primary Key

The primary key uniquely identifies an item in a DynamoDB table.

There are two main types.

### Partition Key

A single attribute is used as the primary key.

Example:

```text
UserId
```

Example items:

```text
UserId = 101
UserId = 102
UserId = 103
```

Each value must be unique.

---

## 14. Composite Primary Key

A composite primary key contains:

```text
Partition Key + Sort Key
```

Example:

```text
Partition Key: UserId
Sort Key: OrderId
```

Example:

```text
UserId    OrderId
------    -------
101       ORD001
101       ORD002
102       ORD003
```

The same partition key can have multiple items as long as the sort key is different.

---

# 15. Creating a DynamoDB Table

Basic steps:

1. Open AWS Management Console.
2. Search for **DynamoDB**.
3. Click **Create table**.
4. Enter the table name.

Example:

```text
Users
```

5. Configure the partition key.

Example:

```text
UserId
```

6. Choose the required capacity mode.
7. Create the table.

---

# 16. Adding Items to DynamoDB

After creating the table:

```text
DynamoDB
   |
   v
Users Table
   |
   v
Create Item
```

Example:

```text
UserId = 101
Name   = Harish
Age    = 25
City   = Hyderabad
```

Another item:

```text
UserId = 102
Name   = Rahul
Age    = 27
City   = Mumbai
```

---

# 17. DynamoDB Capacity Modes

DynamoDB provides two common capacity modes.

### Provisioned Capacity

You specify the required:

* Read Capacity Units (RCU)
* Write Capacity Units (WCU)

Example:

```text
Read Capacity  = 5 RCU
Write Capacity = 5 WCU
```

### On-Demand Capacity

AWS automatically manages capacity based on application traffic.

It is useful when traffic is unpredictable or workloads vary.

---

# 18. DynamoDB Read and Write

### Write

Adding or updating an item is a write operation.

```text
Application
     |
     v
DynamoDB
     |
     v
Write Item
```

### Read

Retrieving an item is a read operation.

```text
Application
     |
     v
DynamoDB
     |
     v
Read Item
```

---

# 19. DynamoDB CLI Commands

### List Tables

```bash
aws dynamodb list-tables
```

### Create Table

Example:

```bash
aws dynamodb create-table \
  --table-name Users \
  --attribute-definitions AttributeName=UserId,AttributeType=N \
  --key-schema AttributeName=UserId,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST
```

### List Tables

```bash
aws dynamodb list-tables
```

### Describe Table

```bash
aws dynamodb describe-table \
  --table-name Users
```

### Put Item

```bash
aws dynamodb put-item \
  --table-name Users \
  --item '{"UserId":{"N":"101"},"Name":{"S":"Harish"},"Age":{"N":"25"}}'
```

### Get Item

```bash
aws dynamodb get-item \
  --table-name Users \
  --key '{"UserId":{"N":"101"}}'
```

### Scan Table

```bash
aws dynamodb scan \
  --table-name Users
```

### Delete Item

```bash
aws dynamodb delete-item \
  --table-name Users \
  --key '{"UserId":{"N":"101"}}'
```

---

# 20. RDS vs DynamoDB

| Feature           | RDS                           | DynamoDB                                 |
| ----------------- | ----------------------------- | ---------------------------------------- |
| Database type     | Relational                    | NoSQL                                    |
| Data model        | Tables, rows, columns         | Items and attributes                     |
| SQL               | Yes                           | No traditional SQL                       |
| Schema            | Structured                    | Flexible                                 |
| Scaling           | Mostly instance/storage based | Highly scalable                          |
| High availability | Multi-AZ                      | Built-in distributed architecture        |
| Best for          | Relational applications       | Large-scale key-value/document workloads |
| Joins             | Supported                     | Not traditional relational joins         |
| Transactions      | Supported                     | Supported                                |
| AWS managed       | Yes                           | Yes                                      |

---

# 21. When to Use RDS

Use RDS when:

* Application requires a relational database.
* SQL queries are required.
* Relationships between tables are important.
* Joins are required.
* Structured data is being used.

Example applications:

```text
Banking Application
ERP Application
E-commerce Application
Employee Management System
```

---

# 22. When to Use DynamoDB

Use DynamoDB when:

* Very low latency is required.
* Application needs large-scale NoSQL storage.
* Traffic can grow significantly.
* Flexible data models are required.
* Serverless architecture is being used.

Example applications:

```text
Gaming Applications
IoT Applications
Serverless Applications
Real-time Applications
High-traffic Web Applications
```

---

# 23. Simple Architecture Example

### RDS Architecture

```text
User
 |
 v
Application / EC2
 |
 v
RDS PostgreSQL
 |
 v
Relational Data
```

### DynamoDB Architecture

```text
User
 |
 v
API / Lambda
 |
 v
DynamoDB
 |
 v
NoSQL Items
```

---

# 24. Key Points

* **RDS** is a managed relational database service.
* RDS supports engines such as MySQL, PostgreSQL, MariaDB, Oracle and SQL Server.
* RDS provides automated backups and snapshots.
* **Multi-AZ** is mainly used for high availability.
* **Read Replicas** are mainly used for read scaling.
* **DynamoDB** is a managed NoSQL database.
* DynamoDB stores data as items and attributes.
* DynamoDB uses a **partition key** and optionally a **sort key**.
* DynamoDB supports **Provisioned** and **On-Demand** capacity modes.
* RDS is generally preferred for relational/SQL workloads.
* DynamoDB is generally preferred for highly scalable key-value/document workloads.
