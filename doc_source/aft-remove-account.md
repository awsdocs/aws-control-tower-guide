# Remove an account from AFT<a name="aft-remove-account"></a>

This section walks you through the steps to remove accounts from AFT, so they are no longer deployed or updated by the pipeline\. For example, you may be closing accounts for retiring applications, isolating accounts that are compromised, or moving accounts from one organization to another\. 

Removing an account from AFT is not the same process as removing an account from AWS Control Tower or deleting it from AWS\. When you follow these steps, the account is not deleted, it is only removed from management by AFT\. It is still managed by AWS Control Tower\. To delete the account completely, you may follow the steps to remove the account from AWS Control Tower management, and remove the account in the [Billing console](https://console.aws.amazon.com/billing/), as you would for any other AWS account\.

**Important**  
Removing an account from the AFT pipeline is irreversible\. It can result in a loss of state\.

**Step 1: Remove account from the `git` repository that stores your account requests**

In the `git` repository where you store account requests, delete the account request for the account you intend to remove from AFT\.

**Step 2: Delete the Terraform workspace \(For Terraform Cloud and Terraform Enterprise customers only\)**

Delete the global customizations and account customizations workspaces for the account you intend to remove from AFT\. 

**Step 3: Delete the Terraform state from the Amazon S3 backend**

In the AFT management account, delete all relevant folders inside Amazon S3 buckets for the account you intend to remove from AFT\. In the examples that follow, replace the placeholder number `012345678901` with the AFT management account ID number\.

If you chose Terraform OSS, you will find 3 folders for each account \(related to customizations pipeline state, global and account customizations state\) in the `aft-backend-012345678901-primary-region` and `aft-backend-012345678901-secondary-region` Amazon S3 buckets\.

If you chose Terraform Cloud or Terraform Enterprise, you will find a folder for each account \(related to customizations pipeline state\) in the `aft-backend-012345678901-primary-region` and `aft-backend-012345678901-secondary-region` S3 buckets\. 
