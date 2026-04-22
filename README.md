# AWS-Virtual-Private-Cloud-VPC-Setup-with-EC2

AWS VPC Setup with EC2 Deployment
1. Purpose

This SOP provides a step-by-step guide to creating a secure and scalable cloud network using Amazon Web Services (AWS). It covers:

Creating a Virtual Private Cloud (VPC)
Configuring subnets, route tables, and internet access
Implementing security using NACLs and Security Groups
Launching and connecting to an EC2 instance
Enabling monitoring with Flow Logs
2. Overview of Architecture
VPC → Private network environment
Subnets → Logical network segments (Frontend, Backend, Database)
Internet Gateway → Enables internet access
Route Table → Controls traffic routing
Network ACLs → Subnet-level security
Security Groups → Instance-level security
EC2 Instance → Virtual machine running workloads
3. Prerequisites
Active AWS account
Access to AWS Management Console
Basic understanding of networking (CIDR, IP addressing)
Remote Desktop Client (for Windows EC2 connection)
4. Procedure
4.1 Create VPC
Log in to AWS Console
Search for VPC
Click Create VPC
Select VPC Only
Configure:
Name: Firstbank-VPC
IPv4 CIDR: (e.g., 10.0.0.0/16)
Tenancy: Default
Disable VPC Encryption
Click Create VPC

✅ A default Route Table is automatically created

4.2 Create and Attach Internet Gateway
Navigate to Internet Gateways
Click Create Internet Gateway
Name: Firstbank-IG
Click Create
Attach to VPC:
Select the IG
Click Actions → Attach to VPC
Select Firstbank-VPC
Click Attach
4.3 Configure Route Table
Go to Route Tables
Rename the default route table
Click Edit Routes
Add:
Destination: 0.0.0.0/0
Target: Internet Gateway (Firstbank-IG)
Save changes
4.4 Create Subnets

Delete default subnets first.

Create 3 subnets:

Subnet Name	CIDR Block	Purpose
Frontend-Subnet	10.0.1.0/24	Public
Backend-Subnet	10.0.2.0/24	Private
Database-Subnet	10.0.3.0/24	Private

Steps:

Go to Subnets
Click Create Subnet
Select VPC
Assign:
Name
Availability Zone
CIDR block
Repeat for all three
4.5 Associate Subnets with Route Table
Open Route Table
Go to Subnet Associations
Click Edit Associations
Select all 3 subnets
Save
4.6 Enable Internet Access

Ensure route table includes:

0.0.0.0/0 → Internet Gateway
4.7 Configure VPC Flow Logs
Open VPC → Flow Logs
Click Create Flow Log
Configure:
Name: Accept-Flow-Log
Filter: ACCEPT
Destination: S3
Create S3 Bucket:
Name bucket
Copy ARN
Paste ARN into Flow Log setup
Create log

Repeat for:

Reject-Flow-Log (Filter: REJECT)
4.8 Create Network ACLs

Create 3 NACLs:

Frontend-NACL
Backend-NACL
Database-NACL
Associate with Subnets:
Frontend → Frontend-NACL
Backend → Backend-NACL
Database → Database-NACL
4.9 Configure NACL Rules
Frontend NACL

Inbound Rules:

Allow RDP (TCP 3389)
Allow DNS (TCP/UDP 53)

Outbound Rules:

Allow All Traffic
4.10 Create Security Groups

Create:

Frontend-SG
Backend-SG
Database-SG
Frontend Security Group Rules:
Inbound:
RDP (3389)
Source:
My IP (recommended) OR
0.0.0.0/0 (open access)
4.11 Enable DNS Hostnames
Select VPC
Click Actions → Edit VPC Settings
Enable:
DNS Resolution
DNS Hostnames
Save
5. Launch EC2 Instance
Navigate to Amazon EC2
Click Launch Instance
Configure:
Name: Windows-VM
AMI: Windows Server
Instance Type: (e.g., t2.micro)
Key Pair:
Create new key pair (.pem)
Download locally
Network Settings:
Select Firstbank-VPC
Choose Frontend Subnet
Enable Auto-assign Public IP
Select Frontend-SG
Storage:
Adjust size as needed

Click Launch Instance

6. Connect to EC2 (RDP)
Select instance → Click Connect
Choose RDP Client
Download RDP file
Get Password:
Click Get Password
Upload private key (.pem)
Decrypt password
Connect:
Open Remote Desktop
Enter:
IP address
Username: Administrator
Password: Decrypted password
7. Validate Connectivity
Confirm:
Internet access from VM
RDP connection works
DNS resolution works
8. Clean Up

To avoid charges:

Go to EC2 Instances
Select instance
Click Terminate
9. Key Notes / Best Practices
Avoid using 0.0.0.0/0 in production (security risk)
Use private subnets for backend and database
Enable logging for monitoring and auditing
Always terminate unused resources to prevent billing
10. Conclusion

This setup demonstrates a production-style AWS network architecture, including:

Isolated network design
Layered security (NACL + Security Groups)
Internet routing configuration
Monitoring with Flow Logs
Virtual machine deployment and access
