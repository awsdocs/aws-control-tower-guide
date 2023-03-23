# Update an existing account<a name="aft-update-account"></a>

 You might update accounts that AFT provisions by updating previously submitted account requests\. The `git push` command that triggers the account provisioning workflow can be used to process an account update request\. When updating accounts, AFT allows you to update the input for the field `ManagedOrganizationalUnit`, which is part of the required value for `control_tower_parameters`\. The input that you provide for `control_tower_parameters` during account provisioning can't be changed\. AFT also allows you to update the other parameters in your `create-account-request-file`\. For information about these parameters, see [Provision a new account with AFTs](https://docs.aws.amazon.com/controltower/latest/userguide/aft-provision-account.html)\. 

**Note**  
 The supported formats for specifying the `ManagedOrganizationalUnit` in the **aft\-account\-request** repository include the following:   
 `OUName` 
 `OUName (OU-ID)` 

## Update an account that's not provisioned by AFT<a name="aft-update-account-not-provision"></a>

 You can update AWS Control Tower accounts that were created outside of AFT by specifying the account in the **aft\-account\-request** repository\. Make sure that all account details are correct and consistent with the AWS Control Tower organization and respective AWS Service Catalog provisioned product\. 

 **Prerequisites for updating an existing AWS account with AFT** 
+  The AWS account must be part of the AWS Control Tower organization\. 
+  The AWS account must be enrolled in AWS Control Tower\. 