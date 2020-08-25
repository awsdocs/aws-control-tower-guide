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

In this case, you must take two recovery steps before you can proceed with enrolling your existing account\. First, you must terminate the Account Factory provisioned product through the AWS Service Catalog console\. Next, you must use the AWS Organizations console to manually move the account out of the OU and back to the root\. After that is done, create the `AWSControlTowerExecution` role in the account, and then fill in the **Enroll account** form again\. 

## Unable to Update an Account Factory Account<a name="w123aac54c11"></a>

When an account is in an inconsistent state, it cannot be updated successfully from Account Factory or AWS Service Catalog\.

**Case 1: **You may encounter an error message similar to this one:

`AWS Control Tower could not baseline VPC in the managed account because of existing resource dependencies.`

**Common cause:** AWS Control Tower always removes the AWS default VPC during initial provisioning\. To have an AWS default VPC in an account, you must add it after account creation\. AWS Control Tower has its own default VPC that replaces the AWS default VPC, unless you set up Account Factory the way the walkthrough shows you—\-so that AWS Control Tower doesn’t provision a VPC at all\. Then the account has no VPC\. You’d have to re\-add the AWS default VPC if you want to use that one\.

However, AWS Control Tower doesn't support the AWS default VPC\. Deploying one causes the account to enter a `Tainted` state\. When it is in that state, you cannot update the account through AWS Service Catalog\.

**Action to take:** You must delete the default VPC that you added, and then you will be able to update the account\.

**Note**  
The `Tainted` state causes a follow\-on issue: An account that is not updated may prevent enabling guardrails on the OU of which it is a part\.

**Case 2: **You may see an error message similar to this one:

`AWS Control Tower detects that your enrolled account has been moved to a new organizational unit.`

**Common cause:** You attempted to move an account from one registered OU to another, but old AWS Config rules remain\. The account is in an inconsistent state\.

**Action to take:**

**If the account move was intended:**
+ Terminate the account in AWS Service Catalog\.
+ Enroll it again\.
+ *Context/impact:* Deployed AWS Config rules don't match the configuration dictated by the destination OU\.
+  AWS Config rules may remain from the previous OU, causing unintended spending\.
+ Attempts to re\-enroll or update the account will fail due to resource naming conflicts\.

**If the account move was unintended:**
+ Return the account to its original OU\.
+ Update the account from AWS Service Catalog\.
+ In the launch parameters, enter the OU that the account was originally in\.
+ *Context/impact:* If the account is not returned to its original OU, its state will be inconsistent with the guardrails dictated by the new OU it's in\.
+ Updating an account is not a valid remediation, because it does not delete the AWS Config rules associated with its previous OU\.

## Unable to Update Landing Zone<a name="unable-to-update-landing-zone"></a>

When an account is in a **Closed** or **Suspended** state, you may encounter an issue when you try to update your landing zone\. You must delete the provisioned product on every closed account before you perform an update to the landing zone\.

On the AWS Service Catalog provisioned product page, you may see an error message similar to this one:

`AWSControlTowerExecution role can't be assumed on the account.`

**Common cause:** You have suspended an account without deleting the provisioned product\.

**Action to take:** If you see this error, you have two options:

1. Contact AWS Support and reopen the account, delete the provisioned product, then close the account again\.

1. Remove the resources from the StackSets that have been orphaned because of the account closure\. \(This option is available only if the StackSets have instances in **Current** state that you are not removing\.\)

**To remove the resources from the StackSets, do this for each closed account:**
+ Go into each of the AWS Control Tower StackSets and remove the StackInstances from every region, for the account that has been closed\.
+ **IMPORTANT:** Choose the **Retain Stack** option so the StackSet removes only the stack instances\. StackSet can't assume a role from the closed account, so it will fail if it tries to assume the `AWSControlTowerExecution` role, which leads to the error message you received\.

## Failure Error that Mentions AWS Config<a name="aws-config-error"></a>

If AWS Config is enabled in any AWS Region supported by AWS Control Tower, you may receive an error message because a pre\-check has failed\. The message might not seem to explain the problem adequately, due to some underlying behavior of AWS Config\.

**You may receive an error message, similar to one of these:**
+ `AWS Control Tower cannot create an AWS Config delivery channel because one already exists. To continue, delete the existing delivery channel and try again .`
+ `AWS Control Tower cannot create an AWS Config configuration recorder because one already exists. To continue, delete the existing delivery channel and try again .`

