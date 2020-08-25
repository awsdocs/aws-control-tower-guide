# AWS Control Tower User Guide

-----
*****Copyright &copy; 2020 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What Is AWS Control Tower?](what-is-control-tower.md)
   + [How AWS Control Tower Works](how-control-tower-works.md)
   + [What is Compliance?](compliance.md)
   + [Plan your AWS Control Tower landing zone](planning-your-deployment.md)
   + [AWS multi-account strategy for your AWS Control Tower landing zone](aws-multi-account-landing-zone.md)
+ [Setting up](setting-up.md)
+ [Getting started with AWS Control Tower](getting-started-with-control-tower.md)
+ [Limitations and quotas in AWS Control Tower](limits.md)
+ [Best practices for AWS Control Tower administrators](best-practices.md)
   + [AWS Control Tower and VPCs](vpc-concepts.md)
+ [Provision and manage accounts with Account Factory](account-factory.md)
+ [Detect and resolve drift in AWS Control Tower](drift.md)
   + [Manage resources outside of AWS Control Tower](external-resources.md)
   + [Configuration update management in AWS Control Tower](configuration-updates.md)
+ [Enable AWS Control Tower on existing organizations and accounts](existing-orgs.md)
   + [Enroll an existing AWS account](enroll-account.md)
+ [Guardrails in AWS Control Tower](guardrails.md)
   + [Guardrail Reference](guardrails-reference.md)
      + [Mandatory Guardrails](mandatory-guardrails.md)
      + [Strongly Recommended Guardrails](strongly-recommended-guardrails.md)
      + [Elective Guardrails](elective-guardrails.md)
+ [Integrated services](integrated-services.md)
   + [Scripting Environments with AWS CloudFormation](cloudformation.md)
   + [Monitoring Events with CloudTrail](cloudtrail.md)
   + [Monitoring Resources and Services with CloudWatch](cloudwatch.md)
   + [Govern Resource Configurations with AWS Config](config.md)
   + [Manage Permissions for Entities with IAM](iam.md)
   + [Run Serverless Compute Functions with Lambda](lambda.md)
   + [Manage Accounts Through AWS Organizations](organizations.md)
   + [Store Objects with Amazon S3](s3.md)
   + [Provisioning Accounts Through AWS Service Catalog](service-catalog.md)
   + [Managing Users and Access Through AWS Single Sign-On](sso.md)
   + [Tracking Alerts Through Amazon Simple Notification Service](sns.md)
   + [Build Distributed Applications with AWS Step Functions](step-functions.md)
+ [Security in AWS Control Tower](security.md)
   + [Data Protection in AWS Control Tower](controltower-console-encryption.md)
   + [Identity and Access Management in AWS Control Tower](auth-access.md)
      + [Overview of Managing Access Permissions to Your AWS Control Tower Resources](access-control-overview.md)
      + [Using Identity-Based Policies (IAM Policies) for AWS Control Tower](access-control-managing-permissions.md)
   + [Compliance Validation for AWS Control Tower](compliance-program-info.md)
   + [Resilience in AWS Control Tower](disaster-recovery-resiliency.md)
   + [Infrastructure Security in AWS Control Tower](infrastructure-security.md)
+ [Logging and monitoring in AWS Control Tower](logging-and-monitoring.md)
   + [Monitoring](monitoring-overview.md)
   + [Logging AWS Control Tower Actions with AWS CloudTrail](logging-using-cloudtrail.md)
   + [Lifecycle Events in AWS Control Tower](lifecycle-events.md)
+ [Walkthroughs](walkthroughs.md)
   + [Walkthrough: Cleaning up AWS Control Tower Managed Resources](walkthrough-delete.md)
   + [Walkthrough: Configuring AWS Control Tower Without a VPC](configure-without-vpc.md)
   + [Walkthrough: Customize Your AWS Control Tower Landing Zone](customize-landing-zone.md)
   + [Walkthrough: Automated Account Provisioning in AWS Control Tower](automated-provisioning-walkthrough.md)
   + [Walkthrough: Setting Up Security Groups in AWS Control Tower With AWS Firewall Manager](firewall-setup-walkthrough.md)
   + [Walkthrough: Decommission a landing zone](decommission-landing-zone.md)
      + [Overview of the decommissioning process](decommissioning-process-overview.md)
      + [Resources not removed during decommissioning](resources-not-removed.md)
      + [How to decommission a landing zone](how-to-decommission.md)
      + [Setup after decommissioning a landing zone](known-issues-decommissioning.md)
+ [Troubleshooting](troubleshooting.md)
+ [AWS Control Tower release notes](release-notes.md)
   + [January 2020 - Present](January-June-2020.md)
   + [January - December 2019](January-December-2019.md)
+ [Document history](doc-history.md)
+ [AWS glossary](glossary.md)