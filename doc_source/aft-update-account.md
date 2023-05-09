# Update an existing account<a name="aft-update-account"></a>

 You can update accounts that AFT provisions by editing previously submitted account requests and running `git push`\. This command invokes the account provisioning workflow and can process account update requests\. You can update the input for `ManagedOrganizationalUnit`, which is part of the required value for `control_tower_parameters`, and other parameters in the account request Terraform file\. For more information, see [Provision a new account with AFT](https://docs.aws.amazon.com/controltower/latest/userguide/aft-provision-account.html)\. 

**Note**  
 The input that you provide for `control_tower_parameters` can't be changed during account provisioning\.   
 The supported formats for specifying `ManagedOrganizationalUnit` in the **aft\-account\-request** repository include `OUName` and `OUName (OU-ID)`\. 

## Update an account that AFT doesn't provision<a name="aft-update-account-not-provision"></a>

 You can update AWS Control Tower accounts created outside of AFT by specifying the account in the **aft\-account\-request** repository\. 

**Note**  
 Make sure that all account details are correct and consistent with the AWS Control Tower organization and respective AWS Service Catalog provisioned product\. 

**Prerequisites for updating an existing AWS account with AFT**
+  The AWS account must be enrolled in AWS Control Tower\. 
+  The AWS account must be part of the AWS Control Tower organization\. 