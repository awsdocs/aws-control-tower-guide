# Getting Started with AWS Control Tower<a name="getting-started-with-control-tower"></a>

This is the AWS Control Tower getting started procedure for central cloud administrators\. Use this procedure when you're ready to set up your landing zone\. From start to finish, it should take about an hour\. This procedure has two steps\.

## Guidance for Using AWS Control Tower<a name="getting-started-guidance"></a>

We recommend the following when you use AWS Control Tower\. This guidance might change as the service is updated\.

**General Guidance**
+ Do not modify or delete resources created by AWS Control Tower in the master account or in the shared accounts\. Modification of these resources can require an update to your landing zone\.
+ Do not modify or delete the AWS Identity and Access Management \(IAM\) roles created within the shared accounts in the core organizational unit \(OU\)\. Modification of these roles can require an update to your landing zone\.
+ For more information about the resources created by AWS Control Tower, see [What Are the Shared Accounts?](how-control-tower-works.md#what-shared)

**AWS Organizations Guidance**
+ Do not use AWS Organizations to update service control policies \(SCPs\) that are attached by AWS Control Tower to an AWS Control Tower managed OU\. Doing so could result in the guardrails entering an unknown state, which will require you to re\-enable affected guardrails\.
+ If you use Organizations to create, invite, or move accounts within an organization created by AWS Control Tower, those outside accounts will not be managed by AWS Control Tower and will not appear in the **Accounts** table\.
+ If you use Organizations to create or move OUs within an organization created by AWS Control Tower, those outside OUs will not be managed by AWS Control Tower and will not appear in the **Organizational Units** table\.
+ If you use Organizations to rename or delete an OU that was created by AWS Control Tower, then this OU will continue to be displayed by AWS Control Tower using its original name\. You will not be able to provision a new account to this OU using Account Factory\.

**AWS Single Sign\-On Guidance**
+ If you reconfigure your directory in AWS Single Sign\-On to Active Directory, all preconfigured users and groups in AWS SSO will be deleted\.

**Account Factory Guidance**
+ When you use Account Factory to provision new accounts in AWS Service Catalog, do not define `TagOptions`, enable notifications, or create a provisioned product plan\. Doing so can result in a failure to provision a new account\.

## Pre\-Launch Checks for Your Master Account<a name="getting-started-prereqs"></a>

Before AWS Control Tower does any work in your account to set up the landing zone, it runs a series of pre\-launch checks\. These ensure that your master account is ready for the changes that establish your landing zone\. The checks that run before setting up a landing zone are as follows:
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

## Step One: Create Your Shared Account Email Addresses<a name="step-one"></a>

This guide assumes that you're setting up your landing zone in a new AWS account\. For information on creating your account and your IAM administrator, see [Setting Up](setting-up.md)\.

To set up your landing zone, AWS Control Tower requires two unique email addresses that aren't already associated with an AWS account\. These email addresses should each be a collaborative inbox, a shared email account for the different users in your enterprise that will do specific work related to AWS Control Tower\. The email addresses are:
+ **Audit account** – This is for your team of users that need access to the audit information made available by AWS Control Tower\. You can also use this account as the access point for third\-party tools that will perform programmatic auditing of your environment to help you audit for compliance purposes\.
+ **Log archive account** – This is for your team of users that need access to all the logging information for all of your managed accounts within managed OUs in your landing zone\.

## Step Two: Set Up Your Landing Zone<a name="step-two"></a>

AWS Control Tower has no APIs or programmatic access\. To set up your landing zone, perform the following procedure\.

**To set up your landing zone**

1. Open a web browser, and navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. Choose **Set up your landing zone**\.

1. Provide the email addresses for your log archive and audit accounts\. Note that the email addresses must not already have associated AWS accounts\.

1. Review the **Service permissions**, and when you're ready, choose **I understand the permissions AWS Control Tower will use to administer AWS resources and enforce rules on my behalf**\.

1. Choose **Launch your AWS Control Tower**\.

This starts the process of setting up your landing zone, which can take about an hour to complete\. During setup, your core accounts are created, your root and Core OUs are created, and AWS resources are created, modified, or deleted\.

**Important**  
The email address you provided for the audit account will receive **AWS Notification \- Subscription Confirmation** emails from every AWS Region supported by AWS Control Tower\. To receive compliance emails in your audit account, you must choose the **Confirm subscription** link within each email from each AWS Region supported by AWS Control Tower\. 

## Next Steps<a name="getting-started-next"></a>

Now that your landing zone is set up, it's ready for use\.

When you set up a landing zone, AWS Control Tower performs the following actions in your master account on your behalf:
+ Creates two organizational units \(OUs\), **Core** and **Custom**\.
+ Creates two accounts: the log archive account and the security audit account within the Core OU\.
+ Creates a cloud\-native directory in AWS SSO, with preconfigured groups, permission sets, and single sign\-on access\.
+ Applies 17 preventive guardrails, to enforce policies\.
+ Applies two detective guardrails, to detect configuration violations\.
+ Creates the Account Factory product in AWS Service Catalog so your end\-users can provision new AWS accounts within your landing zone\.

To learn more about how you can use AWS Control Tower, see the following topics:
+ Your end users can provision their own AWS accounts in your landing zone using Account Factory\. For more information, see [Configuring and Provisioning Accounts Through AWS Service Catalog](account-factory.md#configure-provision-new-account)\.
+ From time to time, you may need to update your landing zone to get the latest backend updates, the latest guardrails, and to keep your landing zone up\-to\-date\. For more information, see [Configuration Update Management in AWS Control Tower](configuration-updates.md)\.
+ If you encounter issues while using AWS Control Tower, see [Troubleshooting](troubleshooting.md)\.