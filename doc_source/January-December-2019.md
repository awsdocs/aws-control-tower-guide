# January \- December 2019<a name="January-December-2019"></a>

From January 1 through December 31, 2019, AWS Control Tower released the following updates:
+ [General availability of AWS Control Tower version 2\.2](#Version-2-2)
+ [New elective guardrails in AWS Control Tower ](#Elective-gaurdrails)
+ [New detective guardrails in AWS Control Tower ](#New-guardrails)
+ [AWS Control Tower accepts email addresses for shared accounts with different domains than the management account](#Email-address-shared-accounts)
+ [General availability of AWS Control Tower version 2\.1](#Version-2-1)

## General availability of AWS Control Tower version 2\.2<a name="Version-2-2"></a>

**November 13, 2019**

\(Update required for AWS Control Tower landing zone\. For information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.\)

AWS Control Tower version 2\.2 provides three new preventive guardrails that prevent drift in accounts:
+ [Disallow Changes to CloudWatch Logs Log Groups](mandatory-guardrails.md#log-group-deletion-policy) 
+ [Disallow Deletion of AWS Config Aggregation Authorization](mandatory-guardrails.md#config-aggregation-authorization-policy) 
+ [Disallow Deletion of Log Archive](mandatory-guardrails.md#disallow-audit-bucket-deletion) 

A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. When you create your AWS Control Tower landing zone, the landing zone and all the organizational units \(OUs\), accounts, and resources are compliant with the governance rules enforced by your chosen guardrails\. As you and your organization members use the landing zone, changes \(accidental or intentional\) in this compliance status may occur\. Drift detection helps you identify resources that need changes or configuration updates to resolve the drift\. For more information, see [Detect and resolve drift in AWS Control Tower](drift.md)\. 

## New elective guardrails in AWS Control Tower<a name="Elective-gaurdrails"></a>

**September 05, 2019** 

\(No update required for AWS Control Tower landing zone\)

AWS Control Tower now includes the following four new elective  guardrails:
+ [Disallow Delete Actions on Amazon S3 Buckets Without MFA](elective-guardrails.md#disallow-s3-delete-mfa) 
+ [Disallow Cross\-Region Replication for Amazon S3 Buckets](elective-guardrails.md#disallow-s3-ccr) 
+ [Disallow Actions as a Root User](strongly-recommended-guardrails.md#disallow-root-auser-actions)
+ [Disallow Creation of Access Keys for the Root User](strongly-recommended-guardrails.md#disallow-root-access-keys)

A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. Guardrails enable you to express your policy intentions\. For more information, see [Guardrails in AWS Control Tower](guardrails.md)\.

## New detective guardrails in AWS Control Tower<a name="New-guardrails"></a>

**August 25, 2019** 

\(No update required for AWS Control Tower landing zone\)

AWS Control Tower now includes the following eight new detective guardrails:
+ [Disallow Amazon S3 Buckets That Are Not Versioning Enabled](elective-guardrails.md#disallow-s3-no-versioning) 
+ [Disallow Console Access to IAM Users Without MFA](elective-guardrails.md#disallow-console-access-mfa) 
+ [Disallow Access to IAM Users Without MFA](elective-guardrails.md#disallow-access-mfa) 
+ [Disallow Amazon EC2Instance Types That Are Not Amazon EBS\-Optimized](strongly-recommended-guardrails.md#disallow-not-ebs-optimized)
+ [Disallow Amazon EBS Volumes That Are Unattached to An Amazon EC2 Instance](strongly-recommended-guardrails.md#disallow-unattached-ebs) 
+ [Disallow Public Access to Amazon RDS Database Instances](strongly-recommended-guardrails.md#disallow-rds-public-access) 
+ [Disallow Public Access to Amazon RDS Database Snapshots](strongly-recommended-guardrails.md#disallow-rds-snapshot-public-access)
+ [Disallow Amazon RDS Database Instances That Are Not Storage Encrypted](strongly-recommended-guardrails.md#disallow-rds-storage-unencrypted)

A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. A detective guardrail detects noncompliance of resources within your accounts, such as policy violations, and provides alerts through the dashboard\. For more information, see [Guardrails in AWS Control Tower](guardrails.md)\.

## AWS Control Tower accepts email addresses for shared accounts with different domains than the management account<a name="Email-address-shared-accounts"></a>

**August 01, 2019** 

\(No update required for AWS Control Tower landing zone\)

In AWS Control Tower, you can now submit email addresses for shared accounts \(log archive and audit member\) and child accounts \(vended using account factory\) whose domains are different from the management account's email address\. This feature is available only when you create a new landing zone and when you provision new child accounts\.

## General availability of AWS Control Tower version 2\.1<a name="Version-2-1"></a>

**June 24, 2019**

\(Update required for AWS Control Tower landing zone\. For information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.\)

AWS Control Tower is now generally available and supported for production use\. AWS Control Tower is intended for organizations with multiple accounts and teams who are looking for the easiest way to set up their new multi\-account AWS environment and govern at scale\. With AWS Control Tower, you can help make sure that accounts in your organization are compliant with established policies\. End users on distributed teams can provision new AWS accounts quickly\.

Using AWS Control Tower, you can [set up a landing zone](getting-started-with-control-tower.md) that employs best practices such as configuring a [multi\-account structure](https://docs.aws.amazon.com/controltower/latest/userguide/aws-multi-account-landing-zone.html) using AWS Organizations, managing user identities and federated access with AWS Single Sign\-On, enabling account provisioning through AWS Service Catalog, and creating a centralized log archive using AWS CloudTrail and AWS Config\. 

For ongoing governance, you can enable pre\-configured guardrails, which are clearly defined rules for security, operations, and compliance\. Guardrails help prevent deployment of resources that don’t conform to policies and continuously monitor deployed resources for nonconformance\. The AWS Control Tower dashboard provides centralized visibility into an AWS environment including accounts provisioned, guardrails enabled, and the compliance status of accounts\.

You can set up a new multi\-account environment with a single click in the AWS Control Tower console\. There are no additional charges or upfront commitments to use AWS Control Tower\. You pay only for those AWS services that you enabled to set up a landing zone and implement selected guardrails\. 