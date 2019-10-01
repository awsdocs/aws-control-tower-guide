# What Is AWS Control Tower?<a name="what-is-control-tower"></a>

AWS Control Tower provides the easiest way to set up and govern a secure, compliant, multi\-account AWS environment based on best practices established by working with thousands of enterprises\. With AWS Control Tower, end users on your distributed teams can provision new AWS accounts quickly\. Meanwhile your central cloud administrators will know that all accounts are aligned with centrally established, company\-wide compliance policies\.

## Features<a name="features"></a>

AWS Control Tower has the following features:
+ **Landing zone** – A landing zone is a well\-architected, multi\-account AWS environment that's based on security and compliance best practices\. This is the enterprise\-wide container that holds all of your organizational units \(OUs\), accounts, users, and other resources that you want to be subject to compliance regulation\. A landing zone can scale to fit the needs of an enterprise of any size\.
+ **Guardrails** – A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. It's expressed in plain language\. Two kinds of guardrails exist: *preventive* and *detective*\. Three categories of guidance apply to the two kinds of guardrails: *mandatory*, *strongly recommended*, or *elective*\. For more information about guardrails, see [How Guardrails Work](how-control-tower-works.md#how-guardrails-work)\.
+ **Account Factory** – An Account Factory is a configurable account template that helps to standardize the provisioning of new accounts with pre\-approved account configurations\. AWS Control Tower offers a built\-in Account Factory that helps automate the account provisioning workflow in your organization\. For more information, see [Account Factory](account-factory.md)\.
+ **Dashboard** – The dashboard offers continuous oversight of your landing zone to your team of central cloud administrators\. Use the dashboard to see provisioned accounts across your enterprise, guardrails enabled for policy enforcement, guardrails enabled for continuous detection of policy non\-conformance, and noncompliant resources organized by accounts and OUs\.

## Related Services<a name="related-services"></a>

AWS Control Tower is built on top of trusted and reliable AWS services including AWS Service Catalog, AWS Single Sign\-On, and AWS Organizations\. For more information, see [Integrated Services](integrated-services.md)\.

## Pricing<a name="pricing"></a>

No additional charge exists for using AWS Control Tower\. You only pay for the AWS services enabled by AWS Control Tower, and the services you use in your landing zone\. For example, you pay for AWS Service Catalog for provisioning accounts with Account Factory, and AWS CloudTrail for events tracked in your landing zone\. For information about the pricing and fees associated with AWS Control Tower, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing)\.

## Are You a First\-Time User of AWS Control Tower?<a name="first-time-user"></a>

If you’re a first\-time user of this service, we recommend that you read the following:

1. If you’re ready to create your first landing zone, see [Getting Started with AWS Control Tower](getting-started-with-control-tower.md)\.

1. For information on drift detection and prevention, see [Detecting and Resolving Drift in AWS Control Tower](drift.md)\.

1. For security details, see [Security in AWS Control Tower](security.md)\.

1. For information on updating your landing zone and member accounts, see [Configuration Update Management in AWS Control Tower](configuration-updates.md)\.