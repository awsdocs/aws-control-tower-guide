# Enroll an existing AWS account<a name="enroll-account"></a>

You can extend AWS Control Tower governance to an individual, existing AWS account when you *enroll* it into an organizational unit \(OU\) that's already governed by AWS Control Tower\. Eligible accounts exist in *unregistered OUs that are part of the same AWS Organizations organization* as the AWS Control Tower OU\. 

**Set Up Trusted Access First**

Before you can enroll an existing AWS account into AWS Control Tower you must give permission for AWS Control Tower to manage, or *govern*, the account\. Specifically, AWS Control Tower requires permission to establish trusted access between AWS CloudFormation and AWS Organizations on your behalf, so that AWS CloudFormation can deploy your stack automatically to the accounts in your selected organization\.

 To learn more about trusted access and AWS CloudFormation StackSets, see [AWS CloudFormation StackSets and AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-cloudformation.html)\. When trusted access is enabled, AWS CloudFormation can create, update, or delete stacks across multiple accounts and AWS Regions with a single operation\. AWS Control Tower relies on this trust capability so it can apply roles and permissions to existing accounts before it moves them into a registered organizational unit and thereby brings them under governance\.

**What Happens During Account Enrollment**

During the enrollment process, AWS Control Tower performs these actions:
+ Baselines the account, which includes deploying these stack sets:
  + ` AWSControlTowerBP-BASELINE-CLOUDTRAIL`
  + `AWSControlTowerBP-BASELINE-CLOUDWATCH`
  + `AWSControlTowerBP-BASELINE-CONFIG`
  + `AWSControlTowerBP-BASELINE-ROLES`
  + `AWSControlTowerBP-BASELINE-SERVICE-ROLES`

  It is a good idea to review the templates of these stack sets and make sure that they don’t conflict with your existing policies\.
+ Identifies the account through AWS Single Sign\-On or AWS Organizations\.
+ Places the account into the OU that you've specified\. Be sure to apply all SCPs that are applied in the current OU, so that your security posture remains consistent\.
+ Applies mandatory guardrails to the account by means of the SCPs that apply to the selected OU as a whole\.
+ Adds the AWS Config rules that apply the AWS Control Tower detective guardrails to the account\.

**Note**  
When you enroll the account into AWS Control Tower, your account is governed by the AWS CloudTrail trail for the new organization\. If you have an existing deployment of a CloudTrail trail, you may see duplicate charges unless you delete the existing trail for the account before you enroll it in AWS Control Tower\.

**Enrolling Existing Accounts With VPCs**

AWS Control Tower handles VPCs differently when you provision a new account in Account Factory than when you enroll an existing account\.
+ When you create a new account, AWS Control Tower automatically removes the AWS default VPC and creates a new VPC for that account\.
+ When you enroll an existing account, AWS Control Tower does not create a new VPC for that account\.
+ When you enroll an existing account, AWS Control Tower does not remove any existing VPC or AWS default VPC associated with the account\.

