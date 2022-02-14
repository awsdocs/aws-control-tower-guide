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

For more information, see [Types of Governance Drift](governance-drift.md)\.

## Resolving drift<a name="resolving-drift"></a>

Although detection is automatic, the steps to resolve drift must be done through the console\.
+ Many types of drift can be resolved through the **Landing zone settings** page\. You can choose the **Repair** button in the **Versions** section to repair these types of drift\.
+ If your OU has fewer than 300 accounts, you can repair drift by selecting **Re\-register OU** on the **OU** page, to repair drift in Account Factory provisioned accounts, or SCP drift\.

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
| `listOrganizationalUnits` | 8 burst, 5 sustained | 1 per landing zone | 
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

If your landing zone is in a state of drift, the **Enroll account** feature in AWS Control Tower will not work\. In that case, you must provision new accounts through AWS Service Catalog\. For instructions, see [Provision Account Factory accounts with AWS Service Catalog](provision-as-end-user.md)\.

In particular, if you've made certain changes to your accounts by means of AWS Service Catalog, such as changing the name of your portfolio, the **Enroll account** feature will not work\.