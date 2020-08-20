# AWS multi\-account strategy for your AWS Control Tower landing zone<a name="aws-multi-account-landing-zone"></a>

AWS Control Tower customers often seek guidance about how to set up their AWS environment and accounts for best results\. AWS has created a unified set of recommendations, called the *multi\-account strategy*, to help you make the best use of your AWS resources, including your AWS Control Tower landing zone\.

Essentially, AWS Control Tower acts as an orchestration layer that works with other AWS services, which assist you with implementing the AWS multi\-account recommendations for AWS accounts and AWS Organizations\. After your landing zone is set up, AWS Control Tower continues to assist you with maintaining your corporate policies and security practices across multiple accounts and workloads\.

Most landing zones develop over time\. As the number of organizations and accounts in your AWS Control Tower landing zone increases, you can extend your AWS Control Tower deployment in ways that help organize your workloads effectively\. 

**AWS multi\-account strategy: Best practices guidance**

This chapter provides prescriptive guidance on how to plan and set up your AWS Control Tower landing zone, in alignment with the AWS multi\-account strategy\. The chapter gives an example of a landing zone in its early configuration stage, and also later, after some growth has taken place\.

## Create your landing zone as a well\-architected AWS environment<a name="multi-account-guidance"></a>

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
*The landing zone is a cloud environment that offers a recommended starting point, including default accounts, account structure, network and security layouts, and so forth\. From a landing zone, you can deploy workloads that utilize all of your solutions and applications\.*

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
+ **Quota allocation** – AWS quotas are set up on a per account basis\. Separating workloads into different accounts gives each account \(such as a project\) a well\-defined, individual quota\.

**Use multiple organizational units**

AWS Control Tower and other account orchestration frameworks can make changes that cross account boundaries\. Therefore, the AWS best practices address cross\-account changes, which potentially can break an environment or undermine its security\. In some cases, changes can affect the overall environment, beyond policies\. As a result, we recommend that you should set up at least two mandatory accounts, Production and Staging\.

Furthermore, AWS accounts often are grouped into organizational units \(OUs\), for purposes of governance and control\. OUs are designed to handle enforcement of policies across multiple accounts\.

Our recommendation is that, at a minimum, you create a pre\-production \(or Staging\) environment that is distinct from your Production environment—with distinct guardrails and policies\. The Production and Staging environments can be created and governed as separate OUs, and billed as separate accounts\. In addition, you may want to set up a Sandbox OU for code testing\.

**Use a well\-planned structure for OUs in your landing zone**

If you’ve decided to apply the multi\-account strategy to your AWS Control Tower deployment, the following diagram shows a basic starter structure for how to set up OUs in your landing zone\. AWS Control Tower sets up some of these OUs for you automatically\. As your workloads and requirements expand over time, you can extend this landing zone configuration to suit your needs\.

**Note**  
The names given in the examples follow the suggested AWS naming conventions for setting up a well\-architected AWS environment\.

![\[FIRST ILLUSTRATION GOES HERE\]](http://docs.aws.amazon.com/controltower/latest/userguide/images/first-figure.png)

To create a well\-architected AWS environment, the previous diagram show that you’ll need two *foundational* OUs in your AWS Control Tower landing zone:
+ **Core OU** – Contains three shared accounts: the master \(primary\) account, the log archive account, and the security audit account \(also referred to as the audit account\)\.
**Note**  
AWS Control Tower sets up the Core OU for you\.
+ **Infrastructure OU** – Contains your shared services and networking accounts\.
**Note**  
AWS Control Tower does not set up the Infrastructure OU for you\.

As the diagram shows, you can set up *additional* OUs to contain your production workloads and software development workloads\.
+ **Sandbox OU** – An early\-stage software development OU\. For example, it may have a fixed spending limit, or it may not be connected to the production network\.
+ **Workloads OU** – Contains accounts that run your workloads\.
**Note**  
AWS Control Tower does not set up the Sandbox OU or the Workloads OU for you\. It sets up a single OU, called the Custom OU\. These two OUs could be created next to, or instead of, the Custom OU\.
+ **Custom OU** – Within this OU, you can establish AWS accounts for your workloads and development environments\. You can rename the Custom OU to be called the **Workloads** OU, if you prefer, as shown in the diagram\.
**Note**  
AWS Control Tower sets up the Custom OU for you\.

### Example of AWS Control Tower with a complete multi\-account OU structure<a name="guidelines-for-full-multi-account"></a>

As you continue your AWS journey, the complete multi\-account OU structure becomes increasingly important for keeping your environment organized and efficient\. A multi\-account structure serves individual business users, multiple deployments, multiple teams, a robust software development and testing process, and so forth\.

AWS Control Tower currently supports a flat OU hierarchy, which means that nested OUs are not available\. However, you can still build an AWS Control Tower environment to match the AWS multi\-account strategy guidance\. The following diagram shows an example set of OUs for a more mature AWS Control Tower environment that follows AWS multi\-account guidance\.

![\[SECOND ILLUSTRATION GOES HERE\]](http://docs.aws.amazon.com/controltower/latest/userguide/images/second-figure.png)

The previous diagram shows that more Foundational OUs and more Additional OUs have been created than were created in the starter structure\. These OUs serve the additional needs of a large deployment:

In the Foundational OUs column, two OUs have been added to the basic structure:
+ **Security\_Prod OU** – Provides a read\-only area for security policies, as well as a break\-glass security audit area\.
+ **Infrastructure OU** – The Infrastructure OU has been separated into two OUs, Infrastructure\_SDLC \(for pre\-production infrastructure\) and Infrastructure\_Prod \(for production infrastructure\)\.

In the Additional OUs column, these OUs have been added to the basic structure: 
+ **Workloads OU** – The Workloads OU has been separated into two OUs, Workloads\_SDLC \(for pre\-production workloads\) and Workloads\_Prod \(for production workloads\)\.
+ **PolicyStaging OU** – Allows system administrators to test their changes to guardrails and policies before fully applying them\.
+ **Suspended OU** – Offers a location for accounts that may have been disabled temporarily\.