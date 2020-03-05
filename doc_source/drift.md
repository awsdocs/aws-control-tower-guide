# Detecting and Resolving Drift in AWS Control Tower<a name="drift"></a>

Identifying and resolving drift is a regular operations task for AWS Control Tower master account administrators\.

When you create your landing zone, the landing zone and all the OUs, accounts, and resources are compliant with the governance rules enforced by your chosen guardrails\. As you and your organization members use the landing zone, changes in this compliance status may occur\. Some changes may be accidental, and some may be made intentionally to respond to time\-sensitive operational events\.

Changes can complicate your compliance story\. Drift detection assists you in identifying resources that need changes or configuration updates to resolve the drift\. Resolving drift helps to ensure your compliance with governance regulations\.

**Detecting Drift**

Drift is detected automatically by AWS Control Tower\. It is surfaced in the Amazon SNS notifications that are aggregated in the audit account\. Notifications in each member account send alerts to a local Amazon SNS topic, and to a Lambda function\.

Member account administrators can \(and as a best practice, they should\) subscribe to the drift notifications for specific accounts\. The AWS Control Tower console indicates to master account administrators when drift has occurred\.

**Resolving Drift**

Although detection is automatic, the steps to resolve drift must be done through the console\. Many types of drift can be resolved through the **Settings** page\. If the **Repair** button in the **Versions** section of the page is selectable, you can choose **Repair** to repair some types of drift\.  If no drift has occurred, the **Repair** button appears greyed\-out\.

Most types of drift can be resolved by administrators\. A few types of drift cannot be repaired, including deletion of an organizational unit that the AWS Control Tower landing zone requires\. Here are some examples of major drift:

**Non\-repairable Drift**
+ The organizational unit originally named **Core** during landing zone setup by AWS Control Tower must not be deleted, because unrecoverable drift will result\. If this situation arises, you'll see a page instructing you to contact AWS Support\.

**Repairable Drift**
+ AWS Control Tower checks certain IAM roles when you log into the console for *IAM role drift*\. If these roles are missing or inaccessible, you'll see an error page instructing you to repair your landing zone\. These roles are *AWSControlTowerAdmin*, *AWSControlTowerCloudTrailRole*, and *AWSControlTowerStackSetRole*\. 
+  If you delete the organizational unit originally named **Custom** during landing zone setup by AWS Control Tower, your landing zone will be in a state of drift, but you still can use Control Tower\. At least one non\-Core OU is required for AWS Control Tower to operate, but it doesn’t have to be the **Custom** OU\.

**Drift and Account Provisioning**

