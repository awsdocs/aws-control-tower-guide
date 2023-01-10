# About AWS accounts in AWS Control Tower<a name="accounts"></a>

An AWS account is the container for all your owned resources\. These resources include the AWS Identity and Access Management \(IAM\) identities accepted by the account, which determine who has access to that account\. IAM identities can include users, groups, roles, and more\. For more information about working with IAM, users, roles, and policies in AWS Control Tower, see [Identity and Access Management in AWS Control Tower](auth-access.md)\.

**Resources and account creation time**

When AWS Control Tower creates or enrolls an account, it deploys the minimum necessary resource configuration for the account, including resources in the form of [Account Factory templates](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory-considerations.html) and other resources in your landing zone\. These resources may include IAM roles, AWS CloudTrail trails, [AWS Service Catalog provisioned products](https://docs.aws.amazon.com/servicecatalog/latest/userguide/enduser-dashboard.html), and IAM Identity Center users\. AWS Control Tower also deploys resources, as required by the control configuration, for the OU in which the new account is destined to become a member account\.

AWS Control Tower orchestrates the deployment of these resources on your behalf\. It may require several minutes per resource to complete the deployment, so consider the total time before you create or enroll an account\. For more information about managing resources in your accounts, see [Guidance for creating and modifying AWS Control Tower resources](getting-started-guidance.md)\.

## View your accounts<a name="view-your-accounts"></a>

The **Organization** page lists all OUs and accounts in your organization, regardless of OU or enrollment status in AWS Control Tower\. You can view and enroll member accounts into AWS Control Tower — individually or by OU groups — if each account meets the prerequisites for enrollment\.

To view a specific account on the **Organization** page, you can choose **Accounts only** from the dropdown menu at the upper right, and then select the name of your account from the table\. Alternatively, you can select the name of the parent OU from the table, and you can view a list of all accounts within that OU on the **Details** page for that OU\.

On the **Organization** page and the **Account details** page, you can see the account's **State**, which is one of these:
+ **Not enrolled** – The account is a member of the parent OU, but it is not fully managed by AWS Control Tower\. If the parent OU is registered, the account is governed by the preventive controls configured for its registered parent OU, but the OU’s detective controls do not apply to this account\. If the parent OU is unregistered, no controls apply to this account\. 
+ **Enrolling** – Your account is being brought into governance by AWS Control Tower\. We are aligning the account with the control configuration for the parent OU\. This process may require several minutes per account resource\. 
+ **Enrolled** – The account is governed by the controls configured for its parent OU\. It is fully managed by AWS Control Tower\.
+ **Enrollment failed** – AWS Control Tower could not enroll the account\. For more information, see [Common causes for failure of enrollment](quick-account-provisioning.md#common-causes-for-enrollment-failure)\.
+ **Update available** – This account can be updated\. Accounts in this state are still **Enrolled**, but the account must be updated to reflect recent changes made to your environment\. To update a single account, navigate to the account detail page and select **Update account**\.

  If you have multiple accounts with this state under a single OU, you can choose to **Re\-register** the OU and update those accounts together\. 

## About the shared accounts<a name="special-accounts"></a>

Three special AWS accounts are associated with AWS Control Tower; the management account, the **audit** account, and the **log archive** account\. These accounts usually are referred to as *shared accounts*, or sometimes as *core accounts*\.
+ You can select customized names for the audit and log archive accounts when you're setting up your landing zone, but you cannot change the names after setup\. \(This is a one\-time selection\.\)
+ You also can specify an existing AWS account as an AWS Control Tower security or logging account, during the initial landing zone setup process\. This option eliminates the need for AWS Control Tower to create new, shared accounts\. \(This is a one\-time selection\.\)

For more information about the shared accounts and their associated resources, see [What Are the Shared Accounts?](how-control-tower-works.md#what-shared)

### Considerations for bringing existing security or logging accounts<a name="considerations-for-existing-shared-accounts"></a>

Before accepting an AWS account as a security or logging account, AWS Control Tower checks the account for resources that conflict with AWS Control Tower requirements\. For example, you may have a logging bucket with the same name that AWS Control Tower requires\. Also, AWS Control Tower validates that the account can provision resources; for example, by ensuring that AWS STS is enabled, that the account is not suspended, and that AWS Control Tower has permission to provision resources within the account\.

AWS Control Tower does not remove any existing resources in the logging and security accounts that you provide\. However, if you choose to enable the Region deny capability, the Region deny control prevents access to resources in denied Regions\.

### Management account<a name="mgmt-account"></a>

This AWS account launches AWS Control Tower\. By default, the root user for this account and the IAM administrator user for this account have full access to all resources within your landing zone\.

**Note**  
As a best practice, we recommend signing in as an IAM Identity Center user with **Administrator** privileges when performing administrative functions within the AWS Control Tower management console, instead of the signing in as the root or IAM administrator users for this account\.

For more information about the roles and resources available in the management account, see [What is the management account?](how-control-tower-works.md#what-is-mgmt)

### Log archive account<a name="log-archive-account"></a>

This shared account is set up automatically when you create your landing zone\. 

This account contains a central Amazon S3 bucket for storing a copy of all AWS CloudTrail and AWS Config log files for all other accounts in your landing zone\. As a best practice, we recommend restricting log archive account access to teams responsible for compliance and investigations, and their related security or audit tools\. This account can be used for automated security audits, or to host custom AWS Config Rules, such as Lambda functions, to perform remediation actions\.

For more information about the roles and resources available in the log archive account, see [What is the log archive account?](how-control-tower-works.md#what-is-log-archive)

**Note**  
These logs cannot be changed\. All logs are stored for the purposes of audit and compliance investigations related to account activity\.

### Audit account<a name="audit-account"></a>

This shared account is set up automatically when you create your landing zone\. 

The audit account should be restricted to security and compliance teams with auditor \(read\-only\) and administrator \(full\-access\) cross\-account roles to all accounts in the landing zone\. These roles are intended to be used by security and compliance teams to:
+ Perform audits through AWS mechanisms, such as hosting custom AWS Config rule Lambda functions\.
+ Perform automated security operations, such as remediation actions\.

The audit account also receives notifications through the Amazon Simple Notification Service \(Amazon SNS\) service\. Three categories of notification can be received:
+ **All Configuration Events** – This topic aggregates all CloudTrail and AWS Config notifications from all accounts in your landing zone\.
+ **Aggregate Security Notifications** – This topic aggregates all security notifications from specific CloudWatch events, AWS Config Rules compliance status change events, and GuardDuty findings\.
+ **Drift Notifications** – This topic aggregates all the drift warnings discovered across all accounts, users, OUs, and SCPs in your landing zone\. For more information on drift, see [Detect and resolve drift in AWS Control Tower](drift.md)\.

Audit notifications that are triggered within a member account also can send alerts to a local Amazon SNS topic\. This functionality allows account administrators to subscribe to audit notifications that are specific to an individual member account\. As a result, administrators can resolve issues that affect an individual account, while still aggregating all account notifications to your centralized audit account\. For more information, see [Amazon Simple Notification Service Developer Guide](https://docs.aws.amazon.com/sns/latest/dg/)\.

For more information about the roles and resources available in the audit account, see [What is the audit account?](how-control-tower-works.md#what-is-audit) Also see [Programmatic roles and trust relationships for the AWS Control Tower audit account](roles-how.md#stacksets-and-roles)\.

**Important**  
The email address you provided for the audit account receives **AWS Notification \- Subscription Confirmation** emails from every AWS Region supported by AWS Control Tower\. To receive compliance emails in your audit account, you must choose the **Confirm subscription** link within each email from each AWS Region supported by AWS Control Tower\. 

## About member accounts<a name="member-accounts"></a>

Member accounts are the accounts through which your users perform their AWS workloads\. These member accounts can be created in Account Factory, by IAM Identity Center users with **Admin** privileges in the AWS Service Catalog console, or by automated methods\. When created, these member accounts exist in an OU that was created in the AWS Control Tower console, or registered with AWS Control Tower\. For more information, see these related topics:
+ [Provision and manage accounts with Account Factory](account-factory.md)
+ [Automate tasks in AWS Control Tower](automating-tasks.md)
+ [AWS Organizations Terminology and Concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html) in the *AWS Organizations User Guide*\.

Also see [Provision accounts with AWS Control Tower Account Factory for Terraform](taf-account-provisioning.md)\.

**Accounts and controls**  
Member accounts can be *enrolled* in AWS Control Tower, or they can be *unenrolled*\. Controls apply differently to enrolled and unenrolled accounts, and controls may apply to accounts in nested OUs based on inheritance\.
