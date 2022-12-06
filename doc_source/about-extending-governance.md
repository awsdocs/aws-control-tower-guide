# Extend governance to an existing organization<a name="about-extending-governance"></a>

You can add AWS Control Tower governance to an existing organization by setting up a landing zone \(LZ\) as outlined in the AWS Control Tower User Guide at [Getting Started, Step 2](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-with-control-tower.html#step-two)\.

Here's what to expect when you set up your AWS Control Tower landing zone in an existing organization\.
+ You can have one landing zone per AWS Organizations organization\.
+ AWS Control Tower uses the management account from your existing AWS Organizations organization as its management account\. No new management account is needed\.
+  AWS Control Tower sets up two new accounts in a registered OU: an audit account and a logging account\.
+ Your organization's service limits must allow for the creation of these two additional accounts\.
+ After you've launched your landing zone or registered an OU, AWS Control Tower controls apply automatically to all enrolled accounts in that OU\.
+ You can **Enroll** additional existing AWS accounts into an OU that's governed by AWS Control Tower, so that controls apply to those accounts\.
+  You can add more OUs in AWS Control Tower and you can **Register** existing OUs\.

To check other prerequisites for registration and enrollment, see [Getting Started with AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-with-control-tower.html)\.

Here's more detail about how AWS Control Tower controls **do not** apply to your OUs in AWS organizations that don't have AWS Control Tower landing zones set up:
+ New accounts created outside of AWS Control Tower Account Factory are not bound by the registered OU's controls\.
+ New accounts created in OUs that are not registered with AWS Control Tower are not bound by controls, unless you specifically **Enroll** those accounts into AWS Control Tower\. See [Enroll an existing AWS account](enroll-account.md) for more information about enrolling accounts\.
+ Additional existing organizations, existing accounts, and any new OUs or any accounts that you create outside of AWS Control Tower, are not bound by AWS Control Tower controls, unless you separately register the OU or enroll the account\.

For more information about how to apply AWS Control Tower to existing OUs and accounts, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\.

For an overview of the process of setting up an AWS Control Tower landing zone in your existing organization, see the video in the next section\.

**Note**  
During set up, AWS Control Tower performs pre\-checks to avoid common issues\. However, if you are currently using the AWS Landing Zone solution for AWS Organizations, check with your AWS solutions architect before you try to enable AWS Control Tower in your organization to determine if AWS Control Tower may interfere with your current landing zone deployment\. Also, see [What if the account does not meet the prerequisites?](enroll-account.md#fulfill-prerequisites) for information about moving accounts from one landing zone to another\.

## Video: Enable a Landing Zone in existing AWS Organizations<a name="existing-orgs-video"></a>

This video \(7:48\), describes how to set up and enable an AWS Control Tower landing zone in existing AWS Organizations structures\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.

## Considerations for IAM Identity Center and existing organizations<a name="sso-and-existing-orgs"></a>
+ If AWS IAM Identity Center \(successor to AWS Single Sign\-On\) \(IAM Identity Center\) is already set up, the AWS Control Tower home Region must be the same as the IAM Identity Center Region\.
+ AWS Control Tower does not delete an existing configuration\.
+  If IAM Identity Center is already enabled, and if you are using IAM Identity Center Directory, AWS Control Tower adds resources such as permission sets, groups, and so forth, and proceeds as usual\. 
+ If another directory \(external, AD, Managed AD\) is set up, AWS Control Tower does not change the existing configuration\. For more details, see [Considerations for AWS IAM Identity Center \(successor to AWS Single Sign\-On\) \(IAM Identity Center\) customers](getting-started-prereqs.md#sso-considerations)\.

## Access to other AWS services<a name="other-services"></a>

After you bring your organization into AWS Control Tower governance, you still have access to any AWS services that are available through AWS Organizations, by means of the AWS Organizations console and APIs\. For more information, see [Related AWS services](related-information.md#related-aws-services)\.