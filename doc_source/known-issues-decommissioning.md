# Setup after decommissioning a landing zone<a name="known-issues-decommissioning"></a>

After you decommission your landing zone, you cannot successfully execute setup again until manual cleanup is complete\. Also, without manual cleanup of these remaining resources, you may incur unexpected billing charges\. You must attend to these issues:
+ The AWS Control Tower management account is part of the AWS Control Tower **Root OU**\. Be sure that these IAM roles and IAM policies are removed from the management account: 
  + Roles: 

    `- AWSControlTowerAdmin`

    `- AWSControlTowerCloudTrailRole`

    `- AWSControlTowerStackSetRole`
  + Policies: 

    `- AWSControlTowerAdminPolicy`

    `- AWSControlTowerCloudTrailRolePolicy`

    `- AWSControlTowerStackSetRolePolicy`
+ You may wish to delete or update the existing IAM Identity Center configuration for AWS Control Tower before you up a landing zone again, but it is not required that you delete it\.
+ You may wish to remove the VPC created by AWS Control Tower\.
+ Setup fails if the email addresses specified for the logging or audit accounts are associated with an existing AWS account\. You may close the AWS accounts, or use different email addresses to set up a landing zone again\. Alternatively, you may re\-use these existing shared accounts, with the feature that allows you to bring your own logging and audit accounts\. For more information, see [Considerations for bringing existing security or logging accounts](accounts.md#considerations-for-existing-shared-accounts)\.
+ Setup fails if Amazon S3 buckets with the following reserved names already exist in the logging account:
  + `aws-controltower-logs-{accountId}-{region}` \(used for the logging bucket\)\.
  + `aws-controltower-s3-access-logs-{accountId}-{region}` \(used for the logging access bucket\)\.

   You must either rename or remove these buckets, or use a different account for the logging account\.
+ Setup fails if the management account has the existing log group, `aws-controltower/CloudTrailLogs`, in CloudWatch Logs\. You must either rename or remove the log group\. 

If you intend to set up a new landing zone in a new AWS Region, follow this additional step\. Enter the following command through the CLI: 

```
aws organizations disable-aws-service-access --service-principal controltower.amazonaws.com
```

**Note**  
You cannot set up a new landing zone in an organization with top\-level OUs named either **Security** or **Sandbox**\. You must rename or remove these OUs to set up a landing zone again\.