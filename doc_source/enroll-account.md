# Enroll an existing AWS account<a name="enroll-account"></a>

You can extend AWS Control Tower governance to an individual, existing AWS account when you *enroll* it into an organizational unit \(OU\) that's already governed by AWS Control Tower\. Eligible accounts exist in *unregistered OUs that are part of the same AWS Organizations organization* as the AWS Control Tower OU\. 

**Note**  
You cannot enroll an existing account to serve as your Audit or Log Archive account except during initial landing zone setup\.

**Set up trusted access first**

Before you can enroll an existing AWS account into AWS Control Tower you must give permission for AWS Control Tower to manage, or *govern*, the account\. Specifically, AWS Control Tower requires permission to establish trusted access between AWS CloudFormation and AWS Organizations on your behalf, so that AWS CloudFormation can deploy your stack automatically to the accounts in your selected organization\. With this trusted access, the `AWSControlTowerExecution` role conducts activites required to manage each account\. That's why you must add this role to each account before you enroll it\.

 When trusted access is enabled, AWS CloudFormation can create, update, or delete stacks across multiple accounts and AWS Regions with a single operation\. AWS Control Tower relies on this trust capability so it can apply roles and permissions to existing accounts before it moves them into a registered organizational unit, and thereby brings them under governance\.

To learn more about trusted access and AWS CloudFormation StackSets, see [AWS CloudFormation StackSets and AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-cloudformation.html)\. 

## What happens during account enrollment<a name="what-happens-during-account-enrollment"></a>

During the enrollment process, AWS Control Tower performs these actions:
+ Baselines the account, which includes deploying these stack sets:
  + ` AWSControlTowerBP-BASELINE-CLOUDTRAIL`
  + `AWSControlTowerBP-BASELINE-CLOUDWATCH`
  + `AWSControlTowerBP-BASELINE-CONFIG`
  + `AWSControlTowerBP-BASELINE-ROLES`
  + `AWSControlTowerBP-BASELINE-SERVICE-ROLES`
  + `AWSControlTowerBP-VPC-ACCOUNT-FACTORY-V1`

  It is a good idea to review the templates of these stack sets and make sure that they don’t conflict with your existing policies\.
+ Identifies the account through AWS IAM Identity Center \(successor to AWS Single Sign\-On\) or AWS Organizations\.
+ Places the account into the OU that you've specified\. Be sure to apply all SCPs that are applied in the current OU, so that your security posture remains consistent\.
+ Applies mandatory controls to the account by means of the SCPs that apply to the selected OU as a whole\.
+ Enables AWS Config and configures it to record all resources in the account\.
+ Adds the AWS Config rules that apply the AWS Control Tower detective controls to the account\.

**Note**  
When you enroll the account into AWS Control Tower, your account is governed by the AWS CloudTrail trail for the new organization\. If you have an existing deployment of a CloudTrail trail, you may see duplicate charges unless you delete the existing trail for the account before you enroll it in AWS Control Tower\.

## Enrolling existing accounts with VPCs<a name="enroll-existing-accounts-with-vpcs"></a>

AWS Control Tower handles VPCs differently when you provision a new account in Account Factory than when you enroll an existing account\.
+ When you create a new account, AWS Control Tower automatically removes the AWS default VPC and creates a new VPC for that account\.
+ When you enroll an existing account, AWS Control Tower does not create a new VPC for that account\.
+ When you enroll an existing account, AWS Control Tower does not remove any existing VPC or AWS default VPC associated with the account\.

