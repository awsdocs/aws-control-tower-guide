# Remove an account from AFT<a name="aft-remove-account"></a>

**Important**  
 Removing an account from the AFT pipeline is irreversible and can result in a loss of state\. 

 This section describes how to remove an account from AFT, so the pipeling stops deploying and updating it\. For example, you might close an account for an application that you're retiring, isolating a compromised account, or moving an account from one organization to another\. 

 The process for removing an account from AFT isn't the same as removing an account from AWS Control Tower or deleting an account from AWS\. When you follow the procedure, you're not deleting an account\. Instead, you're removing the account, so AFT no longer manages it\. CloudTrail still manages the account\. To delete the account, you may follow the steps to remove the account from AWS Control Tower management, and remove the account in the [Billing console](https://console.aws.amazon.com/billing/), as you would for any other AWS account\. 

**To remove an account from AFT**

1.  ** Remove account from `git` repository that stores your account requests ** 

    In the `git` repository where you store your account requests, delete the account request for the account you intend to remove from AFT\. 
**Note**  
 When you remove an account request from the account request repository, AFT deletes the customization pipeline and the account's metadata\. For more information, see the [1\.8\.0 release notes](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/releases/tag/1.8.0) for AFT on GitHub\. 

1.  ** Delete Terraform workspace \(For Terraform Cloud and Terraform Enterprise customers only\) ** 

    Delete the global customizations and account customizations workspaces for the account that you intend to remove from AFT\. 

1.  ** Delete the Terraform state from the Amazon S3 backend ** 

    In the AFT management account, delete all relevant folders inside the Amazon S3 buckets for the account that you intend to remove from AFT\. In the following examples, replace the placeholder number `012345678901` with the AFT management account ID number\. 

    When you choose Terraform OSS, you find 3 folders for each account in the `aft-backend-012345678901-primary-region` and `aft-backend-012345678901-secondary-region` Amazon S3 buckets\. These folders are related to the customizations pipeline state, account customizations state, and global customizations state\. 

    When you choose Terraform Cloud or Terraform Enterprise, you find a folder for each account in the `aft-backend-012345678901-primary-region` and `aft-backend-012345678901-secondary-region` S3 buckets\. These folders are related to the customizations pipeline state\. 