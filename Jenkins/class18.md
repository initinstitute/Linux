# 📘 Class 18 Notes - GitHub Integration with Jenkins, Monolithic vs Microservices, 2-Tier & 3-Tier Architecture
Recording: https://youtu.be/DOnBfk2jbiU
## 1. Integration of GitHub with Jenkins

GitHub and Jenkins are integrated to automate the software development process. Whenever code is pushed to GitHub, Jenkins automatically detects the changes, pulls the latest code, builds the project, runs tests, and deploys the application.

### Why Integrate GitHub with Jenkins?

- Automates the build process.
- Reduces manual work.
- Detects errors quickly.
- Supports Continuous Integration (CI).
- Improves software quality.

### Architecture

```
Developer
     │
     ▼
GitHub Repository
     │
     │ (Webhook / Poll SCM)
     ▼
Jenkins Server
     │
     ├── Pull Source Code
     ├── Build Application
     ├── Run Tests
     └── Deploy Application
```

### Steps to Integrate GitHub with Jenkins

#### Step 1: Install Git Plugin

- Go to **Manage Jenkins**
- Select **Plugins**
- Install **Git Plugin** (if not already installed)

#### Step 2: Configure Git

Go to:

```
Manage Jenkins
→ Tools
→ Git Installations
```

Provide the Git installation path if required.

#### Step 3: Create a Jenkins Job

- Click **New Item**
- Enter Job Name
- Select **Freestyle Project** (or Pipeline)
- Click **OK**

#### Step 4: Connect GitHub Repository

Under **Source Code Management**

- Select **Git**
- Paste the GitHub Repository URL

Example:

```
https://github.com/username/project.git
```

Provide GitHub credentials if the repository is private.

#### Step 5: Configure Build Trigger

Choose one of the following:

### Poll SCM

Jenkins checks GitHub at regular intervals.

Example:

```
H/5 * * * *
```

Checks every 5 minutes.

### GitHub Webhook

GitHub notifies Jenkins immediately whenever code is pushed.

Webhook URL:

```
http://<jenkins-server>:8080/github-webhook/
```

#### Step 6: Configure Build Steps

Examples:

For Maven

```
clean install
```

For Shell Script

```bash
echo "Building Project..."
mvn clean package
```

#### Step 7: Save and Build

Click

```
Save
→ Build Now
```

Jenkins will:

- Pull latest code
- Build the project
- Execute tests
- Deploy (if configured)

---

# 2. Monolithic Architecture

A **Monolithic Architecture** is a traditional software architecture where the entire application is developed as a single unit.

### Structure

```
Application

├── UI
├── Business Logic
├── Database Access
└── Database
```

Everything is packaged and deployed together.

### Advantages

- Easy to develop initially.
- Easy deployment.
- Good for small applications.
- Simpler testing.

### Disadvantages

- Difficult to maintain as the application grows.
- Entire application must be redeployed for every change.
- Scaling the entire application is expensive.
- Failure in one module may affect the whole application.

### Example

An Online Shopping Application where:

- Login
- Products
- Orders
- Payments

are all inside one application.

---

# 3. Microservices Architecture

Microservices architecture divides a large application into multiple small independent services.

Each service performs one specific task.

### Structure

```
                API Gateway
                     │
      ┌──────────────┼──────────────┐
      │              │              │
      ▼              ▼              ▼
 User Service   Product Service  Order Service
      │              │              │
      ▼              ▼              ▼
 Database      Database      Database
```

Each service has its own:

- Code
- Database
- Deployment
- Team

### Advantages

- Easy to scale individual services.
- Faster deployments.
- Independent development.
- Better fault isolation.
- Easier maintenance.

### Disadvantages

- More complex architecture.
- Requires monitoring.
- Service communication becomes complex.
- Higher infrastructure cost.

### Example

Amazon uses microservices for:

- Login Service
- Cart Service
- Payment Service
- Search Service
- Delivery Service

Each service works independently.

---

# Monolithic vs Microservices

| Feature | Monolithic | Microservices |
|----------|------------|---------------|
| Deployment | Single deployment | Independent deployment |
| Scalability | Scale entire application | Scale only required service |
| Development | Single codebase | Multiple codebases |
| Maintenance | Difficult for large apps | Easier |
| Failure | Entire application affected | Only one service affected |
| Technology | Single technology | Different technologies possible |
| Database | Usually one database | Separate database for each service |

---

# 4. 2-Tier Architecture

A **2-Tier Architecture** consists of two layers:

1. Client Layer
2. Database Layer

The client directly communicates with the database.

### Architecture

```
+-------------------+
| Client            |
| (Application/UI)  |
+---------+---------+
          │
          │
          ▼
+-------------------+
| Database Server   |
+-------------------+
```

### Advantages

- Simple architecture.
- Easy to develop.
- Fast communication.
- Suitable for small applications.

### Disadvantages

- Poor scalability.
- Limited security.
- Difficult maintenance.
- Not suitable for enterprise applications.

### Example

Desktop Inventory Management System.

---

# 5. 3-Tier Architecture

A **3-Tier Architecture** separates the application into three independent layers.

1. Presentation Layer
2. Application Layer
3. Database Layer

### Architecture

```
+-----------------------+
| Presentation Layer    |
| (Browser / UI)        |
+-----------+-----------+
            │
            ▼
+-----------------------+
| Application Layer     |
| (Business Logic)      |
+-----------+-----------+
            │
            ▼
+-----------------------+
| Database Layer        |
| (MySQL, PostgreSQL)   |
+-----------------------+
```

### Layers

### 1. Presentation Layer

- User Interface
- Browser
- Mobile App

### 2. Application Layer

- Business Logic
- Authentication
- APIs
- Processing

### 3. Database Layer

- Stores application data
- Handles queries

### Advantages

- Better security.
- Easy maintenance.
- Scalable.
- Independent layers.
- Suitable for enterprise applications.

### Disadvantages

- More complex than 2-tier.
- Slightly higher deployment cost.

### Example

Banking Applications

```
Customer
     │
     ▼
Web Application
     │
     ▼
Application Server
     │
     ▼
Database
```

---

# Difference Between 2-Tier and 3-Tier Architecture

| Feature | 2-Tier | 3-Tier |
|----------|--------|---------|
| Layers | 2 | 3 |
| Business Logic | Client | Application Server |
| Security | Low | High |
| Scalability | Low | High |
| Maintenance | Difficult | Easy |
| Performance | Good for small apps | Better for enterprise apps |
| Suitable For | Small applications | Large enterprise applications |

---

# Summary

- GitHub integration with Jenkins enables Continuous Integration by automatically building projects whenever code is pushed.
- Monolithic architecture combines all modules into one application, while Microservices split the application into independent services.
- 2-Tier architecture directly connects the client to the database.
- 3-Tier architecture introduces an application layer between the client and the database, improving scalability, security, and maintainability.
