# Troubleshooting<a name="troubleshooting"></a>

If you encounter issues while using AWS Control Tower, you can use the following information to resolve them according to our best practices\. If the issues you encounter are outside the scope of the following information, or if they persist after you've tried to resolve them, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Landing Zone Launch Failed<a name="setup-failed"></a>

If your master account is less than an hour old, then you may encounter issues when the additional accounts are created\.

**Action to take**  
If you encounter this issue, we recommend that you wait an hour, and then try again\.

If still not successfull, make sure you confirm all the e-mails sent to your root account to activate the needed services, have [all your STS endpoint regions enabled](https://console.aws.amazon.com/iam/home#/account_settings)\ and then try again\. When finished, don't forget to disable the STS endpoints you don't have intent to use\.

If the issue persists, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Don't Change Email Addresses Outside of AWS Control Tower<a name="email-change"></a>

The email addresses for your shared service accounts \(the master account, the auditing account, and the log archive account\) should never be changed\. If you've changed one of these email addresses, contact AWS Support\.

The email addresses for your member accounts created in Account Factory can be changed, but only by updating the account in Account Factory\. For more information, see [Updating Account Factory Accounts](account-factory.md#updating-account-factory-accounts)\.
