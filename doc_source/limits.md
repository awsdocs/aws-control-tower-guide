# Limitations and quotas in AWS Control Tower<a name="limits"></a>

This chapter covers the AWS service limitations and quotas that you should keep in mind as you use AWS Control Tower\. If you're unable to set up your landing zone due to a service quota issue, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Limitations in AWS Control Tower<a name="controltower-limits"></a>

This section describes known limitations and unsupported use cases in AWS Control Tower\.
+ AWS Control Tower has overall concurrency limitations\. In general, one operation at a time is permitted\. The exception to this limitation is that optional controls can be activated and deactivated simultaneously, through an asynchronous process\.
+ Email addresses of shared accounts in the Security OU can be changed, but you must update your landing zone to see these changes in the AWS Control Tower console\.
+ A limit of 5 SCPs per OU applies to OUs in your AWS Control Tower landing zone\.
+ Existing OUs with over 300 accounts cannot be registered or re\-registered in AWS Control Tower\.
+ The limit for `EnableControl` and `DisableControl` updates in AWS Control Tower is 10 concurrent operations\.

For information about how to increase certain AWS Control Tower service quotas with an automated request method, view this video: [Automate Service Limit Increases](https://www.youtube.com/watch?v=3WUShZ4lZGE)\. When provisioning new accounts in this environment, you can use lifecycle events to trigger automated requests for service limit increases in specified AWS Regions\. The video also shows how to automate enrollment of new accounts into Enterprise support for your organization\.

## Quotas for Integrated Services<a name="integrated-services-limits"></a>

Each AWS service has its own quotas and limits\. You can find the quotas for each service in its documentation\. For more information, see the related links:
+ **AWS CloudFormation** – [AWS CloudFormation Quotas](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html)
+ **AWS CloudTrail** – [Quotas in AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html)
+ **Amazon CloudWatch** – [CloudWatch Quotas](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html) 
+ **AWS Config** – [AWS Config Quotas](https://docs.aws.amazon.com/config/latest/developerguide/configlimits.html)
+ **AWS Identity and Access Management** – [Quotas for IAM Entities and Objects](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-limits.html)
+ **AWS Lambda** – [AWS Lambda Quotas](https://docs.aws.amazon.com/lambda/latest/dg/limits.html)
+ **AWS Organizations** – [Quotas for AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html)
+ **Amazon Simple Storage Service** – [Bucket Restrictions and Quotas](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html)
+ **AWS Service Catalog** – [AWS Service Catalog Default Service Quotas](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/limits.html)
+ **AWS IAM Identity Center \(successor to AWS Single Sign\-On\)** – [Quotas in IAM Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/limits.html)
+ **Amazon Simple Notification Service** – [Amazon Simple Notification Service \(Amazon SNS\) Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_sns)
+ **AWS Step Functions** – [Quotas](https://docs.aws.amazon.com/step-functions/latest/dg/limits.html)

More information about AWS quotas is available in the [AWS General Reference](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_config)\.