# Overview of AWS Control Tower Account Factory for Terraform<a name="aft-overview"></a>

AWS Control Tower Account Factory for Terraform \(AFT\) sets up a Terraform pipeline that helps you provision and customize your accounts in AWS Control Tower\. It is designed to provide the advantage of Terraform\-based account provisioning, while maintaining AWS Control Tower governance of your accounts\. 

You'll create an *account request* Terraform file, which provides the necessary input that triggers the AFT workflow for account provisioning\. After provisioning is complete, AFT can run additional customization steps, automatically\.

AFT supports Terraform Cloud, Terraform Enterprise, and Terraform Open Source\. It provides the ability to initiate account creation with an input file and a simple `git push` command, as well as the ability to customize new or existing accounts\. Account creation includes all AWS Control Tower governance benefits, and account customizations that assist you to meet your organization’s standard security procedures and compliance guidelines\.

AFT combines the capabilities of other AWS services as [Component services](aft-components.md), to build a framework, with pipelines that deploy Terraform Infrastructure as Code \(IaC\)\. AFT enables you to:
+ Submit account provisioning and update requests in a GitOps model
+ Store account metadata and audit history
+ Apply account\-level tags
+ Add customizations to all accounts, to a set of accounts, or to individual accounts
+ Enable feature options

AFT creates a separate account, called the *AFT management account*, to deploy AFT capabilities\. Before you can set up AFT, you must have an existing AWS Control Tower landing zone\. The AFT management account is not the same as the AWS Control Tower management account\.

**AFT offers flexibility**
+ **Flexibility for your platform:** AFT supports any Terraform Distribution for initial deployment and ongoing operation: Open Source, Cloud, and Enterprise\.
+ **Flexibility for your version control system:** AFT natively relies on AWS CodeCommit, but it supports alternative sources for AWS CodeStar connections\.

**AFT offers feature options**

You can enable several feature options, based on best practices:
+ Creating an organization\-level CloudTrail for logging data events
+ Deleting the AWS default VPC for accounts
+ Enrolling provisioned accounts into the AWS Enterprise Support plan

**Note**  
The AFT pipeline is not intended for use in deploying resources, such as Amazon EC2 instances, that your accounts require to run your applications\. It is intended solely for automated provisioning and customizing of AWS Control Tower accounts\.