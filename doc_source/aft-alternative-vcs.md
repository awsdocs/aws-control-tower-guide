# Alternatives for version control of source code in AFT<a name="aft-alternative-vcs"></a>

AFT natively uses AWS CodeCommit for a source code version control system \(VCS\), but it allows other [AWS CodeStar connections](https://docs.aws.amazon.com/dtconsole/latest/userguide/supported-versions-connections.html) that meet your business requirements or existing architecture\. You can specify a third\-party VCS as part of the AFT deployment prerequisites\.

**AFT supports the following source code control alternatives:**
+ GitHub
+ GitHub Enterprise Server
+ BitBucket

If you select AWS CodeCommit as your VCS, no additional steps are required\. By default, AFT creates the necessary `git` repositories in your environment, with default names\. However, you can override the default repository names for CodeCommit, as needed, to comply with your organizational standards\.

## Set up an alternative source code version control system \(custom VCS\) with AFT<a name="aft-alternate-vcs-steps"></a>

To set up an alternative source code version control system for your AFT deployment, follow these steps\.

**Step 1: Create `git` repositories in a supported third\-party version control system \(VCS\)\.**

If you are not using AWS CodeCommit, you must create `git` repositories in your AFT\-supported, third\-party VCS provider environment for the following items\.
+ **AFT account requests\.** [Sample code available](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-account-request) \. For more information about AFT account requests, see [ Provision a new account with AFT ](aft-provision-account.md)\.
+ **AFT account provisioning customizations\.** [Sample code available](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-account-provisioning-customizations) \. For more information on AFT account provisioning customizations, see [Create your AFT account provisioning customizations state machine](aft-provisioning-framework.md#aft-create-customizations)\.
+ **AFT global customizations\.** [Sample code available](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-global-customizations) \. For more information on AFT global customizations, see [Account customizations](aft-account-customization-options.md)\.
+ **AFT account customizations\.** [Sample code available](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-account-customizations) \. For more information on AFT account customizations, see [Account customizations](aft-account-customization-options.md)\.

**Step 2: Specify the VCS configuration parameters required for AFT deployment**

The following input parameters are needed to configure your VCS provider as part of the AFT deployment\.
+ **vcs\_provider**: If you are not using AWS CodeCommit, specify the VCS provider as `"bitbucket"`, `"github"`, or `"githubenterprise"`, based on your use case\.
+ **github\_enterprise\_url**: For GitHub Enterprise customers only, specify the GitHub URL\.
+ **account\_request\_repo\_name**: By default, this value is set to `aft-account-request` for AWS CodeCommit users\. If you created your repository with a new name in CodeCommit or in an AFT\-supported, third\-party VCS provider environment, update this input value with your actual repository name\. For BitBucket, Github, and GitHub Enterprise, the repository name must have the format `[Org]/[Repo]`\.
+ **account\_customizations\_repo\_name**: By default, this value is set to `aft-account-customizations` for AWS CodeCommit users\. If you created repository with a new name in CodeCommit or in an AFT\-supported, third\-party VCS provider environment, update this input value with your repository name\. For BitBucket, Github, and GitHub Enterprise, the repository name must have the format `[Org]/[Repo]`\.
+ **account\_provisioning\_customizations\_repo\_name**: By default, this value is set to `aft-account-provisioning-customizations` for AWS CodeCommit users\. If you created repository with a new name in AWS CodeCommit or in an AFT\- supported, third\-party VCS provider environment, update this input value with your repository name\. For BitBucket, Github, and GitHub Enterprise, the repository name must have the format `[Org]/[Repo]`\.
+ **global\_customizations\_repo\_name**: By default, this value is set to `aft-global-customizations` for AWS CodeCommit users\. If you created repository with a new name in CodeCommit or in an AFT\-supported, third\-party VCS provider environment, update this input value with your repository name\. For BitBucket, Github, and GitHub Enterprise, the repository name must have the format `[Org]/[Repo]`\.
+ **account\_request\_repo\_branch**: The branch is `main` by default, but the value can be overridden\.

By default, AFT sources from the `main` branch of each `git` repository\. You can override the branch name value with an additional input parameter\. For more information about input parameters, refer to the README file in the [AFT Terraform module](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/blob/main/README.md#inputs)\.

**Step 3: Complete the AWS CodeStar connection for third\-party VCS providers**

When your deployment runs, AFT either creates the required AWS CodeCommit repositories, or it creates an AWS CodeStar connection for your chosen third\-party VCS provider\. In case of the latter, you must manually sign in to the AFT management account’s console to complete the pending AWS CodeStar connection\. See [the AWS CodeStar documentation](https://docs.aws.amazon.com/dtconsole/latest/userguide/connections-update.html) for further instructions on completing the AWS CodeStar connection\. 