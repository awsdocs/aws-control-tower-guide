# Data protection<a name="aft-data-protection"></a>

The [AWS shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in AFT\. For data protection purposes, we recommend the following best practices for security\.
+ Follow the Data Protection guidelines provided by AWS Control Tower\. For details, see [Data Protection in AWS Control Tower](controltower-console-encryption.md)\.
+ Preserve Terraform state configuration generated at the time of AFT deployment\. For details, see [Deploy AWS Control Tower Account Factory for Terraform \(AFT\)](aft-getting-started.md)\.
+ Rotate sensitive credentials periodically as directed by your organization’s security policy\. Examples of secrets are Terraform tokens, `git` tokens, and so forth\.

 **Encryption at rest** 

AFT creates Amazon S3 buckets, Amazon SNS topics, Amazon SQS queues, and Amazon DynamoDB databases that are encrypted at rest with AWS Key Management Service keys\. KMS keys created by AFT have yearly rotation enabled by default\. If you choose the Terraform Cloud or Terraform Enterprise distributions of Terraform, AFT includes a AWS Systems Manager SecureString parameter to store Terraform token values that are sensitive\.

AFT uses AWS services described in [Component services](aft-components.md) that are, by default, encrypted at rest\. For details, see the AWS documentation for each component AWS service of AFT, and learn about the data protection practices followed by each service\.

 **Encryption in transit** 

AFT relies upon AWS services described in [Component services](aft-components.md) that employ encryption in transit, by default\. For details, see the AWS documentation for each component AWS service of AFT, and learn about the data protection practices followed by each service\.

 For Terraform Cloud or Terraform Enterprise distributions, AFT calls an HTTPS endpoint API for access to your Terraform organization\. If you choose a third\-party VCS provider supported by AWS CodeStar connections, AFT calls an HTTPS endpoint API for access to your VCS provider organization\.