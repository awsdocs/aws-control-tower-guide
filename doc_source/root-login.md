# When to sign in as a root user<a name="root-login"></a>

Certain administrative tasks require that you must sign in as a root user\. You can sign in as a root user to an AWS account that was created by account factory in AWS Control Tower\.

**You must sign in as a root user to perform the following actions:**
+ Change certain account settings, including the account name, root user password, or email address\. For more information, see [Update and move account factory accounts with AWS Control Tower or with AWS Service Catalog](updating-account-factory-accounts.md)\.
+ To change or enable your [AWS Support plan](https://docs.aws.amazon.com/controltower/latest/userguide/troubleshooting.html#getting-support)\.
+ To [close an AWS account](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/close-account.html)\.
+ For more information about actions that require root user login credentials, see [Tasks that require root user credentials](https://docs.aws.amazon.com/accounts/latest/reference/root-user-tasks.html) in the *AWS Account Management Reference Guide*\.

**To sign in as root user**

1. Open the AWS sign\-in page\.

   If you don't have the email address of the AWS account to which you require access, you can get it from AWS Control Tower\. Open the console for the management account, choose **Accounts**, and look for the email address\.

1. Enter the email address of the AWS account to which you require access, and then choose **Next**\.

1. Choose **Forgot password?** to have password reset instructions sent to the root user email address\.

1.  Open the password reset email message from the root user mailbox, then follow the instructions to reset your password\.

1. Open the AWS sign\-in page, then sign in with your reset password\.