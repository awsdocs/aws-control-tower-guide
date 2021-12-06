# Best practices for AWS Control Tower administrators<a name="best-practices"></a>

This topic is intended primarily for management account administrators\.

Management account administrators are responsible for explaining some tasks that AWS Control Tower guardrails prevent their member account administrators from doing\. This topic describes some best practices and procedures for transferring this knowledge, and it gives other tips for setting up and maintaining your AWS Control Tower environment efficiently\.

## Explaining Access to Users<a name="explaining-users"></a>

The AWS Control Tower console is available only to users with the management account administrator permissions\. Only these users can perform administrative work within your landing zone\. In accordance with best practices, this means that the majority of your users and member account administrators will never see the AWS Control Tower console\. As a member of the management account administrator group, it's your responsibility to explain the following information to the users and administrators of your member accounts, as appropriate\.
+ Explain which AWS resources that users and administrators have access to within the landing zone\.
+ List the preventive guardrails that apply to each Organizational Unit \(OU\) so that the other administrators can plan and execute their AWS workloads accordingly\.

### Explaining Resource Access<a name="explaining-resource-access"></a>

Some administrators and other users may need an explanation of the AWS resources to which they have access to within your landing zone\. This access can include programmatic access and console\-based access\. Generally speaking, read access and write access for AWS resources is allowed\. To perform work within AWS, your users require some level of access to the specific services they need to do their jobs\.

Some users, such as your AWS developers, may need to know about the resources to which they have access, so they can create engineering solutions\. Other users, such as the end users of the applications that run on AWS services, do not need to know about AWS resources within your landing zone\.