If your landing zone is in a state of drift, **Quick account provisioning** will not work\. In that case, you must provision accounts through AWS Service Catalog\. For instructions, see [Provisioning Account Factory Accounts With AWS Service Catalog](account-factory.md#provision-as-end-user)\.

In particular, if you've made changes to your accounts by means of AWS Service Catalog, such as changing the name of your portfolio, **Quick account provisioning** will not work\. 

**Types of Governance Drift**

Governance drift occurs when organizations and member accounts are changed and updated\. The types of governance drift that can be detected in AWS Control Tower are as follows:

**Topics**
+ [Moved Member Account](#drift-account-moved)
+ [Added Member Account](#drift-account-added)
+ [Removed Member Account](#drift-account-removed)
+ [Unplanned Update to Managed SCP](#drift-scp-update)
+ [SCP Attached to Managed OU](#drift-scp-attached-ou)
+ [SCP Detached from Managed OU](#drift-scp-detached-ou)
+ [SCP Attached to Member Account](#drift-scp-attached-account)
+ [Deleted Managed OU](#drift-ou-deleted)

## Moved Member Account<a name="drift-account-moved"></a>

This kind of drift can occur when a managed account, the audit, or the log archive account is moved from one OU to another OU\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
{
  "Message" : "AWS Control Tower has detected that your managed account 'account-email@amazon.com (012345678909)' has been moved from organizational unit 'Custom (ou-0123-eEXAMPLE)' to 'Core (ou-3210-1EXAMPLE)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/move-account'",
  "MasterAccountId" : "012345678912",
  "OrganizationId" : "o-123EXAMPLE",
  "DriftType" : "AccountMovedBetweenOrganizationalUnits",
  "RemediationStep" : "Update Account Factory Provisioned Product",
  "AccountId" : "012345678909",
  "SourceId" : "012345678909",
  "DestinationId" : "ou-3210-1EXAMPLE"
}
```

### Resolutions<a name="drift-account-moved-resolution"></a>

When this kind of drift occurs, you can resolve it as follows:
+ **Account Factory Provisioned Account** – You can resolve the drift by updating the account in Account Factory\. For more information, see [Updating and Moving Account Factory Accounts](account-factory.md#updating-account-factory-accounts)\.
+ **Shared account** – You can resolve the drift from moving the audit or log archive account by updating your landing zone\. For more information, see [Updating Your Landing Zone](configuration-updates.md#update-controltower)\.

## Added Member Account<a name="drift-account-added"></a>

This kind of drift can occur when a managed account is added to a managed OU\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
"{
  ""Message"" : ""AWS Control Tower has detected that the managed account 'account-email@amazon.com (012345678909)' has been added to organization o-123EXAMPLE. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/add-account'"",
  ""MasterAccountId"" : ""012345678912"",
  ""OrganizationId"" : ""o-123EXAMPLE"",
  ""DriftType"" : ""AccountAddedToOrganization"",
  ""RemediationStep"" : ""Update Account Factory Provisioned Product"",
  ""AccountId"" : ""012345678909""
}"
```

### Resolution<a name="drift-account-moved-resolution"></a>

When this kind of drift occurs, you can resolve it by updating the account in Account Factory\. For more information, see [Updating and Moving Account Factory Accounts](account-factory.md#updating-account-factory-accounts)\.

## Removed Member Account<a name="drift-account-removed"></a>

This kind of drift can occur when a managed account is removed from a managed OU\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
"{
  ""Message"" : ""AWS Control Tower has detected that the managed account 012345678909 has been removed from organization o-123EXAMPLE. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/remove-account'"",
  ""MasterAccountId"" : ""012345678912"",
  ""OrganizationId"" : ""o-123EXAMPLE"",
  ""DriftType"" : ""AccountRemovedFromOrganization"",
  ""RemediationStep"" : ""Add account to Organization and update Account Factory provisioned product"",
  ""AccountId"" : ""012345678909""
}"
```

### Resolution<a name="drift-account-removed-resolution"></a>

When this kind of drift occurs, you can resolve it by updating the account in Account Factory, and adding the account to a managed OU from the Account Factory update wizard\. For more information, see [Updating and Moving Account Factory Accounts](account-factory.md#updating-account-factory-accounts)\.

## Unplanned Update to Managed SCP<a name="drift-scp-update"></a>

This kind of drift can occur when an SCP for a guardrail is updated in the Organizations console or programmatically using the AWS CLI or one of the AWS SDKs\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
"{
  ""Message"" : ""AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)', attached to the managed organizational unit 'Core (ou-0123-1EXAMPLE)', has been modified. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/update-scp'"",
  ""MasterAccountId"" : ""012345678912"",
  ""OrganizationId"" : ""o-123EXAMPLE"",
  ""DriftType"" : ""ServiceControlPolicyUpdated"",
  ""RemediationStep"" : ""Update Control Tower Setup"",
  ""OrganizationalUnitId"" : ""ou-0123-1EXAMPLE"",
  ""PolicyId"" : ""p-tEXAMPLE""
}"
```

### Resolution<a name="drift-scp-update-resolution"></a>

When this kind of drift occurs, you can resolve it by updating your landing zone\. For more information, see [Updating Your Landing Zone](configuration-updates.md#update-controltower)\.

## SCP Attached to Managed OU<a name="drift-scp-attached-ou"></a>

This kind of drift can occur when an SCP is attached to an OU outside of the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
"{
  ""Message"" : ""AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)' has been attached to the managed organizational unit 'Custom (ou-0123-1EXAMPLE)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/scp-detached-ou'"",
  ""MasterAccountId"" : ""012345678912"",
  ""OrganizationId"" : ""o-123EXAMPLE"",
  ""DriftType"" : ""ServiceControlPolicyAttachedToOrganizationalUnit"",
  ""RemediationStep"" : ""Update Control Tower Setup"",
  ""OrganizationalUnitId"" : ""ou-0123-1EXAMPLE"",
  ""PolicyId"" : ""p-tEXAMPLE""
}"
```

### Resolution<a name="drift-scp-attached-ou-resolution"></a>

When this kind of drift occurs, you can resolve it by updating your landing zone\. For more information, see [Updating Your Landing Zone](configuration-updates.md#update-controltower)\.

## SCP Detached from Managed OU<a name="drift-scp-detached-ou"></a>

This kind of drift can occur when an SCP has been detached from an OU outside of the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
"{
  ""Message"" : ""AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)' has been detached from the managed organizational unit 'Custom (ou-0123-1EXAMPLE)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/scp-detached'"",
  ""MasterAccountId"" : ""012345678912"",
  ""OrganizationId"" : ""o-123EXAMPLE"",
  ""DriftType"" : ""ServiceControlPolicyDetachedFromOrganizationalUnit"",
  ""RemediationStep"" : ""Update Control Tower Setup"",
  ""OrganizationalUnitId"" : ""ou-0123-1EXAMPLE"",
  ""PolicyId"" : ""p-tEXAMPLE""
}"
```

### Resolution<a name="drift-scp-detached-ou-resolution"></a>

When this kind of drift occurs, you can resolve it by updating your landing zone\. For more information, see [Updating Your Landing Zone](configuration-updates.md#update-controltower)\.

## SCP Attached to Member Account<a name="drift-scp-attached-account"></a>

This kind of drift can occur when an SCP is attached to an account in the Organizations console\. Guardrails and their SCPs can be enabled on OUs and all of an OU's member accounts through the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
"{
  ""Message"" : ""AWS Control Tower has detected that the managed service control policy 'aws-guardrails-012345 (p-tEXAMPLE)' has been attached to the managed account 'account-email@amazon.com (012345678909)'. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/scp-detached-account'"",
  ""MasterAccountId"" : ""012345678912"",
  ""OrganizationId"" : ""o-123EXAMPLE"",
  ""DriftType"" : ""ServiceControlPolicyAttachedToAccount"",
  ""RemediationStep"" : ""Update Control Tower Setup"",
  ""AccountId"" : ""012345678909"",
  ""PolicyId"" : ""p-tEXAMPLE""
}"
```

### Resolution<a name="drift-scp-attached-account-resolution"></a>

When this kind of drift occurs, you can resolve it by updating your landing zone\. For more information, see [Updating Your Landing Zone](configuration-updates.md#update-controltower)\.

## Deleted Managed OU<a name="drift-ou-deleted"></a>

This kind of drift can occur if a managed OU is deleted outside of the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

```
"{
  ""Message"" : ""AWS Control Tower has detected that the managed organizational unit 'Custom (ou-0123-1EXAMPLE)' has been deleted. For more information, including steps to resolve this issue, see 'https://docs.aws.amazon.com/console/controltower/delete-ou'"",
  ""MasterAccountId"" : ""012345678912"",
  ""OrganizationId"" : ""o-123EXAMPLE"",
  ""DriftType"" : ""OrganizationalUnitDeleted"",
  ""RemediationStep"" : ""Delete managed organizational unit in Control Tower"",
  ""OrganizationalUnitId"" : ""ou-0123-1EXAMPLE""
}"
```

### Resolution<a name="drift-ou-deleted-resolution"></a>

When this kind of drift occurs, a central cloud administrator must sign in to the AWS Control Tower console and delete the managed OU from your list of **Organizational units**\.