# Prerequisite: Automated pre\-launch checks for your management account<a name="getting-started-prereqs"></a>

Before AWS Control Tower sets up the landing zone, it automatically runs a series of pre\-launch checks in your account\. There's no action required on your part for these checks, which ensure that your management account is ready for the changes that establish your landing zone\. Here are the checks that AWS Control Tower runs before setting up a landing zone:
+ The existing service limits for the AWS account must be sufficient for AWS Control Tower to launch\. For more information, see [Limitations and quotas in AWS Control Tower](limits.md)\.
+ The AWS account must be subscribed to the following AWS services:
  + Amazon Simple Storage Service \(Amazon S3\)
  + Amazon Elastic Compute Cloud \(Amazon EC2\)
  + Amazon SNS
  + Amazon Virtual Private Cloud \(Amazon VPC\)
  + AWS CloudFormation
  + AWS CloudTrail
  + Amazon CloudWatch
  + AWS Config
  + AWS Identity and Access Management \(IAM\)
  + AWS Lambda
**Note**  
By default, all accounts are subscribed to these services\.

## Considerations for AWS IAM Identity Center \(successor to AWS Single Sign\-On\) \(IAM Identity Center\) customers<a name="sso-considerations"></a>
+ If AWS IAM Identity Center \(successor to AWS Single Sign\-On\) \(IAM Identity Center\) is already set up, the AWS Control Tower home Region must be the same as the IAM Identity Center Region\.
+ IAM Identity Center can be installed only in the management account of an organization\.
+ Three options apply to your IAM Identity Center directory, based on the identity source you choose:
  + **IAM Identity Center User Store**: If AWS Control Tower is set up with IAM Identity Center, AWS Control Tower creates groups in the IAM Identity Center directory and provisions access to these groups, for the user you select, for member accounts\.
  + **Active Directory**: If IAM Identity Center for AWS Control Tower is set up with Active Directory, AWS Control Tower does not manage the IAM Identity Center directory\. It does not assign users or groups to new AWS accounts\.
  + **External Identity Provider**: If IAM Identity Center for AWS Control Tower is set up with an external identity provider \(IdP\), AWS Control Tower creates groups in the IAM Identity Center directory and provisions access to these groups for the user you select for member accounts\. You can specify an existing user from your external IdP in Account Factory during account creation, and AWS Control Tower gives this user access to the newly vended account when it synchronizes users of the same name between IAM Identity Center and the external IdP\. You can also create groups in your external IdP to match the names of the default groups in AWS Control Tower\. When you assign users to these groups, these users will have access to your enrolled accounts\.

   For more information about working with IAM Identity Center and AWS Control Tower see [Things to Know About IAM Identity Center Accounts and AWS Control Tower](sso.md#sso-good-to-know)

### Considerations for AWS Config and AWS CloudTrail customers<a name="config-and-cloudtrail-considerations"></a>
+ The AWS account cannot have trusted access enabled in the organization  management account for AWS Config or CloudTrail\. For information about how to disable trusted access, see [the AWS Organizations documentation on how to enable or disable trusted access](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_integrate_services.html#orgs_how-to-enable-disable-trusted-access)\.
+ If you have an existing AWS Config recorder, delivery channel, or aggregation setup in any existing accounts that you plan to enroll in AWS Control Tower, you must modify or remove these configurations before you start enrolling the accounts, after your landing zone is set up\. This pre\-check doesn't apply to the AWS Control Tower management account during landing zone launch\. For more information, see [Enroll accounts that have existing AWS Config resources](existing-config-resources.md)\.
+ If you are running ephemeral workloads from accounts in AWS Control Tower, you may see an increase in costs associated with AWS Config\. Contact your AWS account representative for more specific information about managing these costs\.
+ When you enroll an account into AWS Control Tower, your account is governed by the AWS CloudTrail trail for the AWS Control Tower organization\. If you have an existing deployment of a CloudTrail trail in the account, you may see duplicate charges unless you delete the existing trail for the account before you enroll it in AWS Control Tower\. For information about organization\-level trails and AWS Control Tower, see [Pricing](pricing.md)\.

**Note**  
When launching, AWS Security Token Service \(STS\) endpoints must be activated in the management account, for all Regions governed by AWS Control Tower\. Otherwise, the launch may fail midway through the configuration process\.