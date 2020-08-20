# What Is AWS Control Tower?<a name="what-is-control-tower"></a>

AWS Control Tower creates an abstraction or *orchestration* layer that combines and integrates the capabilities of several other [AWS services](https://docs.aws.amazon.com/controltower/latest/userguide/integrated-services.html), including AWS Organizations, AWS Single Sign\-on, and AWS Service Catalog\. AWS Control Tower provides the easiest way to set up and govern a secure, compliant, multi\-account AWS environment based on best practices established by working with thousands of enterprises\.

If you are hosting more than a handful of accounts, it’s beneficial to have an orchestration layer that facilitates account deployment and account governance\. With AWS Control Tower, end users on your distributed teams can provision new AWS accounts quickly\. Meanwhile your central cloud administrators know that all accounts are aligned with centrally established, company\-wide compliance policies\.

For more information about the working with AWS Control Tower and the best practices outlined in the AWS multi\-account strategy, see [Create your landing zone as a well\-architected AWS environment](aws-multi-account-landing-zone.md#multi-account-guidance)\.

## Features<a name="features"></a>

AWS Control Tower has the following features:
+ **Landing zone** – A landing zone is a well\-architected, multi\-account AWS environment that's based on security and compliance best practices\. It is the enterprise\-wide container that holds all of your organizational units \(OUs\), accounts, users, and other resources that you want to be subject to compliance regulation\. A landing zone can scale to fit the needs of an enterprise of any size\.
+ **Guardrails** – A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. It's expressed in plain language\. Two kinds of guardrails exist: *preventive* and *detective*\. Three categories of guidance apply to the two kinds of guardrails: *mandatory*, *strongly recommended*, or *elective*\. For more information about guardrails, see [How Guardrails Work](how-control-tower-works.md#how-guardrails-work)\.
+ **Account Factory** – An Account Factory is a configurable account template that helps to standardize the provisioning of new accounts with pre\-approved account configurations\. AWS Control Tower offers a built\-in Account Factory that helps automate the account provisioning workflow in your organization\. For more information, see [Provision and manage accounts with Account Factory](account-factory.md)\.
+ **Dashboard** – The dashboard offers continuous oversight of your landing zone to your team of central cloud administrators\. Use the dashboard to see provisioned accounts across your enterprise, guardrails enabled for policy enforcement, guardrails enabled for continuous detection of policy non\-conformance, and noncompliant resources organized by accounts and OUs\.

## How AWS Control Tower interacts with other AWS services<a name="related-services"></a>

AWS Control Tower is built on top of trusted and reliable AWS services including AWS Service Catalog, AWS Single Sign\-On, and AWS Organizations\. For more information, see [Integrated services](integrated-services.md)\.

You can incorporate AWS Control Tower with other AWS services into a solution that helps you migrate your existing workloads to AWS\. For more information, see [How to take advantage of AWS Control Tower and CloudEndure to migrate workloads to AWS](http://aws.amazon.com/blogs/mt/how-to-take-advantage-of-aws-control-tower-and-cloudendure-to-migrate-workloads-to-aws/)\.

**Configuration, Governance, and Extensibility**
+ *Automated account configuration:* AWS Control Tower automates account deployment and enrollment by means of an Account Factory \(or “vending machine”\), which is built as an abstraction on top of provisioned products in AWS Service Catalog\. The Account Factory can create and enroll AWS accounts, and it automates the process of applying guardrails and policies to those accounts\.
+ *Centralized governance:* By employing the capabilities of AWS Organizations, AWS Control Tower sets up a framework that ensures consistent compliance and governance across your multi\-account environment\. The AWS Organizations service provides essential capabilities for managing a multi\-account environment, including central governance and management of accounts, account creation from APIs, and service control policies \(SCPs\)\. 

  With AWS Control Tower orchestration, you can follow a set of prescribed rules and definitions that extend the capabilities of AWS Organizations\. For example, you can use guardrails to ensure that security logs and necessary cross\-account access permissions are created, and not altered\.
+ *Extensibility:* You can build or extend your own AWS Control Tower environment by working directly in AWS Organizations\. You can see your changes reflected in AWS Control Tower after you register your existing organizations, enroll your existing accounts into AWS Control Tower, and then update your AWS Control Tower landing zone to reflect your changes\. If your workloads require further advanced capabilities, you can leverage other AWS partner solutions along with AWS Control Tower\. 

## Pricing<a name="pricing"></a>

No additional charge exists for using AWS Control Tower\. You only pay for the AWS services enabled by AWS Control Tower, and the services you use in your landing zone\. For example, you pay for AWS Service Catalog for provisioning accounts with Account Factory, and AWS CloudTrail for events tracked in your landing zone\. For information about the pricing and fees associated with AWS Control Tower, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing)\.

## Are You a First\-Time User of AWS Control Tower?<a name="first-time-user"></a>

If you’re a first\-time user of this service, we recommend that you read the following:

1. If you need more information about how to plan and organize your landing zone, see [Plan your AWS Control Tower landing zone](planning-your-deployment.md) and [AWS multi\-account strategy for your AWS Control Tower landing zone](aws-multi-account-landing-zone.md)\.

1. If you’re ready to create your first landing zone, see [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

1. For information on drift detection and prevention, see [Detect and resolve drift in AWS Control Tower](drift.md)\.

1. For security details, see [Security in AWS Control Tower](security.md)\.

1. For information on updating your landing zone and member accounts, see [Configuration update management in AWS Control Tower](configuration-updates.md)\.