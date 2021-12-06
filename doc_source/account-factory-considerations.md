# Resource Considerations for Account Factory<a name="account-factory-considerations"></a>

When an account is provisioned with Account Factory, the following AWS resources are created within the account\.


| AWS service | Resource type | Resource name | 
| --- | --- | --- | 
| AWS CloudFormation | Stacks |  StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDTRAIL\-\* StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDWATCH\-\* StackSet\-AWSControlTowerBP\-BASELINE\-CONFIG\-\* StackSet\-AWSControlTowerBP\-BASELINE\-ROLES\-\* StackSet\-AWSControlTowerBP\-BASELINE\-SERVICE\-ROLES\-\*  | 
| AWS CloudTrail | Trail | aws\-controltower\-BaselineCloudTrail | 
| Amazon CloudWatch | CloudWatch Event Rules | aws\-controltower\-ConfigComplianceChangeEventRule | 
| Amazon CloudWatch | CloudWatch Logs | aws\-controltower/CloudTrailLogs /aws/lambda/aws\-controltower\-NotificationForwarder | 
| AWS Identity and Access Management | Roles | aws\-controltower\-AdministratorExecutionRole aws\-controltower\-CloudWatchLogsRole aws\-controltower\-ConfigRecorderRole aws\-controltower\-ForwardSnsNotificationRole aws\-controltower\-ReadOnlyExecutionRole  AWSControlTowerExecution | 
| AWS Identity and Access Management | Policies | AWSControlTowerServiceRolePolicy  | 
| Amazon Simple Notification Service | Topics | aws\-controltower\-SecurityNotifications | 
| AWS Lambda | Applications | StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDWATCH\-\* | 
| AWS Lambda | Functions | aws\-controltower\-NotificationForwarder | 