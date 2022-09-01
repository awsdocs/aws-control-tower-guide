# Unmanage an account<a name="unmanage-account"></a>

If you created an account in Account Factory or enrolled an AWS account, and you no longer want the account to be managed by AWS Control Tower in a landing zone, you can unmanage the account\. This can be done in the AWS Service Catalog console by an AWS SSO user in the **AWSAccountFactory** group\. For more information on AWS SSO users or groups, see [Managing Users and Access Through AWS Single Sign\-On](sso.md)\. The following procedure describes how to unmanage a member account\.

**To unmanage an enrolled account**

1. Open the AWS Service Catalog console in your web browser at [https://console.aws.amazon.com/servicecatalog](https://console.aws.amazon.com/servicecatalog)\.

1. From the left navigation pane, choose **Provisioned products list**\.

1. From the list of provisioned accounts, choose the name of the account that you want AWS Control Tower no longer to manage\.

1. On the **Provisioned product details** page, from the **Actions** menu, choose **Terminate**\.

1. From the dialog box that appears, choose **Terminate**\.
**Important**  
The word *terminate* is specific to AWS Service Catalog\. When you terminate an Account Factory account in AWS Service Catalog, the account is not closed\. This action removes the account from its OU and your landing zone\.

1.  When the account has been unmanaged, its status changes to **Not Enrolled**\.

1. If you no longer need the account, close it\. For information about closing AWS accounts, see [Closing an Account](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/close-account.html) in the *AWS Billing User Guide*

**Note**  
An unmanaged account is not closed or deleted\. When the account has been unmanaged, the AWS SSO user that you selected when you created the account in Account Factory still has administrative access to the account\. If you do not want this user to have administrative access, you must change this setting in AWS SSO by updating the account in Account Factory and changing the AWS SSO user email address for the account\. For more information, see [Update and move account factory accounts with AWS Control Tower or with AWS Service Catalog](updating-account-factory-accounts.md)\.

You can view an AWS [YouTube video](https://www.youtube.com/playlist?list=PLhr1KZpdzukdS9skEXbY0z67F-wrcpbjm) that explains how to remove and close down an account in AWS Control Tower\.