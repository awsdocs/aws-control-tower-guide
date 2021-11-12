# Detect and resolve drift in AWS Control Tower<a name="drift"></a>

Identifying and resolving drift is a regular operations task for AWS Control Tower management account administrators\. Resolving drift helps to ensure your compliance with governance requirements\.

When you create your landing zone, the landing zone and all the organizational units \(OUs\), accounts, and resources are compliant with the governance rules enforced by your chosen guardrails\. As you and your organization members use the landing zone, changes in this compliance status may occur\. Some changes may be accidental, and some may be made intentionally to respond to time\-sensitive operational events\.

Drift detection assists you in identifying resources that need changes or configuration updates to resolve the drift\. 

## Detecting drift<a name="detecting-drift"></a>

AWS Control Tower detects drift automatically\. To detect drift, the `AWSControlTowerAdmin` role requires persistent access to your management account so AWS Control Tower can make read\-only API calls to AWS Organizations\. These API calls show up as AWS CloudTrail events\.

Drift is surfaced in the Amazon Simple Notification Service \(Amazon SNS\) notifications that are aggregated in the audit account\. Notifications in each member account send alerts to a local Amazon SNS topic, and to a Lambda function\.

Member account administrators can \(and as a best practice, they should\) subscribe to the SNS drift notifications for specific accounts\. For example, the `aws-controltower-AggregateSecurityNotifications` SNS topic provides drift notifications\. The AWS Control Tower console indicates to management account administrators when drift has occurred\. For more information about SNS topics for drift detection and notification, see [Drift prevention and notification](prevention-and-notification.md)\.

**Drift notification de\-duplication**

If the same type of drift occurs on the same set of resources multiple times, AWS Control Tower sends an SNS notification only for the initial instance of drift\. If AWS Control Tower detects that this instance of drift has been remediated, it sends another notification only if drift re\-occurs for those identical resources\.

**Examples: Account drift and SCP drift are handled in the following manner**
+ If you modify the same managed SCP multiple times, you receive a notification for the first time you modify it\.
+ If you modify a managed SCP, then remediate drift, then modify it again, you'll receive two notifications\.

**Types of account drift**
+ Account moved between OUs
+ Account removed from organization

**Types of policy drift**
+ SCP updated
+ SCP attached to OU
+ SCP detached from OU
+ SCP attached to account

