# Setting up<a name="setting-up"></a>

Before you use AWS Control Tower for the first time, follow the steps in this section to create an AWS account and protect your AWS Control Tower management account\. For information on additional setup tasks specifically for AWS Control Tower, see [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

## Sign up for AWS<a name="setting-up-signup"></a>

When you sign up for Amazon Web Services \(AWS\), your AWS account is automatically signed up for all services in AWS, including AWS Control Tower\. If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

Note your AWS account number, because you need it for other tasks\.

### Sign up for an AWS account<a name="sign-up-for-aws"></a>

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/accounts/latest/reference/root-user-tasks.html)\.

AWS sends you a confirmation email after the sign\-up process is complete\. At any time, you can view your current account activity and manage your account by going to [https://aws\.amazon\.com/](https://aws.amazon.com/) and choosing **My Account**\.

### Create an administrative user<a name="create-an-admin"></a>

After you sign up for an AWS account, create an administrative user so that you don't use the root user for everyday tasks\.

**Secure your AWS account root user**

1.  Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.

   For help signing in by using root user, see [Signing in as the root user](https://docs.aws.amazon.com/signin/latest/userguide/console-sign-in-tutorials.html#introduction-to-root-user-sign-in-tutorial) in the *AWS Sign\-In User Guide*\.

1. Turn on multi\-factor authentication \(MFA\) for your root user\.

   For instructions, see [Enable a virtual MFA device for your AWS account root user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_enable_virtual.html#enable-virt-mfa-for-root) in the *IAM User Guide*\.

**Create an administrative user**
+ For your daily administrative tasks, grant administrative access to an administrative user in AWS IAM Identity Center \(successor to AWS Single Sign\-On\)\.

  For instructions, see [Getting started](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.

**Sign in as the administrative user**
+ To sign in with your IAM Identity Center user, use the sign\-in URL that was sent to your email address when you created the IAM Identity Center user\.

  For help signing in using an IAM Identity Center user, see [Signing in to the AWS access portal](https://docs.aws.amazon.com/signin/latest/userguide/iam-id-center-sign-in-tutorial.html) in the *AWS Sign\-In User Guide*\.

## <a name="w32aac12c13"></a>

**Security for your accounts**  
You can find additional guidance about how to set up best practices that protect the security of your AWS Control Tower accounts, in the AWS Organizations documentation\.  
[Best practices for the management account](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_best-practices_mgmt-acct.html)
[Best practices for member accounts](https://docs.aws.amazon.com/organizations/latest/userguide/best-practices_member-acct.html)

## Next step<a name="setting-up-next-step"></a>

[Getting started with AWS Control Tower](getting-started-with-control-tower.md)