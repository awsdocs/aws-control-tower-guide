# Getting started with AWS Control Tower<a name="getting-started-with-control-tower"></a>

This getting started procedure is for AWS Control Tower central cloud administrators\. Use this procedure when you're ready to set up your landing zone\. From start to finish, it should take about half an hour\. This procedure has a prerequisite and four steps\.

## Prerequisite: Automated pre\-launch checks for your management account<a name="getting-started-prereqs"></a>

Before AWS Control Tower sets up the landing zone, it automatically runs a series of pre\-launch checks in your account\. There's no action required on your part for these checks, which ensure that your management account is ready for the changes that establish your landing zone\. Here are the checks that AWS Control Tower runs before setting up a landing zone:
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

### Considerations for AWS Single Sign\-On \(AWS SSO\) customers<a name="sso-considerations"></a>
+ If AWS Single Sign\-On \(AWS SSO\) is already set up, the AWS Control Tower home Region must be the same as the AWS SSO Region\.
+ AWS SSO can be installed only in the management account of an organization\.
+ Three options apply to your SSO directory, based on the identity source you choose in SSO:
  + **AWS SSO User Store**: If SSO for AWS Control Tower is set up with AWS SSO, AWS Control Tower creates groups in the SSO directory and provisions access to these groups, for the user you select, for member accounts\.
  + **Active Directory**: If SSO for AWS Control Tower is set up with Active Directory, AWS Control Tower does not manage the SSO directory\. It does not assign users or groups to new AWS accounts\.
  + **External Identity Provider**: If AWS SSO for AWS Control Tower is set up with an external identity provider \(IdP\), AWS Control Tower creates groups in the SSO directory and provisions access to these groups for the user you select for member accounts\. You can specify an existing user from your external IdP in Account Factory during account creation, and AWS Control Tower gives this user access to the newly vended account when it synchronizes users of the same name between SSO and the external IdP\. You can also create groups in your external IdP to match the names of the default groups in AWS Control Tower\. When you assign users to these groups, these users will have access to your enrolled accounts\.

   For more information about working with AWS SSO and AWS Control Tower see [Things to Know About SSO Accounts and AWS Control Tower](sso.md#sso-good-to-know)

### Considerations for AWS Config and AWS CloudTrail customers<a name="config-and-cloudtrail-considerations"></a>
+ The AWS account cannot have trusted access enabled in the organization management account for either AWS Config or AWS CloudTrail\. For information about how to disable trusted access, see [the AWS Organizations documentation on how to enable or disable trusted access](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_integrate_services.html#orgs_how-to-enable-disable-trusted-access)\.
+ If you have an existing AWS Config recorder, delivery channel, or aggregation setup in any existing accounts that you plan to enroll in AWS Control Tower, you must modify or remove these configurations before you start enrolling the accounts, after your landing zone is set up\. This pre\-check doesn't apply to the AWS Control Tower management account during landing zone launch\. For more information, see [Enroll accounts that have existing AWS Config resources](existing-config-resources.md)\.
+ If you are running ephemeral workloads from accounts in AWS Control Tower, you may see an increase in costs associated with AWS Config\. Contact your AWS account representative for more specific information about managing these costs\.
+ When you enroll an account into AWS Control Tower, your account is governed by the AWS CloudTrail trail for the AWS Control Tower organization\. If you have an existing deployment of a CloudTrail trail in the account, you may see duplicate charges unless you delete the existing trail for the account before you enroll it in AWS Control Tower\. For information about organization\-level trails and AWS Control Tower, see [Pricing](pricing.md)\.

**Note**  
When launching, AWS Security Token Service \(STS\) endpoints must be activated in the management account, for all Regions supported by AWS Control Tower\. Otherwise, the launch may fail midway through the configuration process\.

## Requirements for your shared account email addresses<a name="step-one"></a>

If you're setting up your landing zone in a new AWS account, for information on creating your account and your IAM administrator, see [Setting up](setting-up.md)\.
+ To set up your landing zone with *new* shared accounts, AWS Control Tower requires two unique email addresses that aren't already associated with an AWS account\. Each of these email addresses will serve as a collaborative inbox \-\- a shared email account \-\- intended for the various users in your enterprise that will do specific work related to AWS Control Tower\.
+ If you are setting up AWS Control Tower for the first time, and if you are bringing existing security and log archive accounts into AWS Control Tower, you can enter the current email addresses of the existing AWS accounts\.

The email addresses are required for:
+ **Audit account** – This account is for your team of users that need access to the audit information made available by AWS Control Tower\. You can also use this account as the access point for third\-party tools that will perform programmatic auditing of your environment to help you audit for compliance purposes\.
+ **Log archive account** – This account is for your team of users that need access to all the logging information for all of your enrolled accounts within registered OUs in your landing zone\.

These accounts are set up in the **Security** OU when you create your landing zone\. As a best practice, we recommend that when you perform actions in these accounts, you should use an AWS SSO user with the appropriately scoped permissions\.

**Note**  
If you specify existing AWS accounts as your **audit** and **log archive** accounts, the existing accounts must pass some pre\-launch checks to ensure that no resources are in conflict with AWS Control Tower requirements\. If these checks are not successful, your landing zone setup may not succeed\. For more information, see [Considerations for bringing existing security or logging accounts](accounts.md#considerations-for-existing-shared-accounts)\.

For the sake of clarity, this *User Guide* always refers to the shared accounts by their default names: **log archive** and **audit**\. As you read this document, remember to substitute the customized names you give to these accounts initially, if you choose to customize them\. You can view your accounts with their customized names on the **Account details** page\.

**Note**  
We are changing our terminology regarding the default names of some AWS Control Tower organizational units \(OUs\) to align with the AWS multi\-account strategy\. You may notice some inconsistencies while we are making a transition to improve the clarity of these names\. The Security OU was formerly called the Core OU\. The Sandbox OU was formerly called the Custom OU\.

## Expectations for landing zone configuration<a name="getting-started-configure"></a>

The process of setting up your AWS Control Tower landing zone has multiple steps\. Certain aspects of your AWS Control Tower landing zone are configurable\. Other choices are "one\-way doors" that cannot be changed after setup\.

**Key items to configure during setup**
+ You can select your top\-level OU names during setup, and you also can change OU names after you've set up your landing zone\. By default, the top\-level OUs are named **Security** and **Sandbox**\. For more information, see [Guidelines to set up a well\-architected environment](aws-multi-account-landing-zone.md#guidelines-for-multi-account-setup)\. 
+ During setup, you can select customized names for the shared accounts that AWS Control Tower creates, called **log archive** and **audit** by default, but you cannot change these names after setup\. \(This is a one\-time selection\.\)
+ During setup, you can optionally specify existing AWS accounts for AWS Control Tower to use as security and logging accounts\. \(This is a one\-time selection\.\)

**Configuration choices that cannot be undone**
+ You cannot change your home Region after you've set up your landing zone\.
+ If you're provisioning Account Factory accounts with VPCs, VPC CIDRs can't be changed after they are created\.

## Configure and launch your landing zone<a name="step-two"></a>

Before you launch your AWS Control Tower landing zone, determine the most appropriate home Region\. For more information, see [Administrative tips for landing zone setup](tips-for-admin-setup.md)\.

**Important**  
Changing your home Region after you have deployed your AWS Control Tower landing zone requires decommissioning as well as the assistance of AWS Support\. This practice is not recommended\.

AWS Control Tower has no APIs or programmatic access\. To configure and launch your landing zone, perform the following series of steps\.

**Prepare: Navigate to the AWS Control Tower console**

1. Open a web browser, and navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. In the console, verify that you are working in your desired home Region for AWS Control Tower\. Then choose **Set up your landing zone**\.

### Step 1\. Review pricing and select your AWS Regions<a name="pricing-and-regions"></a>

Be sure you've correctly designated the AWS Region that you select for your home Region\. After you've deployed AWS Control Tower, you can't change the home Region\.

In this section of the setup process, you can add any additional AWS Regions that you require\. You can add more Regions at a later time, if needed, and you can remove Regions from governance\. 

**To select additional AWS Regions to govern**

1. The panel shows you the current Region selections\. Open the dropdown menu to see a list of additional Regions available for governance\.

1. Check the box next to each Region to bring into governance by AWS Control Tower\. Your home Region selection is not editable\.

### Step 2\. Configure your organizational units \(OUs\)<a name="configure-ous"></a>

If you accept the default names of these OUs, there's no action you need to take for setup to continue\. To change the names of the OUs, enter the new names directly in the form field\.
+ **Foundational OU** – AWS Control Tower relies upon a **Foundational OU** that is initially named the **Security OU**\. You can change the name of this OU during initial setup and afterward, from the OU details page\. This **Security OU** contains your two shared accounts, which by default are called the **log archive** account and the **audit** account\.
+ **Additional OU** – AWS Control Tower can set up one or more **Additional OUs** for you\. We recommend that you provision at least one **Additional OU** in your landing zone, besides the **Security OU**\. If this Additional OU is intended for development projects, we recommend that you name it the **Sandbox OU**, as given in the [Guidelines to set up a well\-architected environment](aws-multi-account-landing-zone.md#guidelines-for-multi-account-setup)\. If you already have an existing OU in AWS Organizations, you may see the option to skip setting up an Additional OU in AWS Control Tower\. 

### Step 3\. Configure your shared accounts and encryption<a name="configure-shared-accounts"></a>

In this section of the setup process, the panel shows the default selections for the names of your shared AWS Control Tower accounts\. These accounts are an essential part of your landing zone\. **Do not move or delete these shared accounts**\. You can choose customized names for the **audit** and **log archive** accounts during setup\. Alternatively, you have a one\-time option to specify existing AWS accounts as your shared accounts\.

You must provide unique email addresses for your log archive and audit accounts, and you can verify the email address that you previously provided for your management account\. Choose the **Edit** button to change the editable default values\.

**About the shared acccounts**
+ **The management account** – The AWS Control Tower management account is part of the Root level\. The management account allows for AWS Control Tower billing\. The account also has administrator permissions for your landing zone\. You cannot create separate accounts for billing and for administrator permissions in AWS Control Tower\.

  The email address shown for the management account is not editable during this phase of setup\. It is shown as a confirmation, so you can check that you're editing the correct management account, in case you have multiple accounts\.
+  **The two shared accounts** – You can choose customized names for these two accounts, and you must supply a unique email address for each account, either new or existing\. If you choose to have AWS Control Tower create new shared accounts for you, the email addresses must not already have associated AWS accounts\.

**To configure the shared accounts, fill in the requested information\.**

1. At the console, enter a name for the account initially called the **log archive** account\. Many customers decide to keep the default name for this account\.

1. Provide a unique email address for this account\.

1. Enter a name for the account initially called the **audit** account\. Many customers choose to call it the **Security** account\.

1. Provide a unique email address for this account\.

#### Optionally configure AWS KMS keys<a name="configure-kms-keys"></a>

If you wish to encrypt and decrypt your resources with an AWS KMS encryption key, select the checkbox\. If you have existing keys, you'll be able to select them from identifiers displayed in a dropdown menu\. You can generate a new key by choosing **Create a key**\. You can add or change a KMS key any time you update your landing zone\.

When you select **Set up landing zone**, AWS Control Tower performs a pre\-check to validate your KMS key\. The key must meet these requirements:
+ Enabled
+ Symmetric
+ Not a multi\-Region key
+ Has correct permissions added to the policy
+ Key is in the management account

You may see an error banner if the key does not meet these requirements\. In that case, choose another key or generate a key\. Be sure to edit the key's permissions policy, as described in the next section\.

##### To make the key's policy update<a name="kms-key-policy-update"></a>

To use a KMS key with AWS Control Tower, you must make a specific policy update to the key\. At minimum, the KMS key must have permissions that allow AWS CloudTrail and AWS Config to use the chosen KMS key\.

**Make the required policy update**

1. Navigate to the AWS KMS console at [https://console.aws.amazon.com/kms](https://console.aws.amazon.com/kms)

1. Select **Customer managed keys** on the left

1. In the table, select the key you wish to edit, or select **Create a key** from the upper right

1. Under the section called **Key policy**, make sure you can see the policy and edit it\. You may need to select **Switch to policy view** on the right\.

You can copy and paste the following example policy statement\. Alternatively, for an existing key, you can ensure that your KMS key has these minimum permissions by adding them to your own existing policy\. You can add these lines as a group in a single JSON statement, or if you prefer, you can incorporate them line by line into your policy's other statements\.

```
{
  "Sid": "Allow CloudTrail and AWS Config to encrypt/decrypt logs",
  "Effect": "Allow",
  "Principal": {
    "Service": [
      "cloudtrail.amazonaws.com",
      "config.amazonaws.com"
   ]
  },
   "Action": [
      "kms:GenerateDataKey",
      "kms:Decrypt"
   ],
    "Resource": "*"
}
```

The AWS Key Management Service \(KMS\) allows you to create multi\-Region KMS keys and asymmetric keys; however, AWS Control Tower does not support multi\-Region keys or asymmetric keys\. AWS Control Tower performs a pre\-check of your existing keys\. You may see an error message if you select a multi\-Region key or an asymmetric key\. In that case, generate another key for use with AWS Control Tower resources\.

For more information about AWS KMS, see [ the AWS KMS Developer Guide\.](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)

Note that customer data in AWS Control Tower is encrypted at rest, by default, using SSE\-S3\.

### Step 4\. Review and set up the landing zone<a name="review-and-set-up"></a>

The next section in the setup shows you the permissions that AWS Control Tower requires for your landing zone\. Choose a checkbox to expand each topic\. You'll be asked to agree to these permissions, which may affect multiple accounts, and to agree to the overall **Terms of Service**\.

**To finalize**

1. At the console, review the **Service permissions**, and when you're ready, choose **I understand the permissions AWS Control Tower will use to administer AWS resources and enforce rules on my behalf**\.

1. To finalize your selections and initialize launch, choose **Set up landing zone**\.

This series of steps starts the process of setting up your landing zone, which can take about thirty minutes to complete\. During setup, AWS Control Tower creates your Root level, the Security OU, and the shared accounts\. Other AWS resources are created, modified, or deleted\.

**Confirm SNS subscriptions**  
The email address you provided for the audit account will receive **AWS Notification – Subscription Confirmation** emails from every AWS Region supported by AWS Control Tower\. To receive compliance emails in your audit account, you must choose the **Confirm subscription** link within each email from each AWS Region supported by AWS Control Tower\. 

## Next steps<a name="getting-started-next"></a>

Now that your landing zone is set up, it's ready for use\.

To learn more about how you can use AWS Control Tower, see the following topics:
+ For recommended administrative practices, see [Best Practices](https://docs.aws.amazon.com/controltower/latest/userguide/best-practices.html)\.
+ You can set up AWS SSO users and groups with specific roles and permissions\. For recommendations, see [Recommendations for setting up groups, roles, and policies](roles-recommendations.md)\.
+ To begin enrolling organizations and accounts from your AWS Organizations deployments, see [Govern existing organizations and accounts](https://docs.aws.amazon.com/controltower/latest/userguide/importing-existing.html)\.
+ Your end users can provision their own AWS accounts in your landing zone using Account Factory\. For more information, see [Permissions for Configuring and Provisioning Accounts](account-factory.md#configure-provision-new-account)\.
+ To assure [Compliance Validation for AWS Control Tower](compliance-program-info.md), your central cloud administrators can review log archives in the Log Archive account, and designated third\-party auditors can review audit information in the Audit \(shared\) account, which is a member of the Security OU\. 
+ To learn more about the capabilities of AWS Control Tower, see [Related information](https://docs.aws.amazon.com/controltower/latest/userguide/related-information.html)\.
+ Try visiting a [curated list of YouTube videos](https://www.youtube.com/playlist?list=PLhr1KZpdzukdS9skEXbY0z67F-wrcpbjm) that explain more about how to use AWS Control Tower functionality\. 
+ From time to time, you may need to update your landing zone to get the latest backend updates, the latest guardrails, and to keep your landing zone up\-to\-date\. For more information, see [Configuration update management in AWS Control Tower](configuration-updates.md)\.
+ If you encounter issues while using AWS Control Tower, see [Troubleshooting](troubleshooting.md)\.