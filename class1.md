Create an ec2 instance (Server, Virtual Machine, Node)

Step-by-Step Guide to Create an EC2 Instance
Log in to AWS Management Console:

Go to the AWS Management Console and log in with your credentials.
Navigate to EC2:

In the AWS Management Console, search for “EC2” in the services search bar and select it.
Launch Instance:

Click on the “Launch Instance” button.
Choose an Amazon Machine Image (AMI):

Select an AMI that suits your needs (e.g., Amazon Linux, Ubuntu, RHEL). You can choose from free-tier eligible options if you're just getting started.
Choose an Instance Type:

Select the instance type (e.g., t2.micro for free tier). Click “Next: Configure Instance Details” after making your selection.
Configure Instance Details:

Set the number of instances, network settings, and other details. For a simple setup, the default settings are usually sufficient. Click “Next: Add Storage.”
Add Storage:

Modify the storage size if necessary. The default is typically sufficient, but you can add more if needed. Click “Next: Add Tags.”
Review and Launch:

Review your instance configuration. Click “Launch.”
Select or Create a Key Pair:

If you don’t have a key pair, create a new one and download it (keep it safe; you won't be able to download it again). If you have one, select it. Click “Launch Instances.”
View Instances:

Click on “View Instances” to see your new EC2 instance. It may take a few minutes to initialize.
Accessing Your EC2 Instance
For Linux Instances:

Use SSH to connect. The command will look like this:
ssh -i /path/to/your-key.pem <username>@<your-ec2-public-ip>
For an Amazon Linux AMI, the username is ec2-user.
For a CentOS AMI, the username is centos or ec2-user.
For a Debian AMI, the username is admin.
For a Fedora AMI, the username is fedora or ec2-user.
For a RHEL AMI, the username is ec2-user or root.
For a SUSE AMI, the username is ec2-user or root.
For an Ubuntu AMI, the username is ubuntu.
For an Oracle AMI, the username is ec2-user.
For a Bitnami AMI, the username is bitnami.

Preffered SSH client: 

git bash 