For more information, see [Types of Governance Drift](#governance-drift)\.

## Resolving drift<a name="resolving-drift"></a>

Although detection is automatic, the steps to resolve drift must be done through the console\.
+ Many types of drift can be resolved through the **Landing zone settings** page\. You can choose the **Repair** button in the **Versions** section to repair these types of drift\.
+ You also can repair drift by selecting **Re\-register OU** on the **OU** page, to repair Account Factory provisioned account drift or SCP drift\.

**Note**  
When you repair your landing zone, the landing zone is upgraded to the latest landing zone version\.

## Considerations about drift and SCP scans<a name="scp-invariance-scans"></a>

 AWS Control Tower scans your managed SCPs daily to verify that the corresponding guardrails are applied correctly and that they have not drifted\. To retrieve the SCPs and run checks on them, AWS Control Tower calls AWS Organizations on your behalf, using a role in your management account\.

If an AWS Control Tower scan discovers drift, you'll receive a notification\. AWS Control Tower sends only one notification per drift issue, so if your landing zone already is in a state of drift, you won't receive additional notifications unless a new drift item is found\.

AWS Organizations limits how often each of its APIs can be called\. This limit is expressed in transactions per second \(TPS\), and known as the *TPS limit*, *throttling rate*, or *API request rate*\. When AWS Control Tower audits your SCPs by calling AWS Organizations, the API calls that AWS Control Tower makes are counted towards your TPS limit, because AWS Control Tower uses the management account to make the calls\.

In rare situations, this limit can be reached when you call the same APIs repeatedly, whether through a third\-party solution or a custom script you wrote\. For example, if you and AWS Control Tower call the same AWS Organizations APIs at the same moment in time \(within 1 second\), and the TPS limits are reached, subsequent calls are throttled\. That is, these calls return an error such as `Rate exceeded`\.

**If an API request rate is exceeded**
+ If AWS Control Tower hits the limit and is throttled, we pause the execution of the audit and resume it at a later time\.
+ If your workload hits the limit and is throttled, the result can range from slight latency all the way to a fatal error in the workload, depending on how the workload is configured\. This edge case is something to be aware of\.

**A daily SCP scan consists of**

1. Retrieving all of your OUs\.

1. For each registered OU, retrieving all SCPs managed by AWS Control Tower that are attached to the OU\. Managed SCPs have identifiers that begin with `aws-guardrails`\.

1. For each preventive guardrail enabled on the OU, verifying that the guardrail's policy statement is present in the OU's managed SCPs\.

The daily scans consume the TPS for the following AWS Organizations APIs:


|  |  |  | 
| --- |--- |--- |
| `listOrganizationalUnits` | 8 burst, 5 sustained | 1 per OU | 
| `listPoliciesForTarget` | 8 burst, 5 sustained | 1 per registered OU | 
| `describePolicy` | 2 TPS | 1 per managed SCP | 

An OU may have one or more managed SCPs\.

## Types of drift to repair right away<a name="types-of-drift"></a>

Most types of drift can be resolved by administrators\. A few types of drift must be repaired immediately, including deletion of an organizational unit that the AWS Control Tower landing zone requires\. Here are some examples of major drift that you may wish to avoid:
+ *Don't delete the Security OU:* The organizational unit originally named **Security** during landing zone setup by AWS Control Tower should not be deleted\. If you delete it, you'll see an error message instructing you to repair the landing zone immediately\. You won't be able to take any other actions in AWS Control Tower until the repair is complete\.
+ *Don't delete required roles:* AWS Control Tower checks certain AWS Identity and Access Management \(IAM\) roles when you log into the console for *IAM role drift*\. If these roles are missing or inaccessible, you'll see an error page instructing you to repair your landing zone\. These roles are `AWSControlTowerAdmin` `AWSControlTowerCloudTrailRole` `AWSControlTowerStackSetRole`\.
+ *Don't delete all Additional OUs:* If you delete the organizational unit originally named **Sandbox** during landing zone setup by AWS Control Tower, your landing zone will be in a state of drift, but you still can use AWS Control Tower\. At least one Additional OU is required for AWS Control Tower to operate, but it doesn’t have to be the **Sandbox** OU\.
+ *Don't remove shared accounts:* If you remove shared accounts from Foundational OUs, such as removing the logging account from the Security OU, your landing zone will be in a state of drift and must be repaired before you can continue using the AWS Control Tower console\.

## Repairable changes to resources<a name="repairable-changes-to-resources"></a>

Here's a list of changes to AWS Control Tower resources that are permitted, although they create repairable drift\. Results of these permitted operations are viewable in the AWS Control Tower console, although a refresh may be required\.

For more information about how to resolve the resulting drift, see [Managing Resources Outside of AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/external-resources.html)\.

**Changes Permitted Outside the AWS Control Tower Console**
+ Change the name of a registered OU\.
+ Change the name of the Security OU\.
+ Change the name of member accounts in non\-Foundational OUs\.
+ Change the name of AWS Control Tower shared accounts in the Security OU\.
+ Delete a non\-Foundational OU\.
+ Delete an enrolled account from a non\-Foundational OU\.
+ Change the email address of a shared account in the Security OU\.
+ Change the email address of a member account in a registered OU\.

**Note**  
Moving accounts between OUs is considered drift, and it must be repaired\.

## Drift and New Account Provisioning<a name="drift-and-accounts"></a>

If your landing zone is in a state of drift, the **Enroll account** feature in AWS Control Tower will not work\. In that case, you must provision new accounts through AWS Service Catalog\. For instructions, see [Provisioning Account Factory Accounts With AWS Service Catalog](account-factory.md#provision-as-end-user)\.

In particular, if you've made certain changes to your accounts by means of AWS Service Catalog, such as changing the name of your portfolio, the **Enroll account** feature will not work\.

## Types of Governance Drift<a name="governance-drift"></a>

Governance drift, also called *organizational drift* occurs when OUs, SCPs, and member accounts are changed or updated\. The types of governance drift that can be detected in AWS Control Tower are as follows:
+ [Moved Member Account](#drift-account-moved)
+ [Removed Member Account](#drift-account-removed)
+ [Unplanned Update to Managed SCP](#drift-scp-update)
+ [SCP Attached to Member Account](#drift-scp-attached-account)
+ [SCP Attached to Managed OU](#drift-scp-attached-ou)
+ [SCP Detached from Managed OU](#drift-scp-detached-ou)
+ [Deleted Foundational OU](#drift-ou-deleted)

Another type of drift is *landing zone drift*, which may be found through the management account\. Landing zone drift consists of IAM role drift, or any type of organizational drift that specifically affects Foundational OUs and shared accounts\.

AWS Control Tower does not look for drift regarding other services that work with the management account, including CloudTrail, CloudWatch, AWS SSO, AWS CloudFormation, AWS Config, and so forth\. No drift detection is available in child accounts, because these accounts are protected by preventive mandatory guardrails\.

## Moved Member Account<a name="drift-account-moved"></a>

This type of drift occurs on the account rather than the OU\. This type of drift can occur when an AWS Control Tower member account, the audit account, or the log archive account is moved from a registered AWS Control Tower OU to any other OU\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that your member account 'account-email@amazon.com (012345678909)' has been moved from organizational unit 'Sandbox (ou-0123-eEXAMPLE)' to 'Security (ou-3210-1EXAMPLE)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/move-account'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "AccountMovedBetweenOrganizationalUnits",
  "RemediationStep" : "Re-register this organizational unit (OU), or if the OU has more than 300 accounts, you must update the provisioned product in Account Factory.",
  "AccountId" : "012345678909",
  "SourceId" : "012345678909",
  "DestinationId" : "ou-3210-1EXAMPLE"
}
```

### Resolutions<a name="drift-account-moved-resolution"></a>

When this type of drift occurs for an Account Factory provisioned account in an OU with up to 300 accounts, you can resolve it by:
+ Navigating to the OU in the AWS Control Tower console to re\-register the OU \(fastest option\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\. 
+ Updating the provisioned product in Account Factory\. For more information, see [Updating and Moving Account Factory Accounts with AWS Service Catalog](account-factory.md#updating-account-factory-accounts)\.
+ Updating your landing zone \(slower option\)\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.
**Note**  
If you have several individual accounts to update, also see this method for making updates with a script: [Provisioning and updating accounts using script automation](configuration-updates.md#update-accounts-by-script)\.
+ When this type of drift occurs in an OU with more than 300 accounts, the drift resolution may depend on which type of account has been moved, as explained in the next paragraphs\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.
  + **If an Account Factory provisioned account is moved** – In an OU with fewer than 300 accounts, you can resolve the account drift by updating the provisioned product in Account Factory, by re\-registering the OU, or by updating your landing zone\. 

    In an OU with more than 300 accounts, you *must* resolve the drift by making an update to the provisioned product for each moved account, because re\-register OU will not perform the update\. For more information, see [Updating and Moving Account Factory Accounts with AWS Service Catalog](account-factory.md#updating-account-factory-accounts)\.
  + **If a shared account is moved** – You can resolve the drift from moving the audit or log archive account by updating your landing zone\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

**Deprecated field name**  
The field name `MasterAccountID` has been changed to `ManagementAccountID` to comply with AWS guidelines\. The old name is **deprecated**\. Beginning in 2022, scripts that contain the deprecated field name will no longer work\.

## Added Member Account<a name="drift-account-added"></a>

Adding an account is not technically drift\. However, AWS Control Tower alerts you when an AWS Control Tower account is added to your AWS Control Tower organization\. For example, an account may be added to your AWS Control Tower organization as part of the drift remediation process if a shared account, such as the audit account or log archive account, has been removed and must be replaced\. The following example shows an Amazon SNS notification you may receive when this type of event is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the account 'account-email@amazon.com (012345678909)' has been added to organization o-123EXAMPLE. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/add-account'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "AccountAddedToOrganization",
  "RemediationStep" : "Update Account Factory Provisioned Product",
  "AccountId" : "012345678909"
}
```

### Resolution<a name="drift-account-added-resolution"></a>

No resolution is required, because adding a member account to an OU or enrolling an Account Factory account does not cause drift\. If a shared account has been removed and re\-added, it is a special case, and you may need to update that shared account or the Security OU\. For information about updating Account Factory accounts, see [Updating and Moving Account Factory Accounts with AWS Service Catalog](account-factory.md#updating-account-factory-accounts)\.

## Removed Member Account<a name="drift-account-removed"></a>

This type of drift can occur when a member account is removed from a registered AWS Control Tower organizational unit\. The following example shows the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the member account 012345678909 has been removed from organization o-123EXAMPLE. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/remove-account'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "AccountRemovedFromOrganization",
  "RemediationStep" : "Add account to Organization and update Account Factory provisioned product",
  "AccountId" : "012345678909"
}
```

### Resolution<a name="drift-account-removed-resolution"></a>
+ When this type of drift occurs in a member account, you can resolve the drift by updating the account in Account Factory\. For example, you can add the account to another registered OU from the Account Factory update wizard\. For more information, see [Updating and Moving Account Factory Accounts with AWS Service Catalog](account-factory.md#updating-account-factory-accounts)\.
+ If a shared account is removed from a Foundational OU, you must resolve the drift by repairing your landing zone\. Until this drift is resolved, you will not be able to use the AWS Control Tower console\.
+ For more information about resolving drift for accounts and OUs, see [Manage resources outside of AWS Control Tower](external-resources.md)\. 

**Note**  
In AWS Service Catalog, the Account Factory provisioned product that represents the account is not updated to remove the account\. Instead, the provisioned product is displayed as `TAINTED` and in an error state\. To clean up, go to the AWS Service Catalog, choose the provisioned product, and then choose **Terminate**\.

## Unplanned Update to Managed SCP<a name="drift-scp-update"></a>

This type of drift can occur when an SCP for a guardrail is updated in the AWS Organizations console or programmatically using the AWS CLI or one of the AWS SDKs\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)', attached to the registered organizational unit 'Security (ou-0123-1EXAMPLE)', has been modified. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/update-scp'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "ServiceControlPolicyUpdated",
  "RemediationStep" : "Update Control Tower Setup",
  "OrganizationalUnitId" : "ou-0123-1EXAMPLE",
  "PolicyId" : "p-tEXAMPLE"
}
```

### Resolution<a name="drift-scp-update-resolution"></a>

When this type of drift occurs in an OU with up to 300 accounts, you can resolve it by:
+ Navigating to the OU in the AWS Control Tower console to re\-register the OU \(fastest option\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\. 
+ Updating your landing zone \(slower option\)\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

When this type of drift occurs in an OU with more than 300 accounts, resolve it by updating your landing zone\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

## SCP Attached to Managed OU<a name="drift-scp-attached-ou"></a>

This type of drift can occur when an SCP for a guardrail is attached to an OU outside of the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)' has been attached to the registered organizational unit 'Sandbox (ou-0123-1EXAMPLE)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/scp-detached-ou'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "ServiceControlPolicyAttachedToOrganizationalUnit",
  "RemediationStep" : "Update Control Tower Setup",
  "OrganizationalUnitId" : "ou-0123-1EXAMPLE",
  "PolicyId" : "p-tEXAMPLE"
}
```

### Resolution<a name="drift-scp-attached-ou-resolution"></a>

When this type of drift occurs in an OU with up to 300 accounts, you can resolve it by:
+ Navigating to the OU in the AWS Control Tower console to re\-register the OU \(fastest option\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\. 
+ Updating your landing zone \(slower option\)\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

When this type of drift occurs in an OU with more than 300 accounts, resolve it by updating your landing zone\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

## SCP Detached from Managed OU<a name="drift-scp-detached-ou"></a>

This type of drift can occur when an SCP for a guardrail has been detached from an OU outside of the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)' has been detached from the registered organizational unit 'Sandbox (ou-0123-1EXAMPLE)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/scp-detached'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "ServiceControlPolicyDetachedFromOrganizationalUnit",
  "RemediationStep" : "Update Control Tower Setup",
  "OrganizationalUnitId" : "ou-0123-1EXAMPLE",
  "PolicyId" : "p-tEXAMPLE"
}
```

### Resolution<a name="drift-scp-detached-ou-resolution"></a>

When this type of drift occurs in an OU with up to 300 accounts, you can resolve it by:
+ Navigating to the OU in the AWS Control Tower console to re\-register the OU \(fastest option\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\. 
+ Updating your landing zone \(slower option\)\. If the drift is affecting a mandatory guardrail, the update process creates a new service control policy \(SCP\) and attaches it to the OU to repair the drift\. For more information about how to update your landing zone, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

When this type of drift occurs in an OU with more than 300 accounts, resolve it by updating your landing zone\. If the drift is affecting a mandatory guardrail, the update process creates a new service control policy \(SCP\) and attaches it to the OU to repair the drift\. For more information about how to update your landing zone, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

## SCP Attached to Member Account<a name="drift-scp-attached-account"></a>

This type of drift can occur when an SCP for a guardrail is attached to an account in the Organizations console\. Guardrails and their SCPs can be enabled on OUs \(and thus applied to all of an OU's enrolled accounts\) through the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)' has been attached to the member account 'account-email@amazon.com (012345678909)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/scp-detached-account'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "ServiceControlPolicyAttachedToAccount",
  "RemediationStep" : "Update Control Tower Setup",
  "AccountId" : "012345678909",
  "PolicyId" : "p-tEXAMPLE"
}
```

### Resolution<a name="drift-scp-attached-account-resolution"></a>

This type of drift occurs on the account rather than the OU\. When this type of drift occurs in a Foundational OU, such as the Security OU, the resolution is to update your landing zone\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

When this type of drift occurs in a non\-Foundational OU with up to 300 accounts, you can resolve it by:
+ Navigating to the OU in the AWS Control Tower console to re\-register the OU \(fastest option\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\.

When this type of drift occurs in an OU with more than 300 accounts, it may not be possible to resolve it successfully\. You may attempt to resolve it by updating your landing zone\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

## Deleted Foundational OU<a name="drift-ou-deleted"></a>

This type of drift applies only to AWS Control Tower Foundational OUs, such as the Security OU\. It can occur if a Foundational OU is deleted outside of the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the registered organizational unit 'Security (ou-0123-1EXAMPLE)' has been deleted. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/delete-ou'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "OrganizationalUnitDeleted",
  "RemediationStep" : "Delete organizational unit in Control Tower",
  "OrganizationalUnitId" : "ou-0123-1EXAMPLE"
}
```

### Resolution<a name="drift-ou-deleted-resolution"></a>

Because this drift occurs for Foundational OUs only, the resolution is to update the landing zone\. When other types of OUs are deleted, AWS Control Tower is updated automatically\.

For more information about resolving drift for accounts and OUs, see [Manage resources outside of AWS Control Tower](external-resources.md)\.