# Change email address of an enrolled account<a name="change-account-email"></a>

 To change the email address of an enrolled member account in AWS Control Tower, follow the procedure in this section\. 

**Note**  
 The following procedure doesn't allow you to change the email address of a **management account**, **log archive account**, or **audit account**\. 

**To change the email address of an account that AWS Control Tower creates**

1.  Recover the root user password for the account\. You can follow the steps in the article [How do I recover a lost or forgotten AWS password?](http://aws.amazon.com/premiumsupport/knowledge-center/recover-aws-password/) 

1.  Sign in to the account with the root user password\. 

1.  Change the email address as you would for any other AWS account, and wait for the change to reflect in AWS Organizations\. You might experience a delay while the email address change finishes updating\. 

1.  Update the provisioned product in Service Catalog using the email address that previously belonged to the account\. The process for updating the provisioned product includes associating the new email address with the provisioned product\. This way the email address change takes effect in AWS Control Tower\. Use the new email address for updates to subsequently provisioned products\. 

 To change the password or email address of a member account that you created with AWS Organizations, see [Accessing a member account as the root user](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_access.html#orgs_manage_accounts_access-as-root) in the *AWS Organizations User Guide*\. 