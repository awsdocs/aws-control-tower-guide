# Optionally self\-manage AWS account access<a name="select-idp"></a>

You can select whether AWS Control Tower sets up AWS account access with AWS Identity and Access Management \(IAM\), or whether to self\-manage AWS account access—either with AWS IAM Identity Center users, roles, and permissions that you can set up and customize on your own, or with another method *such as an external IdP, either for direct account federation or federation to multiple accounts by means of IAM Identity Center*\. You can change this selection later\.

By default, AWS Control Tower sets up AWS IAM Identity Center for your landing zone, in alignment with best\-practices guidance defined in [Organizing your AWS environment using multiple accounts](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/organizing-your-aws-environment.html)\. Most customers choose the default\. Alternative access methods are required sometimes, for regulatory compliance in specific industries or countries, or in AWS Regions where AWS IAM is not available\.

Selection of identity providers at the account level is not supported\. This option applies only for the landing zone as a whole\.

For more information, see [IAM Identity Center guidance](sso-guidance.md)\.