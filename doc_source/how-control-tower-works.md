# How AWS Control Tower Works<a name="how-control-tower-works"></a>

The following describes at a high level how AWS Control Tower works\. Your landing zone is a well\-architected multi\-account environment for all of your AWS resources\. You can use this environment to enforce compliance regulations on all of your AWS accounts\.

## What Happens When You Set Up a Landing Zone<a name="how-it-works-setup"></a>

Before AWS Control Tower does any work in your account to set up the landing zone, it runs a series of prechecks\. These prechecks ensure that your master account is ready for the changes that establish your landing zone\. The prechecks that run before setting up a landing zone are as follows:
+ The existing service limits for the AWS account must be sufficient for AWS Control Tower to launch\. For more information, see [Limits](limits.md)\.
+ The AWS account cannot be a member of an existing AWS Organizations OU \(regardless of whether it’s set up with all features enabled or for consolidated billing\)\.
+ The AWS account must be subscribed to the following AWS services:
  + Amazon Simple Storage Service \(Amazon S3\)
  + Amazon SNS
  + Amazon Virtual Private Cloud \(Amazon VPC\)
  + AWS CloudFormation
  + AWS CloudTrail
  + Amazon CloudWatch
  + AWS Config
  + AWS Identity and Access Management \(IAM\)
  + AWS Lambda
**Note**  
By default, all accounts are subscribed to these services\.
+ The AWS account must not have an AWS Config aggregator already configured\.
+ The AWS account must not have AWS Single Sign\-On \(AWS SSO\) already set up\.

When you set up a landing zone, AWS Control Tower performs the following actions in your master account on your behalf:
+ Creates three Organizations organizational units \(OUs\): Root, Core, and Custom\.
+ Creates two shared accounts: the log archive account and audit account\.
+ Creates a cloud\-native directory in AWS SSO, with preconfigured groups and single sign\-on access\.
+ Applies 17 preventive guardrails to enforce policies\.
+ Applies three detective guardrails to detect configuration violations\.

## What Are the Shared Accounts?<a name="what-shared"></a>

In AWS Control Tower, there are three shared accounts, which are not provisioned in Account Factory: the master account, the log archive account, and the audit account\.

When you create your landing zone a number of AWS resources are created\. To use AWS Control Tower, you must not modify or delete these AWS Control Tower managed resources outside of the supported methods described in this guide\. Deleting or modifying these resources will cause your landing zone to enter an unknown state\.

**Important**  
When you enable guardrails with strongly recommended guidance, AWS Control Tower managed AWS resources are created in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so could result in the guardrails entering an unknown state\. For more information, see [Guardrail Reference](guardrails-reference.md)\.

### What Is the Master Account?<a name="what-is-master"></a>

This is the account that you created specifically for your landing zone\. This account is used for billing for everything in your landing zone\. It's also used for Account Factory provisioning and accounts, as well as to manage OUs and guardrails\.

When you set up your landing zone, the following AWS resources are created within your master account\.


