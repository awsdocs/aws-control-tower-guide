# Setting up<a name="setting-up"></a>

Before you use AWS Control Tower for the first time, complete the following tasks:

1. [Sign up for AWS](#setting-up-signup)

1. [Set up MFA](#setup-mfa)

These tasks create an AWS account and  protect your AWS Control Tower management account\. For information on additional setup tasks specifically for AWS Control Tower, see [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

## Sign up for AWS<a name="setting-up-signup"></a>

When you sign up for Amazon Web Services \(AWS\), your AWS account is automatically signed up for all services in AWS, including AWS Control Tower\. If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html#aws_tasks-that-require-root)\.

Note your AWS account number, because you need it for other tasks\.

## Set up MFA<a name="setup-mfa"></a>

Because of the nature of AWS Control Tower, we strongly recommend that you enable multi\-factor authentication \(MFA\) for your account\. This account becomes your AWS Control Tower management account\. For more information, see [Enable MFA on the AWS Account Root User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html#id_root-user_manage_mfa) in the *IAM User Guide*\.

## <a name="w24aac12c17"></a>

**Security for your accounts**  
You can find additional guidance about how to set up best practices that protect the security of your AWS Control Tower accounts, in the AWS Organizations documentation\.  
[Best practices for the management account](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_best-practices_mgmt-acct.html)
[Best practices for member accounts](https://docs.aws.amazon.com/organizations/latest/userguide/best-practices_member-acct.html)

## Next step<a name="setting-up-next-step"></a>

[Getting started with AWS Control Tower](getting-started-with-control-tower.md)