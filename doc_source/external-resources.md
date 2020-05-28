# Managing Resources Outside of AWS Control Tower<a name="external-resources"></a>

AWS Control Tower sets up accounts, organizational units, and other resources on your behalf, but you are the owner of these resources\. You can change these resources within AWS Control Tower or outside it\. The most common place to change resources outside of AWS Control Tower is the AWS Organizations console\. This topic describes how to reconcile changes to AWS Control Tower resources when you make the changes outside of AWS Control Tower\.

Renaming, deleting, and moving resources outside of the AWS Control Tower console can cause the console to become out of sync and display outdated information\. Some changes can be reconciled automatically\. Other changes require a repair to your landing zone to update the information that's displayed in the AWS Control Tower console\.

****Tasks that require landing zone repair to reconcile your changes****
+ Renaming a registered organizational unit \(OU\)
+ Renaming an enrolled account

****Changes that are updated automatically by AWS Control Tower****
+ Changing the email address of an enrolled account
+ Deleting a registered OU
+ Deleting an enrolled account

**Note**  
AWS Service Catalog handles changes differently than AWS Control Tower\. AWS Service Catalog may create a change in governance posture when it reconciles your changes\. For more information about updating a provisioned product, see [Updating Provisioned Products](https://docs.aws.amazon.com/servicecatalog/latest/userguide/enduser-update.html) in the AWS Service Catalog documentation\.

In general, changes that you make outside the AWS Control Tower console to AWS Control Tower resources create a state of *repairable drift* in your landing zone\. For more information about these changes, see [Repairable Changes to Resources](drift.md#repairable-changes-to-resources)\.

## Referring to Resources Outside of AWS Control Tower<a name="ungoverned-resources"></a>

When you create new OUs and accounts outside of AWS Control Tower, they are not governed by AWS Control Tower, even though they may be displayed\.

**Creating an OU**

Organizational Units \(OUs\) created outside of AWS Control Tower are referred to as *Unregistered*\. They are displayed in the **OU list** page, but they are not governed by AWS Control Tower guardrails\.

**Creating an account**

Accounts created outside of AWS Control Tower are referred to as *Unenrolled*\. Accounts that belong to an organization that’s registered with AWS Control Tower are displayed in the **Accounts list** page\. Accounts that do not belong to a registered organization can be invited by using the AWS Organizations console\. This invitation to join does not enroll the account in AWS Control Tower or extend AWS Control Tower governance to the account\. 

## Changing AWS Control Tower Resource Names<a name="changing-names"></a>

You can change the names of your organizational units \(OUs\) and accounts outside of the AWS Control Tower console, but you must also repair your landing zone so that you can see the updates that you've made\.

**Renaming an OU**

In AWS Organizations, you can change the name of an OU by using either the API or the console\. When you change an OU name outside of AWS Control Tower, you also must repair your landing zone to ensure that AWS Control Tower stays consistent with AWS Organizations\. The **Repair** workflow ensures consistency across services for the core and non\-core \(workload\) OU names\. You can repair this type of drift easily from the **Settings** page\. See "Resolving Drift" in [Detecting and Resolving Drift in AWS Control Tower](drift.md)\.

AWS Control Tower displays the names of OUs in the console and in Account Factory, and you can see when your update has succeeded\.

**Renaming an enrolled account**

Each AWS account has a display name that can be changed in the AWS Billing and Cost Management console\. When you change an account name, you also must repair your AWS Control Tower landing zone to ensure data consistency between services for core and non\-core \(workload\) account names\.

**Note**  
The provisioned product name in AWS Control Tower is set to the AWS account name when the account is provisioned\. If you change this value, you also must update the AWS Service Catalog provisioned product\. This change in AWS Service Catalog may cause a change in governance posture for the account\. 

## Changes that Are Updated Automatically<a name="updated-automatically"></a>

Changes that you make to your account email addresses are updated by AWS Control Tower automatically, but Account Factory does not update them automatically\.

**Changing the email address of a governed account**

AWS Control Tower retrieves and displays email addresses as required by the console experience\. Therefore, core and non\-core account email addresses are updated and shown consistently in AWS Control Tower after you change them\.

**Note**  
In AWS Service Catalog, the Account Factory displays the parameters that were specified in the console when you created a provisioned product\. However, the original account email address is not updated automatically when the account email address changes\. That’s because the account is conceptually contained within the provisioned product; it is not the same as the provisioned product\. To update this value, you must update the provisioned product, which may cause a change in governance posture\.

### Deleting Resources<a name="deleting-resources"></a>

You can delete OUs and accounts in AWS Control Tower and you don't need to take any further action to see the updates\. Account Factory is updated automatically when you delete an OU, but not when you delete an account\.

**Deleting a registered OU**

Within AWS Organizations, you can remove empty organizational units \(OUs\) by using the API or the console\. From the AWS Control Tower console, you can choose **Deregister OU** to remove non\-core, governed OUs that are empty\. OUs that contain accounts cannot be deleted\.

AWS Control Tower receives a notification from AWS Organizations when an OU is deleted\. It updates the OU list in the Account Factory, so that the list of registered OUs remains consistent\.

If you see a deleted OU displayed in the AWS Control Tower console, repair your landing zone to remove outdated entries\. You also can choose **Deregister OU** in the AWS Control Tower console to clean up stale entries\.

**Note**  
In AWS Service Catalog, the Account Factory is updated to remove the deleted OU from the list of available OUs into which you can provision an account\.

**Deleting an enrolled account from an OU**

When you remove an enrolled, non\-core account, AWS Control Tower receives a notification and makes updates, so that the information remains consistent\.

If you see a deleted account displayed in the AWS Control Tower console, repair your landing zone to remove the outdated entry\.

**Note**  
In AWS Service Catalog, the Account Factory provisioned product that represents the governed account is not updated to remove the account\. Instead, the provisioned product is displayed as `TAINTED` and in an error state\. To clean up, go to AWS Service Catalog, choose the provisioned product, and then choose **Terminate**\.