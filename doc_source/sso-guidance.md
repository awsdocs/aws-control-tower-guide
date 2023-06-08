# IAM Identity Center guidance<a name="sso-guidance"></a>

AWS Control Tower recommends that you use AWS Identity and Access Management \(IAM\) to regulate access to your AWS accounts\. However, you have the option to choose whether AWS Control Tower sets up IAM for you, whether you set up IAM for yourself, in a way that meets your business requirements most effectively, or whether to select another method for account access\.

By default, AWS Control Tower sets up AWS IAM for your landing zone, in alignment with best\-practices guidance defined in [Organizing your AWS environment using multiple accounts](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/break-glass-access.html)\. Most customers choose the default\. Alternative access methods are required sometimes, for regulatory compliance in specific industries or countries, or in AWS Regions where AWS IAM is not available\.

**Choosing an option**

From the console, you can choose to self\-manage IAM Identity Center during the landing zone set up process, rather than allowing AWS Control Tower to set it up for you\. At any time later, you can choose to change this selection, by modifying the landing zone settings and updating your landing zone on the landing zone **Settings** page\.

**To change from AWS IAM Identity Center to another identity provider \(IdP\)**

1. Navigate to the landing zone **Settings** page

1. Select the **Configurations** tab

1. Then choose the appropriate radio button, to change your selection for AWS IAM Identity Center\.

After you choose to self\-manage AWS IAM Identity Center as your IdP, AWS Control Tower creates only those roles and policies needed to manage AWS Control Tower, such as `AWSControlTowerAdmin` and `AWSControlTowerAdminPolicy`\. For landing zones that self\-manage, AWS Control Tower no longer creates IAM roles and groupings for customer\-specific use — not during the landing zone set\-up process, nor during account provisioning with Account Factory\.

Account Factory customers with alternative identity providers \(IdPs\) such as Azure AD, Ping, or Okta, can follow the AWS IAM Identity Center [process](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-idp.html) to connect to an external identity provider and onboard their IdP\. You can return to having AWS Control Tower generate your groupings and roles at any time, by modifying the landing zone settings\.
+ For specific information about how AWS Control Tower works with IAM Identity Center based on your identity source, see **Considerations for AWS IAM Identity Center \(successor to AWS Single Sign\-On\) customers** in the [Pre\-launch checks](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-prereqs.html#sso-considerations) section of the *Getting Started* page of this User Guide\.
+ For additional information about how the behavior of AWS Control Tower interacts with IAM Identity Center and different identity sources, refer to [Considerations for Changing Your Identity Source](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-considerations.html) in the *IAM Identity Center User Guide*\.
+ See [Manage Users and Access Through AWS IAM Identity Center \(successor to AWS Single Sign\-On\)](sso.md) for more information about working with AWS Control Tower and IAM Identity Center\.