**Common cause:** When the AWS Config service is enabled on an AWS account, it creates a configuration recorder and delivery channel with a default naming\. If you disable the AWS Config service through the console, it does not delete the configuration recorder or the delivery channel\. You must delete them through the CLI\. If the AWS Config service is enabled in any one of the Regions supported by AWS Control Tower, it can result in this failure\.

**Action to take:** Delete the configuration recorder and delivery channel in all supported regions\. Disabling AWS Config is not enough, the configuration recorder and delivery channel must be deleted by means of the CLI\. After you’ve deleted the configuration recorder and delivery channel from the CLI, you can try again to launch AWS Control Tower and enroll the account\.

If you are in the process of deploying a provisioned product, you must delete the provisioned product before you retry\. Otherwise, you may see an error message similar to this one:
+ `An error occurred (InvalidParametersException) when calling the ProvisionProduct operation: A stack named Stackname already exists.`

In the message, *`Stackname`* specifies the name of the stack\.

Here are some example AWS Config CLI commands you can use to determine the status of your configuration recorder and delivery channel\.

**View commands:**
+ `aws configservice describe-delivery-channels`
+ `aws configservice describe-delivery-channel-status`
+ `aws configservice describe-configuration-recorders`
+ `The normal response is something like "name": "default"`

**Delete commands:**
+ `aws configservice stop-configuration-recorder --configuration-recorder-name NAME-FROM-DESCRIBE-OUTPUT`
+ `aws configservice delete-delivery-channel --delivery-channel-name NAME-FROM-DESCRIBE-OUTPUT`
+ `aws configservice delete-configuration-recorder --configuration-recorder-name NAME-FROM-DESCRIBE-OUTPUT`

