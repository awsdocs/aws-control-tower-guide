# Provision and manage accounts with Account Factory<a name="account-factory"></a>

This chapter includes an overview and procedures for provisioning new member accounts in your AWS Control Tower landing zone with Account Factory\.

## Permissions for Configuring and Provisioning Accounts<a name="configure-provision-new-account"></a>

The AWS Control Tower account factory enables cloud administrators and AWS Single Sign\-On end users to provision accounts in your landing zone\. By default, AWS SSO users that provision accounts must be in the **AWSAccountFactory** group or the management group\. 

**Note**  
Exercise caution when working from the management account, as you would when using any account that has generous permissions across your organization\.

The AWS Control Tower management account has a trust relationship with the **AWSControlTowerExecution** role, which enables account setup from the management account, including some automated account setup\. For more information about the **AWSControlTowerExecution** role, see [How AWS Control Tower works with roles to create and manage accounts](roles-how.md)\.

**Note**  
To enroll an existing AWS account into AWS Control Tower, that account must have the **AWSControlTowerExecution** role enabled\. For more information about how to enroll an existing account, see [Enroll an existing AWS account](enroll-account.md)\.

## Considerations on Managing Account Factory Accounts<a name="closing-and-repurposing"></a>

Accounts that you provision through the AWS Control Tower Account Factory can be updated, they can be closed, or they can be repurposed\. For example, you can repurpose existing accounts for other workloads and other users by updating the user parameters for the account\.

If you specify a new SSO user email address when you update the provisioned product associated with an account that was vended by account factory, AWS Control Tower creates a new SSO user account\. The previously created user account is not removed\. If you prefer to remove the previous SSO user email from AWS SSO, see [Disabling a User](https://docs.aws.amazon.com/singlesignon/latest/userguide/disableuser.html)\.

With Account Factory you also can change the organizational unit \(OU\) for an account, or you can unmanage an account, by following the procedures in this chapter\. For more information on unmanaging an account, see [Unmanage a member account](unmanage-account.md)\. Certain updates require that you or an administrator must [Sign in as a Root User](root-login.md) to the account, to gain appropriate permissions\.