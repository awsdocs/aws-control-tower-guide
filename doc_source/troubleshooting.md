# Troubleshooting<a name="troubleshooting"></a>

If you encounter issues while using AWS Control Tower, you can use the following information to resolve them according to our best practices\. If the issues you encounter are outside the scope of the following information, or if they persist after you've tried to resolve them, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Landing Zone Launch Failed<a name="setup-failed"></a>

Common causes of landing zone launch failure:
+ Lack of response to a confirmation email message\.
+ AWS CloudFormation StackSet failure\.

**Confirmation email messages**: If your master account is less than an hour old, you may encounter issues when the additional accounts are created\.

**Action to take**  
If you encounter this issue, check your email\. You might have been sent confirmation email that is awaiting response\. Alternatively, we recommend that you wait an hour, and then try again\. If the issue persists, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\. 

**Failed StackSets**: Another possible cause of landing zone launch failure is AWS CloudFormation StackSet failure\. AWS Security Token Service \(STS\) regions must be enabled in the master account for all AWS Regions in which AWS Control Tower is supported, so that the provisioning can be successful; otherwise, stack sets will fail to launch\. 

**Action to take**  
Be sure to enable all of your required AWS Security Token Service [ \(STS\) endpoint regions](https://console.aws.amazon.com/iam/home#/account_settings) before you launch AWS Control Tower\.

Currently, AWS Control Tower is supported in the following AWS Regions:
+ US East \(N\. Virginia\)
+ US East \(Ohio\)
+ US West \(Oregon\)
+ Europe \(Ireland\)
+ Asia Pacific \(Sydney\)

## Account Provisioning Failed<a name="account-provisioning-failed"></a>

 If you encounter this issue, check for these common causes\.

**When you filled out the account provisioning form, you may have:**
+ specified **tagOptions**,
+ enabled SNS notifications,
+ enabled provisioned product notifications\.

Try again to provision your account, without specifying any of those options\. For more information, see [Provisioning Account Factory Accounts With AWS Service Catalog](account-factory.md#provision-as-end-user)\.

**Another common cause for failure:**
+ If you created a provisioned product plan \(to view resource changes\), your account provisioning may remain in an **In progress** state indefinitely\.

If your account provisioning takes longer than one hour, it's best to terminate the provisioning process and try again\.

## Error When Changing Email Addresses<a name="email-change"></a>

The email addresses for your shared service accounts \(the master account, the auditing account, and the log archive account\) may be changed through the AWS billing page, and then you must update your landing zone for the changes to take effect\. For more information, see [How do I change the email address associated with my AWS account?](http://aws.amazon.com/premiumsupport/knowledge-center/change-email-address/)

The email addresses for your member accounts created in Account Factory cannot be changed\. If you have changed a member account email, you’ll receive an error message from the AWS Service Catalog, and your provisioned product will be in an unknown or **TAINTED** state\.

**If you’ve changed the email address for a member account that was created in Account Factory, follow these steps to repair the situation:**
+ Restore the account email of the managed account in question to what it was originally\. The AWS Control Tower console should still show the old email for the account\.
+ Update the provisioned product, keeping the same email address that was used when the account was originally created\.

If you still receive an error message, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

For more information about updating accounts, see [Updating and Moving Account Factory Accounts](account-factory.md#updating-account-factory-accounts)\.

## Don't Migrate Your Account's Organizational Unit Outside of AWS Control Tower<a name="ou-change"></a>

To migrate an account's organizational unit in AWS Control Tower, use the instructions for updating an account in Account Factory\. In step 4\(e\), choose the name of the new Organizational Unit for the account, instead of the name of the current Organizational Unit\.

For more information, see [Updating and Moving Account Factory Accounts](account-factory.md#updating-account-factory-accounts)\.

## Received an Insufficient Permissions Error<a name="insufficient-permissions"></a>

It's possible that your account may not have the necessary permissions to perform certain work in certain AWS Organizations\. If you encounter the following type of error, check all the permissions areas, such as IAM or SSO permissions, to make sure your permission is not being denied from those places:

"You have insufficient permissions to perform AWS Organizations API actions\." 

If you believe your work requires the action you're attempting, and you can't locate any relevant restriction, contact your system administrator or [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Detective guardrails are not taking effect on accounts<a name="guardrail-errors"></a>

If you've recently expanded your AWS Control Tower deployment into a new AWS Region, newly\-applied detective guardrails do not take effect on new accounts you create **in any region** until the individual accounts within OUs governed by AWS Control Tower are updated\. Existing detective guardrails on existing accounts are still in effect\.

For example, if you’ve recently updated your AWS Control Tower landing zone for release 2\.3, which enables the Asia Pacific \(Sydney\) region, deploying the 2\.3 release updates the landing zone, but it does not update each individual account\.

If you have not updated your landing zone for AWS Control Tower release 2\.3, and if you do not require your workloads to run in the Asia Pacific \(Sydney\) region, consider remaining at release 2\.2\. As long as release 2\.2 is in effect, the behavior of deploying detective guardrails to accounts is unchanged\. 

**Action to take: Update accounts\.**

To update multiple individual accounts for release 2\.3, you can use the APIs from AWS Service Catalog and the AWS CLI to automate the updates\. For more information about how to approach the update process, see this [Video Walkthrough](automated-provisioning-walkthrough.md#automated-provisioning-video)\.  You can substitute the **UpdateProvisionedProduct** API for the **ProvisionProduct** API shown in the video\.

 If you have further difficulties with enabling detective guardrails on your accounts, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.


## AWS Support<a name="getting-support"></a>

If you want to move your existing member accounts into a different support plan, you can sign in to each account with root account credentials, [compare plans](https://console.aws.amazon.com/support/plans/home#/), and set the support level that you prefer\. 

We recommend that you update the MFA and account security contacts when you make changes to your support plan\. 