**For more information, see the AWS Config documentation**
+  [Managing the Configuration Recorder \(AWS CLI\)](https://docs.aws.amazon.com/config/latest/developerguide/stop-start-recorder.html#managing-recorder_cli)
+   [Managing the Delivery Channel](https://docs.aws.amazon.com/config/latest/developerguide/manage-delivery-channel.html)

## No Launch Paths Found Error<a name="no-launch-paths-found"></a>

When you're trying to create a new account, you may see an error message similar to this one:

`No launch paths found for resource: prod-dpqqfywxxxx`

This error message is generated by AWS Service Catalog, which is the integrated service that helps provision accounts in AWS Control Tower\.

**Common Causes:**
+ You may be logged in as root\. AWS Control Tower does not support creating accounts when you're logged in as root\.
+ Your SSO user has not been added to the appropriate permission group\.
+ If you are authenticated as an IAM user, you must add it to the AWS Service Catalog portfolio so that it has the correct permissions\.

## Received an Insufficient Permissions Error<a name="insufficient-permissions"></a>

It's possible that your account may not have the necessary permissions to perform certain work in certain AWS Organizations\. If you encounter the following type of error, check all the permissions areas, such as IAM or SSO permissions, to make sure your permission is not being denied from those places:

"You have insufficient permissions to perform AWS Organizations API actions\." 

If you believe your work requires the action you're attempting, and you can't locate any relevant restriction, contact your system administrator or [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Detective guardrails are not taking effect on accounts<a name="guardrail-errors"></a>

If you've recently expanded your AWS Control Tower deployment into a new AWS Region, newly\-applied detective guardrails do not take effect on new accounts you create **in any region** until the individual accounts within OUs governed by AWS Control Tower are updated\. Existing detective guardrails on existing accounts are still in effect\.

For example, if you’ve recently updated your AWS Control Tower landing zone for release 2\.3, which enables the Asia Pacific \(Sydney\) region, deploying the 2\.3 release updates the landing zone, but it does not update each individual account\.

If you have not updated your landing zone for AWS Control Tower release 2\.3, and if you do not require your workloads to run in the Asia Pacific \(Sydney\) region, consider remaining at release 2\.2\. As long as release 2\.2 is in effect, the behavior of deploying detective guardrails to accounts is unchanged\.

If you try to enable a detective guardrail before updating your accounts, you may see an error message similar to this one:

`AWS Control Tower can't enable the selected guardrail on this OU. AWS Control Tower cannot apply the guardrail on the OU ou-xxx-xxxxxxxx, because child accounts have dependencies that are missing. Update all child accounts under the OU, then try again.`

**Action to take: Update accounts\.**

To update multiple individual accounts, you can use the APIs from AWS Service Catalog and the AWS CLI to automate the updates\. For more information about how to approach the update process, see this [Video Walkthrough](automated-provisioning-walkthrough.md#automated-provisioning-video)\.  You can substitute the **UpdateProvisionedProduct** API for the **ProvisionProduct** API shown in the video\.

 If you have further difficulties with enabling detective guardrails on your accounts, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## Failure to move an Account Factory account directly from one AWS Control Tower landing zone to another AWS Control Tower landing zone<a name="failure-to-move"></a>

**Warning**  
This practice is not recommended\. It does not meet one of the prerequisites for account enrollment\. If you have tried to do this unsupported action and you find yourself receiving multiple error messages, here is some information that might be helpful\.

To move an account that you’ve provisioned through Account Factory into another landing zone that’s managed by AWS Control Tower, under another master account, you must remove all of the IAM roles and the stacks associated with that account from the original OU\. Remove these resources from every region in which the account is deployed\.

**Note**  
The best way to remove the resources is to deprovision the account in its original OU before you try to move it\.

If you don’t remove the resources, enrollment into the new OU will fail, somewhat spectacularly\. You may encounter one or more error messages, and you will keep receiving similar error messages until the remaining roles and stacks are removed from every region in which the account was deployed\.

Each time you receive an error message, you must remove the account from the new OU, delete the old resource that is the subject of the error message, and then attempt to move the account back into the new OU\. This process of removing\-and\-deleting must be repeated for every remaining resource, for every region in which the account was deployed, possibly 10 or 20 times\. These repeated errors occur because the account was provisioned into an OU with an SCP that prevents IAM role deletion\. You can make the recovery process shorter by deleting all the account's resources before you retry\.

The examples below represent the types of failure messages you may receive if undeleted roles and stacks remain\. You would most likely see one of these messages at a time, for each time you attempt to enroll the account, as long as old resources remain\. 

 The values of the resource ID strings have been modified for the examples\. Their values will not be the same in an error message you may receive\. You may see a message similar to the following examples:
+ `AWS Control Tower cannot create the IAM role aws-controltower-AdministratorExecutionRole because the role already exists. To continue, delete the existing IAM role and try again.`
+ `AWS Control Tower cannot create the IAM role aws-controltower-ConfigRecorderRole because the role already exists. To continue, delete the existing IAM role and try again.`
+ `AWS Control Tower cannot create the IAM role aws-controltower-ForwardSnsNotificationRole because the role already exists. To continue, delete the existing IAM role and try again.`

 Or you may see an error message about a stack set failure, similar to this one:

```
 
"Error\":\"StackSetFailState\",
\"Cause\":\"StackSetOperation on AWSControlTowerBP-BASELINE-CLOUDWATCH 
with id 8aXXXXf5-e0XX-4XXa-bc4XX-dXXXXXee31 
has reached SUCCEEDED state but has 1 NON-CURRENT stack instances; 
here is the summary :{ StackSet Id: 
AWSControlTowerBP-BASELINE-CLOUDWATCH:40XXXbf2-Xead-46a1-XXXa-eXXXXecb2ee2, 
Stack instance Id: 
arn:aws:cloudformation:eu-west-1:1X23456789XX:
            stack/StackSet-AWSControlTowerBP-BASELINE-CLOUDWATCH-4feXXXXXX-ecXX-XXc6-bXXX-4ae678/4feXXXXXX-ecX-4ae123458, 
Status: OUTDATED, 
Status Reason: ResourceLogicalId:ForwardSnsNotification, 
ResourceType:AWS::Lambda::Function, 
ResourceStatusReason:aws-controltower-NotificationForwarder already exists in stack 
arn:aws:cloudformation:eu-west-1:1X23456789XX:
            stack/StackSet-AWSControlTowerBP-BASELINE-CLOUDWATCH-4feXXXXXX-ecXX-XXc6-bXXX-4ae678/4feXXXXXX-ecX-4ae123458.
```

After all of the remaining resources are removed from the first OU, you’ll be able to invite, provision, or enroll the account into the new OU successfully\.

## AWS Support<a name="getting-support"></a>

If you want to move your existing member accounts into a different support plan, you can sign in to each account with root account credentials, [compare plans](https://console.aws.amazon.com/support/plans/home#/), and set the support level that you prefer\. 

We recommend that you update the MFA and account security contacts when you make changes to your support plan\. 