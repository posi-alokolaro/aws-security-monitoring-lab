# AWS Secure Infrastructure & Security Monitoring Lab

## Overview

This project demonstrates the deployment, hardening, and monitoring of a small AWS environment using Amazon VPC, EC2, IAM, S3, CloudTrail, and CloudWatch.

The goal was to apply least-privilege access controls, restrict network access, generate authorized and unauthorized API activity, and investigate those events using AWS security logging.

## Architecture

The environment includes:

- Custom Amazon VPC
- Public subnet and Internet Gateway
- Amazon EC2 Linux instance
- Security group restricting SSH access to a trusted IP
- Private Amazon S3 bucket
- EC2 IAM role using temporary credentials
- Custom least-privilege IAM policy
- AWS CloudTrail management and S3 data-event logging
- Amazon CloudWatch monitoring and alerting

## IAM Least-Privilege Controls

The EC2 instance was assigned an IAM role rather than using static AWS access keys.

The custom policy allowed:

- `s3:ListBucket`
- `s3:GetObject`
- `s3:PutObject`

The policy intentionally did not allow:

- `s3:DeleteObject`
- `s3:ListAllMyBuckets`

Permissions were restricted to a single private S3 bucket.

## Security Validation

The EC2 instance successfully performed authorized actions:

| Action | Result |
|---|---|
| PutObject | Allowed |
| GetObject | Allowed |
| DeleteObject | AccessDenied |
| ListAllMyBuckets | AccessDenied |

This confirmed that the IAM policy enforced the intended least-privilege restrictions.

## CloudTrail Investigation

S3 object-level data events were enabled in AWS CloudTrail.

A controlled test generated:

- `PutObject`
- `GetObject`
- `DeleteObject`

CloudTrail recorded the successful read/write activity and captured the unauthorized deletion attempt.

The denied event showed:

```text
Event: DeleteObject
Category: Data
Source: s3.amazonaws.com
Error Code: AccessDenied
IAM Role: SecurityLabS3LeastPrivilege
