# Provision and manage accounts in AWS Control Tower<a name="provision-and-manage-accounts"></a>

This chapter includes an overview and procedures for provisioning and managing member accounts in your AWS Control Tower landing zone\. For more information about accounts in AWS Control Tower, see  [About AWS accounts in AWS Control Tower](accounts.md)\.

## Methods of provisioning<a name="methods-of-provisioning"></a>

AWS Control Tower provides four methods for creating and updating member accounts\. Two methods are primarily console\-based, and two methods are primarily automated\.

**Overview**

The standard way to create member accounts is through Account Factory, a console\-based product that's part of the AWS Service Catalog\. If your landing zone is not in a state of drift, you can use **Enroll account** as a method to add new accounts from the console, as well as to enroll existing AWS accounts into AWS Control Tower\.

If you intend to set up or manage many accounts as a group, you may prefer an automated method: either to configure new accounts programmatically using IAM roles and Lambda functions, or to use a `GitOps` approach with AFT\.

**Console\-based methods:**
+ Through the Account Factory console that is part of AWS Service Catalog\. See [Provision and manage accounts with Account Factory](account-factory.md)\.
+ Through the **Enroll account** feature within AWS Control Tower, if your landing zone is not in a state of drift\. See [Create or Enroll An Individual Account](quick-account-provisioning.md)\.

**Automated methods:**
+ From your AWS Control Tower landing zone's management account, using Lambda code and appropriate IAM roles\. See [Automated Account Provisioning With IAM Roles](roles-how.md#automated-provisioning)\.
+ From the AWS Control Tower Account Factory for Terraform \(AFT\), which relies on Account Factory and a GitOps model to allow automation of account provisioning and updating\. See [Provision accounts with AWS Control Tower Account Factory for Terraform](taf-account-provisioning.md)\.

## Permissions required<a name="permissions"></a>

The permissions required for each method of provisioning and updating are discussed in each section, respectively\. With the appropriate user group permissions, provisioners can specify standardized baselines and network configurations for any accounts in their organization\.

For general information about permissions required in AWS Control Tower, see [Using Identity\-Based Policies \(IAM Policies\) for AWS Control Tower](access-control-managing-permissions.md)\. For information about roles and accounts in AWS Control Tower, see [How AWS Control Tower works with roles to create and manage accounts](roles-how.md)\.