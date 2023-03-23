# Provision a new account with AFT<a name="aft-provision-account"></a>

 To provision a new account with AFT, create an `account-request file` that contains the appropriate values for the parameters in your `git` repository\. You can see an example of an `account-request file` on [GitHub](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-account-request)\. 

**Note**  
 When you run the `git push` command to begin processing your account request, the command triggers the `ct-aft-account-request` operation in the AWS CodePipeline that's in your AFT management account\. 

**Include the following parameters in your `account-request file`:**
+  The value of **module name** must be unique per AWS account request\. 
+  The value of **module source** is the path to the account request Terraform module that AFT provides\. 
+  The value of `control_tower_parameters` captures the input that's required to create an AWS Control Tower account\. The value includes the following input fields: 
  + **AccountEmail**
  + **AccountName**
  +  **ManagedOrganizationalUnit** 
  + **SSOUserEmail**
  + **SSOUserFirstName**
  + **SSOUserLastName**

**Note**  
 The supported formats for specifying the `ManagedOrganizationalUnit` in the **aft\-account\-request** repository include the following:   
 `OUName` 
 `OUName (OU-ID)` 
+  The parameter `account_tags` captures user\-defined keys and values, which can tag AWS accounts according to your business criteria\. For more information, see [Tagging AWS Organizations resources](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_tagging.html) in the *AWS Organizations User Guide*\. 
+  The value of `change_management_parameters` captures additional information, such as the reason why the account request was created and the identifier of the user who initiated the account request\. The values include the following input fields: 
  + **change\_requested\_by**
  + **change\_reason**
+  The parameter `custom_fields` captures custom keys and values\. The values deploy as SSM parameters in the vended account that's under the namespace **"/aft/account\-request/custom\-fields/"**\. If a custom field is removed from the account request, the field is removed from the SSM Parameter Store for the vended account\. The `custom_fields` parameter allows you to collect additional metadata with the account request\. This metadata can trigger additional processing during provisioning or when updating an account\. You can refer to this metadata during account customizations to determine the proper guardrails that will deploy\. For example, an account that's subject to regulatory compliance might deploy additional AWS Config Rules\. 
+  \(Optional\) The parameter `account_customizations_name` captures the account template folder to be used for account customizations\. For more information, see [Account customizations](aft-account-customization-options.md)\. 

 For more information about setting up accounts in AWS Control Tower, see [Provision and manage accounts with Account Factory](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory.html)\. 