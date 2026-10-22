# Class 60: AWS Other Important Services (AWS Secrets Manager, AWS Systems Manager Parameter Store, AWS Elastic Beanstalk, AWS Organizations, AWS Control Tower,
Landing Zone, Guardrails, AWS Systems Manager, AWS KMS, Cross-Account IAM Role, AWS STS AssumeRole, AWS CloudFormation, HashiCorp Vault)


class URL's: https://youtu.be/NrvVx0lW9qM
             https://youtu.be/3lnpFx5CSYA
## 1. AWS Secrets Manager

**AWS Secrets Manager** is a service used to securely store, manage, and retrieve sensitive information such as passwords, database credentials, and API keys.

### Why do we use Secrets Manager?

- To avoid storing passwords directly in application code.
- To securely share database credentials with applications.
- To rotate secrets automatically.
- To control access using IAM permissions.
- To encrypt secrets using AWS KMS.

### Example

Suppose a Django application connects to an RDS database. Instead of writing the database password in the application code, we store it in Secrets Manager.

The application retrieves the password securely when required.

### Important Features

- Secure secret storage.
- Automatic secret rotation when supported and configured.
- Integration with AWS services.
- Encryption using AWS KMS.
- Access control through IAM.

**Interview Question:** What is the difference between Secrets Manager and Parameter Store?

---

## 2. AWS Systems Manager Parameter Store

**AWS Systems Manager Parameter Store** is a service used to store configuration data and secrets as parameters.

It helps manage application configurations centrally.

### Types of Parameters

1. **String:** Stores plain-text configuration values.
2. **StringList:** Stores comma-separated values.
3. **SecureString:** Stores sensitive values encrypted using AWS KMS.

### Example

Suppose an application needs the following configuration:

```text
APP_ENV = production
DB_HOST = mydb.example.com
DB_PASSWORD = mypassword
```

We can store these values in Parameter Store rather than hardcoding them in the application.

For sensitive values, use `SecureString`.

### Important Features

- Centralized configuration management.
- SecureString encryption using KMS.
- IAM-based access control.
- Version history for parameters.
- Integration with AWS Systems Manager and other AWS services.

### Secrets Manager vs Parameter Store

| Feature | Secrets Manager | Parameter Store |
|---|---|---|
| Main purpose | Manage secrets | Manage configuration and parameters |
| Password rotation | Built-in rotation workflows | Usually requires custom automation |
| Configuration values | Supported | Common use case |
| Encryption | Supported | Supported with SecureString |
| Cost | Generally higher | Standard parameters have no additional charge within service limits |

**Remember:** Use Secrets Manager when managed secret rotation is important. Use Parameter Store for centralized application configuration and simpler parameter management.

---

## 3. AWS Elastic Beanstalk

**AWS Elastic Beanstalk** is a Platform as a Service (PaaS) offering that helps deploy and manage applications without manually managing every underlying AWS resource.

You upload your application, and Elastic Beanstalk provisions and manages the required infrastructure.

### How does it work?

Elastic Beanstalk can manage resources such as:

- EC2 instances
- Load balancers
- Auto Scaling groups
- Security groups
- CloudWatch monitoring

### Example

Suppose you have a Python Flask application.

1. Develop the application.
2. Upload the application to Elastic Beanstalk.
3. Select the Python platform.
4. Elastic Beanstalk creates the required environment.
5. The application becomes accessible through its environment URL.

### Deployment Process

```text
Developer
    |
    v
Application Code
    |
    v
Elastic Beanstalk
    |
    v
Load Balancer + Auto Scaling + EC2
    |
    v
Running Application
```

### Important Features

- Application deployment.
- Environment management.
- Monitoring and health reporting.
- Automatic scaling when configured.
- Multiple deployment options.

**Remember:** Elastic Beanstalk manages much of the infrastructure, but you can still configure the underlying resources.

---

## 4. AWS Organizations

