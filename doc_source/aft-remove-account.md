# Remove an account from AFT<a name="aft-remove-account"></a>

 This topic describes how to remove an account from AFT, so the AFT pipeline stops deploying and updating the account\. 

**Important**  
 Removing an account from the AFT pipeline is irreversible and can result in a loss of state\. 

 You might remove an account from AFT when you want to close an account for a retired application, isolate a compromised account, or move an account from one organization to another organization\. 

**Note**  
 Removing an account from AFT is different than deleting an AWS Control Tower account or AWS account\. When you remove an account from AFT, AWS Control Tower still manages the account\. To delete an AWS Control Tower account or AWS account, see the following:   
 [Unmanage an account](https://docs.aws.amazon.com/controltower/latest/userguide/unmanage-account.html) in the *AWS Control Tower User Guide*\. 
 [Closing an account](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/close-account.html) in the *AWS Billing User Guide*\. 

**To remove an account from the AFT pipelines**

 The following procedure describes how to remove an account from AFT\. 

1.  ** Remove account from `git` repository that stores account requests ** 

    In the `git` repository where you store account requests, delete the account request for the account you want to remove from AFT\. 

    When you remove an account request from the account request repository, AFT deletes the customization pipeline and account metadata\. For more information, see the [1\.8\.0 release notes](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/releases/tag/1.8.0) for AFT on GitHub\. 

1.  ** Delete Terraform workspace \(For Terraform Cloud and Terraform Enterprise customers only\) ** 

    Delete the global customizations and account customizations workspaces for the account that you want to remove from AFT\. 

1.  ** Delete Terraform state from Amazon S3 backend ** 

    In the AFT management account, delete all relevant folders inside of the Amazon S3 buckets for the account that you want to remove from AFT\. 
**Tip**  
 In the following examples, replace `012345678901` with the AFT management account ID number\. 

**Example: Terraform OSS**  
 When you choose Terraform OSS, you find 3 folders for each account in the `aft-backend-012345678901-primary-region` and `aft-backend-012345678901-secondary-region` Amazon S3 buckets\. These folders are related to the *account customizations state*, *customizations pipeline state*, and *global customizations state* 

**Example: Terraform Cloud or Terraform Enterprise**  
 When you choose Terraform Cloud or Terraform Enterprise, you find a folder for each account in the `aft-backend-012345678901-primary-region` and `aft-backend-012345678901-secondary-region` Amazon S3 buckets\. These folders are related to the *customizations pipeline state*\. 