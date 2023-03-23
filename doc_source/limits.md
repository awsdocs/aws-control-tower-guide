# Limitations and quotas in AWS Control Tower<a name="limits"></a>

This chapter covers the AWS service limitations and quotas that you should keep in mind as you use AWS Control Tower\. If you're unable to set up your landing zone due to a service quota issue, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Limitations in AWS Control Tower<a name="controltower-limits"></a>

This section describes known limitations and unsupported use cases in AWS Control Tower\.
+ AWS Control Tower has overall concurrency limitations\. In general, one operation at a time is permitted\. Two exceptions to this limitation are allowed:
  + Optional controls can be activated and deactivated concurrently, through an asynchronous process\. Up to ten \(10\) control\-related operations at a time can be in progress\.
  + Accounts can be provisioned, updated, and enrolled concurrently in Account Factory, through an asynchronous process, with up to five \(5\) account\-related operations in progress simultaneously\.
+ Email addresses of shared accounts in the Security OU can be changed, but you must update your landing zone to see these changes in the AWS Control Tower console\.
+ A limit of 5 SCPs per OU applies to OUs in your AWS Control Tower landing zone\.
+ Existing OUs with over 300 accounts cannot be registered or re\-registered in AWS Control Tower\.
+ The limit for `EnableControl` and `DisableControl` updates in AWS Control Tower is 10 concurrent operations\.

You can contact AWS Support to request a limit increase for some resources in AWS Control Tower\. For example, you can request a limit increase from five of up to ten \(10\) concurrent account\-related operations\. Some AWS Control Tower performance metrics may change after a limit increase\.

**Video: Automate requests for service limit increase**

This video \(7:24\) describes how to automate service limit increases for deployments in AWS Control Tower\. It also shows how to automate enrolllment of new accounts into AWS Enterprise support for your organization\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.

When provisioning new accounts in this environment, you can use lifecycle events to trigger automated requests for service limit increases in specified AWS Regions\. 

**Control limitations**  
If you modify AWS Control Tower resources, such as an SCP, or remove any AWS Config resource, such as a Config recorder or aggregator, AWS Control Tower can no longer guarantee that the controls are functioning as designed\. Therefore, the security of your multi\-account environment may be compromised\. The AWS shared responsibility model of security is applicable to any such changes you may make\.   
 AWS Control Tower helps maintain the integrity of your environment by resetting the SCPs of the controls to their standard configuration when you update your landing zone\. Changes that you may have made to SCPs are replaced by the standard version of the control, by design\.  
Some controls in AWS Control Tower do not operate in certain AWS Regions where AWS Control Tower is available, because those Regions do not support the required underlying functionality\. As a result, when you deploy that control, it may not be operating in all Regions that you govern with AWS Control Tower\. This limitation affects certain detective controls, certain proactive controls, and certain controls in the Security Hub Service\-managed Standard: AWS Control Tower\. For more information about Regional availability, see the [Regional services list documentation](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/) and the [Security Hub controls reference documentation](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-controls-reference.html)\.  
You can view the Regions for each control in the AWS Control Tower console\.

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
+ **Service Catalog** – [Service Catalog Default Service Quotas](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/limits.html)
+ **AWS IAM Identity Center \(successor to AWS Single Sign\-On\)** – [Quotas in IAM Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/limits.html)
+ **Amazon Simple Notification Service** – [Amazon Simple Notification Service \(Amazon SNS\) Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_sns)
+ **AWS Step Functions** – [Quotas](https://docs.aws.amazon.com/step-functions/latest/dg/limits.html)

More information about AWS quotas is available in the [AWS General Reference](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_config)\.