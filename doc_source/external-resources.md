# Manage resources outside of AWS Control Tower<a name="external-resources"></a>

AWS Control Tower sets up accounts, organizational units, and other resources on your behalf, but you are the owner of these resources\. You can change these resources within AWS Control Tower or outside it\. The most common place to change resources outside of AWS Control Tower is the AWS Organizations console\. This topic describes how to reconcile changes to AWS Control Tower resources when you make the changes outside of AWS Control Tower\.

Renaming, deleting, and moving resources outside of the AWS Control Tower console causes the console to become out of sync\. Many changes can be reconciled automatically\. Certain changes require a repair to your landing zone to update the information that's displayed in the AWS Control Tower console\.

In general, changes that you make outside the AWS Control Tower console to AWS Control Tower resources create a state of *repairable drift* in your landing zone\. For more information about these changes, see [Repairable changes to resources](drift.md#repairable-changes-to-resources)\.

****Tasks that require landing zone repair****
+ Deleting the Security OU *\(A special case, not to be done lightly\.\)*
+ Removing a shared account from the Security OU *\(Not recommended, requires help from AWS Support\.\)*
+ Updating, attaching, or detaching an SCP associated with the Security OU\.

****Changes that are updated automatically by AWS Control Tower****
+ Changing the email address of an enrolled account
+ Renaming an enrolled account
+ Creating a new top\-level organizational unit \(OU\)
+ Renaming a registered OU
+ Deleting a registered OU *\(Except the Security OU, which requires an update\.\)*
+ Deleting an enrolled account *\(Except a shared account in the Security OU\.\)*

**Note**  
AWS Service Catalog handles changes differently than AWS Control Tower\. AWS Service Catalog may create a change in governance posture when it reconciles your changes\. For more information about updating a provisioned product, see [Updating Provisioned Products](https://docs.aws.amazon.com/servicecatalog/latest/userguide/enduser-update.html) in the AWS Service Catalog documentation\.

## Referring to Resources Outside of AWS Control Tower<a name="ungoverned-resources"></a>

When you create new OUs and accounts outside of AWS Control Tower, they are not governed by AWS Control Tower, even though they may be displayed\.

**Creating an OU**

Organizational Units \(OUs\) created outside of AWS Control Tower are referred to as *Unregistered*\. They are displayed in the **OU list** page, but they are not governed by AWS Control Tower guardrails\.

**Creating an account**

Accounts created outside of AWS Control Tower are referred to as *Unenrolled*\. Accounts that belong to an organization that’s registered with AWS Control Tower are displayed in the **Accounts list** page\. Accounts that do not belong to a registered organization can be invited by using the AWS Organizations console\. This invitation to join does not enroll the account in AWS Control Tower or extend AWS Control Tower governance to the account\. To extend governance by enrolling the account, go to the Account Factory page in AWS Control Tower and choose **Enroll account**\.

## Changing AWS Control Tower Resource Names<a name="changing-names"></a>

You can change the names of your organizational units \(OUs\) and accounts outside of the AWS Control Tower console, and the console updates automatically to reflect those changes\.

**Renaming an OU**

In AWS Organizations, you can change the name of an OU by using either the AWS Organizations API or the console\. When you change an OU name outside of AWS Control Tower, the AWS Control Tower console automatically reflects the name change\. However, if you provision your accounts using AWS Service Catalog, you also must repair your landing zone to ensure that AWS Control Tower stays consistent with AWS Organizations\. The **Repair** workflow ensures consistency across services for the Foundational and Additional OUs\. You can repair this type of drift from the **Settings** page\. See "Resolving Drift" in [Detect and resolve drift in AWS Control Tower](drift.md)\.

AWS Control Tower displays the names of OUs in the AWS Control Tower dashboard and displays Additional OUs in Account Factory\. You can see when your landing zone repair has succeeded\.

**Renaming an enrolled account**

Each AWS account has a display name that can be changed in the AWS Billing and Cost Management console\. When you rename an account that's enrolled in AWS Control Tower, the name change is automatically reflected in AWS Control Tower\. 

## Deleting the Security OU<a name="delete-security-ou"></a>

Before you can delete the **Security** OU, you must make sure it contains no accounts\. Specifically, you must remove the Log Archive and Audit accounts from the OU\. We recommend that you move these accounts to another OU\. When you run the **Repair** function, AWS Control Tower creates a new Security OU, and it moves these accounts back into the new Security OU\. AWS Control Tower marks these accounts as drifted\.

**Note**  
The action of deleting your Security OU is not to be performed without due consideration\. The action could create compliance concerns if logging is suspended temporarily, and because some guardrails might not be enforced\.

If you delete the **Security** OU, you will see an error message prompting you to repair your landing zone\. You must repair your landing zone before you can take any other actions in AWS Control Tower\. You will not be able to perform any actions in the AWS Control Tower console and you will not be able to create any new accounts in AWS Service Catalog until the repair is done\.

This type of drift is a special case\. You won't be able to view the **Settings** page to see the **Repair** button there\. In this situation, the landing zone repair process creates a new Security OU and moves the two shared accounts into the new Security OU\. The same process repairs the drift in the log archive and audit accounts\.

 For general information about drift, see "Resolving Drift" in [Detect and resolve drift in AWS Control Tower](drift.md)\.

## Removing an account from the Security OU<a name="removed-shared-account"></a>

We do not recommend that you remove any of the shared accounts from your organization or move them out of the **Security** OU\. If you have removed a shared account accidentally, you can follow the remediation steps below to restore the account\. To start the remediation process from within the AWS Control Tower console, follow the semi\-manual remediation steps\. Ensure the user or role you use to access the AWS Control Tower console has permissions to run `organizations:InviteAccountToOrganization`\. If you don't have such permissions, use the manual remediation steps which use both the AWS Control Tower console and the AWS Organizations console\.

You can also start the remediation process from the AWS Organizations console\. This is a slightly longer, fully manual procedure\. When following the manual remediation steps, you'll switch between the AWS Organizations console and the AWS Control Tower console\. When working in AWS Organizations, you'll need a user or role with the `AWSOrganizationsFullAccess` managed policy or equivalent\. When working in the AWS Control Tower console, you'll need a user or role with the `AWSControlTowerServiceRolePolicy` managed policy or equivalent, and permission to run all AWS Control Tower actions \(controltower:\*\)\.

If the remediation steps don't restore the account, contact AWS Support\.

**The results of removing a shared account through AWS Organizations:**
+ The account is no longer protected by AWS Control Tower mandatory guardrail service control policies \(SCPs\)\. **Result:** *The resources created by AWS Control Tower in the account may be modified or deleted\.*
+ The account is no longer under the AWS Organizations management account\. **Result:** *The administrator of the AWS Organizations management account no longer has visibility into the account's spending\.*
+ The account is no longer guaranteed to be monitored by AWS Config\. **Result:** *The administrator of the AWS Organizations management account may not be able to detect resource changes\.*
+ The account is no longer in the organization\. **Result:** *AWS Control Tower updates and repair will fail\.*

**To restore a shared account using the AWS Control Tower console \(semi\-manual procedure\)**

1. Sign in to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\. You must sign in as an AWS Identity and Access Management \(IAM\) user or role with permissions to run `organizations:InviteAccountToOrganization`\. If you don't have such permissions, use the manual remediation procedure described later in this topic\.

1. On the **Landing zone drift detected** page, choose **Re\-Invite** to remediate shared account removal by re\-inviting the shared account into the organization\. An automatically\-generated email is sent to the email address for the account\.

1. Accept the invitation to bring the shared account back into the organization\. Do one of the following:
   + Sign in to the shared account that was removed, then go to [https://console\.aws\.amazon\.com/organizations/home\#/invites](https://console.aws.amazon.com/organizations/home#/invites)
   + If you have access to the email message sent when you re\-invited the account, sign in to the removed account, then click the link in the message to navigate directly to the account invitation\. 
   + If the shared account that was removed is not in another organization, sign into the account, open the AWS Organizations console and navigate to **Invitations**\.

1. Sign in to the management account again, or reload the AWS Control Tower console if it's already open\. Navigate to **Landing zone settings** and choose **Repair** to repair the landing zone\.

1. Wait for the repair process to complete\.

If remediation is successful, the shared account appears in a normal state and compliance\. 

If the remediation steps don't restore the account, contact AWS Support\.

**To restore a shared account using the AWS Control Tower and AWS Organizations consoles \(Manual remediation\)**

1. Sign in to the AWS Organizations console at [https://console.aws.amazon.com/organizations/](https://console.aws.amazon.com/organizations/)\. You must sign in as an IAM user or role with the `AWSOrganizationsFullAccess` managed policy or equivalent\. 

1. Invite the shared account back to the organization\. For information on the requirements, prerequisites, and procedure for inviting an account to AWS Organizations, see [Inviting an AWS account to your organization ](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_invites.html) in the *AWS Organizations User Guide*\.

1. Sign in to the shared account that was removed, then go to [https://console\.aws\.amazon\.com/organizations/home\#/invites](https://console.aws.amazon.com/organizations/home#/invites) to accept the invitation\.

1. Sign in to the management account again\. 

1. Sign in to the AWS Control Tower console as an IAM user or role with the `AWSControlTowerServiceRolePolicy` managed policy or equivalent, and permissions to run all AWS Control Tower actions \(controltower:\*\)\.

1. Navigate to **Landing zone settings** and choose **Repair** to repair the landing zone\.

1. Wait for the repair process to complete\.

If remediation is successful, the shared account appears in a normal state and compliance\.

If the remediation steps don't restore the account, contact AWS Support\.

## Changes that Are Updated Automatically<a name="updated-automatically"></a>

Changes that you make to your account email addresses are updated by AWS Control Tower automatically, but Account Factory does not update them automatically\.

**Changing the email address of a governed account**

AWS Control Tower retrieves and displays email addresses as required by the console experience\. Therefore, shared and other account email addresses are updated and shown consistently in AWS Control Tower after you change them\.

**Note**  
In AWS Service Catalog, the Account Factory displays the parameters that were specified in the console when you created a provisioned product\. However, the original account email address is not updated automatically when the account email address changes\. That’s because the account is conceptually contained within the provisioned product; it is not the same as the provisioned product\. To update this value, you must update the provisioned product, which may cause a change in governance posture\.

### Deleting Resources<a name="deleting-resources"></a>

You can delete OUs and accounts in AWS Control Tower and you don't need to take any further action to see the updates\. Account Factory is updated automatically when you delete an OU, but not when you delete an account\.

**Deleting a registered OU \(except the Security OU\)**

Within AWS Organizations, you can remove empty organizational units \(OUs\) by using the API or the console\. OUs that contain accounts cannot be deleted\.

AWS Control Tower receives a notification from AWS Organizations when an OU is deleted\. It updates the OU list in the Account Factory, so that the list of registered OUs remains consistent\.

If you see a deleted OU displayed in the AWS Control Tower console, repair your landing zone to remove outdated entries\.

**Note**  
In AWS Service Catalog, the Account Factory is updated to remove the deleted OU from the list of available OUs into which you can provision an account\.

**Deleting an enrolled account from an OU**

When you delete an enrolled account, AWS Control Tower receives a notification and makes updates, so that the information remains consistent\.

If you see a deleted account displayed in the AWS Control Tower console, repair your landing zone to remove the outdated entry\.

**Note**  
In AWS Service Catalog, the Account Factory provisioned product that represents the governed account is not updated to delete the account\. Instead, the provisioned product is displayed as `TAINTED` and in an error state\. To clean up, go to AWS Service Catalog, choose the provisioned product, and then choose **Terminate**\.