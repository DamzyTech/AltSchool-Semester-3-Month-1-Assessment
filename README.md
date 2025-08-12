# AltSchool-Semester-3-Month-1-Assessment
## Product Concept: CloudLaunch
You are working on a lightweight product called CloudLaunch, a platform that showcases a basic company website and stores some internal private documents. 
You are required to deploy it using AWS core services. This exercise demonstrates your understanding of AWS fundamentals including S3, IAM, and VPCs.

Your assessment consists of two main tasks: hosting a static site securely on S3 and designing a VPC network layout.
You must implement strict access controls for users and resources.

## Task 1: Static Website Hosting Using S3 + IAM User with Limited Permissions
Objective:
Host a public website and manage private file storage using Amazon S3, and control access via IAM. 
The website should be a basic webpage for the CloudLauch project. Its content is not important, so much effort is not required.

### S3 Buckets:
Create the following S3 buckets:

#### Cloudlaunch-site-bucket
Hosts a simple static website (HTML/CSS/JS).
Enable static website hosting.
Publicly accessible (read-only for anonymous users).
Bonus: Set up a CloudFront distribution in front of this bucket for HTTPS and global caching.

#### Cloudlaunch-private-bucket
Not publicly accessible.
A designated IAM user (created by you) should have permissions to GetObject and PutObject only (no Delete).

#### Cloudlaunch-visible-only-bucket
Not publicly accessible.
The IAM user should be able to list this bucket (see it in S3 list) but not access its contents.

 #### IAM User:

Create a user named cloudlaunch-user with the following permissions:

Access limited to only the above three buckets.
ListBucket on all three.
GetObject/PutObject on cloudlaunch-private-bucket only.
GetObject on cloudlaunch-site-bucket only.
No DeleteObject permissions anywhere.
No access to cloudlaunch-visible-only-bucket content.
Use JSON policy documents to define and attach the custom IAM policy.
