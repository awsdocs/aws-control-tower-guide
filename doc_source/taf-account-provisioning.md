# Provision accounts with AWS Control Tower Account Factory for Terraform<a name="taf-account-provisioning"></a>

AWS Control Tower Account Factory for Terraform \(AFT\) follows a GitOps model to automate the processes of account provisioning and account updating in AWS Control Tower\. You'll create an *account request* Terraform file, which provides the necessary input that triggers the AFT workflow for account provisioning\.

When account provisioning or updating is complete, the AFT workflow continues\. It runs the AFT account provisioning framework and account customizations steps\.

**Note**  
AFT does not affect workflow performance in AWS Control Tower\. For example, if you provision an account through Account Factory, or through AFT, the same back\-end workflow occurs, without any timing differences\. AFT enables automation, therefore the time required for provisioning may be less noticeable\.

## Prerequisites<a name="aft-prerequisites"></a>

Before AFT can begin to provision accounts, some prerequisite steps are required\. You must create:
+ **A fully deployed AFT environment\.** For more information about AFT deployment, see one or more of these sections:
  + [Deploy AWS Control Tower Account Factory for Terraform \(AFT\)](aft-getting-started.md)
  + [Overview of AWS Control Tower Account Factory for Terraform](aft-overview.md)
  + [Configure and launch your AWS Control Tower Account Factory for Terraform](aft-getting-started.md#aft-configure-and-launch)
+ **One or more AFT `git` repositories in your environment\.** For more information, see [Post\-deployment steps](aft-post-deployment.md) in [Deploy AWS Control Tower Account Factory for Terraform \(AFT\)](aft-getting-started.md)\.
+ **An account template folder in your chosen `git` repository for account customizations\.** This folder is optional\.

## Provision a new account with AFT<a name="aft-provision-account"></a>

To begin provisioning, set up a *create account request* file that contains appropriate input values for the following parameters in your chosen `git` repository\. You can view an example account request file, which you can push to the `git` repository you've set up for storing your AFT account requests\. For more information, see **Step 3** of your [post\-deployment steps](https://docs.aws.amazon.com/controltower/latest/userguide/aft-post-deployment.html)\.

 When you give the `git push` command, it triggers the `ct-aft-account-request` operation in the AWS CodePipeline in the AFT management account, to process your account request\.

**Include the following list of parameters in your file:**
+ The value of **module name** must be unique per AWS account request\.
+ The value of **module source** is the path to the account request Terraform module provided by AFT\. 
+ The value of `control_tower_parameters` captures the input required to create an AWS Control Tower account, as follows\.
  + **AccountEmail**
  + **AccountName**
  + **ManagedOrganizationalUnit**
  + **SSOUserEmail**
  + **SSOUserFirstName**
  + **SSOUserLastName**

For more information about setting up accounts in AWS Control Tower, see [Provision and manage accounts with Account Factory](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory.html)\.
+ The parameter `account_tags` captures user\-defined keys and values that can tag AWS accounts according to your business criteria\. See [the AWS Organizations documentation](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_tagging.html) for more information on account tags\.
+ The parameter `change_management_parameters` captures additional information you may want to keep, such as the reason for the account request, and and identifier for who initiated the request\.
  + `change_requested_by`
  + `change_reason`
+ The parameter `custom_fields` captures custom keys and values\. You may want to collect additional metadata that can be logged with the account request\. This metadata can trigger additional processing, either during provisioning or when updating an account\. You can refer to this metadata during account customizations to determine the proper guardrails to deploy\. For example, an account that is subject to regulatory compliance could deploy an additional AWS Config rule\.
+ The parameter `account_customizations_name` is optional\. It captures the specified account template folder for account customizations\. See [Account customizations](aft-account-customization-options.md) for more information\.

## Update an existing account<a name="aft-update-account"></a>

You may update exsting accounts that AFT has provisioned by updating previously submitted account requests\. The `git push` action triggers the account provisioning workflow, which processes an account update request\.

When you're updating an existing account, AFT allows you to update the `ManagedOrganizationalUnit` field of the `control_tower_parameters` input, and other fields that are NOT part of the `control_tower_parameters` input\. The input to `control_tower_parameters`, which you provided during provisioning, cannot be changed\.

**Note**  
You can update existing AWS Control Tower accounts that were created outside of AFT by specifying the account in the `aft-account-request` repository\.

## Submit multiple account requests<a name="aft-multiple-account-requests"></a>

Although AWS Control Tower account factory can process a single request at any time, the AFT pipeline allows you to submit multiple account requests\. AFT queues all the requests to be processed by AWS Control Tower account factory, and they are processed in first\-in, first\-out \(FIFO\) order\.

You can create a single account request Terraform file for each account, or you can cascade multiple requests in a single Terraform file\.