| AWS service | Resource type | Resource name | 
| --- | --- | --- | 
| AWS Organizations | Accounts | audit log archive | 
| AWS Organizations | OUs | Core Custom | 
| AWS Organizations | Service Control Policies | aws\-guardrails\-\*  | 
| AWS CloudFormation | Stacks | AWSControlTowerBP\-BASELINE\-CLOUDTRAIL\-MASTER | 
| AWS CloudFormation | StackSets |  AWSControlTowerBP\-BASELINE\-CLOUDTRAIL AWSControlTowerBP\-BASELINE\-CLOUDWATCH AWSControlTowerBP\-BASELINE\-CONFIG AWSControlTowerBP\-BASELINE\-ROLES AWSControlTowerBP\-BASELINE\-SERVICE\-ROLES AWSControlTowerBP\-SECURITY\-TOPICS AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-READ\-PROHIBITED AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-WRITE\-PROHIBITED AWSControlTowerLoggingResources AWSControlTowerSecurityResources  | 
| AWS Service Catalog | Product | AWS Control Tower Account Factory | 
| AWS CloudTrail | Trail | aws\-controltower\-BaselineCloudTrail | 
| Amazon CloudWatch | CloudWatch Logs | aws\-controltower/CloudTrailLogs | 
| AWS Identity and Access Management | Roles | AWSControlTowerAdmin AWSControlTowerStackSetRole AWSControlTowerCloudTrailRolePolicy | 
| AWS Identity and Access Management | Policies | AWSControlTowerServiceRolePolicy AWSControlTowerAdminPolicy AWSControlTowerCloudTrailRolePolicy AWSControlTowerStackSetRolePolicy | 
| AWS Single Sign\-On | Directory groups | AWSAccountFactory AWSAuditAccountAdmins AWSControlTowerAdmins AWSLogArchiveAdmins AWSLogArchiveViewers AWSSecurityAuditors AWSSecurityAuditPowerUsers AWSServiceCatalogAdmins  | 
| AWS Single Sign\-On | Permission Sets | AWSAdministratorAccess AWSPowerUserAccess AWSServiceCatalogAdminFullAccess AWSServiceCatalogEndUserAccess AWSReadOnlyAccess AWSOrganizationsFullAccess  | 

### What Is the Log Archive Account?<a name="what-is-log-archive"></a>

This account is used as a repository of logs of API activities and resource configurations from all accounts in the landing zone\.

When you set up your landing zone, the following AWS resources are created within your log archive account\.


| AWS service | Resource type | Resource Name | 
| --- | --- | --- | 
| AWS CloudFormation | Stacks | StackSet\-AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-READ\-PROHIBITED\- StackSet\-AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-WRITE\-PROHIBITED StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDWATCH\- StackSet\-AWSControlTowerBP\-BASELINE\-CONFIG\- StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDTRAIL\- StackSet\-AWSControlTowerBP\-BASELINE\-SERVICE\-ROLES\- StackSet\-AWSControlTowerBP\-BASELINE\-ROLES\- StackSet\-AWSControlTowerLoggingResources\- | 
| AWS Config | AWS Config Rules | AWSControlTower\_AWS\-GR\_AUDIT\_BUCKET\_PUBLIC\_READ\_PROHIBITED AWSControlTower\_AWS\-GR\_AUDIT\_BUCKET\_PUBLIC\_WRITE\_PROHIBIT | 
| AWS CloudTrail | Trails | aws\-controltower\-BaselineCloudTrail | 
| Amazon CloudWatch | CloudWatch Event Rules | aws\-controltower\-ConfigComplianceChangeEventRule | 
| Amazon CloudWatch | CloudWatch Logs | aws\-controltower/CloudTrailLogs /aws/lambda/aws\-controltower\-NotificationForwarder | 
| AWS Identity and Access Management | Roles | aws\-controltower\-AdministratorExecutionRole aws\-controltower\-CloudWatchLogsRole aws\-controltower\-ConfigRecorderRole aws\-controltower\-ForwardSnsNotificationRole aws\-controltower\-ReadOnlyExecutionRole AWSControlTowerExecution | 
| AWS Identity and Access Management | Policies | AWSControlTowerServiceRolePolicy | 
| Amazon Simple Notification Service | Topics | aws\-controltower\-SecurityNotifications | 
| AWS Lambda | Applications | StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDWATCH\-\* | 
| AWS Lambda | Functions | aws\-controltower\-NotificationForwarder | 
| Amazon Simple Storage Service | Buckets | aws\-controltower\-logs\-\* aws\-controltower\-s3\-access\-logs\-\* | 

### What Is the Audit Account?<a name="what-is-audit"></a>

A restricted account for your security and compliance teams to gain read and write access to all accounts in your landing zone\.

When you set up your landing zone, the following AWS resources are created within your audit account\.


