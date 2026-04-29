# Lab M2.05 - IAM Roles for EC2

## Overview

In this lab, I created an IAM role for an EC2 instance with limited permissions to access a specific S3 bucket and send logs to CloudWatch. The goal was to avoid using hardcoded AWS access keys and instead use temporary credentials provided automatically by AWS through IAM roles.

The IAM role was attached directly to the EC2 instance, allowing secure access to AWS services without running `aws configure`.

---

# Why IAM Roles Are Better Than Access Keys

IAM roles are more secure than access keys because they provide temporary credentials automatically managed by AWS. Access keys are long term credentials and can accidentally be exposed in source code, GitHub repositories, configuration files, or shared environments.

With IAM roles, the EC2 instance automatically receives temporary credentials through the AWS metadata service. This removes the need to store secrets inside the server.

IAM roles also make credential rotation automatic and reduce the risk of credential leaks.

---

# Step by Step Process

## 1. Created the IAM Role

I opened the IAM console and created a new role called:

`ec2-s3-cloudwatch-role`

The trusted entity type selected was:

`AWS Service`

Use case:

`EC2`

---

## 2. Created the Custom S3 Policy

I created a custom IAM policy allowing access only to a single S3 bucket.

Permissions granted:

- s3:GetObject
- s3:PutObject
- s3:ListBucket

The policy was restricted to the following bucket only:

`eric-borba-bootcamp-bucket-2026`

---

## 3. Attached Policies to the Role

The following policies were attached to the role:

- Custom S3 access policy

---

## 4. Attached the IAM Role to the EC2 Instance

Inside the EC2 console, I selected the running instance and modified the IAM role attached to it.

Path used in the console:

Actions → Security → Modify IAM Role

---

## 5. Installed and Tested AWS CLI

After connecting to the EC2 instance through SSH, I verified the AWS CLI installation and tested the IAM role permissions.

The command:

`aws sts get-caller-identity`

confirmed that the instance was using an assumed IAM role instead of access keys.

I was also able to upload a file to the S3 bucket successfully.

---

# Policy Explanation

The custom IAM policy follows the principle of least privilege by allowing access only to one specific S3 bucket.

Permissions granted:

`ListBucket`

Allows listing bucket contents.

`GetObject`

Allows reading objects inside the bucket.

`PutObject`

Allows uploading files to the bucket.

No delete permissions or access to other AWS services were granted.

---

# Security Best Practices Followed

I followed several AWS security best practices during this lab.

No hardcoded credentials were used.

No `aws configure` command was executed.

The EC2 instance used temporary credentials automatically provided by the IAM role.

Permissions were restricted to a single S3 bucket.

The IAM policy followed the least privilege principle by granting only the permissions required for the lab.

---

# Troubleshooting Steps

During testing, I initially ran the following command incorrectly:

`aws s3 cp s3://eric-borba-bootcamp-bucket-2026`

AWS returned an error because the source file path was missing.

I corrected the command to:

`aws s3 cp test.txt s3://eric-borba-bootcamp-bucket-2026`

After correcting the syntax, the file upload completed successfully.

I also waited a short time after attaching the IAM role because IAM role propagation can sometimes take a few seconds before credentials become available to the EC2 instance.

---

# Result

The EC2 instance successfully accessed the S3 bucket without using access keys or running `aws configure`.

The IAM role authentication was verified using:

`aws sts get-caller-identity`

which returned an assumed role ARN.