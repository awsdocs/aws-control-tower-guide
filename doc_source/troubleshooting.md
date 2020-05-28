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

## New Account Provisioning Failed<a name="account-provisioning-failed"></a>

 If you encounter this issue, check for these common causes\.

**When you filled out the account provisioning form, you may have:**
+ specified **tagOptions**,
+ enabled SNS notifications,
+ enabled provisioned product notifications\.

Try again to provision your account, without specifying any of those options\. For more information, see [Provisioning Account Factory Accounts With AWS Service Catalog](account-factory.md#provision-as-end-user)\.

**Other common causes for failure:**
+ If you created a provisioned product plan \(to view resource changes\), your account provisioning may remain in an **In progress** state indefinitely\.
+ Creation of a new account in Account Factory will fail while other AWS Control Tower configuration changes are in progress\. For example, while a process is running to add a guardrail to an OU, Account Factory will display an error message if you try to provision an account\.

**To check the status of a previous action in AWS Control Tower**
+ Navigate to **AWS CloudFormation > AWS StackSets**
+ Check each stack set related to AWS Control Tower \(prefix: "AWSControlTower"\)
+ Look for AWS StackSets operations that are still running\.

If your account provisioning takes longer than one hour, it's best to terminate the provisioning process and try again\.

## Failed to Enroll an Existing Account<a name="enrollment-failed"></a>

If you try once to enroll an existing AWS account and that enrollment fails, when you try a second time, the error message may tell you that the stack set exists\. To continue, you must remove the provisioned product in Account Factory\.

If the reason for the first enrollment failure was that you forgot to create the `AWSControlTowerExecution` role in the account in advance, the error message you'll receive correctly tells you to create the role\. However, when you try to create the role, you are likely to receive another error message stating that AWS Control Tower could not create the role\. This error occurs because the process has been partially completed\.

In this case, you must take two recovery steps before you can proceed with enrolling your existing account\. First, you must terminate the provisioned product in Account Factory\. Next, you must use the AWS Organizations console to manually move the account out of the OU and back to the root\. After that is done, create the `AWSControlTowerExecution` role in the account, and then fill in the **Enroll account** form again\. 

## Unable to Update an Account Factory Account<a name="w92aac51c11"></a>

You may encounter an issue that prevents you from updating a provisioned account\. You can see an error message similar to this one: `AWS Control Tower could not baseline VPC in the managed account because of existing resource dependencies.`

**Common cause:** AWS Control Tower always removes the AWS default VPC during initial provisioning\. To have an AWS default VPC in an account, you must add it after account creation\. AWS Control Tower has its own default VPC that replaces the AWS default VPC, unless you set up Account Factory the way the walkthrough shows you—\-so that AWS Control Tower doesn’t provision a VPC at all\. Then the account has no VPC\. You’d have to re\-add the AWS default VPC if you want to use that one\.

However, AWS Control Tower doesn't support the AWS default VPC\. Deploying one causes the account to enter a `Tainted` state\. When it is in that state, you cannot update the account through AWS Service Catalog\.

The `Tainted` state causes a follow\-on issue: An account that is not updated may prevent enabling guardrails on the OU of which it is a part\.

**Action to take:** You must delete the default VPC that you added, and then you will be able to update the account\.

## Received an Insufficient Permissions Error<a name="insufficient-permissions"></a>

It's possible that your account may not have the necessary permissions to perform certain work in certain AWS Organizations\. If you encounter the following type of error, check all the permissions areas, such as IAM or SSO permissions, to make sure your permission is not being denied from those places:

"You have insufficient permissions to perform AWS Organizations API actions\." 

If you believe your work requires the action you're attempting, and you can't locate any relevant restriction, contact your system administrator or [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Detective guardrails are not taking effect on accounts<a name="guardrail-errors"></a>

If you've recently expanded your AWS Control Tower deployment into a new AWS Region, newly\-applied detective guardrails do not take effect on new accounts you create **in any region** until the individual accounts within OUs governed by AWS Control Tower are updated\. Existing detective guardrails on existing accounts are still in effect\.

For example, if you’ve recently updated your AWS Control Tower landing zone for release 2\.3, which enables the Asia Pacific \(Sydney\) region, deploying the 2\.3 release updates the landing zone, but it does not update each individual account\.

If you have not updated your landing zone for AWS Control Tower release 2\.3, and if you do not require your workloads to run in the Asia Pacific \(Sydney\) region, consider remaining at release 2\.2\. As long as release 2\.2 is in effect, the behavior of deploying detective guardrails to accounts is unchanged\. 

**Action to take: Update accounts\.**

To update multiple individual accounts, you can use the APIs from AWS Service Catalog and the AWS CLI to automate the updates\. For more information about how to approach the update process, see this [Video Walkthrough](automated-provisioning-walkthrough.md#automated-provisioning-video)\.  You can substitute the **UpdateProvisionedProduct** API for the **ProvisionProduct** API shown in the video\.

 If you have further difficulties with enabling detective guardrails on your accounts, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## AWS Support<a name="getting-support"></a>

If you want to move your existing member accounts into a different support plan, you can sign in to each account with root account credentials, [compare plans](https://console.aws.amazon.com/support/plans/home#/), and set the support level that you prefer\. 

We recommend that you update the MFA and account security contacts when you make changes to your support plan\. 