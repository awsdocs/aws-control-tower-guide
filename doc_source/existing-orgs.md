# Enable AWS Control Tower on existing organizations and accounts<a name="existing-orgs"></a>

If you have existing AWS Organizations and AWS accounts, you can apply AWS Control Tower guardrails to them\.  

**Terminology**
+ When you bring an existing organization into AWS Control Tower, it's called *registering* the organization, or *extending governance* to the organization\.
+ When you bring an AWS account into AWS Control Tower, it's called *enrolling* the account\.

On the **OU** page, you can view all of your AWS Organizations, including OUs that are registered with AWS Control Tower and those that are not registered\. The **Accounts** page lists all accounts in your organization, regardless of OU or enrollment status in AWS Control Tower\. You can view and enroll accounts individually within the OUs, if the accounts meet the prerequisites for enrollment\.

## About extending governance to an organization<a name="about-extending-governance"></a>

You can add AWS Control Tower governance to an existing organization by setting up a landing zone \(LZ\) as outlined in the AWS Control Tower User Guide at [Getting Started, Step 2](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-with-control-tower.html#step-two)\.

Here's what to expect when you set up your AWS Control Tower landing zone in an existing organization\.
+ You can have one landing zone per AWS Organizations organization\.
+ AWS Control Tower uses the master account from your existing AWS Organizations organization as its master account\. No new master account is needed\.
+  AWS Control Tower sets up two new accounts in a registered OU: an audit account and a logging account\.
+ Your organization's service limits must allow for the creation of these two additional accounts\.
+ Once you've launched your landing zone in the registered OU, AWS Control Tower guardrails apply automatically to accounts in that OU\.
+ You can **Enroll** additional existing AWS accounts into an OU that's governed by AWS Control Tower, so that guardrails apply to those accounts\.
+ You cannot use the AWS Control Tower landing zone that you've set up in your existing organization to extend governance to any other OU\.

To check other prerequisites for registration and enrollment, see [Getting Started with AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-with-control-tower.html)\.

Here's more detail about how AWS Control Tower guardrails **do not** apply to your OUs in AWS Organizations that don't have AWS Control Tower landing zones set up:
+ New accounts created outside of AWS Control Tower Account Factory are not bound by the registered OU's guardrails\.
+ New accounts created in OUs that are not registered with AWS Control Tower are not bound by guardrails, unless you specifically **Enroll** those accounts into AWS Control Tower\. See [Enroll an existing AWS account](enroll-account.md) for more information about enrolling accounts\.
+  Additional existing AWS Organizations, existing accounts, and any new OUs or any accounts that you create outside of AWS Control Tower, are not bound by AWS Control Tower guardrails, unless you separately register the OU or enroll the account\.

For an overview of the process of setting up an AWS Control Tower landing zone in your existing organization, see the video in the next section\.

**Note**  
If you currently are using the AWS Landing Zone solution for AWS Organizations, check with your AWS solutions architect before you try to enable AWS Control Tower in your organization\. AWS Control Tower cannot perform pre\-checks that determine whether AWS Control Tower may interfere with your current landing zone deployment\. Also, see [What if the account does not meet the prerequisites?](enroll-account.md#fulfill-prerequisites) for information about moving accounts from one landing zone to another\.

## Enable a Landing Zone in Existing AWS Organizations<a name="existing-orgs-video"></a>

This video \(6:52\) describes how to set up and enable an AWS Control Tower landing zone in existing AWS Organizations\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.