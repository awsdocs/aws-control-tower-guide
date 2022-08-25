# AWS multi\-account strategy for your AWS Control Tower landing zone<a name="aws-multi-account-landing-zone"></a>

AWS Control Tower customers often seek guidance about how to set up their AWS environment and accounts for best results\. AWS has created a unified set of recommendations, called the *multi\-account strategy*, to help you make the best use of your AWS resources, including your AWS Control Tower landing zone\.

Essentially, AWS Control Tower acts as an orchestration layer that works with other AWS services, which assist you with implementing the AWS multi\-account recommendations for AWS accounts and AWS Organizations\. After your landing zone is set up, AWS Control Tower continues to assist you with maintaining your corporate policies and security practices across multiple accounts and workloads\.

Most landing zones develop over time\. As the number of organizational units \(OUs\) and accounts in your AWS Control Tower landing zone increases, you can extend your AWS Control Tower deployment in ways that help organize your workloads effectively\. This chapter provides prescriptive guidance on how to plan and set up your AWS Control Tower landing zone, in alignment with the AWS multi\-account strategy, and extend it over time\.

For a general discussion about best practices for organizational units, see [Best Practices for Organizational Units with AWS Organizations](http://aws.amazon.com/blogs/mt/best-practices-for-organizational-units-with-aws-organizations/)\.

## AWS multi\-account strategy: Best practices guidance<a name="multi-account-guidance"></a>

AWS best practices for a well\-architected environment recommend that you should separate your resources and workloads into multiple AWS accounts\. You can think of AWS accounts as isolated resource containers: they offer workload categorization, as well as blast radius reduction when things go wrong\.

**Definition of an AWS account**  
*An AWS account acts as a resource container and resource isolation boundary\.*

**Note**  
An AWS account is not the same as a user account, which is set up through Federation or AWS Identity and Access Management \(IAM\)\.

**More about AWS accounts**

An AWS account provides the ability to isolate resources and to contain security threats for your AWS workloads\. An account also provides a mechanism for billing and for governance of a workload environment\.

The AWS account is the primary implementation mechanism to provide a resource container for your workloads\. If your environment is well\-architected, you can manage multiple AWS accounts effectively, and thus, manage multiple workloads and environments\.

AWS Control Tower sets up a well\-architected environment\. It relies upon AWS accounts, along with AWS Organizations, which help govern changes to your environment that can extend across multiple accounts\.

**Definition of a well\-architected environment**  
*AWS defines a well\-architected environment as one that begins with a landing zone\.*

AWS Control Tower offers a landing zone that is set up automatically\. It enforces guardrails to ensure compliance with your corporate guidelines, across multiple accounts in your environment\.

**Definition of a landing zone**  
*The landing zone is a cloud environment that offers a recommended starting point, including default accounts, account structure, network and security layouts, and so forth\. From a landing zone, you can deploy workloads that utilize your solutions and applications\.*

## Guidelines to set up a well\-architected environment<a name="guidelines-for-multi-account-setup"></a>

The three key components of a well\-architected environment, explained in the following sections, are:
+ Multiple AWS accounts
+ Multiple organizational units \(OUs\)
+ A well\-planned structure

**Use multiple AWS accounts**

One account isn’t enough to set up a well\-architected environment\. By using multiple accounts, you can best support your security goals and business processes\. Here are some benefits of using a multi\-account approach:
+ **Security controls** – Applications have different security profiles, so they require different control policies and mechanisms\. For example, it’s far easier to talk to an auditor and point to a single account hosting the payment card industry \(PCI\) workload\.
+ **Isolation** – An account is a unit of security protection\. Potential risks and security threats can be contained within an account without affecting others\. Therefore, security needs may require you to isolate accounts from one another\. For example, you may have teams with different security profiles\.
+ **Many teams** – Teams have different responsibilities and resource needs\. By setting up multiple accounts, the teams cannot interfere with one another, as they might when using the same account\.
+ **Data Isolation** – Isolating data stores to an account helps limit the number of people who have access to data and can manage the data store\. This isolation helps prevent unauthorized exposure of highly private data\. For example, data isolation helps support compliance with the General Data Protection Regulation \(GDPR\)\.
+ **Business process** – Business units or products often have completely different purposes and processes\. Individual accounts can be established to serve business\-specific needs\.
+ **Billing** – An account is the only way to separate items at a billing level, including things like transfer charges and so forth\. The multi\-account strategy helps create separate billable items across business units, functional teams, or individual users\.
+ **Quota allocation** – AWS quotas are set up on a per\-account basis\. Separating workloads into different accounts gives each account \(such as a project\) a well\-defined, individual quota\.

**Use multiple organizational units**

AWS Control Tower and other account orchestration frameworks can make changes that cross account boundaries\. Therefore, the AWS best practices address cross\-account changes, which potentially can break an environment or undermine its security\. In some cases, changes can affect the overall environment, beyond policies\. As a result, we recommend that you should set up at least two mandatory accounts, Production and Staging\.

Furthermore, AWS accounts often are grouped into organizational units \(OUs\), for purposes of governance and control\. OUs are designed to handle enforcement of policies across multiple accounts\.

Our recommendation is that, at a minimum, you create a pre\-production \(or Staging\) environment that is distinct from your Production environment—with distinct guardrails and policies\. The Production and Staging environments can be created and governed as separate OUs, and billed as separate accounts\. In addition, you may want to set up a Sandbox OU for code testing\.

**Use a well\-planned structure for OUs in your landing zone**

AWS Control Tower sets up some OUs for you automatically\. As your workloads and requirements expand over time, you can extend the original landing zone configuration to suit your needs\.

**Note**  
The names given in the examples follow the suggested AWS naming conventions for setting up a multi\-account AWS environment\. You can rename your OUs after you've set up your landing zone, by selecting **Edit** on the OU detail page\.

**Recommendations**

After AWS Control Tower sets up the first, required OU for you — the Security OU — we recommend creating some additional OUs in your landing zone\.

We recommend that you allow AWS Control Tower to create at least one additional OU, called the Sandbox OU\. This OU is for your software development environments\. AWS Control Tower can set up the Sandbox OU for you during landing zone creation, if you select it\. 

Two recommended other OUs you can set up on your own: the Infrastructure OU, to contain your shared services and networking accounts, and an OU to contain your production workloads, called the Workloads OU\. You can add additional OUs in your landing zone through the AWS Control Tower console on the **Organizational units** page\.

**Recommended OUs besides the ones set up automatically**
+ **Infrastructure OU** – Contains your shared services and networking accounts\.
**Note**  
AWS Control Tower does not set up the Infrastructure OU for you\.
+ **Sandbox OU** – A software development OU\. For example, it may have a fixed spending limit, or it may not be connected to the production network\.
**Note**  
AWS Control Tower recommends that you set up the Sandbox OU, but it is optional\. It can be set up automatically as part of configuring your landing zone\.
+ **Workloads OU** – Contains accounts that run your workloads\.
**Note**  
AWS Control Tower does not set up the Workloads OU for you\.

## Example of AWS Control Tower with a complete multi\-account OU structure<a name="guidelines-for-full-multi-account"></a>

AWS Control Tower supports a nested OU hierarchy, which means that you can create a hierarchical OU structure that meets your organization's requirements\. You can build an AWS Control Tower environment to match the AWS multi\-account strategy guidance\.

You also can build a simpler, flat OU structure that performs well and aligns with the AWS multi\-account guidance\. Just because you can build a hierarchical OU structure, it does not mean that you must do so\.
+ To view a diagram that shows an example set of OUs in an expanded, flat AWS Control Tower environment with AWS multi\-account guidance, see [ Example: Workloads in a Flat OU Structure](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/appendix-e-establish-multi-account.html#example-workloads-flat-structure)\.
+ For more information about how AWS Control Tower works with nested OU structures, see [Nested OUs in AWS Control Tower](nested-ous.md)\.
+ For more information about how AWS Control Tower aligns with the AWS guidance, see the AWS white paper, [Organizing Your AWS Environment Using Multiple Accounts](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/appendix-e-establish-multi-account.html)\.

The diagram on the linked page shows that more Foundational OUs and more Additional OUs have been created\. These OUs serve the additional needs of a larger deployment\.

In the Foundational OUs column, two OUs have been added to the basic structure:
+ **Security\_Prod OU** – Provides a read\-only area for security policies, as well as a break\-glass security audit area\.
+ **Infrastructure OU** – You may wish to separate the Infrastructure OU, recommended previously, into two OUs, Infrastructure\_Test \(for pre\-production infrastructure\) and Infrastructure\_Prod \(for production infrastructure\)\.

In the Additional OUs area, several more OUs have been added to the basic structure\. The following are the next recommended OUs to create as your environment grows: 
+ **Workloads OU** – The Workloads OU, recommended previously but optional, has been separated into two OUs, Workloads\_Test \(for pre\-production workloads\) and Workloads\_Prod \(for production workloads\)\.
+ **PolicyStaging OU** – Allows system administrators to test their changes to guardrails and policies before fully applying them\.
+ **Suspended OU** – Offers a location for accounts that may have been disabled temporarily\.

## About the Root<a name="about-the-root"></a>

The Root is not an OU\. It is a container for the management account, and for all OUs and accounts in your organization\. Conceptually, the Root contains all of the OUs\. It cannot be deleted\. You cannot govern enrolled accounts at the Root level within AWS Control Tower\. Instead, govern enrolled accounts within your OUs\. For a helpful diagram, see [the AWS Organizations documentation](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
