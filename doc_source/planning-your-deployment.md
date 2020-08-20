# Plan your AWS Control Tower landing zone<a name="planning-your-deployment"></a>

When you go through the setup process, AWS Control Tower launches a key resource associated with your account, called a *landing zone*, which serves as a home for your organizations and their accounts\. 

**Note**  
You can have one landing zone per organization\.

**Ways to Set Up AWS Control Tower**

You can set up an AWS Control Tower landing zone in an existing organization, or you can start with a new organization that contains your AWS Control Tower landing zone\.
+ [Launch AWS Control Tower in an Existing Organization](#deploy-with-existing-orgs): This section is for customers who have existing AWS Organizations ready to bring into governance by AWS Control Tower\.
+ [Launch AWS Control Tower in a New Organization](#fresh-deployment-no-existing-orgs): This section is for customers without existing AWS Organizations, OUs, and accounts\.

**Note**  
Special consideration: If you currently are using the AWS Landing Zone solution for AWS Organizations, check with your AWS solutions architect before you try to enable AWS Control Tower in your organization\. AWS Control Tower cannot perform pre\-checks that determine whether AWS Control Tower may interfere with your current landing zone deployment\. Also, see [What if the account does not meet the prerequisites?](enroll-account.md#fulfill-prerequisites) for information about moving accounts from one landing zone to another\.

## Launch AWS Control Tower in an Existing Organization<a name="deploy-with-existing-orgs"></a>

By setting up an AWS Control Tower landing zone in an existing organization, you can start working immediately, in parallel with your existing AWS Organizations environment\. Your other OUs created within AWS Organizations are unchanged, because they are not managed by AWS Control Tower\. You can continue to use those OUs and accounts exactly as they are\.

 AWS Control Tower consolidates by using the master account from your existing organization as its master account\. No new master account is needed\.

**Note**  
To set up AWS Control Tower on an existing organization, your service limits must allow for the creation of at least two additional accounts\.

**Effects of AWS Control Tower on your existing organization**

AWS Control Tower adds two accounts to your organization: an audit account and a logging account\. These accounts keep a record of actions taken by your team, in their individual user accounts\. The audit and logging accounts appear in the **Core** OU within your AWS Control Tower landing zone\.

When you set up your landing zone, the accounts added by AWS Control Tower become part of your existing AWS Organizations, and as such they become part of the billing for your existing organization\.

### Summary of Capabilities<a name="comparison-existing-and-not-existing-orgs"></a>

Enabling AWS Control Tower on an existing AWS Organizations organization provides several major enhancements to the organization\.
+ It allows for unified billing across your organization’s groups, because accounts added by AWS Control Tower will become part of your existing organization\.
+ It gives you the ability to administer all accounts from one master account in your OU\.
+ It simplifies how you apply and enforce guardrails that cover security and compliance for existing and new accounts\.

**Important**  
Launching your AWS Control Tower landing zone in an existing AWS Organizations organization does not enable you to extend AWS Control Tower governance from that organization to other OUs or accounts that are not registered with AWS Control Tower\.

To launch AWS Control Tower in your existing organization, follow the process outlined in [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

For more information about how AWS Control Tower interacts with existing AWS Organizations, see [Enable AWS Control Tower on existing organizations and accounts](existing-orgs.md)\.

## Launch AWS Control Tower in a New Organization<a name="fresh-deployment-no-existing-orgs"></a>

If you're new to AWS Control Tower and you haven't worked with AWS Organizations, the best place to begin is with our [Setting up](setting-up.md) document\.

AWS Control Tower sets up an organization for you automatically when you don't have one set up\.