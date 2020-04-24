# Enable AWS Control Tower on an Existing Organization<a name="existing-orgs"></a>

You can add AWS Control Tower governance to an existing organization by setting up a landing zone \(LZ\) as outlined in the AWS Control Tower User Guide at [Getting Started, Step 2](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-with-control-tower.html#step-two)\. Here's what to expect when you set up your AWS Control Tower landing zone in an existing organization\.
+ You can have one landing zone per AWS Organizations organization\.
+ AWS Control Tower uses the master account from your existing AWS Organizations organization as its master account\. No new master account is needed\. However, AWS Control Tower sets up two new accounts in a registered OU: an audit account and a logging account\.
+ Your organization's service limits must allow for the creation of these two additional accounts\.
+ Once you've launched your landing zone in the registered OU, AWS Control Tower guardrails apply automatically to all accounts in that OU\.

Here's more detail about how AWS Control Tower guardrails **do not** apply to your OUs in AWS Organizations that don't have landing zones set up:
+ New accounts created outside of AWS Control Tower Account Factory are not bound by the registered OU's guardrails\.
+ New accounts created in OUs that are not registered with AWS Control Tower are not bound by guardrails, unless you specifically **Enroll** those accounts into AWS Control Tower\. See [Enrolling an Existing AWS Account in AWS Control Tower](enroll-account.md) for more information about enrolling accounts\.
+  Additional existing AWS Organizations, existing accounts, and any new OUs or any accounts that you create outside of AWS Control Tower, are not bound by guardrails\. 

For an overview of the process of setting up an AWS Control Tower landing zone in your existing organization, see the video in the next section\.

## Enable a Landing Zone in Existing AWS Organizations<a name="existing-orgs-video"></a>

This video \(7:18\) describes how to set up and enable an AWS Control Tower landing zone in existing AWS Organizations\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.