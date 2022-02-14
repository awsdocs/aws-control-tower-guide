# Change email address of an enrolled account<a name="change-account-email"></a>

Follow the procedure given in this section to change the email address of an enrolled member account\. This procedure does not allow you to change the email address of the Management account, Log Archive account, or Audit account\.

**To change the email address of an account created by AWS Control Tower**

1. Recover the root password for the account\. You can follow the steps outlined in this article, [How do I recover a lost or forgotten AWS password?](http://aws.amazon.com/premiumsupport/knowledge-center/recover-aws-password/)

1. Sign in to the account with the root password\.

1. Change the email address as you would for any other AWS account\.

1. You may experience a delay before AWS Organizations reflects the update\. Wait for the email address change to be reflected in AWS Organizations\.

1. Update the provisioned product in AWS Service Catalog using the account's previous email address\. The update process associates the new email address with the provisioned product, so that the email update takes effect in AWS Control Tower\. Subsequent provisioned product updates are made with the new email address\.

To change the password or email address of a member account you created with AWS Organizations, see [Accessing a member account as the root user](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_access.html#orgs_manage_accounts_access-as-root), in the AWS Organizations documentation\.