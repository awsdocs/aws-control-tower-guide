# What Is AWS Control Tower?<a name="what-is-control-tower"></a>

AWS Control Tower offers a straightforward way to set up and govern an AWS multi\-account environment, following prescriptive best practices\. AWS Control Tower *orchestrates* the capabilities of several other [AWS services](https://docs.aws.amazon.com/controltower/latest/userguide/integrated-services.html), including AWS Organizations, AWS Service Catalog, and AWS IAM Identity Center \(successor to AWS Single Sign\-On\), to build a landing zone in less than an hour\. Resources are set up and managed on your behalf\. 

AWS Control Tower orchestration extends the capabilities of AWS Organizations\. To help keep your organizations and accounts from *drift*, which is divergence from best practices, AWS Control Tower applies preventive and detective controls \(sometimes called *guardrails*\)\. For example, you can use controls to help ensure that security logs and necessary cross\-account access permissions are created, and not altered\.

If you are hosting more than a handful of accounts, it’s beneficial to have an orchestration layer that facilitates account deployment and account governance\. You can adopt AWS Control Tower as your primary way to provision accounts and infrastructure\. With AWS Control Tower, you can more easily adhere to corporate standards, meet regulatory requirements, and follow best practices\.

AWS Control Tower enables end users on your distributed teams to provision new AWS accounts quickly, by means of configurable account templates in Account Factory\. Meanwhile, your central cloud administrators can monitor that all accounts are aligned with established, company\-wide compliance policies\.

In short, AWS Control Tower offers the easiest way to set up and govern a secure, compliant, multi\-account AWS environment based on best practices established by working with thousands of enterprises\. For more information about the working with AWS Control Tower and the best practices outlined in the AWS multi\-account strategy, see [AWS multi\-account strategy: Best practices guidance](aws-multi-account-landing-zone.md#multi-account-guidance)\.

## Features<a name="features"></a>

AWS Control Tower has the following features:
+ **Landing zone** – A landing zone is a well\-architected, [multi\-account environment](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/appendix-e-establish-multi-account.html#example-workloads-flat-structure) that's based on security and compliance best practices\. It is the enterprise\-wide container that holds all of your organizational units \(OUs\), accounts, users, and other resources that you want to be subject to compliance regulation\. A landing zone can scale to fit the needs of an enterprise of any size\.
+ **Controls** – A control \(sometimes called a *guardrail*\) is a high\-level rule that provides ongoing governance for your overall AWS environment\. It's expressed in plain language\. Two kinds of controls exist: *preventive* and *detective*\. Three categories of guidance apply to the two kinds of controls: *mandatory*, *strongly recommended*, or *elective*\. For more information about controls, see [How Controls Work](how-control-tower-works.md#how-controls-work)\.
+ **Account Factory** – An Account Factory is a configurable account template that helps to standardize the provisioning of new accounts with pre\-approved account configurations\. AWS Control Tower offers a built\-in Account Factory that helps automate the account provisioning workflow in your organization\. For more information, see [Provision and manage accounts with Account Factory](account-factory.md)\.
+ **Dashboard** – The dashboard offers continuous oversight of your landing zone to your team of central cloud administrators\. Use the dashboard to see provisioned accounts across your enterprise, controls enabled for policy enforcement, controls enabled for continuous detection of policy non\-conformance, and noncompliant resources organized by accounts and OUs\.

## How AWS Control Tower interacts with other AWS services<a name="related-services"></a>

AWS Control Tower is built on top of trusted and reliable AWS services including AWS Service Catalog, AWS IAM Identity Center \(successor to AWS Single Sign\-On\), and AWS Organizations\. For more information, see [Integrated services](integrated-services.md)\.

You can incorporate AWS Control Tower with other AWS services into a solution that helps you migrate your existing workloads to AWS\. For more information, see [How to take advantage of AWS Control Tower and CloudEndure to migrate workloads to AWS](http://aws.amazon.com/blogs/mt/how-to-take-advantage-of-aws-control-tower-and-cloudendure-to-migrate-workloads-to-aws/)\.

**Configuration, Governance, and Extensibility**
+ *Automated account configuration:* AWS Control Tower automates account deployment and enrollment by means of an Account Factory \(or “vending machine”\), which is built as an abstraction on top of provisioned products in AWS Service Catalog\. The Account Factory can create and enroll AWS accounts, and it automates the process of applying controls and policies to those accounts\.
+ *Centralized governance:* By employing the capabilities of AWS Organizations, AWS Control Tower sets up a framework that ensures consistent compliance and governance across your multi\-account environment\. The AWS Organizations service provides essential capabilities for managing a multi\-account environment, including central governance and management of accounts, account creation from APIs, and service control policies \(SCPs\)\. 

  
+ *Extensibility:* You can build or extend your own AWS Control Tower environment by working directly in AWS Organizations, as well as in the AWS Control Tower console\. You can see your changes reflected in AWS Control Tower after you register your existing organizations and enroll your existing accounts into AWS Control Tower\. You can update your AWS Control Tower landing zone to reflect your changes\. If your workloads require further advanced capabilities, you can leverage other AWS partner solutions along with AWS Control Tower\. 

  

## Are You a First\-Time User of AWS Control Tower?<a name="first-time-user"></a>

If you’re a first\-time user of this service, we recommend that you read the following:

1. If you need more information about how to plan and organize your landing zone, see [Plan your AWS Control Tower landing zone](planning-your-deployment.md) and [AWS multi\-account strategy for your AWS Control Tower landing zone](aws-multi-account-landing-zone.md)\.

1. If you’re ready to create your first landing zone, see [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

1. For information on drift detection and prevention, see [Detect and resolve drift in AWS Control Tower](drift.md)\.

1. For security details, see [Security in AWS Control Tower](security.md)\.

1. For information on updating your landing zone and member accounts, see [Configuration update management in AWS Control Tower](configuration-updates.md)\.