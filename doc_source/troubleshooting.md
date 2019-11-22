# Troubleshooting<a name="troubleshooting"></a>

If you encounter issues while using AWS Control Tower, you can use the following information to resolve them according to our best practices\. If the issues you encounter are outside the scope of the following information, or if they persist after you've tried to resolve them, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Landing Zone Launch Failed<a name="setup-failed"></a>

If your master account is less than an hour old, you may encounter issues when the additional accounts are created\.

**Action to take**  
If you encounter this issue, check your email\. You might have been sent confirmation email that is awaiting response\. Alternatively, we recommend that you wait an hour, and then try again\. If the issue persists, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\. 

## Don't Change Email Addresses Outside of AWS Control Tower<a name="email-change"></a>

The email addresses for your shared service accounts \(the master account, the auditing account, and the log archive account\) should never be changed\. If you've changed one of these email addresses, contact AWS Support\.

The email addresses for your member accounts created in Account Factory can be changed, but only by updating the account in Account Factory\. For more information, see [Updating Account Factory Accounts](account-factory.md#updating-account-factory-accounts)\.

## Don't Migrate Your Account's Organizational Unit Outside of AWS Control Tower<a name="ou-change"></a>

To migrate an account's organizational unit in AWS Control Tower, use the instructions for updating an account in Account Factory\. In step 4\(e\), choose the name of the new Organizational Unit for the account, instead of the name of the current Organizational Unit\.

For more information, see [Updating Account Factory Accounts](account-factory.md#updating-account-factory-accounts)\.

## AWS Support<a name="getting-support"></a>

 By default, your AWS Control Tower master account is created with the [Enterprise support](https://aws.amazon.com/premiumsupport/plans/enterprise/) plan\. Provisioned user accounts are launched with the Basic support plan\. They do not inherit the support plan from your AWS Control Tower master account\. 

If you want to move your existing member accounts into a different support plan, the quickest way is to send the account list to your master account's [Support Concierge](https://aws.amazon.com/premiumsupport/plans/enterprise/) and request that they make the support change for your member accounts\. 

If you don't have AWS Enterprise Support, you can sign in to each account with root account credentials, [compare plans](https://console.aws.amazon.com/support/plans/home#/), and set the support level that you prefer\. 

We recommend that you update the MFA and account security contacts when you make changes to your support plan\. 