# How AWS Control Tower Works<a name="how-control-tower-works"></a>

This section describes at a high level how AWS Control Tower works\. Your landing zone is a well\-architected multi\-account environment for all of your AWS resources\. You can use this environment to enforce compliance regulations on all of your AWS accounts\.

## Structure of an AWS Control Tower Landing Zone<a name="landing-zone-structure"></a>

The structure of a landing zone in AWS Control Tower is as follows:
+ **Root** – The parent that contains all other OUs in your landing zone\. 
+ **Security OU** – This OU contains the Log Archive and Audit accounts\. These accounts often are referred to as *shared accounts*\. When you launch your landing zone, you can choose customized names for these shared accounts, and you have the option to bring existing AWS accounts into AWS Control Tower for security and logging\. However, these cannot be renamed later, and existing accounts cannot be added for security and logging after initial launch\.
+ **Sandbox OU** – The Sandbox OU is created when you launch your landing zone, if you enable it\. This and other registered OUs contain the enrolled accounts that your users work with to perform their AWS workloads\.
+ **IAM Identity Center directory** – This directory houses your IAM Identity Center users\. It defines the scope of permissions for each IAM Identity Center user\.
+ **IAM Identity Center users** – These are the identities that your users can assume to perform their AWS workloads in your landing zone\.

## What happens when you set up a landing zone<a name="how-it-works-setup"></a>

When you set up a landing zone, AWS Control Tower performs the following actions in your management account on your behalf:
+ Creates two AWS Organizations organizational units \(OUs\): Security, and Sandbox \(optional\), contained within the organizational root structure\.
+ Creates or adds two shared accounts in the Security OU: the Log Archive account and the Audit account\.
+ Creates a cloud\-native directory in IAM Identity Center, with preconfigured groups and single sign\-on access\.
+ Applies all mandatory, preventive controls to enforce policies\.
+ Applies all mandatory, detective controls to detect configuration violations\.
+ Preventive controls are not applied to the management account\.
+ Except for the management account, controls are applied to the organization as a whole\.

**Safely Managing Resources Within Your AWS Control Tower Landing Zone and Accounts**
+ When you create your landing zone, a number of AWS resources are created\. To use AWS Control Tower, you must not modify or delete these AWS Control Tower managed resources outside of the supported methods described in this guide\. Deleting or modifying these resources will cause your landing zone to enter an unknown state\. For details, see [Guidance for creating and modifying AWS Control Tower resources](getting-started-guidance.md)
+ When you enable optional controls \(those with *strongly recommended or elective * guidance\), AWS Control Tower creates AWS resources that it manages in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so can result in the controls entering an unknown state\. For more information, see [The AWS Control Tower controls library](controls-reference.md)\.

## What Are the Shared Accounts?<a name="what-shared"></a>

In AWS Control Tower, the shared accounts in your landing zone are provisioned during setup: the management account, the log archive account, and the audit account\.

### What is the management account?<a name="what-is-mgmt"></a>

This is the account that you created specifically for your landing zone\. This account is used for billing for everything in your landing zone\. It's also used for Account Factory provisioning of accounts, as well as to manage OUs and controls\.

**Note**  
It is not recommended to run any type of production workloads from an AWS Control Tower management account\. Create a separate AWS Control Tower account to run your workloads\. 

When you set up your landing zone, the following AWS resources are created within your management account\.


