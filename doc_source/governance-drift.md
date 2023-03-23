# Types of Governance Drift<a name="governance-drift"></a>

Governance drift, also called *organizational drift* occurs when OUs, SCPs, and member accounts are changed or updated\. The types of governance drift that can be detected in AWS Control Tower are as follows:
+ [Moved Member Account](#drift-account-moved)
+ [Removed Member Account](#drift-account-removed)
+ [Unplanned Update to Managed SCP](#drift-scp-update)
+ [SCP Attached to Member Account](#drift-scp-attached-account)
+ [SCP Attached to Managed OU](#drift-scp-attached-ou)
+ [SCP Detached from Managed OU](#drift-scp-detached-ou)
+ [Deleted Foundational OU](#drift-ou-deleted)

Another type of drift is *landing zone drift*, which may be found through the management account\. Landing zone drift consists of IAM role drift, or any type of organizational drift that specifically affects Foundational OUs and shared accounts\.

A special case of landing zone drift is *role drift*, which is detected when a required role is not available\. If this type of drift occurs, the console displays a warning page and some instructions on how to restore the role\. Your landing zone is unavailable until the role drift is repaired\. For more information about drift, see *Don't delete required roles* in the section called [Types of drift to repair right away](drift.md#types-of-drift)\.

AWS Control Tower does not look for drift regarding other services that work with the management account, including CloudTrail, CloudWatch, IAM Identity Center, AWS CloudFormation, AWS Config, and so forth\. No drift detection is available in child accounts, because these accounts are protected by preventive mandatory controls\.

## Moved Member Account<a name="drift-account-moved"></a>

This type of drift occurs on the account rather than the OU\. This type of drift can occur when an AWS Control Tower member account, the audit account, or the log archive account is moved from a registered AWS Control Tower OU to any other OU\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that your member account 'account-email@amazon.com (012345678909)' has been moved from organizational unit 'Sandbox (ou-0123-eEXAMPLE)' to 'Security (ou-3210-1EXAMPLE)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/move-account'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "ACCOUNT_MOVED_BETWEEN_OUS",
  "RemediationStep" : "Re-register this organizational unit (OU), or if the OU has more than 300 accounts, you must update the provisioned product in Account Factory.",
  "AccountId" : "012345678909",
  "SourceId" : "012345678909",
  "DestinationId" : "ou-3210-1EXAMPLE"
}
```

### Resolutions<a name="drift-account-moved-resolution"></a>

When this type of drift occurs for an Account Factory provisioned account in an OU with up to 300 accounts, you can resolve it by:
+ Navigating to the **Organization** page in the AWS Control Tower console, selecting the account, and choosing **Update account** at the upper right \(fastest option for individual accounts\)\.
+ Navigating to the **Organization** page in the AWS Control Tower console, then choosing **Re\-register** for the OU that contains the account \(fastest option for multiple accounts\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\. 
+ Updating the provisioned product in Account Factory\. For more information, see [Update and move account factory accounts with AWS Control Tower or with AWS Service Catalog](updating-account-factory-accounts.md)\.
**Note**  
If you have several individual accounts to update, also see this method for making updates with a script: [Provision and update accounts using automation](update-accounts-by-script.md)\.
+ When this type of drift occurs in an OU with more than 300 accounts, the drift resolution may depend on which type of account has been moved, as explained in the next paragraphs\. For more information, see [Update Your Landing Zone](update-controltower.md)\.
  + **If an Account Factory provisioned account is moved** – In an OU with fewer than 300 accounts, you can resolve the account drift by updating the provisioned product in Account Factory, by re\-registering the OU, or by updating your landing zone\. 

    In an OU with more than 300 accounts, you *must* resolve the drift by making an update to each moved account, either through the AWS Control Tower console or the provisioned product because re\-register OU will not perform the update\. For more information, see [Update and move account factory accounts with AWS Control Tower or with AWS Service Catalog](updating-account-factory-accounts.md)\.
  + **If a shared account is moved** – You can resolve the drift from moving the audit or log archive account by updating your landing zone\. For more information, see [Update Your Landing Zone](update-controltower.md)\.

**Deprecated field name**  
The field name `MasterAccountID` has been changed to `ManagementAccountID` to comply with AWS guidelines\. The old name is **deprecated**\. Beginning in 2022, scripts that contain the deprecated field name will no longer work\.

## Removed Member Account<a name="drift-account-removed"></a>

This type of drift can occur when a member account is removed from a registered AWS Control Tower organizational unit\. The following example shows the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the member account 012345678909 has been removed from organization o-123EXAMPLE. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/remove-account'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "ACCOUNT_REMOVED_FROM_ORGANIZATION",
  "RemediationStep" : "Add account to Organization and update Account Factory provisioned product",
  "AccountId" : "012345678909"
}
```

### Resolution<a name="drift-account-removed-resolution"></a>
+ When this type of drift occurs in a member account, you can resolve the drift by updating the account in the AWS Control Tower console, or in Account Factory\. For example, you can add the account to another registered OU from the Account Factory update wizard\. For more information, see [Update and move account factory accounts with AWS Control Tower or with AWS Service Catalog](updating-account-factory-accounts.md)\.
+ If a shared account is removed from a Foundational OU, you must resolve the drift by repairing your landing zone\. Until this drift is resolved, you will not be able to use the AWS Control Tower console\.
+ For more information about resolving drift for accounts and OUs, see [If you manage resources outside of AWS Control Tower](external-resources.md)\. 

**Note**  
In Service Catalog, the Account Factory provisioned product that represents the account is not updated to remove the account\. Instead, the provisioned product is displayed as `TAINTED` and in an error state\. To clean up, go to the Service Catalog, choose the provisioned product, and then choose **Terminate**\.

## Unplanned Update to Managed SCP<a name="drift-scp-update"></a>

This type of drift can occur when an SCP for a control is updated in the AWS Organizations console or programmatically using the AWS CLI or one of the AWS SDKs\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)', attached to the registered organizational unit 'Security (ou-0123-1EXAMPLE)', has been modified. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/update-scp'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "SCP_UPDATED",
  "RemediationStep" : "Update Control Tower Setup",
  "OrganizationalUnitId" : "ou-0123-1EXAMPLE",
  "PolicyId" : "p-tEXAMPLE"
}
```

### Resolution<a name="drift-scp-update-resolution"></a>

When this type of drift occurs in an OU with up to 300 accounts, you can resolve it by:
+ Navigating to the **Organization** page in the AWS Control Tower console to re\-register the OU \(fastest option\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\. 
+ Updating your landing zone \(slower option\)\. For more information, see [Update Your Landing Zone](update-controltower.md)\.

When this type of drift occurs in an OU with more than 300 accounts, resolve it by updating your landing zone\. For more information, see [Update Your Landing Zone](update-controltower.md)\.

## SCP Attached to Managed OU<a name="drift-scp-attached-ou"></a>

This type of drift can occur when an SCP for a control is attached to any other OU\. This occurrence is especially common when you are working on your OUs from outside of the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)' has been attached to the registered organizational unit 'Sandbox (ou-0123-1EXAMPLE)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/scp-detached-ou'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "SCP_ATTACHED_TO_OU",
  "RemediationStep" : "Update Control Tower Setup",
  "OrganizationalUnitId" : "ou-0123-1EXAMPLE",
  "PolicyId" : "p-tEXAMPLE"
}
```

### Resolution<a name="drift-scp-attached-ou-resolution"></a>

When this type of drift occurs in an OU with up to 300 accounts, you can resolve it by:
+ Navigating to the **Organization** page in the AWS Control Tower console to re\-register the OU \(fastest option\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\. 
+ Updating your landing zone \(slower option\)\. For more information, see [Update Your Landing Zone](update-controltower.md)\.

When this type of drift occurs in an OU with more than 300 accounts, resolve it by updating your landing zone\. For more information, see [Update Your Landing Zone](update-controltower.md)\.

## SCP Detached from Managed OU<a name="drift-scp-detached-ou"></a>

This type of drift can occur when an SCP for a control has been detached from an OU that's managed by AWS Control Tower\. This occurrence is especially common when you're working from outside of the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)' has been detached from the registered organizational unit 'Sandbox (ou-0123-1EXAMPLE)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/scp-detached'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "SCP_DETACHED_FROM_OU",
  "RemediationStep" : "Update Control Tower Setup",
  "OrganizationalUnitId" : "ou-0123-1EXAMPLE",
  "PolicyId" : "p-tEXAMPLE"
}
```

### Resolution<a name="drift-scp-detached-ou-resolution"></a>

When this type of drift occurs in an OU with up to 300 accounts, you can resolve it by:
+ Navigating to the OU in the AWS Control Tower console to re\-register the OU \(fastest option\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\. 
+ Updating your landing zone \(slower option\)\. If the drift is affecting a mandatory control, the update process creates a new service control policy \(SCP\) and attaches it to the OU to repair the drift\. For more information about how to update your landing zone, see [Update Your Landing Zone](update-controltower.md)\.

When this type of drift occurs in an OU with more than 300 accounts, resolve it by updating your landing zone\. If the drift is affecting a mandatory control, the update process creates a new service control policy \(SCP\) and attaches it to the OU to repair the drift\. For more information about how to update your landing zone, see [Update Your Landing Zone](update-controltower.md)\.

## SCP Attached to Member Account<a name="drift-scp-attached-account"></a>

This type of drift can occur when an SCP for a control is attached to an account in the Organizations console\. Guardrails and their SCPs can be enabled on OUs \(and thus applied to all of an OU's enrolled accounts\) through the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)' has been attached to the member account 'account-email@amazon.com (012345678909)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/scp-detached-account'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "SCP_ATTACHED_TO_ACCOUNT",
  "RemediationStep" : "Re-register this organizational unit (OU)",
  "AccountId" : "012345678909",
  "PolicyId" : "p-tEXAMPLE"
}
```

### Resolution<a name="drift-scp-attached-account-resolution"></a>

This type of drift occurs on the account rather than the OU\.

When this type of drift occurs for accounts in a Foundational OU, such as the Security OU, the resolution is to update your landing zone\. For more information, see [Update Your Landing Zone](update-controltower.md)\.

When this type of drift occurs in a non\-Foundational OU with up to 300 accounts, you can resolve it by:
+ Detaching the AWS Control Tower SCP from the account factory account\.
+ Navigating to the OU in the AWS Control Tower console to re\-register the OU \(fastest option\)\. For more information, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\.

When this type of drift occurs in an OU with more than 300 accounts, you may attempt to resolve it by updating the account factory configuration for the account\. It may not be possible to resolve it successfully\. For more information, see [Update Your Landing Zone](update-controltower.md)\.

## Deleted Foundational OU<a name="drift-ou-deleted"></a>

This type of drift applies only to AWS Control Tower Foundational OUs, such as the Security OU\. It can occur if a Foundational OU is deleted outside of the AWS Control Tower console\. Foundational OUs cannot be moved without creating this type of drift, because moving an OU is the same as deleting it and then adding it someplace else\. When you resolve the drift by updating your landing zone, AWS Control Tower replaces the Foundational OU in the original location\. The following example shows an Amazon SNS notification you may receive when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that the registered organizational unit 'Security (ou-0123-1EXAMPLE)' has been deleted. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/delete-ou'",
  "ManagementAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "ORGANIZATIONAL_UNIT_DELETED",
  "RemediationStep" : "Delete organizational unit in Control Tower",
  "OrganizationalUnitId" : "ou-0123-1EXAMPLE"
}
```

### Resolution<a name="drift-ou-deleted-resolution"></a>

Because this drift occurs for Foundational OUs only, the resolution is to update the landing zone\. When other types of OUs are deleted, AWS Control Tower is updated automatically\.

For more information about resolving drift for accounts and OUs, see [If you manage resources outside of AWS Control Tower](external-resources.md)\.