# Provision and manage accounts with Account Factory<a name="account-factory"></a>

 This chapter includes an overview and procedures for provisioning new member accounts in an AWS Control Tower landing zone with Account Factory\. 

## Permissions for configuring and provisioning accounts<a name="configure-provision-new-account"></a>

The AWS Control Tower Account Factory enables cloud administrators and users in AWS IAM Identity Center \(successor to AWS Single Sign\-On\) to provision accounts in your landing zone\. By default, IAM Identity Center users that provision accounts must be in the `AWSAccountFactory` group or the management group\. 

**Note**  
Exercise caution when working from the management account, as you would when using any account that has permissions across your organization\.

The AWS Control Tower management account has a trust relationship with the `AWSControlTowerExecution` role, which allows account setup from the management account, including some automated account setup\. For more information about the `AWSControlTowerExecution` role, see [How AWS Control Tower works with roles to create and manage accounts](roles-how.md)\.

**Note**  
To enroll an existing AWS account into AWS Control Tower, that account must have the `AWSControlTowerExecution` role enabled\. For more information about how to enroll an existing account, see [Enroll an existing AWS account](enroll-account.md)\.

## Considerations for managing accounts in Account Factory<a name="closing-and-repurposing"></a>

 You can update, unmanage, and close accounts that you create and provision through Account Factory\. You can recycle accounts by updating the user parameters in the accounts that you want to repurpose\. You can also change an account's organizational unit \(OU\)\. 

**Note**  
 When updating a provisioned product that's associated with an account that Account Factory vends, if you specify a new user email address for AWS IAM Identity Center \(successor to AWS Single Sign\-On\), AWS Control Tower creates a new user in IAM Identity Center\. The previously created account isn't removed\. For information about removing the previous IAM Identity Center user email address from IAM Identity Center, see [Disabling a User](https://docs.aws.amazon.com/singlesignon/latest/userguide/disableuser.html)\. 