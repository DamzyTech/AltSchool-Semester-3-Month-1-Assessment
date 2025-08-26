# CloudLaunch AWS Infrastructure Project

## 🚀 Project Overview

CloudLaunch is a comprehensive AWS infrastructure project that demonstrates the implementation of secure cloud architecture using Amazon S3, IAM, and VPC services. This project showcases best practices for hosting static websites, managing user permissions, and designing scalable network infrastructure.

## 📋 Table of Contents

- [Task 1: Static Website Hosting with S3 + IAM](#task-1-static-website-hosting-with-s3--iam)
- [Task 2: VPC Design for CloudLaunch Environment](#task-2-vpc-design-for-cloudlaunch-environment)
- [Live Links](#live-links)
- [IAM Policy Configuration](#iam-policy-configuration)
- [Security Features](#security-features)
- [Architecture Diagrams](#architecture-diagrams)
- [Testing and Verification](#testing-and-verification)
- [Future Enhancements](#future-enhancements)

## Task 1: Static Website Hosting with S3 + IAM

### 🎯 Objective
Create a secure, multi-tiered storage solution with a public static website and implement granular IAM permissions for controlled access.

### 📦 What We Accomplished

#### S3 Bucket Configuration

We successfully created three strategically designed S3 buckets, each serving a specific purpose:

**1. cloudlaunch-site-bucket (Public Website)**
- **Purpose**: Hosts our CloudLaunch static website
- **Configuration**: 
  - Static website hosting enabled
  - Public read access for anonymous users
  - Serves as the main entry point for our application
- **Content**: A modern, responsive website showcasing CloudLaunch features
- **Security**: Read-only access ensures content integrity while maintaining public accessibility

**2. cloudlaunch-private-bucket (Secure Storage)**
- **Purpose**: Stores sensitive application data and user uploads
- **Configuration**:
  - Completely private with no public access
  - Accessible only through authenticated IAM users
  - Supports both read and write operations for authorized users
- **Use Case**: Perfect for storing user documents, configuration files, and application backups

**3. cloudlaunch-visible-only-bucket (Metadata Storage)**
- **Purpose**: Demonstrates advanced IAM permission control
- **Configuration**:
  - Users can see the bucket exists (ListBucket permission)
  - No access to actual file contents (no GetObject permission)
  - Useful for storing metadata or system information that should be visible but not readable

#### Static Website Features

Our CloudLaunch website includes:

- **Modern Design**: Gradient backgrounds, glassmorphism effects, and smooth animations
- **Responsive Layout**: Works perfectly on desktop, tablet, and mobile devices
- **Interactive Elements**: Hover effects, smooth scrolling, and dynamic content
- **Feature Showcase**: Six key features highlighting CloudLaunch capabilities:
  - 🚀 Quick Deployment
  - 🔒 Secure by Default
  - 📈 Auto Scaling
  - 🛠️ Developer Tools
  - 🌍 Global Reach
  - 💡 Expert Support

#### IAM User Implementation

**User Created**: `cloudlaunch-user`

**Permission Strategy**: We implemented a principle of least privilege with precise access control:

- **S3 Access Patterns**:
  - Can list all three buckets (visibility for management)
  - Read-only access to the public website bucket
  - Full read/write access to the private bucket (no delete permissions)
  - Can see but cannot access the visible-only bucket contents
  - No access to any other S3 buckets in the account

- **Security Boundaries**:
  - Explicit deny policies prevent privilege escalation
  - No delete permissions anywhere (data protection)
  - Bucket access restricted to only the three specified buckets

## Task 2: VPC Design for CloudLaunch Environment

### 🎯 Objective
Design and implement a secure, scalable network infrastructure that supports future application expansion with proper isolation and security controls.

### 🏗️ Network Architecture

#### VPC Foundation

**cloudlaunch-vpc (10.0.0.0/16)**
- **Address Space**: Provides 65,536 IP addresses for massive scalability
- **Region**: Deployed in a single AWS region for optimal latency
- **Design Philosophy**: Three-tier architecture supporting web, application, and database layers

#### Subnet Design

Our subnet strategy implements network segmentation best practices:

**1. Public Subnet (10.0.1.0/24)**
- **IP Range**: 256 IP addresses (10.0.1.1 - 10.0.1.254)
- **Purpose**: Hosts internet-facing resources like load balancers, NAT gateways
- **Connectivity**: Direct internet access through Internet Gateway
- **Future Use**: Application Load Balancers, bastion hosts, public-facing services
- **Availability Zone**: Strategically placed for high availability

**2. Application Subnet (10.0.2.0/24)**
- **IP Range**: 256 IP addresses (10.0.2.1 - 10.0.2.254)
- **Purpose**: Private subnet for application servers and business logic
- **Security**: No direct internet access, protected from external threats
- **Communication**: Can communicate with other VPC subnets
- **Future Use**: EC2 instances running web applications, API servers, microservices
- **Availability Zone**: Different from public subnet for fault tolerance

**3. Database Subnet (10.0.3.0/28)**
- **IP Range**: 14 IP addresses (10.0.3.1 - 10.0.3.14)
- **Purpose**: Highly secure subnet for database servers
- **Size Rationale**: Smaller subnet as databases typically need fewer instances
- **Security**: Most restrictive access, isolated from internet
- **Future Use**: RDS instances, ElastiCache, database replicas
- **Availability Zone**: Third AZ for database high availability

#### Internet Connectivity

**Internet Gateway: cloudlaunch-igw**
- **Attachment**: Connected to cloudlaunch-vpc
- **Function**: Enables internet access for public subnet resources
- **Scalability**: Horizontally scalable and highly available by default
- **Security**: Only accessible from public subnet through route table configuration

#### Routing Strategy

We implemented a sophisticated routing strategy that provides internet access where needed while maintaining security:

**1. Public Route Table (cloudlaunch-public-rt)**
- **Internet Route**: 0.0.0.0/0 → cloudlaunch-igw
- **Local Route**: 10.0.0.0/16 → local (automatic)
- **Association**: cloudlaunch-public-subnet
- **Purpose**: Enables bidirectional internet communication

**2. Application Route Table (cloudlaunch-app-rt)**
- **Local Route**: 10.0.0.0/16 → local (automatic)
- **Internet Access**: None (completely private)
- **Association**: cloudlaunch-app-subnet
- **Security Benefit**: Prevents accidental internet exposure

**3. Database Route Table (cloudlaunch-db-rt)**
- **Local Route**: 10.0.0.0/16 → local (automatic)
- **Internet Access**: None (maximum security)
- **Association**: cloudlaunch-db-subnet
- **Security Benefit**: Highest level of isolation

#### Security Group Configuration

**1. Application Security Group (cloudlaunch-app-sg)**
- **Inbound Rules**:
  - HTTP (port 80) from VPC CIDR (10.0.0.0/16)
  - Allows load balancer communication from public subnet
  - Internal application communication within VPC
- **Outbound Rules**: Default (all outbound traffic allowed)
- **Use Case**: Protects application servers while allowing necessary communication

**2. Database Security Group (cloudlaunch-db-sg)**
- **Inbound Rules**:
  - MySQL (port 3306) from Application Subnet only (10.0.2.0/24)
  - Highly restrictive access pattern
  - Prevents any direct database access from internet or public subnet
- **Outbound Rules**: Default (allows responses to inbound connections)
- **Security Philosophy**: Database isolation with application-only access

#### IAM VPC Permissions

Enhanced the `cloudlaunch-user` with comprehensive VPC read-only access:

- **VPC Visibility**: Can view VPC configuration and status
- **Subnet Information**: Access to subnet details, CIDR blocks, and availability zones
- **Route Table Access**: Can view routing configuration (but not modify)
- **Security Group Visibility**: Can see security group rules and associations
- **Internet Gateway Information**: Can view IGW status and attachments
- **Modification Prevention**: Explicit deny on all create, modify, and delete operations

## 🔗 Live Links

### Static Website
**S3 Website URL**: `http://cloudlaunch-site-bucket.s3-website-[region].amazonaws.com`
> Replace `[region]` with your actual AWS region (e.g., us-east-1)

### CloudFront Distribution (Optional)
**CloudFront URL**: `https://[distribution-id].cloudfront.net`
> If you implemented the bonus CloudFront distribution, replace `[distribution-id]` with your actual CloudFront distribution ID

**Benefits of CloudFront Integration**:
- HTTPS encryption for secure communication
- Global edge locations for reduced latency
- Built-in DDoS protection
- Caching for improved performance
- Custom domain support

## 🔐 IAM Policy Configuration

The following JSON policy provides comprehensive access control for the `cloudlaunch-user`:

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

### Policy Explanation

**Permission Breakdown**:

1. **ListSpecificBucketsOnly**: Allows the user to see the contents of our three specific buckets
2. **GetObjectFromSiteBucket**: Read-only access to the public website files
3. **GetPutObjectPrivateBucket**: Full read/write access to the private bucket (excluding delete)
4. **VPCReadOnlyAccess**: Comprehensive read-only access to VPC components
5. **DenyDeleteOperations**: Explicit prevention of any delete operations on S3 resources
6. **DenyAccessToOtherBuckets**: Ensures user cannot access any other S3 buckets
7. **DenyVPCModifications**: Prevents any modifications to VPC infrastructure

## 🛡️ Security Features

### Defense in Depth Strategy

**Network Level Security**:
- VPC isolation from internet (private subnets)
- Security groups acting as virtual firewalls
- Route table controls preventing unwanted internet access
- Subnet segmentation for different application tiers

**Access Control Security**:
- IAM policies with least privilege principle
- Explicit deny policies preventing privilege escalation
- Granular permissions for different resource types
- No delete permissions to prevent data loss

**Data Protection**:
- Private S3 buckets with no public access
- Encryption in transit (HTTPS with CloudFront)
- Bucket policies restricting access patterns
- Version control capabilities (can be enabled)

## 📊 Architecture Diagrams

### Network Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                    cloudlaunch-vpc (10.0.0.0/16)           │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌──────────────┐ │
│  │   Public Subnet │  │  Application    │  │   Database   │ │
│  │  (10.0.1.0/24)  │  │    Subnet       │  │    Subnet    │ │
│  │                 │  │  (10.0.2.0/24)  │  │(10.0.3.0/28)│ │
│  │  ┌───────────┐  │  │                 │  │              │ │
│  │  │    IGW    │  │  │  ┌───────────┐  │  │  ┌────────┐  │ │
│  │  │           │◄─┼──┼──┤    ALB    │  │  │  │   RDS  │  │ │
│  │  └───────────┘  │  │  └───────────┘  │  │  └────────┘  │ │
│  └─────────────────┘  └─────────────────┘  └──────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### S3 Storage Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                    CloudLaunch S3 Storage                   │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌──────────────┐ │
│  │  Site Bucket    │  │  Private Bucket │  │ Visible-Only │ │
│  │   (Public)      │  │   (Private)     │  │   Bucket     │ │
│  │                 │  │                 │  │  (Private)   │ │
│  │  Static Website │  │  User Files     │  │  Metadata    │ │
│  │  HTML/CSS/JS    │  │  App Data       │  │  System Info │ │
│  │                 │  │  Backups        │  │              │ │
│  └─────────────────┘  └─────────────────┘  └──────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

## 🧪 Testing and Verification

### Automated Testing Scripts

We've provided comprehensive testing scripts to verify all functionality:

**S3 Permissions Test**: Verifies bucket access patterns and permission boundaries
**VPC Configuration Test**: Confirms network setup and IAM read-only access
**Security Boundary Test**: Ensures denied operations are properly blocked

### Manual Testing Checklist

- [ ] Static website loads correctly in browser
- [ ] CloudFront distribution serves content over HTTPS (if configured)
- [ ] IAM user can list all three buckets
- [ ] IAM user can read from public bucket
- [ ] IAM user can read/write to private bucket
- [ ] IAM user cannot access visible-only bucket contents
- [ ] IAM user cannot delete any S3 objects
- [ ] IAM user can view VPC components
- [ ] IAM user cannot modify VPC infrastructure

## 🚀 Future Enhancements

### Phase 1 Enhancements
- **SSL Certificate**: Add custom domain with AWS Certificate Manager
- **CDN Optimization**: Advanced CloudFront caching strategies
- **Monitoring**: CloudWatch dashboards for S3 and VPC metrics
- **Backup Strategy**: S3 cross-region replication for disaster recovery

### Phase 2 Scalability
- **Multi-AZ Deployment**: Expand subnets across multiple availability zones
- **NAT Gateway**: Add NAT gateway for outbound internet access from private subnets
- **VPC Peering**: Connect to other VPCs for multi-environment architectures
- **Transit Gateway**: Implement for complex network topologies

### Phase 3 Advanced Features
- **EC2 Deployment**: Deploy actual application servers in the VPC
- **RDS Implementation**: Deploy managed databases in the database subnet
- **ELB Integration**: Add Application Load Balancers in the public subnet
- **Auto Scaling**: Implement auto-scaling groups for high availability

### Phase 4 Enterprise Features
- **AWS WAF**: Web Application Firewall for advanced security
- **GuardDuty**: Threat detection and security monitoring
- **Config Rules**: Automated compliance checking
- **Organizations**: Multi-account governance and security

## 📈 Project Benefits

### Business Value
- **Cost Optimization**: Pay-as-you-go model with no upfront costs
- **Scalability**: Infrastructure that grows with business needs
- **Global Reach**: CloudFront enables worldwide content delivery
- **Security**: Enterprise-grade security controls and compliance

### Technical Benefits
- **High Availability**: Multi-AZ architecture for fault tolerance
- **Performance**: CDN integration for fast content delivery
- **Maintainability**: Clear separation of concerns and documented architecture
- **Flexibility**: Modular design supporting future enhancements

## 📞 Support and Maintenance

### Monitoring Recommendations
- Set up CloudWatch alarms for S3 storage usage
- Monitor VPC flow logs for security analysis
- Track CloudFront performance metrics
- Implement billing alerts for cost management

### Regular Maintenance Tasks
- Review IAM policies quarterly for security compliance
- Update S3 lifecycle policies for cost optimization
- Rotate access keys and review user permissions
- Test disaster recovery procedures

---

## 📄 Conclusion

The CloudLaunch project successfully demonstrates a comprehensive AWS infrastructure implementation that combines static website hosting, granular access control, and secure network design. This architecture provides a solid foundation for scalable web applications while maintaining the highest security standards.

The project showcases best practices in:
- **Security**: Multi-layered security approach with least privilege access
- **Scalability**: Architecture designed for growth and expansion
- **Maintainability**: Well-documented and clearly structured components
- **Cost-Effectiveness**: Optimized resource usage with pay-as-you-go model

This infrastructure is production-ready and can serve as a template for similar projects requiring secure, scalable cloud architecture.

---

**Project Created**: [Current Date]  
**AWS Services Used**: S3, IAM, VPC, EC2, CloudFront  
**Architecture Type**: Three-tier web application infrastructure  
**Security Model**: Defense in depth with IAM and network controls
