# Setup After Decommissioning a Landing Zone<a name="known-issues-decommissioning"></a>

After you decommission your landing zone, you cannot successfully execute setup again until manual cleanup is complete\. Also, without manual cleanup of these remaining resources, you may incur unexpected billing charges\. You must attend to these issues:
+ You cannot set up a landing zone in an existing AWS Organizations organization\. You must delete the organization to set up a landing zone again\. For more instructions on how to delete an organization, refer to [the AWS Organizations User Guide](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_org_delete.html)\.
**Note**  
Before you can delete the AWS Control Tower **Core OU** organization, you must first delete the logging and audit accounts, but not the master account\. To delete these accounts, you must [Log in as a Root User](best-practices.md#root-login) to the audit account and to the logging account and delete them individually\. 
+ The AWS Control Tower master account is part of the AWS Control Tower **Root OU**\. Be sure that these IAM roles and IAM policies are removed from the master account: 
  + Roles: 

    `- AWSControlTowerAdmin`

    `- AWSControlTowerCloudTrailRole`

    `- AWSControlTowerStackSetRole`
  + Policies: 

    `- AWSControlTowerAdminPolicy`

    `- AWSControlTowerCloudTrailRolePolicy`

    `- AWSControlTowerStackSetRolePolicy`
+ You may wish to delete or update the existing AWS SSO configuration for AWS Control Tower before you up a landing zone again, but it is not required that you delete it\.
+ Setup fails if the email addresses specified for the logging or audit accounts are associated with an existing AWS account\. You must close the AWS accounts, or use different email addresses to set up a landing zone again\. 
+ Setup fails if S3 buckets with the following reserved names already exist in the logging account:
  + `aws-controltower-logs-{accountId}-{region}` \(used for the logging bucket\)\.
  + `aws-controltower-s3-access-logs-{accountId}-{region}` \(used for the logging access bucket\)\.

   You must either rename or remove these buckets, or use a different account for the logging account\.
+ Setup fails if the master account has the existing log group, `aws-controltower/CloudTrailLogs`, in CloudWatch Logs\. You must either rename or remove the log group\. 