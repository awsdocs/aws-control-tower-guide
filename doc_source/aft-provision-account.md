# Provision a new account with AFT<a name="aft-provision-account"></a>

To begin provisioning, set up a *create account request* file that contains appropriate input values for the following parameters in your chosen `git` repository\. You can view an example account request file, which you can push to the `git` repository you've set up for storing your AFT account requests\. For more information, see **Step 2** of your [post\-deployment steps](https://docs.aws.amazon.com/controltower/latest/userguide/aft-post-deployment.html)\.

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
+ The parameter `account_tags` captures user\-defined keys and values that can tag AWS accounts according to your business criteria\. For more information about account tags, see the [Tagging AWS Organizations resources](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_tagging.html) in the *AWS Organizations User Guide*\.
+ The parameter `change_management_parameters` captures additional information that you may want to keep, such as the reason for the account request, and the identifier for who initiated the request\.
  + `change_requested_by`
  + `change_reason`
+  The parameter `custom_fields` captures custom keys and values\. The values are deployed as SSM Parameters in the vended account under the namespace: **"/aft/account\-request/custom\-fields/"**\. If a custom field is removed from the account request, the field is removed from the SSM Parameter Store for that vended account\. The `custom_fields` parameter allows you to collect additional metadata with the account request\. This metadata can trigger additional processing, either during provisioning or when updating an account\. You can refer to this metadata during account customizations to determine the proper guardrails to deploy\. For example, an account that is subject to regulatory compliance could deploy additional AWS Config Rules\.
+ The parameter `account_customizations_name` is optional\. It captures the specified account template folder for account customizations\. See [Account customizations](aft-account-customization-options.md) for more information\.