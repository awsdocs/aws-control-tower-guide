# Proactive controls<a name="proactive-controls"></a>

Proactive controls are *optional controls* implemented with [AWS CloudFormation hooks](https://docs.aws.amazon.com/cloudformation-cli/latest/userguide/hooks.html#hooks-characteristics)\. Proactive controls fall into three main **Categories**\.

These controls are referred to as *proactive* because they check your resources – before the resources are deployed – to determine whether the new resources will comply with the controls that are activated in your environment\.

In the AWS Control Tower console, you can view the controls in groups according to their assigned categories, which are: 
+ **Control objectives**: Specific purposes for implementing controls in your environment\.  
+ **Frameworks**: Industry\-standard compliance frameworks\.
+ **Services**: The AWS services that the control may govern\.

In this reference guide, the proactive controls are categorized according to their associated AWS services\.

**Note**  
You must apply an elective, SCP\-based control with the identifier **CT\.CLOUDFORMATION\.PR\.1** before you can activate proactive controls on an OU\. See [Disallow management of resource types, modules, and hooks within the AWS CloudFormation registry](elective-controls.md#disallow-cfn-extensions)\. If this SCP is not activated, you'll see an error message directing you to enable this control as a prerequisite, or showing it as a dependency for other proactive controls\.

 **Behavior of proactive controls**

Proactive controls check resources whenever those resources are created or updated by means of AWS CloudFormation stack operations\. Specifically, these proactive controls are implemented as `preCreate` and `preUpdate` AWS CloudFormation hook handlers\. As a consequence, these controls may not affect requests that are made directly to services through the AWS console, through AWS APIs, or through other means such as AWS SDKs, or other Infrastructure\-as\-Code \(IaC\) tools\. For more information about when `preCreate` and `preUpdate` hooks operate, see [AWS CloudFormation hooks](https://docs.aws.amazon.com/https://docs.aws.amazon.com/cloudformation-cli/latest/userguide/hooks.html#hooks-characteristics)\.

When you follow an example template to set up a test for a proactive control in your environment, be aware that the template is created to test one specific control only\. Other controls may not receive a PASS rating for that template\. This behavior is expected\. We recommend that you test proactive controls individually before you enable them in your environment\.

**Topics**
+ [Amazon API Gateway controls](api-gateway-rules.md)
+ [AWS Certificate Manager controls](acm-rules.md)
+ [Amazon CloudFront controls](cloudfront-rules.md)
+ [AWS CloudTrail controls](cloudtrail-rules.md)
+ [AWS CodeBuild controls](codebuild-rules.md)
+ [AWS Database Migration Service \(DMS\) controls](dms-rules.md)
+ [Amazon DynamoDB controls](dynamodb-rules.md)
+ [DynamoDB Accelerator controls](dax-rules.md)
+ [AWS Elastic Beanstalk controls](ebs-rules.md)
+ [Amazon Elastic Compute Cloud \(EC2\) controls](ec2-rules.md)
+ [Amazon Elastic Container Registry controls](ecr-rules.md)
+ [Amazon Elastic Container Service controls](ecs-rules.md)
+ [Amazon Elastic File System controls](efs-rules.md)
+ [Elastic Load Balancing controls](elb-rules.md)
+ [Amazon GuardDuty controls](guard-duty-rules.md)
+ [AWS Identity and Access Management \(IAM\) controls](iam-rules.md)
+ [AWS Key Management Service \(AWS KMS\) controls](kms-rules.md)
+ [Amazon Kinesis controls](kinesis-rules.md)
+ [AWS Lambda controls](lambda-rules.md)
+ [AWS Network Firewall controls](network-firewall-rules.md)
+ [Amazon Relational Database Service \(RDS\) controls](rds-rules.md)
+ [Amazon Redshift controls](redshift-rules.md)
+ [Amazon Simple Storage Service \(S3\) controls](s3-rules.md)
+ [Amazon Simple Queue Service \(SQS\) controls](sqs-rules.md)
+ [AWS WAF regional controls](waf-regional-rules.md)
+ [AWS WAF controls](waf-rules.md)
+ [AWS WAFV2 controls](wafv2-rules.md)