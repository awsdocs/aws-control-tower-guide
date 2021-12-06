# Post\-deployment steps<a name="aft-post-deployment"></a>

After the AFT infrastructure deployment is complete, follow these additional steps to complete the setup process and get ready to provision accounts\. 

**Step 1: \(Optional\) Complete AWS CodeStar connections with your desired VCS provider**

If you choose a third\-party VCS provider, AFT establishes AWS CodeStar connections, and you confirm them\. Refer to [Alternatives for version control of source code in AFT](aft-alternative-vcs.md) to learn how to set up AFT with your preferred VCS\.

The initial step of establishing the AWS CodeStar connection is accomplished by AFT\. You must confirm the connection\.

**Step 2: \(Mandatory\) Grant access to AWS Control Tower account factory portfolio**

AFT requires access to the AWS Control Tower account factory portfolio in AWS Service Catalog for the *AFT management account*, so AFT can process your account provisioning requests\. To enable portfolio access, grant permission for the **AWSAFTExecution** role, for the AWS Control Tower account factory portfolio\. See [the AWS Service Catalog documentation](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/catalogs_portfolios_users.html) for instructions on how to grant access to the AWS Service Catalog portfolios\.

**Step 3: \(Mandatory\) Populate each repository**

AFT requires that you manage [four repositories:](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos)

1. Account requests – This repository handles placing or updating account requests\. [Examples available](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-account-request) \. For more information about AFT account requests, see [Provision a new account with AFT](taf-account-provisioning.md#aft-provision-account)\.

1. AFT account provisioning customizations – This repository manages customizations that are applied to all accounts created by and managed with AFT, before beginning the global customizations stage\. [Examples available](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-account-provisioning-customizations) \. To create AFT account provisioning customizations, see [Create your AFT account provisioning customizations state machine](aft-provisioning-framework.md#aft-create-customizations)\.

1. Global customizations – This repository manages customizations that are applied to all accounts created by and managed with AFT\. [Examples available](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-global-customizations) \. To create AFT global customizations, see [Apply global customizations](aft-account-customization-options.md#aft-global-customizations)\.

1. Account customizations – This repository manages customizations that are applied only to specific accounts created by and managed with AFT\. [Examples available](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-account-customizations) \. To create AFT account customizations, see [Apply account customizations](aft-account-customization-options.md#aft-account-customizations)\.

AFT expects each of these repositories to follow a specific directory structure\. Templates with which to populate your repositories, and instructions for how to populate those templates, are available in the Account Factory for Terraform module in the [AFT github repository](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main)\.