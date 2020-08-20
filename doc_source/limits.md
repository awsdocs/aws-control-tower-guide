# Limitations and quotas in AWS Control Tower<a name="limits"></a>

This chapter covers the AWS service limitations and quotas that you should keep in mind as you use AWS Control Tower\. If you're unable to set up your landing zone due to a service quota issue, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Limitations in AWS Control Tower<a name="controltower-limits"></a>

This section describes known limitations and unsupported use cases in AWS Control Tower\.
+ Nested OUs are not displayed in the AWS Control Tower console\.
+ Creation of nested OUs from the AWS Control Tower console is not supported\.
+ Email addresses of shared accounts in the Core OU can be changed, but you must update your landing zone to see these changes in the AWS Control Tower console\.
+ A limit of 5 SCPs per OU applies to OUs in your AWS Control Tower landing zone\.

## Quotas for Integrated Services<a name="integrated-services-limits"></a>

Each AWS service has its own quotas and limits\. You can find the quotas for each service in its documentation\. For more information, see the related links:
+ **AWS CloudFormation** – [AWS CloudFormation Quotas](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html)
+ **AWS CloudTrail** – [Quotas in AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html)
+ **Amazon CloudWatch** – [CloudWatch Quotas](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html) 
+ **AWS Config** – [AWS Config Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_config)
+ **AWS Identity and Access Management** – [Quotas for IAM Entities and Objects](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-limits.html)
+ **AWS Lambda** – [AWS Lambda Quotas](https://docs.aws.amazon.com/lambda/latest/dg/limits.html)
+ **AWS Organizations** – [Quotas for AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html)
+ **Amazon Simple Storage Service** – [Bucket Restrictions and Quotas](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html)
+ **AWS Service Catalog** – [AWS Service Catalog Default Service Quotas](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/limits.html)
+ **AWS Single Sign\-On** – [Quotas in AWS SSO](https://docs.aws.amazon.com/singlesignon/latest/userguide/limits.html)
+ **Amazon Simple Notification Service** – [Amazon Simple Notification Service \(Amazon SNS\) Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_sns)
+ **AWS Step Functions** – [Quotas](https://docs.aws.amazon.com/step-functions/latest/dg/limits.html)