**Recommended: You can set up a two\-step approach to account enrollment**
+ First, use an AWS Config *conformance pack* to evaluate how your accounts may be affected by some AWS Control Tower guardrails\. To determine how enrollment into AWS Control Tower may affect your accounts, see [AWS Control Tower Detective Guardrails as an AWS Config Conformance Pack](http://aws.amazon.com/blogs/mt/aws-control-tower-detective-guardrails-as-an-aws-config-conformance-pack)\. 
+ Next, you may wish to enroll the account\. If the compliance results are satisfactory, the migration path is easier because you can enroll the account without unexpected consequences\.
+ After you've done your evaluation, if you decide to set up an AWS Control Tower landing zone, you may need to remove the AWS Config delivery channel and configuration recorder that were created for your evaluation\. Then you'll be able to set up AWS Control Tower successfully\.

**Note**  
The conformance pack also works in situations where the accounts are located in OUs registered by AWS Control Tower, but the workloads run within AWS Regions that don’t have AWS Control Tower support\. You can use the conformance pack to manage resources in accounts that exist in Regions where AWS Control Tower is not deployed\.

## Prerequisites for Enrollment<a name="enrollment-prerequisites"></a>

These prerequisites are required before you can enroll an account in AWS Control Tower:

1. The account must not have an AWS Config configuration recorder or delivery channel\. These must be deleted through the AWS CLI before you can enroll an account\. Otherwise, enrollment will fail\.

1. The account that you wish to enroll must exist in the same AWS Organizations organization as the AWS Control Tower master account\. The account that exists can be enrolled *only* into the same organization as the AWS Control Tower master account, in an OU that already is registered with AWS Control Tower\. 

1. Before you can enroll an existing account in AWS Control Tower, the account must have the following roles, permissions, and trust relationships in place\. Otherwise, enrollment will fail\.

   Role Name: `AWSControlTowerExecution`

   Role Permission: `AdministratorAccess` \(AWS managed policy\)

   Role Trust Relationship:

   ```
           
           {
           "Version": "2012-10-17",
           "Statement": [
            {
           "Effect": "Allow",
           "Principal": {
           "AWS": "arn:aws:iam::Master Account ID:root"
                },
               "Action": "sts:AssumeRole",
               "Condition": {}
             }
             ]
            }
   ```

To check other prerequisites for enrollment, see [Getting Started with AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-with-control-tower.html)\.

**Note**  
When you enroll an account into AWS Control Tower, your account is governed by the AWS CloudTrail trail for the AWS Control Tower organization\. If you have an existing deployment of a CloudTrail trail, you may see duplicate charges unless you delete the existing trail for the account before you enroll it in AWS Control Tower\.

## <a name="enrollment-steps"></a>

After the **AdministratorAccess** permission is in place in your existing account, follow these steps to enroll the account:

**To enroll an individual account in AWS Control Tower**
+ Navigate to the AWS Control Tower Account Factory page and select **Enroll account**\.
+ Specify the current email address of the existing account you'd like to enroll in AWS Control Tower\.
+ Specify the first and last name of the account owner\.
+ Specify the organizational unit \(OU\) in which you'd like to enroll the account\.
+ Choose **Enroll account**\.

**Common Causes for Failure of Enrollment**
+ Your IAM principal may lack the necessary permissions to provision an account\. To enroll an existing account, the **AWSControlTowerExecution** role must be present in the account you're enrolling\.
+ AWS Security Token Service \(AWS STS\) is disabled in your AWS account in your home region, or in any region supported by AWS Control Tower\.
+ You may be signed in to an account that needs to be added to the Account Factory Portfolio in AWS Service Catalog\. The account must be added before you'll have access to Account Factory so you can create or enroll an account in AWS Control Tower\. If the appropriate user or role is not added to the Account Factory Portfolio, you’ll receive an error when you attempt to add an account\.
+ You may be signed in as root\.
+ The account you're trying to enroll may have AWS Config settings that are residual\. In particular, the account must not have a configuration recorder or delivery channel, so these must be deleted through the AWS CLI before you can enroll an account\.
+ If the account belongs to another OU with a master account, including another AWS Control Tower OU, you must terminate the account in its current OU before it can join another OU\. Existing resources must be removed in the original OU\. Otherwise, enrollment will fail\.

For more information about how AWS Control Tower works with roles when you're creating new accounts or enrolling existing accounts, see [How AWS Control Tower Works With Roles to Create and Manage Accounts ](how-control-tower-works.md#roles-how)\.

## What if the account does not meet the prerequisites?<a name="fulfill-prerequisites"></a>

To fulfill the prerequisites for account enrollment, you can follow these preparatory steps to move an account into the same organization as AWS Control Tower\.

**Preparatory steps to bring an account into the same organization as AWS Control Tower**

1. Drop the account from its existing organization\. \(You must provide a separate payment method if you use this approach\.\)

1. Invite the account into the AWS Control Tower organization\.

1. Accept the invitation\. \(The account shows up in the root of the organization\.\) This step moves the account into the same organization as AWS Control Tower\. It establishes SCPs and consolidated billing\.

1. Now you must fulfill the remaining enrollment prerequisites:
   + Create the necessary role\.
   + Clear out the default VPC\. \(This part is optional—AWS Control Tower does not change your existing default VPC\.\)
   + Delete the AWS Config configuration recorder or delivery channel through the CLI if one exists\.
   + Any other prerequisites, as needed\.

1. Enroll the account into AWS Control Tower\. This step brings the account into full AWS Control Tower governance\.

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

**Note**  
You can send the invitation for the new organization before the account drops out of the old organization\. The invitation will be waiting when the account drops out of its existing organization\.

**Moving from a different organization and master account**
+ **Moving from a different master account**: This practice is not recommended\. It may be easier to create a new account\. If the account you wish to enroll was previously created or enrolled in another organization with a different AWS Control Tower landing zone and master account, you must deprovision the account \(and all of its resources\) in its existing OU before you move it to the new OU\. Otherwise, enrollment will fail, and it may be very time consuming to repair the error\. See [Failure to move an Account Factory account directly from one AWS Control Tower landing zone to another AWS Control Tower landing zone](troubleshooting.md#failure-to-move)\.
+ **Moving from a registered OU to another registered OU**: This action causes you to receive a notification similar to this one:

  `AWS Control Tower detects that your enrolled account has been moved to a new organizational unit.`

  This action places the account into an inconsistent state, and it cannot be updated\. Instead of using AWS Service Catalog to move the account, terminate the account in the previous OU\. It is not an option to create an account in one registered OU and then move it to another registered OU\. Because of resources that remain in the original OU, you may incur unintended charges\.

### <a name="steps-to-deprovision-resources"></a>

**Steps for deprovisioning an account from an OU so it can be enrolled, keeping its stack**

1. Optionally, to keep the applied CFN, delete the stack instance from the stack sets, making sure to choose **Retain stacks** for the instance\.

1. Terminate the account provisioned product in AWS Service Catalog Account Factory\. \(This step only removes the provisioned product from AWS Control Tower, it does not actually delete the account\.\)

1. Optionally, set up the account with the necessary billing details, as required for any account that does not belong to an organization, then remove the account from the organization\. You would do this so that the account does not count against the total in your AWS Organizations quota\.

1. Clean up the account, if resources remain, and close it, following account closure steps given in [Unmanaging a Member Account](account-factory.md#unmanage-account)\.

1. If you have a **Suspended** OU with defined guardrails, you can move the account there instead of doing Step 1\.

## Manually add the required IAM role to an existing AWS account and enroll it<a name="enroll-manually"></a>

If you’ve already set up your AWS Control Tower landing zone, you can begin enrolling your organization’s accounts into an OU that is registered with AWS Control Tower\. If you haven't set up your landing zone, follow the steps as described in the AWS Control Tower User Guide at [Getting Started, Step 2](https://docs.aws.amazon.com/             controltower/latest/userguide/getting-started-with-control-tower.html#step-two)\. After the landing zone is ready, complete the following steps to bring existing accounts into governance by AWS Control Tower, manually\.

**Be sure to review the prerequisites noted previously in this chapter\.**

Before enrolling an account with AWS Control Tower, you must give AWS Control Tower permission to manage that account\. To do so, you’ll add a role that has full access to the account, as shown in the steps that follow\. These steps must be performed for each account that you enroll\.

**For each account:**

**Step 1: Sign in with administrator access to the master account of the organization that currently contains the account you wish to enroll\.**

For example, if you created this account from AWS Organizations and you use a cross\-account IAM role to sign in, then you may follow these steps:

1. Sign into your organization’s master account\.

1. Go to **AWS Organizations**\.

1. Under **Accounts**, select the account you want to enroll and copy its account ID\.

1. Open the account dropdown menu on the top navigation bar and choose **Switch Role**\.

1. On the **Switch role** form, fill in the following fields:
   + Under **Account**, enter the account ID you copied\.
   + Under **Role**, enter the name of the IAM role that enables cross\-account access to this account\. The name of this role was defined when the account was created\. If you did not specify a role name when you created the account, enter the default role name, `OrganizationAccountAccessRole`\.

1. Choose **Switch Role**\.

1. You should now be signed into the AWS management console as the child account\.

1. When you’re finished, stay in the child account for the next part of the procedure\.

1. Make note of the master account ID, because you will need to enter it in the next step\.

**Step 2: Give AWS Control Tower permission to manage the account\.**

1. Go to **IAM**\.

1. Go to **Roles**\.

1. Choose **Create role**\.

1. When asked to select which service the role is for, select **EC2** and choose **Next:Permissions**\. You will change this to “AWS Control Tower” later\.

1. When asked to attach policies, choose **AdministratorAccess**\.

1. Choose **Next:Tags**\.

1. You may see an optional screen titled **Add tags**\. Skip this screen for now by choosing **Next:Review**

1. On the **Review** screen, in the **Role name** field, enter `AWSControlTowerExecution`\.

1. Enter a brief description in the **Description** box, such as *Allows full account access for enrollment\.*

1. Choose **Create role**\.

1. Navigate to the role you just created\. Choose **Roles** on the left\. Select `AWSControlTowerExecution`\.

1. Under **Trust relationships**, choose **Edit trust relationship**\.

1. Copy the code example shown here and paste it into the Policy Document\. Replace the string *`Master Account ID`* with the actual master account ID of your master account\. Here is the policy to paste:

   ```
     {
           "Version": "2012-10-17",
           "Statement": [
            {
           "Effect": "Allow",
           "Principal": {
           "AWS": "arn:aws:iam::Master Account ID:root"
                },
               "Action": "sts:AssumeRole",
               "Condition": {}
             }
             ]
            }
   ```

**Step 3: Enroll the account by moving it into a registered OU, and verify enrollment\.**

After you’ve set up the necessary permissions by creating the role, follow these steps to enroll the account and verify enrollment\.

1. **Sign in again as Admin and go to AWS Control Tower\.**

1. 

**Enroll the account\.**
   + From the Account Factory page in AWS Control Tower, choose **Enroll account**\. Fill in the required fields\. Use the email address associated with the account you just updated\.
     + Specify the current email address of the existing account you'd like to enroll in AWS Control Tower\.
     + Specify the first and last name of the account owner\.
     + Specify the organizational unit \(OU\) in which you'd like to enroll the account\.
   + Choose **Enroll account**\.

1. 

**Verify enrollment\.**
   + From AWS Control Tower, choose **Accounts**\.
   + Look for the account you have recently enrolled\. Its initial state will show a status of **Enrolling**\.
   + When the state changes to **Enrolled**, the move was successful\.

To continue this process, sign into each account in your organization that you want to enroll in AWS Control Tower\. Repeat the prerequisite steps and the enrollment steps for each account\.

## Automated Enrollment of AWS Organizations Accounts<a name="automated-account-enrollment"></a>

You can use the enrollment method described in a blog post called **[ Enroll existing AWS accounts into AWS Control Tower](http://aws.amazon.com/blogs/field-notes/enroll-existing-aws-accounts-into-aws-control-tower/)** to enroll your AWS Organizations accounts into AWS Control Tower with a programmatic process\.