| AWS service | Resource type | Resource name | 
| --- | --- | --- | 
| AWS Organizations | Accounts | audit log archive | 
| AWS Organizations | OUs | Security Sandbox | 
| AWS Organizations | Service Control Policies | aws\-guardrails\-\*  | 
| AWS CloudFormation | Stacks | AWSControlTowerBP\-BASELINE\-CLOUDTRAIL\-MASTER AWSControlTowerBP\-BASELINE\-CONFIG\-MASTER \(in version 2\.6 and later\) | 
| AWS CloudFormation | StackSets |  AWSControlTowerBP\-BASELINE\-CLOUDTRAIL \(Not deployed in 3\.0 and later\)  AWSControlTowerBP\-BASELINE\-CLOUDWATCH AWSControlTowerBP\-BASELINE\-CONFIG AWSControlTowerBP\-BASELINE\-ROLES AWSControlTowerBP\-BASELINE\-SERVICE\-ROLES AWSControlTowerBP\-SECURITY\-TOPICS AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-READ\-PROHIBITED AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-WRITE\-PROHIBITED AWSControlTowerLoggingResources AWSControlTowerSecurityResources AWSControlTowerExecutionRole  | 
| AWS Service Catalog | Product | AWS Control Tower Account Factory | 
| AWS Config | Aggregator | aws\-controltower\-ConfigAggregatorForOrganizations | 
| AWS CloudTrail | Trail | aws\-controltower\-BaselineCloudTrail | 
| Amazon CloudWatch | CloudWatch Logs | aws\-controltower/CloudTrailLogs | 
| AWS Identity and Access Management | Roles | AWSControlTowerAdmin AWSControlTowerStackSetRole AWSControlTowerCloudTrailRolePolicy | 
| AWS Identity and Access Management | Policies | AWSControlTowerServiceRolePolicy AWSControlTowerAdminPolicy AWSControlTowerCloudTrailRolePolicy AWSControlTowerStackSetRolePolicy | 
| AWS IAM Identity Center \(successor to AWS Single Sign\-On\) | Directory groups | AWSAccountFactory AWSAuditAccountAdmins AWSControlTowerAdmins AWSLogArchiveAdmins AWSLogArchiveViewers AWSSecurityAuditors AWSSecurityAuditPowerUsers AWSServiceCatalogAdmins  | 
| AWS IAM Identity Center \(successor to AWS Single Sign\-On\) | Permission Sets | AWSAdministratorAccess AWSPowerUserAccess AWSServiceCatalogAdminFullAccess AWSServiceCatalogEndUserAccess AWSReadOnlyAccess AWSOrganizationsFullAccess  | 

**Note**  
The AWS CloudFormation StackSet `BP_BASELINE_CLOUDTRAIL` is not deployed in landing zone versions 3\.0 or later\. However, it continues to exist in earlier versions of the landing zone, until you update your landing zone\.

### What is the log archive account?<a name="what-is-log-archive"></a>

This account works as a repository for logs of API activities and resource configurations from all accounts in the landing zone\.

When you set up your landing zone, the following AWS resources are created within your log archive account\.


| AWS service | Resource type | Resource Name | 
| --- | --- | --- | 
| AWS CloudFormation | Stacks | StackSet\-AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-READ\-PROHIBITED\- StackSet\-AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-WRITE\-PROHIBITED StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDWATCH\- StackSet\-AWSControlTowerBP\-BASELINE\-CONFIG\- StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDTRAIL\- StackSet\-AWSControlTowerBP\-BASELINE\-SERVICE\-ROLES\-  StackSet\-AWSControlTowerBP\-BASELINE\-ROLES\- StackSet\-AWSControlTowerLoggingResources\- | 
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

### What is the audit account?<a name="what-is-audit"></a>

The audit account is a restricted account that's designed to give your security and compliance teams read and write access to all accounts in your landing zone\. From the audit account, you have programmatic access to review accounts, by means of a role that is granted to Lambda functions only\. The audit account does not allow you to log in to other accounts manually\. For more information about Lambda functions and roles, see [Configure a Lambda function to assume a role from another AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-assume-iam-role)\.  

When you set up your landing zone, the following AWS resources are created within your audit account\.


| AWS service | Resource type | Resource name | 
| --- | --- | --- | 
| AWS CloudFormation | Stacks | StackSet\-AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-READ\-PROHIBITED\- StackSet\-AWSControlTowerGuardrailAWS\-GR\-AUDIT\-BUCKET\-PUBLIC\-WRITE\-PROHIBITED\- StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDWATCH\- StackSet\-AWSControlTowerBP\-BASELINE\-CONFIG\- StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDTRAIL\- StackSet\-AWSControlTowerBP\-BASELINE\-SERVICE\-ROLES\-  StackSet\-AWSControlTowerBP\-SECURITY\-TOPICS\- StackSet\-AWSControlTowerBP\-BASELINE\-ROLES\- StackSet\-AWSControlTowerSecurityResources\-\* | 
| AWS Config | Aggregator | aws\-controltower\-GuardrailsComplianceAggregator | 
| AWS Config | AWS Config Rules | AWSControlTower\_AWS\-GR\_AUDIT\_BUCKET\_PUBLIC\_READ\_PROHIBITED AWSControlTower\_AWS\-GR\_AUDIT\_BUCKET\_PUBLIC\_WRITE\_PROHIBITED | 
| AWS CloudTrail | Trail | aws\-controltower\-BaselineCloudTrail | 
| Amazon CloudWatch | CloudWatch Event Rules | aws\-controltower\-ConfigComplianceChangeEventRule | 
| Amazon CloudWatch | CloudWatch Logs | aws\-controltower/CloudTrailLogs /aws/lambda/aws\-controltower\-NotificationForwarder | 
| AWS Identity and Access Management | Roles | aws\-controltower\-AdministratorExecutionRole aws\-controltower\-CloudWatchLogsRole aws\-controltower\-ConfigRecorderRole aws\-controltower\-ForwardSnsNotificationRole aws\-controltower\-ReadOnlyExecutionRole aws\-controltower\-AuditAdministratorRole aws\-controltower\-AuditReadOnlyRole AWSControlTowerExecution | 
| AWS Identity and Access Management | Policies | AWSControlTowerServiceRolePolicy | 
| Amazon Simple Notification Service | Topics | aws\-controltower\-AggregateSecurityNotifications aws\-controltower\-AllConfigNotifications aws\-controltower\-SecurityNotifications | 
| AWS Lambda | Functions | aws\-controltower\-NotificationForwarder | 

