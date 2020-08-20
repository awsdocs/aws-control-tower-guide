# Getting started with AWS Control Tower<a name="getting-started-with-control-tower"></a>

This getting started procedure is for AWS Control Tower central cloud administrators\. Use this procedure when you're ready to set up your landing zone\. From start to finish, it should take about an hour\. This procedure has a prerequisite and two steps\.

## Prerequisite: Automated Pre\-Launch Checks for Your Master Account<a name="getting-started-prereqs"></a>

Before AWS Control Tower sets up the landing zone, it automatically runs a series of pre\-launch checks in your account\. There's no action required on your part for these checks, which ensure that your master account is ready for the changes that establish your landing zone\. Here are the checks that AWS Control Tower runs before setting up a landing zone:
+ The existing service limits for the AWS account must be sufficient for AWS Control Tower to launch\. For more information, see [Limitations and quotas in AWS Control Tower](limits.md)\.
+ The AWS account must be subscribed to the following AWS services:
  + Amazon Simple Storage Service \(Amazon S3\)
  + Amazon Elastic Compute Cloud \(Amazon EC2\)
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
+ If AWS Single Sign\-On \(AWS SSO\) is already set up, the AWS Control Tower home region must be the same as the AWS SSO region\.
+ The AWS account cannot have trusted access enabled in the organization master account for either AWS Config or AWS CloudTrail\. We recommend that you do not turn AWS Config off to set up AWS Control Tower and then turn it back on\. If you do so, you'll incur additional charges\. 

**Note**  
When you enroll an account into AWS Control Tower, your account is governed by the AWS CloudTrail trail for the AWS Control Tower organization\. If you have an existing deployment of a CloudTrail trail, you may see duplicate charges unless you delete the existing trail for the account before you enroll it in AWS Control Tower\.

## Step One: Create Your Shared Account Email Addresses<a name="step-one"></a>

If you're setting up your landing zone in a new AWS account, for information on creating your account and your IAM administrator, see [Setting up](setting-up.md)\.

To set up your landing zone, AWS Control Tower requires two unique email addresses that aren't already associated with an AWS account\. These email addresses should each be a collaborative inbox, a shared email account for the different users in your enterprise that will do specific work related to AWS Control Tower\. The email addresses are:
+ **Audit account** – This account is for your team of users that need access to the audit information made available by AWS Control Tower\. You can also use this account as the access point for third\-party tools that will perform programmatic auditing of your environment to help you audit for compliance purposes\.
+ **Log archive account** – This account is for your team of users that need access to all the logging information for all of your managed accounts within managed OUs in your landing zone\.

## Step Two: Set Up Your Landing Zone<a name="step-two"></a>

Before you set up your AWS Control Tower landing zone, determine the most appropriate home region\. For more information, see [Administrative Tips for Landing Zone Setup](best-practices.md#tips-for-admin-setup)\. 

AWS Control Tower has no APIs or programmatic access\. To set up your landing zone, perform the following procedure:

**To set up your landing zone**

1. Open a web browser, and navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. In the console, verify that you are working in your desired home region for AWS Control Tower\. Then choose **Set up your landing zone**\.

1. Provide the email addresses for your log archive and audit accounts\. Note that the email addresses must not already have associated AWS accounts\.

1. Review the **Service permissions**, and when you're ready, choose **I understand the permissions AWS Control Tower will use to administer AWS resources and enforce rules on my behalf**\.

1. Choose **Launch your AWS Control Tower**\.

This starts the process of setting up your landing zone, which can take about an hour to complete\. During setup, your core accounts are created, your root and Core OUs are created, and AWS resources are created, modified, or deleted\.

**Important**  
The email address you provided for the audit account will receive **AWS Notification \- Subscription Confirmation** emails from every AWS Region supported by AWS Control Tower\. To receive compliance emails in your audit account, you must choose the **Confirm subscription** link within each email from each AWS Region supported by AWS Control Tower\. 

## Next Steps<a name="getting-started-next"></a>

Now that your landing zone is set up, it's ready for use\.

To learn more about how you can use AWS Control Tower, see the following topics:
+ For recommended administrative practices, see [Best Practices](https://docs.aws.amazon.com/controltower/latest/userguide/best-practices.html)\.
+ You can set up AWS SSO users and groups with specific roles and permissions\. For recommendations, see [Recommendations for Setting Up Groups, Roles, and Policies](best-practices.md#roles-recommendations)\.
+ Your end users can provision their own AWS accounts in your landing zone using Account Factory\. For more information, see [Permissions for Configuring and Provisioning Accounts](account-factory.md#configure-provision-new-account)\.
+ To assure [Compliance Validation for AWS Control Tower](compliance-program-info.md), your central cloud administrators can review log archives in the log archive account, and designated third\-party auditors can review audit information in the audit shared account\. 
+ From time to time, you may need to update your landing zone to get the latest backend updates, the latest guardrails, and to keep your landing zone up\-to\-date\. For more information, see [Configuration update management in AWS Control Tower](configuration-updates.md)\.
+ If you encounter issues while using AWS Control Tower, see [Troubleshooting](troubleshooting.md)\.