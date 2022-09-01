# Provision a new account with AFT<a name="aft-provision-account"></a>

To begin provisioning, set up a *create account request* file that contains appropriate input values for the following parameters in your chosen `git` repository\. You can view an example account request file, which you can push to the `git` repository you've set up for storing your AFT account requests\. For more information, see **Step 3** of your [post\-deployment steps](https://docs.aws.amazon.com/controltower/latest/userguide/aft-post-deployment.html)\.

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
+ The parameter `account_tags` captures user\-defined keys and values that can tag AWS accounts according to your business criteria\. See [the AWS Organizations documentation](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_tagging.html) for more information on account tags\.
+ The parameter `change_management_parameters` captures additional information you may want to keep, such as the reason for the account request, and and identifier for who initiated the request\.
  + `change_requested_by`
  + `change_reason`
+ The parameter `custom_fields` captures custom keys and values\. You may want to collect additional metadata that can be logged with the account request\. This metadata can trigger additional processing, either during provisioning or when updating an account\. You can refer to this metadata during account customizations to determine the proper guardrails to deploy\. For example, an account that is subject to regulatory compliance could deploy an additional AWS Config rule\.
+ The parameter `account_customizations_name` is optional\. It captures the specified account template folder for account customizations\. See [Account customizations](aft-account-customization-options.md) for more information\.