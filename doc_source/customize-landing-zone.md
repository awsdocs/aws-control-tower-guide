# Walkthrough: Customize Your AWS Control Tower Landing Zone<a name="customize-landing-zone"></a>

Certain aspects of your AWS Control Tower landing zone are configurable\. Follow the steps on the AWS Control Tower console\.

**Select customized names during setup**
+ You can select your top\-level OU names and you can change OU names after you've set up your landing zone\.
+ You can select the names of your shared **Audit** and **Log Archive** accounts, but you cannot change the names after setup\. \(This is a one\-time selection\.\)

**Select AWS Regions**
+ You can customize your landing zone by selecting specific AWS Regions for governance\. Follow the steps in the AWS Control Tower console\.
+ You can select and de\-select AWS Regions for governance when you update your landing zone\.
+ You can select and de\-select the Region Deny guardrail to control user access to most AWS services in ungoverned AWS Regions\.

**Customize by adding optional guardrails**
+ Strongly recommended and elective guardrails are optional, which means that you can customize the level of enforcement for your landing zone by choosing which ones to enable\. Optional guardrails are not enabled by default\. 
+ The optional Data Residency guardrails allow you to customize the Regions in which you store and allow access to your data\.

## Customize with AWS CloudFormation templates<a name="customize-with-templates"></a>

You can add customizations to your AWS Control Tower landing zone using an AWS CloudFormation template and service control policies \(SCPs\)\. You can deploy the custom template and policies to individual accounts and organizational units \(OUs\) within your organization\.

This solution integrates with AWS Control Tower [lifecycle events](https://docs.aws.amazon.com/controltower/latest/userguide/lifecycle-events.html) to ensure that resource deployments stay in sync with the landing zone\. For example, when a new account is created using the AWS Control Tower account factory, the solution ensures that all resources attached to the account's OUs are deployed automatically\.

The deployment documentation for this AWS Control Tower solution architecture is available through the [AWS Solutions web page](http://aws.amazon.com/solutions/customizations-for-aws-control-tower/)\.