## How controls work<a name="how-controls-work"></a>

A control is a high\-level rule that provides ongoing governance for your overall AWS environment\. Each control enforces a single rule, and it's expressed in plain language\. You can change the elective or strongly recommended controls that are in force, at any time, from the AWS Control Tower console or the AWS Control Tower APIs\. Mandatory controls are always applied, and they can't be changed\.

Preventive controls prevent actions from occurring\. For example, the elective control called **Disallow Changes to Bucket Policy for Amazon S3 Buckets** \(Previously called **Disallow Policy Changes to Log Archive**\) prevents any IAM policy changes within the log archive shared account\. Any attempt to perform a prevented action is denied and logged in CloudTrail\. The resource is also logged in AWS Config\.

Detective controls detect specific events when they occur and log the action in CloudTrail\. For example, the strongly recommended control called **Detect Whether Encryption is Enabled for Amazon EBS Volumes Attached to Amazon EC2 Instances** detects whether an unencrypted Amazon EBS volume is attached to an EC2 instance in your landing zone\.

Proactive controls check whether resources are compliant with your company policies and objectives, before the resources are provisioned in your accounts\. If the resources are out of compliance, they are not provisioned\. Proactive controls monitor resources that would be deployed in your accounts by means of AWS CloudFormation templates\.

*For those who are familiar with AWS:* In AWS Control Tower preventive controls are implemented with Service Control Policies \(SCPs\)\. Detective controls are implemented with AWS Config rules\. Proactive controls are implemented with AWS CloudFormation hooks\.

### Related Topics<a name="how-controls-related"></a>
+ [About controls in AWS Control Tower](controls.md)
+ [Detect and resolve drift in AWS Control Tower](drift.md)

## How AWS Control Tower Works With StackSets<a name="stacksets-how"></a>



AWS Control Tower uses AWS CloudFormation StackSets to set up resources in your accounts\. Each stack set has StackInstances that correspond to accounts, and to AWS Regions per account\. AWS Control Tower deploys one stack set instance per account and Region\.

AWS Control Tower applies updates to certain accounts and AWS Regions selectively, based on AWS CloudFormation parameters\. When updates are applied to some stack instances, other stack instances may be left in **Outdated** status\. This behavior is expected and normal\.

When a stack instance goes into **Outdated** status, it usually means that the stack corresponding to that stack instance is not aligned with the latest template in the stack set\. The stack remains in the older template, so it might not include the latest resources or parameters\. The stack is still completely usable\.

 Here's a quick summary of what behavior to expect, based on AWS CloudFormation parameters that are specified during an update:

If the stack set update includes changes to the template \(that is, if the `TemplateBody` or `TemplateURL` properties are specified\), or if the `Parameters` property is specified, AWS CloudFormation marks all stack instances with a status of **Outdated** prior to updating the stack instances in the specified accounts and AWS Regions\. If the stack set update does not include changes to the template or parameters, AWS CloudFormation updates the stack instances in the specified accounts and Regions, while leaving all other stack instances with their existing stack instance status\. To update all of the stack instances associated with a stack set, do not specify the `Accounts` or `Regions` properties\.

For more information, see [Update Your Stack Set](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-update.html) in the AWS CloudFormation User Guide\.