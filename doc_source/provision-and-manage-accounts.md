# Provision and manage accounts in AWS Control Tower<a name="provision-and-manage-accounts"></a>

This chapter includes an overview and procedures for provisioning and managing member accounts in your AWS Control Tower landing zone\.

It also includes an overview and procedures for enrolling an existing AWS account into AWS Control Tower\.

For more information about accounts in AWS Control Tower, see  [About AWS accounts in AWS Control Tower](accounts.md)\. For information about enrolling multiple acounts into AWS Control Tower, see [Register an existing organizational unit with AWS Control Tower](importing-existing.md)\.

**Note**  
You can perform up to five \(5\) account\-related operations concurrently, including provisioning, updating, and enrolling\.

## Methods of provisioning<a name="methods-of-provisioning"></a>

AWS Control Tower provides several methods for creating and updating member accounts\. Some methods are primarily console\-based, and some methods are primarily automated\.

**Overview**

The standard way to create member accounts is through Account Factory, a console\-based product that's part of the Service Catalog\. If your landing zone is not in a state of drift, you can use **Create account** as a method to add new accounts from the console, as well as **Enroll account** to enroll existing AWS accounts into AWS Control Tower\.

With Account Factory, you can provision basic accounts, by relying on the AWS Control Tower default settings\. You also can provision customized accounts that meet requirements for specialized use cases\.

Account Factory Customization \(AFC\) is a way of provisioning customized accounts from the AWS Control Tower console, and it automates the customization and deployment of your accounts\. It allows console\-based, automated provisioning, after some one\-time setup steps, which eliminates the need to write scripts or set up pipelines\. For more information, see [Customize accounts with Account Factory Customization \(AFC\)](af-customization-page.md)\.

**Console\-based methods:**
+ Through the Account Factory console that is part of Service Catalog, for basic or customized accounts\. See [Provision and manage accounts with Account Factory](account-factory.md)\.
+ Through the **Enroll account** feature within AWS Control Tower, if your landing zone is not in a state of drift\. See [Enroll an existing account](quick-account-provisioning.md)\.
+ You can create, update, or enroll up to five accounts at the same time in Account Factory\.

**Automated methods:**
+ **Lambda code: **From your AWS Control Tower landing zone's management account, using Lambda code and appropriate IAM roles\. See [Automated Account Provisioning With IAM Roles](roles-how.md#automated-provisioning)\.
+ **Terraform: **From the AWS Control Tower Account Factory for Terraform \(AFT\), which relies on Account Factory and a GitOps model to allow automation of account provisioning and updating\. See [ Provision accounts with AWS Control Tower Account Factory for Terraform \(AFT\) ](taf-account-provisioning.md)\.
+ **Account Factory customization in the AWS Control Tower console:** After the setup steps, future provisioning of customized accounts requires no additional configuration or pipeline maintenance\. Accounts are provisioned by means of AWS CloudFormation templates\.

## What happens when AWS Control Tower creates an account<a name="what-happens-in-account-creation"></a>

New accounts in AWS Control Tower are created and then provisioned by an interaction among AWS Control Tower, AWS Organizations, and Service Catalog\. For steps to create an account through the AWS Control Tower console, see [Enroll an existing account](quick-account-provisioning.md)\.

**Behind the scenes of account creation**

1. You initiate the request, for example, from the AWS Control Tower Account Factory page, or directly from the Service Catalog console, or by calling the Service Catalog `ProvisionProduct` API\.

1. Service Catalog calls AWS Control Tower\.

1. AWS Control Tower begins a workflow, which as a first step calls the AWS Organizations `CreateAccount` API\.

1. After AWS Organizations creates the account, AWS Control Tower completes the provisioning process by applying blueprints and controls\.

1. Service Catalog continues to poll AWS Control Tower to check for completion of the provisioning process\.

1. When the workflow in AWS Control Tower is complete, Service Catalog finalizes the account's state and informs you \(the requester\) of the result\.

## Permissions required<a name="permissions"></a>

The permissions required for each method of provisioning and updating are discussed in each section, respectively\. With the appropriate user group permissions, provisioners can specify standardized baselines and network configurations for any accounts in their organization\.

For general information about permissions required in AWS Control Tower, see [Using identity\-based policies \(IAM policies\) for AWS Control Tower](access-control-managing-permissions.md)\. For information about roles and accounts in AWS Control Tower, see [How AWS Control Tower works with roles to create and manage accounts](roles-how.md)\.

## <a name="w32aac32c17"></a>

**Security for your accounts**  
You can find guidance about best practices to protect the security of your AWS Control Tower management account and member accounts in the AWS Organizations documentation\.  
[Best practices for the management account](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_best-practices_mgmt-acct.html)
[Best practices for member accounts](https://docs.aws.amazon.com/organizations/latest/userguide/best-practices_member-acct.html)