**AWS Organizations** is a service used to centrally manage multiple AWS accounts.

Instead of managing every AWS account separately, an organization can group accounts and apply centralized policies.

### Why do we use it?

- Centralized account management.
- Consolidated billing.
- Grouping accounts by department or environment.
- Applying Service Control Policies (SCPs).
- Separating production and development environments.

### Example

A company has four AWS accounts:

```text
AWS Organization
       |
       |--- Production Account
       |--- Development Account
       |--- Testing Account
       |--- Security Account
```

The company can manage these accounts centrally using AWS Organizations.

### Important Concepts

- **Management account:** The main account that manages the organization.
- **Member account:** An account belonging to the organization.
- **Organizational Unit (OU):** A group used to organize accounts.
- **Service Control Policy (SCP):** Defines the maximum permissions available to IAM principals in affected accounts. It does not grant permissions by itself.

**Example:** An SCP can restrict member accounts from using certain AWS Regions, subject to the policy configuration and applicable exceptions.

---

## 5. AWS Control Tower

**AWS Control Tower** helps organizations establish and govern a secure multi-account AWS environment.

It automates the setup of a landing zone and provides controls to help maintain governance across accounts.

### Why do we use Control Tower?

- To establish a standardized multi-account environment.
- To automate account creation and enrollment.
- To apply governance controls.
- To monitor compliance with organizational rules.
- To integrate with AWS Organizations and other AWS governance services.

### Example

A company wants separate accounts for:

- Production
- Development
- Testing
- Security and auditing

Control Tower helps establish this environment using predefined configurations and governance controls.

### Important Features

- Landing zone setup.
- Account Factory for account provisioning.
- Preventive and detective controls.
- Integration with AWS Organizations.
- Centralized governance and compliance monitoring.

**Remember:** AWS Control Tower is a governance service, not a replacement for AWS Organizations. It builds on Organizations to simplify multi-account setup and management.

---

## 6. Landing Zone

A **Landing Zone** is a well-designed AWS environment that provides a foundation for running workloads securely across multiple accounts.

It establishes the account structure, security configuration, logging, and governance needed by an organization.

### Why do we need a Landing Zone?

- To separate production and non-production workloads.
- To establish security standards.
- To centralize audit logs.
- To implement identity and access management.
- To apply governance policies consistently.

### Example Architecture

```text
AWS Landing Zone
       |
       |--- Management Account
       |
       |--- Security Account
       |
       |--- Log Archive Account
       |
       |--- Production OU
       |       |--- Production Account
       |
       |--- Non-Production OU
               |--- Development Account
               |--- Testing Account
```

This is an example structure; the exact design depends on organizational requirements.

### Important Point

A landing zone can be built using AWS Control Tower or designed and managed through other AWS tools and processes.

---

## 7. Guardrails in AWS Control Tower

**Guardrails**, now generally referred to as controls in AWS Control Tower, help enforce or monitor rules across an AWS environment.

They help ensure that AWS accounts follow the organization's security and compliance requirements.

### Types of Controls

#### 1. Preventive Controls

Prevent specified actions from being performed.

They commonly use Service Control Policies (SCPs) or other supported policy mechanisms.

**Example:** Prevent users in governed accounts from disabling certain security services.

#### 2. Detective Controls

Detect whether resources or configurations violate defined requirements.

They commonly use AWS Config rules.

**Example:** Detect S3 buckets that do not meet required security settings.

#### 3. Proactive Controls

Evaluate supported resources against rules before provisioning, preventing noncompliant resources from being deployed through supported workflows.

### Simple Comparison

| Control | Purpose |
|---|---|
| Preventive | Stops restricted actions |
| Detective | Identifies noncompliant resources |
| Proactive | Checks resources before deployment |

**Remember:** Controls help govern accounts, but their actual enforcement depends on the control type, configuration, and supported resources.

---

## 8. AWS Systems Manager

