# Detect and resolve drift in AWS Control Tower<a name="drift"></a>

Identifying and resolving drift is a regular operations task for AWS Control Tower master account administrators\.

When you create your landing zone, the landing zone and all the OUs, accounts, and resources are compliant with the governance rules enforced by your chosen guardrails\. As you and your organization members use the landing zone, changes in this compliance status may occur\. Some changes may be accidental, and some may be made intentionally to respond to time\-sensitive operational events\.

Changes can complicate your compliance story\. Drift detection assists you in identifying resources that need changes or configuration updates to resolve the drift\. Resolving drift helps to ensure your compliance with governance regulations\.

**Detecting Drift**

Drift is detected automatically by AWS Control Tower\. It is surfaced in the Amazon SNS notifications that are aggregated in the audit account\. Notifications in each member account send alerts to a local Amazon SNS topic, and to a Lambda function\.

Member account administrators can \(and as a best practice, they should\) subscribe to the SNS drift notifications for specific accounts\. For example, the `aws-controltower-AggregateSecurityNotifications` SNS topic provides drift notifications\. The AWS Control Tower console indicates to master account administrators when drift has occurred\.

**Resolving Drift**

Although detection is automatic, the steps to resolve drift must be done through the console\. Many types of drift can be resolved through the **Settings** page\. If the **Repair** button in the **Versions** section of the page is selectable, you can choose **Repair** to repair some types of drift\.  If no drift has occurred, the **Repair** button appears greyed\-out\.

## <a name="types-of-drift"></a>

Most types of drift can be resolved by administrators\. A few types of drift must be repaired immediately, including deletion of an organizational unit that the AWS Control Tower landing zone requires\. Here are some examples of major drift:
+ The organizational unit originally named **Core** during landing zone setup by AWS Control Tower should not be deleted\. If you delete it, you'll see an error message instructing you to repair the landing zone immediately\. You won't be able to take any other actions in AWS Control Tower until the repair is complete\.
+ AWS Control Tower checks certain IAM roles when you log into the console for *IAM role drift*\. If these roles are missing or inaccessible, you'll see an error page instructing you to repair your landing zone\. These roles are *AWSControlTowerAdmin*, *AWSControlTowerCloudTrailRole*, and *AWSControlTowerStackSetRole*\.
+  If you delete the organizational unit originally named **Custom** during landing zone setup by AWS Control Tower, your landing zone will be in a state of drift, but you still can use AWS Control Tower\. At least one non\-Core OU is required for AWS Control Tower to operate, but it doesn’t have to be the **Custom** OU\.

## Repairable Changes to Resources<a name="repairable-changes-to-resources"></a>

Here's a list of changes to AWS Control Tower resources that are permitted, although they create repairable drift\. Results of these permitted operations are viewable in the AWS Control Tower console, although a refresh may be required\.

For more information about how to resolve the resulting drift, see [Managing Resources Outside of AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/external-resources.html)\.

**Changes Permitted Outside the AWS Control Tower Console**
+ Change the name of a non\-Core OU\.
+ Change the name of the Core OU\.
+ Change the name of non\-Core \(member\) accounts\.
+ Change the name of Core \(shared\) accounts\.
+ Delete a non\-Core OU\.
+ Delete a non\-Core OU \(member\) account\.
+ Change the email address of a Core \(shared\) account\.
+ Change the email address of a non\-core \(member\) account\.

**Note**  
Moving accounts between OUs is considered drift, and it must be repaired\.

## Drift and New Account Provisioning<a name="drift-and-accounts"></a>

