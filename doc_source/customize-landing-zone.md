# Walkthrough: Customize Your AWS Control Tower Landing Zone<a name="customize-landing-zone"></a>

Certain aspects of your AWS Control Tower landing zone are configurable in the console\. To make these customizations, follow the steps given by the AWS Control Tower console\.

**Select customized names during setup**
+ You can select your top\-level OU names and you can change OU names after you've set up your landing zone\.
+ You can select the names of your shared **Audit** and **Log Archive** accounts, but you cannot change the names after setup\. \(This is a one\-time selection\.\)

**Select AWS Regions**
+ You can customize your landing zone by selecting specific AWS Regions for governance\. Follow the steps in the AWS Control Tower console\.
+ You can select and de\-select AWS Regions for governance when you update your landing zone\.
+ You can select and de\-select the Region Deny guardrail to control user access to most AWS services in ungoverned AWS Regions\.

**Customize by adding optional guardrails**
+ Strongly recommended and elective guardrails are optional, which means that you can customize the level of enforcement for your landing zone by choosing which ones to enable\. [Optional guardrails](optional-guardrails.md) are not enabled by default\. 
+ The optional [Guardrails that enhance data residency protection](data-residency-guardrails.md) allow you to customize the Regions in which you store and allow access to your data\.

**Configure customizations outside the AWS Control Tower console**

Some customizations are not available through the AWS Control Tower console, but they can be implemented in other ways\. For example:
+ You can customize accounts during provisioning with Account Factory for Terraform\.
+ You can customize your AWS Control Tower landing zone with an AWS CloudFormation template and service control policies \(SCPs\)\. You can deploy the custom template and policies to individual accounts and organizational units \(OUs\) within your organization\.

## Customize with AWS CloudFormation templates<a name="customize-with-templates"></a>

The AWS CloudFormation customization process is integrated with AWS Control Tower [lifecycle events](https://docs.aws.amazon.com/controltower/latest/userguide/lifecycle-events.html), so that your resource deployments stay synchronized with your landing zone\. For example, when a new account is created by the AWS Control Tower account factory, the resources attached to the account's OU can be deployed automatically\.

To learn more, see the [https://docs.aws.amazon.com/solutions/latest/customizations-for-aws-control-tower/welcome.html](https://docs.aws.amazon.com/solutions/latest/customizations-for-aws-control-tower/welcome.html) for AWS Control Tower customizations with AWS CloudFormation templates\.

**Benefits of AWS CloudFormation customization**
+ **Quicker set up for a customized and secure AWS environment** – With AWS Control Tower and other highly\-available, trusted AWS services, you can establish a secure, multi\-account AWS environment more quickly, and incorporate AWS best practices\.
+ **Customization to your requirements** – You can use the AWS CloudFormation template and service control policies to customize your AWS Control Tower landing zone for your business requirements\.
+ **Automate with AWS Control Tower lifecycle events** – Lifecycle events allow you to deploy resources based on completion of a previous series of events\. You can rely on a lifecycle event to help you deploy resources automatically\.

For more information, see the deployment documentation for this AWS Control Tower solution architecture, available through the [AWS Solutions web page](http://aws.amazon.com/solutions/customizations-for-aws-control-tower/)\.