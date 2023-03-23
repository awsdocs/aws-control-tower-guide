# Plan your AWS Control Tower landing zone<a name="planning-your-deployment"></a>

When you go through the setup process, AWS Control Tower launches a key resource associated with your account, called a *landing zone*, which serves as a home for your organizations and their accounts\. 

**Note**  
You can have one landing zone per organization\.

For information about some best practices to follow when you plan and set up your landing zone, see [AWS multi\-account strategy for your AWS Control Tower landing zone](aws-multi-account-landing-zone.md)\.

**Ways to Set Up AWS Control Tower**

You can set up an AWS Control Tower landing zone in an existing organization, or you can start by creating a new organization that contains your AWS Control Tower landing zone\.
+ [Launch AWS Control Tower in an Existing Organization](#deploy-with-existing-orgs): This section is for customers who have existing AWS Organizations ready to bring into governance by AWS Control Tower\.
+ [Launch AWS Control Tower in a New Organization](#fresh-deployment-no-existing-orgs): This section is for customers without existing AWS Organizations, OUs, and accounts\.

**Note**  
If you already have an AWS Organizations landing zone, you can extend AWS Control Tower governance from the existing landing zone to some or all of your existing OUs and accounts within an organization\. See [Govern existing organizations and accounts](https://docs.aws.amazon.com/controltower/latest/userguide/importing-existing.html)\.

## Compare functionality<a name="functionality-comparison"></a>

Here's a brief comparison of the differences between adding AWS Control Tower to an existing organization or extending AWS Control Tower governance to OUs and accounts\. Also, some special considerations apply if you are moving to AWS Control Tower from the AWS Landing Zone solution\.

**About Adding to an Existing Organization:** Adding AWS Control Tower to an existing organization is something you can accomplish within the AWS console\. In this case, you’ve already got an organization that you’ve created in the AWS Organizations service, that organization is not currently registered with AWS Control Tower, and you want to *add a landing zone afterward*\.

When you *add* a landing zone to an existing organization, AWS Control Tower sets up a parallel structure, at the AWS Organizations level\. It doesn’t change the OUs and accounts within your existing organization\.

**About Extending Governance:** Extending governance applies to specific OUs and accounts *within a single organization that's already registered* with AWS Control Tower, which means that a landing zone already exists for that organization\. Extending governance means that AWS Control Tower controls are extended so that their constraints apply to the specific OUs and accounts within that registered organization\. In this case, you're not launching a new landing zone, you're only expanding the current landing zone for your organization\.

**Important**  
Special consideration: If you currently are using the [AWS Landing Zone solution \(ALZ\)](http://aws.amazon.com/solutions/implementations/aws-landing-zone/) for AWS Organizations, check with your AWS solutions architect before you try to enable AWS Control Tower in your organization\. AWS Control Tower cannot perform pre\-checks that determine whether AWS Control Tower may interfere with your current landing zone deployment\. For more information, see [Walkthrough: Move from ALZ to AWS Control Tower](alz-to-control-tower.md)\. Also, for information about moving accounts from one landing zone to another, see [What if the account does not meet the prerequisites?](enroll-account.md#fulfill-prerequisites)

## Launch AWS Control Tower in an Existing Organization<a name="deploy-with-existing-orgs"></a>

By setting up an AWS Control Tower landing zone in an existing organization, you can start working immediately, in parallel with your existing AWS Organizations environment\. Your other OUs created within AWS Organizations are unchanged, because they are not registered with AWS Control Tower\. You can continue to use those OUs and accounts exactly as they are\.

 AWS Control Tower consolidates by using the management account from your existing organization as its management account\. No new management account is needed\. You can launch your AWS Control Tower landing zone from your existing management account\.

**Note**  
To set up AWS Control Tower on an existing organization, your service limits must allow for the creation of at least two additional accounts\.

**Effects of adding AWS Control Tower to your existing organization**

AWS Control Tower creates two accounts in your organization: an audit account and a logging account\. These accounts keep a record of actions taken by your team, in their individual end\-user accounts\. The **Audit** and **Log archive** accounts appear in the **Security** OU within your AWS Control Tower landing zone\.

When you set up your landing zone, the accounts added by AWS Control Tower become part of your existing AWS Organizations, and as such they become part of the billing for your existing organization\.

### Summary of capabilities<a name="comparison-existing-and-not-existing-orgs"></a>

Enabling AWS Control Tower on an existing AWS Organizations organization provides several major enhancements to the organization\.
+ It allows for unified billing across your organization’s groups, because accounts added by AWS Control Tower will become part of your existing organization\.
+ It gives you the ability to administer all accounts from one management account in your OU\.
+ It simplifies how you apply and enforce controls that cover security and compliance for existing and new accounts\.

**Important**  
Launching your AWS Control Tower landing zone in an existing AWS Organizations organization does not enable you to extend AWS Control Tower governance from that organization to other OUs or accounts that are not registered with AWS Control Tower\.

To launch AWS Control Tower in your existing organization, follow the process outlined in [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

For more information about how AWS Control Tower interacts with existing AWS Organizations organizations, see [Govern organizations and accounts with AWS Control Tower](existing-orgs.md)\.

## Launch AWS Control Tower in a New Organization<a name="fresh-deployment-no-existing-orgs"></a>

If you're new to AWS Control Tower and you haven't worked with AWS Organizations, the best place to begin is with our [Setting up](setting-up.md) document\.

AWS Control Tower sets up an organization for you automatically when you don't have one set up\.