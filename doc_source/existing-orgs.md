# Govern organizations and accounts with AWS Control Tower<a name="existing-orgs"></a>

All organizational units \(OUs\) and accounts that you create in AWS Control Tower are governed automatically by AWS Control Tower\. Also, if you have existing OUs and accounts that were created outside of AWS Control Tower, you can bring them into AWS Control Tower governance\. 

For existing AWS Organizations and AWS accounts, most customers prefer to enroll groups of accounts by registering the entire organizational unit \(OU\) that contains the accounts\. You also can enroll accounts individually\. For more information on enrolling individual accounts, see [Enroll an existing AWS account](enroll-account.md)\.

**Terminology**
+ When you bring an existing organization into AWS Control Tower, it's called *registering* the organization, or *extending governance* to the organization\.
+ When you bring an AWS account into AWS Control Tower, it's called *enrolling* the account\.

**View your OUs and accounts**

On the AWS Control Tower **Organization** page, you can view all the OUs in your AWS Organizations, including OUs that are registered with AWS Control Tower and those that are not registered\. You can view nested OUs as part of the hierarchy\. An easy way to view your organizational units on the **Organization** page is to select **Organizational units only** from the dropdown at the upper right\.

The **Organization** page lists all accounts in your organization, regardless of OU or enrollment status in AWS Control Tower\. An easy way to view your accounts on the **Organization** page is to select **Accounts only** from the dropdown at the upper right\. You can view, update, and enroll accounts individually within the OUs, if the accounts meet the prerequisites for enrollment\.

If you do not select any filtering, the **Organization** page displays your accounts and OUs in a hierarchy\. It is a central location for monitoring and taking actions on all of your AWS Control Tower resources\. For more information about the **Organization** page, you can view the video walkthrough\.

## Video Walkthrough<a name="organization-page-video"></a>

This video \(4:01\) describes how to work with the **Organization** page in AWS Control Tower\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.

## <a name="topics-for-existing-orgs-and-accounts"></a>

**Topics**
+ [Register an existing organizational unit with AWS Control Tower](importing-existing.md)
+  [Enroll an existing AWS account](enroll-account.md)