# Customize your AWS Control Tower landing zone<a name="customize-landing-zone"></a>

## <a name="w371aac24b3"></a>

Certain aspects of your AWS Control Tower landing zone are configurable in the console, such as selection of Regions and optional controls\. Other changes may be made outside the console, with automation\.

For example, you can create more extensive customizations of your landing zone with the *Customizations for AWS Control Tower* capability, a GitOps\-style customization framework that works with AWS CloudFormation templates and AWS Control Tower lifecycle events\.

## Customize from the AWS Control Tower console<a name="console-customize"></a>

To make these customizations to your landing zone, follow the steps given by the AWS Control Tower console\.

**Select customized names during setup**
+ You can select your top\-level OU names during setup\. You can rename your OUs at any time using the AWS Organizations console, but making changes to your OUs in AWS Organizations may cause repairable [drift](drift.md)\.
+ You can select the names of your shared **Audit** and **Log Archive** accounts, but you cannot change the names after setup\. \(This is a one\-time selection\.\)

**Tip**  
Remember that renaming an OU in AWS Organizations does not update the corresponding provisioned product in Account Factory\. To update the provisioned product automatically \(and avoid drift\), you must perform the OU operation through AWS Control Tower, including creating, deleting, or re\-registering an OU\.

**Select AWS Regions**
+ You can customize your landing zone by selecting specific AWS Regions for governance\. Follow the steps in the AWS Control Tower console\.
+ You can select and de\-select AWS Regions for governance when you update your landing zone\.
+ You can set the Region Deny control to **Enabled** or **Not enabled**, and control user access to most AWS services in ungoverned AWS Regions\.

**Customize by adding optional controls**
+ Strongly recommended and elective controls are optional, which means that you can customize the level of enforcement for your landing zone by choosing which ones to enable\. [Optional controls](optional-controls.md) are not enabled by default\. 
+ The optional [Controls that enhance data residency protection](data-residency-controls.md) allow you to customize the Regions in which you store and allow access to your data\.

**Customize your AWS CloudTrail trails**
+ When you update your landing zone to version 3\.0 or later, you can choose to opt into or opt out of organization\-level CloudTrail trails managed by AWS Control Tower\. You can change this selection any time you update your landing zone\. AWS Control Tower creates an organization\-level trail in your management account, and that trail enters active or inactive status, based on your choice\. Landing zone 3\.0 does not support account\-level CloudTrail trails; however, if you require these, you can configure and manage your own trails\. You may incur additional cost for duplicate trails\.

## Automate customizations outside the AWS Control Tower console<a name="automate-customizations"></a>

Some customizations are not available through the AWS Control Tower console, but they can be implemented in other ways\. For example:
+ You can customize accounts during provisioning, in a GitOps\-style workflow, with [*Account Factory for Terraform \(AFT\)*](taf-account-provisioning.md)\.

  AFT is deployed with a Terraform module, available in the [AFT repository](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main)\.
+ You can customize your AWS Control Tower landing zone with [*Customizations for AWS Control Tower*](cfct-overview.md) \(CfCT\), a package of functionality that is built upon AWS CloudFormation templates and service control policies \(SCPs\)\. You can deploy the custom templates and policies to individual accounts and organizational units \(OUs\) within your organization\.

  Source code for CfCT is available in a [GitHub repository](https://github.com/aws-solutions/aws-control-tower-customizations)\.

## Benefits of Customizations for AWS Control Tower \(CfCT\)<a name="benefits-of-cfct"></a>

The package of functionality that we refer to as *Customizations for AWS Control Tower* \(CfCT\) helps you create more extensive customizations for your landing zone than you can create in the AWS Control Tower console\. It offers a GitOps\-style, automated process\. You can reshape your landing zone to meet your business requirements\.

This *infrastructure\-as\-code* customization process integrates AWS CloudFormation templates with AWS service control policies \(SCPs\) and AWS Control Tower [lifecycle events](lifecycle-events.md), so that your resource deployments remain synchronized with your landing zone\. For example, when you create a new account with Account Factory, the resources attached to the account and the OU can be deployed automatically\.

**Note**  
Unlike Account Factory and AFT, CfCT is not specifically intended to create new accounts, but to customize accounts and OUs in your landing zone by deploying resources that you specify\.

**Benefits**
+ **Expand a customized and secure AWS environment** – You can expand your multi\-account AWS Control Tower environment more quickly, and incorporate AWS best practices into a repeatable customization workflow\.
+ **Instantiate your requirements** – You can customize your AWS Control Tower landing zone for your business requirements, with the AWS CloudFormation templates and service control policies that express your policy intentions\.
+ **Automate further with AWS Control Tower lifecycle events** – Lifecycle events allow you to deploy resources based on completion of a previous series of events\. You can rely on a lifecycle event to help you deploy resources to accounts and OUs, automatically\.
+ **Extend your network architecture** – You can deploy customized network architectures that improve and protect your connectivity, such as a transit gateway\.

## Additional CfCT examples<a name="cfct-examples"></a>
+ An example networking use case with *Customizations for AWS Control Tower* \(CfCT\) is given in the AWS Architecture blog post, [Deploy consistent DNS with AWS Service Catalog and AWS Control Tower customizations](http://aws.amazon.com/blogs/architecture/deploy-consistent-dns-with-aws-service-catalog-and-aws-control-tower-customizations/)\.
+ A specific example [related to CfCT and Amazon GuardDuty](https://github.com/aws-samples/aws-security-reference-architecture-examples/tree/main/aws_sra_examples/solutions/guardduty/guardduty_org/customizations_for_aws_control_tower) is available on GitHub in the [`aws-samples` repository](https://github.com/aws-samples/aws-security-reference-architecture-examples)\.
+ Additional code examples regarding CfCT are available as part of the AWS Security Reference Architecture, in the [`aws-samples` repository](https://github.com/aws-samples/aws-security-reference-architecture-examples)\. Many of these examples contain sample `manifest.yaml` files in a directory named `customizations_for_aws_control_tower`\.

For more information about the AWS Security Reference Architecture, see the [AWS Prescriptive Guidance pages](https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/welcome.html)\.