**AWS Systems Manager (SSM)** is a service used to manage, operate, automate, and monitor AWS infrastructure.

It helps administrators manage EC2 instances and other supported resources from a central location.

### Important Features

#### 1. Session Manager

Allows administrators to connect to supported instances without opening inbound SSH or RDP ports, when the required configuration is in place.

#### 2. Run Command

Runs commands on managed instances remotely.

**Example:** Install NGINX on multiple EC2 instances.

#### 3. Patch Manager

Helps automate operating system patching for supported managed nodes.

#### 4. Automation

Automates operational tasks such as starting, stopping, or configuring resources.

#### 5. Parameter Store

Stores configuration values and secrets.

#### 6. Fleet Manager

Provides a centralized interface for managing supported servers and instances.

### Example

Suppose a company has 100 EC2 instances.

Instead of logging in to every instance individually, an administrator can use Systems Manager Run Command to execute an approved command across multiple managed instances.

**Remember:** Systems Manager requires appropriate IAM permissions and the necessary managed-node configuration, such as SSM Agent and connectivity.

---

## 9. AWS KMS

**AWS Key Management Service (KMS)** is a service used to create and manage cryptographic keys for encrypting and decrypting data.

It integrates with many AWS services, including S3, EBS, RDS, and Secrets Manager.

### Why do we use KMS?

- To encrypt sensitive data.
- To control who can use encryption keys.
- To audit key usage.
- To manage key policies and rotation settings.
- To support encryption across AWS services.

### Types of KMS Keys

#### AWS Owned Keys

Owned and managed by AWS for supported services. Customers generally do not manage these keys directly.

#### AWS Managed Keys

Created and managed by AWS for a particular service in the customer's account.

#### Customer Managed Keys

Created or configured and managed by the customer, with greater control over policies, rotation settings, and permissions.

### Example: S3 Encryption

1. A user uploads an object to S3.
2. S3 uses the configured encryption method and key.
3. The data is stored encrypted.
4. An authorized request can retrieve the data, subject to the required permissions.

### Important Concepts

- **Encryption:** Converts readable data into protected ciphertext.
- **Decryption:** Converts ciphertext back into readable data.
- **Key policy:** Defines permissions for using a KMS key.
- **Key rotation:** Changes the cryptographic key material according to the applicable rotation mechanism.

**Remember:** Encryption permissions and data access permissions are separate. A user may need permission to access the resource and permission to use the KMS key.

---

## 10. Cross-Account Role and AssumeRole

Cross-account access allows a user or service in one AWS account to access resources in another AWS account without sharing long-term access keys.

It commonly uses an IAM role and AWS Security Token Service (STS).

### Example

Suppose:

- Account A is the Development account.
- Account B is the Production account.

A developer in Account A needs read-only access to an S3 bucket in Account B.

### How does it work?

1. Create an IAM role in Account B.
2. Configure the role's trust policy to allow the intended principal from Account A to assume the role.
3. Attach the required permissions to the role.
4. Grant the principal in Account A permission to call `sts:AssumeRole`.
5. The principal assumes the role.
6. AWS STS returns temporary security credentials.
7. The principal uses those credentials to access permitted resources in Account B.

### Trust Policy Example

The following policy belongs to the role in Account B. Replace the account ID with the actual trusted account ID.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111122223333:root"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

Here, `111122223333` represents Account A. The trust policy allows principals from that account to assume the role, subject to their permissions and any additional conditions.

### Difference Between Trust Policy and Permission Policy

- **Trust policy:** Defines who can assume the role.
- **Permission policy:** Defines what the role can do after it is assumed.

### AWS CLI Example

```bash
aws sts assume-role \
  --role-arn arn:aws:iam::444455556666:role/CrossAccountReadOnlyRole \
  --role-session-name DevSession
```

The command returns temporary credentials when the request is authorized.

**Remember:** Cross-account access should follow least privilege. Avoid sharing permanent access keys between accounts.

