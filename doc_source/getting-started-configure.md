# Expectations for landing zone configuration<a name="getting-started-configure"></a>

The process of setting up your AWS Control Tower landing zone has multiple steps\. Certain aspects of your AWS Control Tower landing zone are configurable\. Other choices are "one\-way doors" that cannot be changed after setup\.

**Key items to configure during setup**
+ You can select your top\-level OU names during setup, and you also can change OU names after you've set up your landing zone\. By default, the top\-level OUs are named **Security** and **Sandbox**\. For more information, see [Guidelines to set up a well\-architected environment](aws-multi-account-landing-zone.md#guidelines-for-multi-account-setup)\. 
+ During setup, you can select customized names for the shared accounts that AWS Control Tower creates, called **log archive** and **audit** by default, but you cannot change these names after setup\. \(This is a one\-time selection\.\)
+ During setup, you can optionally specify existing AWS accounts for AWS Control Tower to use as audit and log archive accounts\. If you plan to specify existing AWS accounts, and if those accounts have existing AWS Config resources, you must delete the existing AWS Config resources before you can enroll the accounts into AWS Control Tower\. \(This is a one\-time selection\.\)
+ If you are setting up for the first time, or if you're upgrading to landing zone version 3\.0, you can choose whether to allow AWS Control Tower to set up an organization\-level AWS CloudTrail trail for your organization, or you can opt out of trails that are managed by AWS Control Tower and manage your own CloudTrail trails\. You can opt into or opt out of organization\-level trails that are managed by AWS Control Tower any time you update your landing zone\.
+ You can optionally set a customized retention policy for your Amazon S3 log bucket and log access bucket, when you set up or update your landing zone\.
+ You can optionally specify a previously\-defined *blueprint* to use for provisioning customized member accounts from the AWS Control Tower console\. You can customize accounts later if you do not have a blueprint available\. See [Customize accounts with Account Factory Customization \(AFC\)](af-customization-page.md)\.

**Configuration choices that cannot be undone**
+ You cannot change your home Region after you've set up your landing zone\.
+ If you're provisioning Account Factory accounts with VPCs, VPC CIDRs can't be changed after they are created\.