AWS offers tools to identify the scope of a user's AWS resource access\. After you identify the scope of a user's access, you can share that information with the user, in accordance with your organization's information management policies\. For more information about these tools, see the links that follow\. 
+ **AWS access advisor** – The AWS Identity and Access Management \(IAM\) access advisor tool lets you determine the permissions that your developers have by analyzing the last timestamp when an IAM entity, such as a user, role, or group, called an AWS service\. You can audit service access and remove unnecessary permissions, and you can automate the process if needed\. For more information, see [our AWS Security blog post](http://aws.amazon.com/blogs/security/automate-analyzing-permissions-using-iam-access-advisor)\.
+ **IAM policy simulator** – With the IAM policy simulator, you can test and troubleshoot IAM\-based and resource\-based policies\. For more information, see [Testing IAM Policies with the IAM Policy Simulator](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_testing-policies.html)\.
+ **AWS CloudTrail logs** – You can review AWS CloudTrail logs to see actions taken by a user, role, or AWS service\. For more information about CloudTrail, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)\.

  Actions taken by CloudTrail landing zone administrators are logged in the landing zone management account\. Actions taken by member account administrators and users are logged in the shared log archive account\.

  You can view a summary table of AWS Control Tower events in the [**Activities** page\.](https://console.aws.amazon.com/)

### Explaining Preventive Guardrails<a name="explaining-preventive-guardrails"></a>

A preventive guardrail ensures that your organization's accounts maintain compliance with your corporate policies\. The status of a preventive guardrail is either **enforced** or **not\-enabled**\. A preventive guardrail prevents policy violations by using service control policies \(SCPs\)\. In comparison, a detective guardrail informs you of various events or states that exist, by means of defined AWS Config rules\.

Some of your users, such as AWS developers, may need to know about the preventive guardrails that apply to any accounts and OUs they use, so they can create engineering solutions\. The following procedure offers some guidance on how to provide this information for the right users, according to your organization's information management policies\.

**Note**  
This procedure assumes you've already created at least one child OU within your landing zone, as well as at least one AWS Single Sign\-On user\.

**To show preventive guardrails for users with a need to know**

1. Sign in to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower/](https://console.aws.amazon.com/controltower/)\.

1. From the left navigation, choose **Organizational units**\.

1. From the table, choose the **name** of one of the OUs for which your user needs information about the applicable guardrails\.

1. Note the name of the OU and the guardrails that apply to this OU\.

1. Repeat the previous two steps for each OU about which your user needs information\.

For detailed information about the guardrails and their functions, see [Guardrails in AWS Control Tower](guardrails.md)\.

## Guidance for Creating and Modifying AWS Control Tower Resources<a name="getting-started-guidance"></a>

We recommend the following practices as you create and modify resources in AWS Control Tower\. This guidance might change as the service is updated\.

**General Guidance**
+ Do not modify or delete resources created by AWS Control Tower in the management account or in the shared accounts\. Modification of these resources can require you to update your landing zone or re\-register an OU\.
+ Do not modify or delete the AWS Identity and Access Management \(IAM\) roles created within the shared accounts in the Security organizational unit \(OU\)\. Modification of these roles can require an update to your landing zone\.
+ For more information about the resources created by AWS Control Tower, see [What Are the Shared Accounts?](how-control-tower-works.md#what-shared)
+ Do not disallow usage of any AWS Regions through either SCPs or AWS Security Token Service \(STS\)\. Doing so will cause AWS Control Tower to enter an undefined state\. If you disallow Regions with AWS STS, your functionality will fail in those Regions, because authentication would be unavailable in those Regions\.
+ The AWS Organizations **FullAWSAccess** SCP must be applied and should not be merged with other SCPs\. Change to this SCP is not reported as drift; however, some changes may affect AWS Control Tower functionality in unpredictable ways, if access to certain resources is denied\. For example, if the SCP is detached, or modified, an account may lose access to an AWS Config recorder or create a gap in CloudTrail logging\.
+ In general, AWS Control Tower performs a single action at a time, which must be completed before another action can begin\. For example, if you attempt to provision an account while the process of enabling a guardrail is already in operation, account provisioning will fail\. The only concurrent action allowed in AWS Control Tower is deployment of detective guardrails\. See [Concurrent detective guardrail deployment](enable-guardrails.md#concurrent-detective-guardrails)\.
+ We recommend that you keep each registered OU to a maximum of 300 accounts, so that you can update those accounts with the **Re\-register OU** capability whenever account updates are required, such as when you configure new Regions for governance\.
+  Keep an active AWS Config recorder\. If you delete your Config recorder, detective guardrails cannot detect and report drift\. Non\-compliant resources may be reported as **Compliant** due to insufficient information\. 
+ Do not delete the **AWSControlTowerExecution** role from your member accounts, even in unenrolled accounts\. If you do, you will not be able to enroll these accounts with AWS Control Tower, or register their immediate parent OUs\.

## AWS Organizations Guidance<a name="orgs-guidance"></a>
+ Do not use AWS Organizations to update service control policies \(SCPs\) attached to an OU that is registered with AWS Control Tower\. Doing so could result in the guardrails entering an unknown state, which will require you to repair your landing zone or re\-register your OU in AWS Control Tower\. Instead, you can create new SCPs and attach those to the OUs rather than editing the SCPs that AWS Control Tower has created\.
+ Moving individual, already enrolled, accounts into AWS Control Tower, from outside of a registered OU, causes drift that must be repaired\. See [Types of Governance Drift](drift.md#governance-drift)\.
+ If you use AWS Organizations to create, invite, or move accounts within an organization registered with AWS Control Tower, those accounts are not enrolled by AWS Control Tower and those changes are not recorded\. If you need access to these accounts through SSO, see [Member Account Access](http://aws.amazon.com/premiumsupport/knowledge-center/organizations-member-account-access/)\.
+ If you use AWS Organizations to move an OU into an organization created by AWS Control Tower, the external OU is not registered by AWS Control Tower\.
+ AWS Control Tower handles permission filtering differently than AWS Organizations does\. If your accounts are provisioned with AWS Control Tower account factory, end users can see the names and parents of all OUs in the AWS Control Tower console, even if they don't have permission to retrieve those names and parents from AWS Organizations directly\.
+ AWS Control Tower does not support mixed permissions on organizations, such as permission to view an OU's parent but not to view OU names\. For this reason, AWS Control Tower administrators are expected to have full permissions\.

## AWS Single Sign\-On Guidance<a name="sso-guidance"></a>
+ For specific information about how AWS Control Tower works with SSO based on your identity source, see **Considerations for AWS Single Sign\-On \(SSO\) customers** in the [Prerequisites](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-with-control-tower.html#getting-started-prereqs) section of the *Getting Started* page of this User Guide\.
+ For additional information about how the behavior of AWS Control Tower interacts with AWS SSO and different identity sources, refer to [Considerations for Changing Your Identity Source](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-considerations.html) in the AWS SSO documentation\.
+ See [Managing Users and Access Through AWS Single Sign\-On](sso.md) for more information about working with AWS Control Tower and AWS SSO\.

## Account Factory Guidance<a name="af-guidance"></a>
+ When you use Account Factory to provision new accounts in AWS Service Catalog, do not define `TagOptions`, enable notifications, or create a provisioned product plan\. Doing so can result in a failure to provision a new account\.
+ If you are authenticated as an IAM user when you provision accounts in Account Factory or when you use the **Enroll account** feature, be sure the IAM user is [added to the AWS Service Catalog portfolio](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/catalogs_portfolios_users.html) so that it has the correct permissions\. Otherwise, you may receive an error message from AWS Service Catalog that is difficult to understand\. Common causes for this type of error are given in the Troubleshooting guide\. In particular, refer to the section entitled [No Launch Paths Found Error](troubleshooting.md#no-launch-paths-found)\.
+ Remember that only one account can be provisioned at a time\.

## Guidance on Subscribing to SNS Topics<a name="sns-guidance"></a>
+ The `aws-controltower-AllConfigNotifications` SNS topic receives all events published by AWS Config, including compliance notifications and AWS CloudWatch event notifications\. For example, this topic informs you if a guardrail violation has occurred\. It also gives information about other types of events\. \(Learn more from [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/notifications-for-AWS-Config.html) about what they publish when this topic is configured\.\) 
+ [Data Events](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html?icmpid=docs_cloudtrail_console#logging-data-events) from the `aws-controltower-BaselineCloudTrail` trail are set to publish to the `aws-controltower-AllConfigNotifications` SNS topic as well\.
+ To receive detailed compliance notifications, we recommend that you subscribe to the `aws-controltower-AllConfigNotification` SNS topic\. This topic aggregates compliance notifications from all child accounts\.
+ To receive drift notifications and other notifications as well as compliance notifications, but fewer notifications overall, we recommend that you subscribe to the `aws-controltower-AggregateSecurityNotifications` SNS topic\.

For more information about SNS topics and compliance, see [Drift prevention and notification](prevention-and-notification.md)\.

## Guidance for KMS keys<a name="kms-guidance"></a>

AWS Control Tower works with AWS Key Management Service \(KMS\)\. Optionally, if you wish to encrypt and decrypt your AWS Control Tower resources with an encryption key that you manage, you can generate and configure AWS KMS keys\. You can add or change a KMS key any time you update your landing zone\. As a best practice, we recommend using your own KMS keys and changing them from time to time\.

The AWS Key Management Service \(KMS\) allows you to create multi\-Region KMS keys and asymmetric keys; however, AWS Control Tower does not support multi\-Region keys or asymmetric keys\. AWS Control Tower performs a pre\-check of your existing keys\. You may see an error message if you select a multi\-Region key or an asymmetric key\. In that case, generate another key for use with AWS Control Tower resources\.

*For customers who operate an AWS CloudHSM cluster:* Create a custom key store associated with your CloudHSM cluster\. Then you can create a KMS key, which resides in the CloudHSM custom key store you created\. You can add this KMS key to AWS Control Tower\.

You must make a specific update to the permissions policy of a KMS key to make it work with AWS Control Tower\. For details, refer to the section called [To make the key's policy update](getting-started-with-control-tower.md#kms-key-policy-update)\.