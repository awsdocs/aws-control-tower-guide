# Close an account created in Account Factory<a name="delete-account"></a>

Accounts created in Account Factory are AWS accounts\. For information about closing AWS accounts, see [Closing an Account](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/close-account.html) in the *AWS Billing User Guide*\.

**Note**  
 Closing an AWS account is not the same as unmanaging an account from AWS Control Tower—these are separate actions\. You must unmanage the account before you close it\.

## Close an AWS Control Tower member account through AWS Organizations<a name="close-account-with-orgs-api"></a>

You can close your AWS Control Tower member accounts from your organization’s management account without a requirement to sign in to each member account individually with root credentials, by means of AWS Organizations\. You cannot close your management account in this way, however\. 

When you call the AWS Organizations [CloseAccount API](https://docs.aws.amazon.com/organizations/latest/APIReference/API_CloseAccount.html), or close an account in the AWS Organizations console, the member account is isolated for 90 days, as any AWS account would be\. The account shows a **Suspended** status in AWS Control Tower and AWS Organizations\. If you attempt to work with the account during that 90 days, AWS Control Tower gives an error message\.

Before the 90 days expire, you can restore the member account, as you can do with any AWS account\. After that 90\-day time, the account’s records are removed\.

We recommend, as a best practice, to unmanage a member account before you close that account\. If you close a member account without first unmanaging it, AWS Control Tower shows the account’s status as **Suspended**, but also as **Enrolled**\. As a result, if you attempt to **Re\-register** the account's OU during that 90\-day time, AWS Control Tower produces an error message\. The suspended account essentially blocks the re\-registering actions with a pre\-check failure\. If you remove the account from the OU, you can **Re\-register** the OU, but AWS may produce an error regarding a missing method of payment for the account\. To work around this constraint, create another OU, and move the account to that OU before you try to re\-register\. We recommend naming this OU the **Suspended** OU\.

**Note**  
If you do not unmanage the account before you close it, you must delete the account's provisioned product in AWS Service Catalog after those 90 days are finished\.

For more information, see the AWS Organizations documentation about the [CloseAccount API](https://docs.aws.amazon.com/organizations/latest/APIReference/API_CloseAccount.html)\.