# Deploy AWS Control Tower Account Factory for Terraform \(AFT\)<a name="aft-getting-started"></a>

This section is intended for administrators of AWS Control Tower environments who wish to set up Account Factory for Terraform \(AFT\) in their existing environment\. It describes how to set up an Account Factory for Terraform \(AFT\) environment with a new, dedicated AFT management account\.

**Note**  
AFT is deployed with a Terraform module, available in the [AFT repository](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main)\. We recommend that you refer to AFT Terraform modules from the GitHub source, and do not clone it, so that you can control and consume updates to the modules as they are available\.

 For details about the latest releases of the AWS Control Tower Account Factory for Terraform \(AFT\) functionality, see [the Releases file](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/releases) for this GitHub repository\.

**Deployment prerequisites**

AFT has prerequisites for installation and for running\. This guide assumes that you can create and interact with each of the following resources required to deploy the AFT solution:
+  An AWS Control Tower landing zone\.
+ A Terraform version and distribution\. See [Terraform version supported](version-supported.md) for more details about compatible AFT Terraform distributions\.
+ A home Region for your AWS Control Tower landing zone\.
+ If you are not choosing AWS CodeCommit – you must set up available git\-based repositories if you plan to use a third\-party version control system \(VCS\)\. See [Alternatives for version control of source code in AFT](aft-alternative-vcs.md) for instructions on how to set up these repositories in your environment\.
+ A runtime environment in which to run the Terraform module that installs AFT\.
+ AFT feature options that you can select as part of your AFT deployment\. For more information, see [Enable feature options](aft-feature-options.md)\.

**Note**  
The easiest deployment for AFT is to choose AWS CodeCommit as your VCS and Terraform OSS as your distribution\. If you choose another VCS or Terraform distribution, be sure to read the customization instructions provided in other sections of this guide\.

## Configure and launch your AWS Control Tower Account Factory for Terraform<a name="aft-configure-and-launch"></a>

Five steps are required to configure and launch your AFT environment\.

**Step 1: Launch your AWS Control Tower landing zone**

Before launching AFT, you must have a working AWS Control Tower landing zone in your AWS account\. You will configure and launch AFT from the AWS Control Tower management account\.

**Step 2: Create a new organizational unit for AFT \(recommended\)**

We recommend that you create a separate OU in your AWS Organization, where you will deploy the AFT management account\. Create an OU through your *AWS Control Tower management account*\. For instructions on how to create an OU, refer to [Create an organization](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_org_create.html#create-org) in the *AWS Organizations User Guide*\.

**Step 3: Provision the AFT management account**

AFT requires a separate AWS account to manage and orchestrate its own requests\. From the AWS Control Tower management account that's associated with your AWS Control Tower landing zone, you'll provision this account for AFT\.

To provision the AFT management account, see [Provision Account Factory accounts with AWS Service Catalog](provision-as-end-user.md)\. When specifying an OU, be sure to select the OU you created in Step 2\. When specifying a name, use `AFT-Management`\.

**Note**  
It can take up to 30 minutes for the account to be fully provisioned\. Validate that you have access to the AFT management account\.

**Step 4: Ensure that the Terraform environment is available for deployment**

This step assumes that you are experienced with Terraform, and that you have procedures in place for executing Terraform\. AFT supports Terraform Version `0.15.x` or later\.

**Step 5: Call the Account Factory for Terraform module to deploy AFT**

The Account Factory for Terraform module must be called while you are authenticated with **AdministratorAccess** credentials in your AWS Control Tower management account\.

AWS Control Tower, through the AWS Control Tower management account, sends a Terraform module that establishes all infrastructure necessary to orchestrate your AWS Control Tower account factory requests\. You can view that module in [the AFT repository](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main)\. Refer to the module’s README file for information about the input required to run the module and deploy AFT\.

If you have established pipelines for managing Terraform in your environment, you can integrate this module into your existing workflow\. Otherwise, run the module from any environment that is authenticated with the required credentials\.

**Note**  
The AFT Terraform module does not manage a backend Terraform state\. Be sure to preserve the Terraform state file that’s generated, after applying the module, or set up a Terraform backend using Amazon S3 and DynamoDB\.

Certain input variables may contain sensitive values, such as a private `ssh` key or Terraform token\. These values may be viewable as plain text in Terraform state file, depending on your deployment method\. It is your responsibility to protect the Terraform state file, which may contain sensitive data\. See [the Terraform documentation](https://www.terraform.io/docs/language/state/sensitive-data.html) for more information\.

**Note**  
Deploying AFT through the Terraform module requires several minutes\. Initial deployment may require up to 30 minutes\. As a best practice, use AWS Security Token Service \(STS\) credentials and ensure that the credentials have a timeout sufficient for a full deployment, because a timeout causes the deployment to fail\. The minimum timeout for AWS STS credentials is 60 minutes or more\. Alternatively, you can leverage any IAM user that has **AdministratorAccess** permissions in the AWS Control Tower management account\.