---

## 11. AWS CloudFormation

**AWS CloudFormation** is an Infrastructure as Code (IaC) service used to create, update, and manage AWS resources using templates.

Instead of creating resources manually through the AWS Console, you define them in a template.

### Why do we use CloudFormation?

- Automate infrastructure creation.
- Reuse infrastructure templates.
- Maintain consistent environments.
- Track infrastructure changes.
- Manage resources as a stack.

### Example

Suppose you need an S3 bucket.

You can define it in a CloudFormation template:

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Description: Create an S3 bucket

Resources:
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-unique-demo-bucket-12345
```

The bucket name must be globally unique.

### Important Concepts

- **Template:** YAML or JSON file describing resources.
- **Stack:** A collection of resources managed together by CloudFormation.
- **Change Set:** A preview of proposed stack changes.
- **Stack Update:** Applies changes to an existing stack.
- **Rollback:** Reverts supported failed stack operations where possible.

### CloudFormation Workflow

```text
Write Template
      |
      v
Create Stack
      |
      v
CloudFormation
      |
      v
Create AWS Resources
      |
      v
Manage and Update Stack
```

**Remember:** CloudFormation is AWS-native IaC. Terraform is another IaC tool that supports multiple providers.

---

## 12. HashiCorp Vault

**HashiCorp Vault** is a secrets management tool used to securely store, access, and manage sensitive information.

It can be deployed in different environments and is not limited to AWS.

### Why do we use Vault?

- Store database passwords and API keys.
- Generate dynamic credentials.
- Manage secret access policies.
- Issue and manage supported certificates.
- Protect secrets using encryption and access controls.
- Provide audit logging when configured.

### Example

Suppose a Jenkins pipeline needs database credentials for a deployment.

Instead of storing the password directly in the Jenkinsfile, Jenkins can retrieve the secret from Vault using an approved authentication method.

### How does Vault work?

1. An application or user authenticates to Vault.
2. Vault verifies the identity.
3. Policies determine which secrets the identity can access.
4. Vault returns the authorized secret or credential.
5. Audit logs can record the access when audit devices are configured.

### Static vs Dynamic Secrets

**Static Secrets:** Existing credentials stored and managed in Vault.

**Dynamic Secrets:** Credentials generated on demand by Vault through a configured secrets engine.

For example, Vault can generate database credentials with limited permissions and a defined lifetime when the database integration is configured.

### Vault vs AWS Secrets Manager

| Feature | HashiCorp Vault | AWS Secrets Manager |
|---|---|---|
| Platform | Multi-environment | AWS service |
| Secret storage | Yes | Yes |
| Dynamic credentials | Supported through secrets engines | Supported for certain integrations |
| AWS integration | Yes | Native AWS integration |
| Deployment | Self-managed or supported managed offerings | AWS-managed service |
| Access control | Vault policies and authentication methods | IAM and resource policies |

**Remember:** Vault is useful when an organization needs centralized secrets management across multiple platforms or environments.

---

## 13. Quick Revision

| Service | Main Purpose |
|---|---|
| Secrets Manager | Securely manage secrets and automate supported rotation workflows |
| Parameter Store | Store configuration parameters and SecureString values |
| Elastic Beanstalk | Deploy and manage applications with less infrastructure management |
| AWS Organizations | Centrally manage multiple AWS accounts |
| AWS Control Tower | Set up and govern a multi-account AWS environment |
| Landing Zone | Provide a secure, governed foundation for AWS accounts |
| Guardrails / Controls | Prevent, detect, or proactively check policy violations |
| Systems Manager | Manage and automate operations on supported resources |
| AWS KMS | Manage encryption keys |
| Cross-Account Role | Access resources in another AWS account using temporary credentials |
| CloudFormation | Create and manage AWS infrastructure using templates |
| HashiCorp Vault | Manage secrets across different platforms and environments |
