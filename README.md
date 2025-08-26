# ALTSCHOOL CLOUD ENGINEERING SEMESTER 3 - MONTH 1 ASSESSMENT

## QUESTION

You are working on a lightweight product called CloudLaunch, a platform that showcases a basic company website and stores some internal private documents. You are required to deploy it using AWS core services. This exercise demonstrates your understanding of AWS fundamentals, including S3, IAM, and VPCs.

Your assessment consists of two main tasks: hosting a static site securely on S3 and designing a VPC network layout.
You must implement strict access controls for users and resources.

## 📋 Table of Contents

- [Task 1: S3 Static Website + IAM](#task-1-s3-static-website--iam)
- [Task 2: VPC Network Design](#task-2-vpc-network-design)
- [Live Links](#live-links)
- [IAM Policy](#iam-policy)
- [Testing](#testing)

---

## Task 1: S3 Static Website + IAM

### Step 1: Create S3 Buckets

**1.1 Create cloudlaunch-site-bucket (Public)**
- Navigate to S3 Console
- Create a bucket with the name `cloudlaunch-site-bucket-damzy`
- Uncheck "Block all public access"
- Acknowledge public access warning
- Click "Create bucket"

**1.2 Enable Static Website Hosting**
- Go to the bucket Properties tab
- Enable "Static website hosting"
- Set index document: `index.html`
- Save changes

**1.3 Add Bucket Policy for Public Access**
```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Sid": "PublicReadGetObject",
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::cloudlaunch-site-bucket-damzy/*"
    }]
}


```

**1.4 Create cloudlaunch-private-bucket**
- Create a bucket with the name `cloudlaunch-private-bucket-damzy`
- Keep "Block all public access" checked
- Click "Create bucket"

**1.5 Create cloudlaunch-visible-only-bucket**
- Create bucket with name `cloudlaunch-visible-only-bucket-damzy`
- Keep "Block all public access" checked
- Click "Create bucket"

<img width="690" height="414" alt="image" src="https://github.com/user-attachments/assets/f06e1b6f-7023-49e5-9284-62520b078755" />


### Step 2: Upload Website Content

**2.1 Create Website Files**
- Create `index.html` with CloudLaunch website content
- Upload to `cloudlaunch-site-bucket-damzy`
- Test website URL from bucket properties

### Step 3: Create IAM User

**3.1 Create User**
- Navigate to IAM Console → Users
- Click "Create user"
- Username: `cloudlaunch-user`
- Set password
- Click "Create user"

**3.2 Create Custom IAM Policy**
- Go to IAM → Policies
- Click "Create policy"
- Use JSON editor with the policy below
- Name: `CloudLaunch-User-S3-Policy`
- Create policy

**3.3 Attach Policy to User**
- Go to Users → `cloudlaunch-user`
- Click "Add permissions" → "Attach policies directly"
- Select `CloudLaunch-User-S3-Policy`
- Add permissions
<img width="943" height="415" alt="image" src="https://github.com/user-attachments/assets/0cd9df6c-5d69-4660-8e94-5713d961cda9" />
---

## Task 2: VPC Network Design

### Step 4: Create VPC

**4.1 Create Main VPC**
- Navigate to VPC Console
- Click "Create VPC"
- Choose "VPC only"
- Name: `cloudlaunch-vpc`
- IPv4 CIDR: `10.0.0.0/16`
- Create VPC

### Step 5: Create Subnets

**5.1 Create Public Subnet**
- Go to Subnets → "Create subnet"
- VPC: `cloudlaunch-vpc`
- Name: `cloudlaunch-public-subnet`
- CIDR: `10.0.1.0/24`
- Choose availability zone, e.g., eu-west-1a
- Create subnet

**5.2 Create Application Subnet**
- Create subnet
- VPC: `cloudlaunch-vpc`
- Name: `cloudlaunch-app-subnet`
- CIDR: `10.0.2.0/24`
- Choose a different availability zone, e.g., eu-west-1b
- Create subnet

**5.3 Create Database Subnet**
- Create subnet
- VPC: `cloudlaunch-vpc`
- Name: `cloudlaunch-db-subnet`
- CIDR: `10.0.3.0/28`
- Choose a third availability zone, e.g., eu-west-1c
- Create subnet

<img width="958" height="391" alt="image" src="https://github.com/user-attachments/assets/ce4c826b-a9aa-4b0a-aa42-908154c2a50f" />


### Step 6: Create Internet Gateway

**6.1 Create IGW**
- Go to Internet Gateways
- Click "Create internet gateway"
- Name: `cloudlaunch-igw`
- Create an internet gateway

**6.2 Attach to VPC**
- Select the IGW
- Actions → "Attach to VPC"
- Select `cloudlaunch-vpc`
- Attach internet gateway
<img width="960" height="386" alt="image" src="https://github.com/user-attachments/assets/252433c5-3bd7-447c-9faa-58146195bfab" />

### Step 7: Configure Route Tables

**7.1 Create Public Route Table**
- Go to Route Tables
- Click "Create route table"
- Name: `cloudlaunch-public-rt`
- VPC: `cloudlaunch-vpc`
- Create route table

**7.2 Add Internet Route**
- Select `cloudlaunch-public-rt`
- Routes tab → "Edit routes"
- Add route: `0.0.0.0/0` → `cloudlaunch-igw`
- Save changes

**7.3 Associate Public Subnet**
- Subnet associations tab
- "Edit subnet associations"
- Select `cloudlaunch-public-subnet`
- Save associations

**7.4 Create Application Route Table**
- Create route table: `cloudlaunch-app-rt`
- VPC: `cloudlaunch-vpc`
- Associate with `cloudlaunch-app-subnet`
- Do NOT add internet route

**7.5 Create Database Route Table**
- Create route table: `cloudlaunch-db-rt`
- VPC: `cloudlaunch-vpc`
- Associate with `cloudlaunch-db-subnet`
- Do NOT add internet route
<img width="960" height="392" alt="image" src="https://github.com/user-attachments/assets/f62635c0-069a-4b55-8235-b087217cfcf3" />
### Step 8: Create Security Groups

**8.1 Create Application Security Group**
- Go to Security Groups
- Click "Create security group"
- Name: `cloudlaunch-app-sg`
- VPC: `cloudlaunch-vpc`
- Add inbound rule: HTTP (80) from `10.0.0.0/16`
- Create security group

**8.2 Create Database Security Group**
- Create security group
- Name: `cloudlaunch-db-sg`
- VPC: `cloudlaunch-vpc`
- Add inbound rule: MySQL (3306) from `10.0.2.0/24`
- Create security group
<img width="959" height="392" alt="image" src="https://github.com/user-attachments/assets/40c83ff9-ade0-4569-9980-53d4f3d7e396" />

### Step 9: IAM Permissions

**9.1 Create VPC Policy**
- Go to IAM → Policies
- Create policy: `CloudLaunch-VPC-ReadOnly-Policy`
- Add VPC read-only permissions
- Create policy

**9.2 Attach to User**
- Go to Users → `cloudlaunch-user`
- Add permissions → Attach policies
- Select `CloudLaunch-VPC-ReadOnly-Policy`
- Add permissions

---

## 🔗 Live Links

### Static Website
**S3 Website URL**: `http://cloudlaunch-site-bucket.s3-website-[region].amazonaws.com`

### CloudFront Distribution (Bonus)
**CloudFront URL**: `https://[distribution-id].cloudfront.net`

*Replace placeholders with your actual URLs*

---

## 🔐 IAM Policy

Complete policy attached to `cloudlaunch-user`:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListSpecificBucketsOnly",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": [
                "arn:aws:s3:::cloudlaunch-site-bucket",
                "arn:aws:s3:::cloudlaunch-private-bucket",
                "arn:aws:s3:::cloudlaunch-visible-only-bucket"
            ]
        },
        {
            "Sid": "GetObjectFromSiteBucket",
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::cloudlaunch-site-bucket/*"
        },
        {
            "Sid": "GetPutObjectPrivateBucket",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::cloudlaunch-private-bucket/*"
        },
        {
            "Sid": "VPCReadOnlyAccess",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVpcs",
                "ec2:DescribeSubnets",
                "ec2:DescribeRouteTables",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeNetworkAcls",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeRegions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "DenyDeleteOperations",
            "Effect": "Deny",
            "Action": [
                "s3:DeleteObject",
                "s3:DeleteObjectVersion",
                "s3:DeleteBucket"
            ],
            "Resource": [
                "arn:aws:s3:::cloudlaunch-site-bucket",
                "arn:aws:s3:::cloudlaunch-site-bucket/*",
                "arn:aws:s3:::cloudlaunch-private-bucket",
                "arn:aws:s3:::cloudlaunch-private-bucket/*",
                "arn:aws:s3:::cloudlaunch-visible-only-bucket",
                "arn:aws:s3:::cloudlaunch-visible-only-bucket/*"
            ]
        },
        {
            "Sid": "DenyAccessToOtherBuckets",
            "Effect": "Deny",
            "Action": "s3:*",
            "NotResource": [
                "arn:aws:s3:::cloudlaunch-site-bucket",
                "arn:aws:s3:::cloudlaunch-site-bucket/*",
                "arn:aws:s3:::cloudlaunch-private-bucket",
                "arn:aws:s3:::cloudlaunch-private-bucket/*",
                "arn:aws:s3:::cloudlaunch-visible-only-bucket",
                "arn:aws:s3:::cloudlaunch-visible-only-bucket/*"
            ]
        },
        {
            "Sid": "DenyVPCModifications",
            "Effect": "Deny",
            "Action": [
                "ec2:Create*",
                "ec2:Delete*",
                "ec2:Modify*",
                "ec2:Associate*",
                "ec2:Disassociate*",
                "ec2:Attach*",
                "ec2:Detach*",
                "ec2:Replace*"
            ],
            "Resource": "*"
        }
    ]
}
```

---

## 🧪 Testing

### Quick Verification Commands

**Test S3 Access:**
```bash
# Configure AWS CLI
aws configure --profile cloudlaunch-user

# Test bucket listing
aws s3 ls --profile cloudlaunch-user

# Test file upload to private bucket
echo "test" | aws s3 cp - s3://cloudlaunch-private-bucket/test.txt --profile cloudlaunch-user

# Test file download
aws s3 cp s3://cloudlaunch-private-bucket/test.txt - --profile cloudlaunch-user
```

**Test VPC Access:**
```bash
# List VPCs
aws ec2 describe-vpcs --profile cloudlaunch-user

# List subnets
aws ec2 describe-subnets --profile cloudlaunch-user

# Try to modify (should fail)
aws ec2 create-tags --resources vpc-xxx --tags Key=Test,Value=Fail --profile cloudlaunch-user
```

---

## 📊 Infrastructure Summary

### S3 Buckets Created:
- ✅ `cloudlaunch-site-bucket` (Public website)
- ✅ `cloudlaunch-private-bucket` (Private storage)
- ✅ `cloudlaunch-visible-only-bucket` (Visible but not accessible)

### VPC Architecture:
```
cloudlaunch-vpc (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24) → Internet Gateway
├── App Subnet (10.0.2.0/24) → Private
└── DB Subnet (10.0.3.0/28) → Private
```

### Security Groups:
- ✅ `cloudlaunch-app-sg` (HTTP from VPC)
- ✅ `cloudlaunch-db-sg` (MySQL from app subnet)

### IAM User:
- ✅ `cloudlaunch-user` with limited S3 and VPC read-only permissions

---

**Project Completed**: [Date]  
**Total Steps**: 37 steps across 2 main tasks  
**AWS Services**: S3, IAM, VPC, EC2 (security groups), CloudFront (optional)
