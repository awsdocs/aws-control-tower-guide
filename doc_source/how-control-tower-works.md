# How AWS Control Tower Works<a name="how-control-tower-works"></a>

This section describes at a high level how AWS Control Tower works\. Your landing zone is a well\-architected multi\-account environment for all of your AWS resources\. You can use this environment to enforce compliance regulations on all of your AWS accounts\.

## Structure of an AWS Control Tower Landing Zone<a name="landing-zone-structure"></a>

The structure of a landing zone in AWS Control Tower is as follows:
+ **Root** – The parent that contains all other OUs in your landing zone\. 
+ **Core OU** – This OU contains the log archive and audit member accounts\. These accounts often are referred to as *shared accounts*\.
+ **Custom OU** – The custom OU is created when you launch your landing zone\. This and other member OUs contain the member accounts that your users work with to perform their AWS workloads\.
+ **AWS SSO directory** – This directory houses your AWS SSO users\. It defines the scope of permissions for each AWS SSO user\.
+ **AWS SSO users** – These are the identities that your users can assume to perform their AWS workloads in your landing zone\.

## What Happens When You Set Up a Landing Zone<a name="how-it-works-setup"></a>

When you set up a landing zone, AWS Control Tower performs the following actions in your master account on your behalf:
+ Creates three Organizations organizational units \(OUs\): Root, Core, and Custom\.
+ Creates two shared accounts: the log archive account and audit account\.
+ Creates a cloud\-native directory in AWS SSO, with preconfigured groups and single sign\-on access\.
+ Applies 20 preventive guardrails to enforce policies\.
+ Applies six detective guardrails to detect configuration violations\.
+ Preventive guardrails are not applied to the master account\.
+ Except for the master account, guardrails are applied to the organization as a whole\.