| AWS service | Resource type | Resource name | 
| --- | --- | --- | 
| AWS CloudFormation | Stacks | StackSet\-AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-READ\-PROHIBITED\- StackSet\-AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-WRITE\-PROHIBITED\- StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDWATCH\- StackSet\-AWSControlTowerBP\-BASELINE\-CONFIG\- StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDTRAIL\- StackSet\-AWSControlTowerBP\-BASELINE\-SERVICE\-ROLES\- StackSet\-AWSControlTowerBP\-SECURITY\-TOPICS\- StackSet\-AWSControlTowerBP\-BASELINE\-ROLES\- StackSet\-AWSControlTowerSecurityResources\-\* | 
| AWS Config | Aggregator | aws\-controltower\-GuardrailsComplianceAggregator | 
| AWS Config | AWS Config Rules | AWSControlTower\_AWS\-GR\_AUDIT\_BUCKET\_PUBLIC\_READ\_PROHIBITED AWSControlTower\_AWS\-GR\_AUDIT\_BUCKET\_PUBLIC\_WRITE\_PROHIBITED | 
| AWS CloudTrail | Trail | aws\-controltower\-BaselineCloudTrail | 
| Amazon CloudWatch | CloudWatch Event Rules | aws\-controltower\-ConfigComplianceChangeEventRule | 
| Amazon CloudWatch | CloudWatch Logs | aws\-controltower/CloudTrailLogs /aws/lambda/aws\-controltower\-NotificationForwarder | 
| AWS Identity and Access Management | Roles | aws\-controltower\-AdministratorExecutionRole aws\-controltower\-CloudWatchLogsRole aws\-controltower\-ConfigRecorderRole aws\-controltower\-ForwardSnsNotificationRole aws\-controltower\-ReadOnlyExecutionRole aws\-controltower\-SecurityAdministratorRole aws\-controltower\-SecurityReadOnlyRole AWSControlTowerExecution | 
| AWS Identity and Access Management | Policies | AWSControlTowerServiceRolePolicy | 
| Amazon Simple Notification Service | Topics | aws\-controltower\-AggregateSecurityNotifications aws\-controltower\-AllConfigNotifications aws\-controltower\-SecurityNotifications | 
| AWS Lambda | Functions | aws\-controltower\-NotificationForwarder | 

## How Guardrails Work<a name="how-guardrails-work"></a>

A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. It's expressed in plain language\. Compliance needs evolve, so while each guardrail enforces a single rule, you can change which strongly recommended guardrails are enforced at any time from the AWS Control Tower console\. Mandatory guardrails are always applied, and can't be changed\.

preventive guardrails prevent actions from occurring\. For example, the **Disallow policy changes to log archive** guardrail prevents any IAM policy changes within the log archive shared account\. Any attempt to perform a prevented action is denied and logged in CloudTrail\. The resource is also logged in AWS Config\.

Detective guardrails detect specific events when they occur and log the action in CloudTrail\. For example, the **Enable encryption for EBS volumes attached to EC2 instances** detects if an unencrypted Amazon EBS volume is attached to an EC2 instance in your landing zone\.

### Related Topics<a name="how-guardrails-related"></a>
+ [Guardrails in AWS Control Tower](guardrails.md)
+ [Detecting and Resolving Drift in AWS Control Tower](drift.md)

## How AWS Regions Work With AWS Control Tower<a name="region-how"></a>

Currently, AWS Control Tower is supported in the following AWS Regions:
+ US East \(N\. Virginia\)
+ US East \(Ohio\)
+ US West \(Oregon\)
+ EU \(Ireland\)

When you create a landing zone, the Region that you're using to access the AWS Management Console becomes your home AWS Region for AWS Control Tower\. During the creation process, some resources are provisioned in the home AWS Region\. Other resources, like OUs, and AWS accounts are global\.

Currently, all preventive guardrails work globally\. Detective guardrails, however, only work in AWS Control Tower supported regions\.