# Monitor Events with CloudTrail<a name="cloudtrail"></a>

AWS Control Tower configures AWS CloudTrail to enable centralized logging and auditing\. With CloudTrail, the management account can review administrative actions and lifecycle events for member accounts\.

CloudTrail helps you monitor your AWS environment in the cloud by keeping a history of AWS API calls for your accounts\. For example, you can identify the users and accounts that called AWS APIs for services that support CloudTrail, the source IP address from which the calls were made, and the time when the calls occurred\. You can integrate CloudTrail into applications using the API, automate trail creation for your organization, check the status of your trails, and control how administrators turn CloudTrail logging on and off\. For more information, see *[AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)*\.

**Organization\-level trails**

AWS Control Tower sets up a new CloudTrail trail when you set up a landing zone\. It is an *organization\-level trail*, which means that it logs all events for the management account and all member accounts in the organization\. This feature relies on *trusted access* to give the management account permissions to create a trail on every member account\.

For more information about AWS Control Tower and CloudTrail organization trails, see [Creating a trail for an organization](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/creating-trail-organization.html)\.

**Note**  
In AWS Control Tower releases before landing zone version 3\.0, AWS Control Tower created a member account trail in each account\. When you update to release 3\.0, your CloudTrail trail becomes an organization trail\. For best practices when moving between trails, see [Best practices for changing trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/creating-trail-organization.html#creating-an-organizational-trail-best-practice) in the *CloudTrail User Guide*\.

When you enroll an account into AWS Control Tower, your account is governed by the AWS CloudTrail trail for the AWS Control Tower organization\. If you have an existing deployment of a CloudTrail trail in that account, you may see duplicate charges unless you delete the existing trail for the account before you enroll it in AWS Control Tower\. 

**Note**  
When you update to landing zone version 3\.0, AWS Control Tower deletes the account\-level trails of your enrolled accounts on your behalf\. Your existing, account\-level log files are preserved in their Amazon S3 bucket\.