**Safely Managing Resources Within Your AWS Control Tower Landing Zone and Accounts**
+ When you create your landing zone, a number of AWS resources are created\. To use AWS Control Tower, you must not modify or delete these AWS Control Tower managed resources outside of the supported methods described in this guide\. Deleting or modifying these resources will cause your landing zone to enter an unknown state\. For details, see [Guidance for Creating and Modifying AWS Control Tower Resources](best-practices.md#getting-started-guidance)
+ When you enable guardrails with *strongly recommended* guidance, AWS Control Tower creates AWS resources that it manages in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so can result in the guardrails entering an unknown state\. For more information, see [Guardrail Reference](guardrails-reference.md)\.

## What Are the Shared Accounts?<a name="what-shared"></a>

In AWS Control Tower, three shared accounts in your landing zone are not provisioned in Account Factory: the master account, the log archive account, and the audit account\.

### What Is the Master Account?<a name="what-is-master"></a>

This is the account that you created specifically for your landing zone\. This account is used for billing for everything in your landing zone\. It's also used for Account Factory provisioning of accounts, as well as to manage OUs and guardrails\.

**Note**  
It is not recommended to run any type of production workloads from an AWS Control Tower master account\. Create a separate AWS Control Tower account to run your workloads\. 

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

This account works as a repository for logs of API activities and resource configurations from all accounts in the landing zone\.

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

The audit account is a restricted account that's designed to give your security and compliance teams read and write access to all accounts in your landing zone\. From the audit account, you have programmatic access to review accounts, by means of a role that is granted to Lambda functions only\. The audit account does not allow you to log in to other accounts manually\. For more information about Lambda functions and roles, see [Configure a Lambda function to assume a role from another AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-assume-iam-role)\.  

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

A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. Each guardrail enforces a single rule, and it's expressed in plain language\. Compliance needs evolve, and you can change the elective or strongly recommended guardrails that are in force, at any time, from the AWS Control Tower console\. Mandatory guardrails are always applied, and they can't be changed\.

Preventive guardrails prevent actions from occurring\. For example, the **Disallow policy changes to log archive** guardrail prevents any IAM policy changes within the log archive shared account\. Any attempt to perform a prevented action is denied and logged in CloudTrail\. The resource is also logged in AWS Config\.

Detective guardrails detect specific events when they occur and log the action in CloudTrail\. For example, the **Enable encryption for EBS volumes attached to EC2 instances** detects if an unencrypted Amazon EBS volume is attached to an EC2 instance in your landing zone\.

### Related Topics<a name="how-guardrails-related"></a>
+ [Guardrails in AWS Control Tower](guardrails.md)
+ [Detect and resolve drift in AWS Control Tower](drift.md)

## How AWS Regions Work With AWS Control Tower<a name="region-how"></a>

Currently, AWS Control Tower is supported in the following AWS Regions:
+ US East \(N\. Virginia\)
+ US East \(Ohio\)
+ US West \(Oregon\)
+ Europe \(Ireland\)
+ Asia Pacific \(Sydney\)

When you create a landing zone, the region that you're using for access to the AWS Management Console becomes your home AWS Region for AWS Control Tower\. During the creation process, some resources are provisioned in the home AWS Region\. Other resources, such as OUs and AWS accounts, are global\.

Currently, all preventive guardrails work globally\. Detective guardrails, however, only work in regions where AWS Control Tower is supported\. For more information about the behavior of guardrails when you activate AWS Control Tower in a new region, see [Deploying AWS Control Tower to a New AWS Region](configuration-updates.md#deploying-to-new-region)\.

## How AWS Control Tower Works With Roles to Create and Manage Accounts<a name="roles-how"></a>

AWS Control Tower creates a customer's account by calling the `CreateAccount` API of AWS Organizations\. When AWS Organizations creates this account, it creates a role within that account, which AWS Control Tower names by passing in a parameter to the API\. The name of the role is `AWSControlTowerExecution`\.

AWS Control Tower takes over the `AWSControlTowerExecution` role for all accounts created by Account Factory\. Using this role, AWS Control Tower *baselines* the account and applies mandatory \(and any other enabled\) guardrails, which results in creation of other roles\. These roles in turn are used by other services, such as AWS Config\.

**Note**  
To baseline an account is to set up its blueprints and guardrails\. The baselining process also sets up the centralized logging and security audit roles on the account, as part of deploying the blueprints\. AWS Control Tower baselines are contained in the roles that you apply to every managed account\. 

**The AWSControlTowerExecution role, explained**

The `AWSControlTowerExecution` role allows AWS Control Tower to manage your individual accounts and report information about them to your audit and logging accounts\.
+ `AWSControlTowerExecution` allows auditing by the AWS Control Tower audit account\.
+ `AWSControlTowerExecution` helps you configure your organizations's logging, so that all the logs for every account are sent to the logging account\.

After you’ve completed setting up accounts, `AWSControlTowerExecution` ensures that your selected AWS Control Tower guardrails apply automatically to every individual account in your organization, as well as to every new account you create in AWS Control Tower\. Therefore, you can provide compliance and security reports with ease, based on the auditing and logging features embodied by AWS Control Tower guardrails\. Your security and compliance teams can verify that all requirements are met, and that no organizational drift has occurred\. For more information about drift, see [the AWS Control Tower User Guide](https://docs.aws.amazon.com/controltower/latest/userguide/drift.html)\. 

To summarize, the `AWSControlTowerExecution` role and its associated policy gives you flexible control of security and compliance across your entire organization\. Therefore, breaches of security are less likely to occur\.

## How AWS Control Tower Works With StackSets<a name="stacksets-how"></a>

AWS Control Tower uses CloudFormation StackSets to set up resources in your accounts\. Each stack set has StackInstances that correspond to multiple accounts, and to multiple AWS Regions per account\. Control Tower applies updates to certain accounts and AWS Regions selectively, based on certain CloudFormation parameters\. When updates are applied to some stack instances, other stack instances may be left in **Outdated** status\. This behavior is expected and normal\.

When a stack instance goes into **Outdated** status, it usually means that the stack corresponding to that stack instance is not aligned with the latest template in the stack set\. The stack remains in the older template, so it might not include the latest resources or parameters\. The stack is still completely usable\.

 Here's a quick summary of what behavior to expect, based on AWS CloudFormation parameters that are specified during an update:

If the stack set update includes changes to the template \(that is, if the `TemplateBody` or `TemplateURL` properties are specified\), or if the `Parameters` property is specified, AWS CloudFormation marks all stack instances with a status of **Outdated** prior to updating the stack instances in the specified accounts and AWS Regions\. If the stack set update does not include changes to the template or parameters, AWS CloudFormation updates the stack instances in the specified accounts and Regions, while leaving all other stack instances with their existing stack instance status\. To update all of the stack instances associated with a stack set, do not specify the `Accounts` or `Regions` properties\.

For more information, see [Update Your Stack Set](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-update.html) in the AWS CloudFormation User Guide\.