If your landing zone is in a state of drift, the **Enroll account** feature in AWS Control Tower will not work\. In that case, you must provision new accounts through AWS Service Catalog\. For instructions, see [Provisioning Account Factory Accounts With AWS Service Catalog](account-factory.md#provision-as-end-user)\.

In particular, if you've made certain changes to your accounts by means of AWS Service Catalog, such as changing the name of your portfolio, the **Enroll account** feature will not work\.

## Types of Governance Drift<a name="governance-drift"></a>

Governance drift occurs when organizations and member accounts are changed and updated\. The types of governance drift that can be detected in AWS Control Tower are as follows:
+ [Moved Member Account](#drift-account-moved)
+ [Added Member Account](#drift-account-added)
+ [Removed Member Account](#drift-account-removed)
+ [Unplanned Update to Managed SCP](#drift-scp-update)
+ [SCP Attached to Member Account](#drift-scp-attached-account)
+ [SCP Detached from Managed OU](#drift-scp-detached-ou)
+ [Deleted Managed OU](#drift-ou-deleted)

## Moved Member Account<a name="drift-account-moved"></a>

This kind of drift can occur when a member account, the audit account, or the log archive account is moved from one AWS Control Tower OU to another AWS Control Tower OU\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

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
+ **Account Factory Provisioned Account** – You can resolve the drift by updating the account in Account Factory\. For more information, see [Updating and Moving Account Factory Accounts with AWS Service Catalog](account-factory.md#updating-account-factory-accounts)\.
+ **Shared account** – You can resolve the drift from moving the audit or log archive account by updating your landing zone\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

## Added Member Account<a name="drift-account-added"></a>

This kind of drift can occur when an AWS Control Tower member account is added to an AWS Control Tower OU\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

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

### Resolution<a name="drift-account-added-resolution"></a>

When this kind of drift occurs, you can resolve it by updating the account in Account Factory\. For more information, see [Updating and Moving Account Factory Accounts with AWS Service Catalog](account-factory.md#updating-account-factory-accounts)\.

For more information about resolving drift for accounts and OUs, see [Manage resources outside of AWS Control Tower](external-resources.md)\.

## Removed Member Account<a name="drift-account-removed"></a>

This kind of drift can occur when an AWS Control Tower member account is removed from an AWS Control Tower OU\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

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

When this kind of drift occurs, AWS Control Tower receives a notification and makes an automatic update\. If the deleted member account continues to be displayed, you can resolve the drift by repairing your landing zone\. You also could resolve the drift by updating the account in Account Factory, and adding the account to another registered OU from the Account Factory update wizard\. For more information, see [Updating and Moving Account Factory Accounts with AWS Service Catalog](account-factory.md#updating-account-factory-accounts)\.

For more information about resolving drift for accounts and OUs, see [Manage resources outside of AWS Control Tower](external-resources.md)\.

**Note**  
In AWS Service Catalog, the Account Factory provisioned product that represents the account is not updated to remove the account\. Instead, the provisioned product is displayed as `TAINTED` and in an error state\. To clean up, go to the AWS Service Catalog, choose the provisioned product, and then choose **Terminate**\.

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

When this kind of drift occurs, you can resolve it by updating your landing zone\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

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

When this kind of drift occurs, you can resolve it by updating your landing zone\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

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

When this kind of drift occurs, you can resolve it by updating your landing zone\. If the drift is affecting a mandatory guardrail, the update process creates a new SCP and attaches it to the OU to repair the drift\. For more information about how to update your landing zone, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

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

When this kind of drift occurs, you can resolve it by updating your landing zone\. For more information, see [Update Your Landing Zone](configuration-updates.md#update-controltower)\.

## Deleted Managed OU<a name="drift-ou-deleted"></a>

This kind of drift can occur if an AWS Control Tower OU is deleted outside of the AWS Control Tower console\. The following is an example of the Amazon SNS notification when this type of drift is detected\.

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

When this kind of drift occurs, AWS Control Tower usually can update the list of registered OUs automatically\. If a deleted OU is shown, a central cloud administrator must sign in to the AWS Control Tower console and delete the OU from your list of **Organizational units**\.

For more information about resolving drift for accounts and OUs, see [Manage resources outside of AWS Control Tower](external-resources.md)\.