**Tip**  
You can change the default behavior for new accounts by configuring Account Factory, so it does not set up a VPC by default for accounts in your organization under AWS Control Tower\. For more information, see [Create an Account in AWS Control Tower Without a VPC](configure-without-vpc.md#create-without-vpc)\.

## What if the account does not meet the prerequisites?<a name="fulfill-prerequisites"></a>

Remember that, as a prerequisite, accounts eligible to be enrolled into AWS Control Tower governance must be part of the same overall organization\. To fulfill this prerequisite for account enrollment, you can follow these preparatory steps to move an account into the same organization as AWS Control Tower\.

**Preparatory steps to bring an account into the same organization as AWS Control Tower**

1. Drop the account from its existing organization\. \(You must provide a separate payment method if you use this approach\.\)

1. Invite the account into the AWS Control Tower organization\. You can follow this procedure given in the AWS Organizations documentation, [Inviting an AWS account to join your organization](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_invites.html)\.

1. Accept the invitation\. \(The account shows up in the root of the organization\.\) This step moves the account into the same organization as AWS Control Tower\. It establishes SCPs and consolidated billing\.

**Tip**  
You can send the invitation for the new organization before the account drops out of the old organization\. The invitation will be waiting when the account drops out of its existing organization\.

**Now you must fulfill the remaining prerequisites:**

1. Create the necessary `AWSControlTowerExecution` role\.

1. Clear out the default VPC\. \(This part is optional—AWS Control Tower does not change your existing default VPC\.\)

1. Delete or modify any existing AWS Config configuration recorder or delivery channel through the AWS CLI or AWS CloudShell\. See [Example AWS Config CLI commands for resource status](#example-config-cli-commands) and [Enroll accounts that have existing AWS Config resources](existing-config-resources.md)\.

1. Any other prerequisites, as needed\.

After you've completed these preparatory steps, you can enroll the account into AWS Control Tower\. See [Steps to enroll an account](quick-account-provisioning.md#enrollment-steps)\. This step brings the account into full AWS Control Tower governance\.

## <a name="steps-to-deprovision-resources"></a>

**Optional steps for deprovisioning an account so it can be enrolled, keeping its stack**

1. Optionally, to keep the applied AWS CloudFormation stack, delete the stack instance from the stack sets, making sure to choose **Retain stacks** for the instance\.

1. Terminate the account provisioned product in AWS Service Catalog Account Factory\. \(This step only removes the provisioned product from AWS Control Tower, it does not actually delete the account\.\)

1. Optionally, set up the account with the necessary billing details, as required for any account that does not belong to an organization, then remove the account from the organization\. You would do this so that the account does not count against the total in your AWS Organizations quota\.

1. Clean up the account, if resources remain, and close it, following account closure steps given in [Unmanage an account](unmanage-account.md)\.

1. If you have a **Suspended** OU with defined controls, you can move the account there instead of doing Step 1\.

## Example AWS Config CLI commands for resource status<a name="example-config-cli-commands"></a>

Here are some example AWS Config CLI commands you can use to determine the status of your configuration recorder and delivery channel\.

**View commands:**
+ `aws configservice describe-delivery-channels`
+ `aws configservice describe-delivery-channel-status`
+ `aws configservice describe-configuration-recorders`

The normal response is something like `"name": "default"`

**Delete commands:**
+ `aws configservice stop-configuration-recorder --configuration-recorder-name NAME-FROM-DESCRIBE-OUTPUT`
+ `aws configservice delete-delivery-channel --delivery-channel-name NAME-FROM-DESCRIBE-OUTPUT`
+ `aws configservice delete-configuration-recorder --configuration-recorder-name NAME-FROM-DESCRIBE-OUTPUT`

## Automated enrollment of AWS Organizations accounts<a name="automated-account-enrollment"></a>

You can use the enrollment method described in a blog post called [Enroll existing AWS accounts into AWS Control Tower](http://aws.amazon.com/blogs/architecture/field-notes-enroll-existing-aws-accounts-into-aws-control-tower) to enroll your AWS Organizations accounts into AWS Control Tower with a programmatic process\.

The following YAML template may assist you in creating the required role in an account, so that it can be enrolled programmatically\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure the AWSControlTowerExecution role to enable use of your
  account as a target account in AWS CloudFormation StackSets.
Parameters:
  AdministratorAccountId:
    Type: String
    Description: AWS Account Id of the administrator account (the account in which
      StackSets will be created).
    MaxLength: 12
    MinLength: 12
Resources:
  ExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: AWSControlTowerExecution
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              AWS:
                - !Ref AdministratorAccountId
            Action:
              - sts:AssumeRole
      Path: /
      ManagedPolicyArns:
        - !Sub "arn:${AWS::Partition}:iam::aws:policy/AdministratorAccess"
```
