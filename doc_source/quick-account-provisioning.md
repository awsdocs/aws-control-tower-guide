# Enroll an existing account<a name="quick-account-provisioning"></a>

The **Enroll account** feature is available in the AWS Control Tower console, for enrolling existing AWS accounts so that they are governed by AWS Control Tower\. For more information, see [Enroll an existing AWS account](enroll-account.md)\.

The **Enroll account** capability is available when your landing zone is not in a state of [drift](https://docs.aws.amazon.com/controltower/latest/userguide/drift.html)\. To view this capability in the console:
+ Navigate to the **Organization** page in AWS Control Tower\.
+ Find the name of the account you wish to enroll\. To find it, choose **Accounts only** from the dropdown menu at the upper right, and then locate the account name in the filtered table\.
+ Follow the steps for enrolling an individual account, as shown in the [Steps to enroll an account](#enrollment-steps) section\.

**Note**  
When you are enrolling an existing AWS account, be sure to verify the existing email address\. Otherwise, a new account may be created\.

Certain errors may require that you refresh the page and try again\. If your landing zone is in a state of drift, you may not be able to use the **Enroll account** capability successfully\. You'll need to provision new accounts through Account Factory until your landing zone drift has been resolved\. 

When you enroll accounts from the AWS Control Tower console, you must be signed into an account with an IAM user that has the `AWSServiceCatalogEndUserFullAccess` policy enabled, along with Administrator access permissions to use the AWS Control Tower console, and you cannot be signed in as the **Root** user\.

Accounts that you enroll may be updated by means of AWS Service Catalog and the AWS Control Tower account factory, as you would update any other account\. Update procedures are given in the section called [Update and move account factory accounts with AWS Control Tower or with AWS Service Catalog](updating-account-factory-accounts.md)\. 

## Steps to enroll an account<a name="enrollment-steps"></a>

After the **AdministratorAccess** permission \(policy\) is in place in your existing account, follow these steps to enroll the account:

**To enroll an individual account in AWS Control Tower**
+ Navigate to the AWS Control Tower **Organization** page\.
+ On the **Organization** page, accounts that are eligible to be enrolled allow you to select **Enroll** from the **Actions** dropdown menu at the top of the section\. These accounts also show an **Enroll account** button when you view them on the **Account details** page\.
+ When you choose **Enroll account**, you’ll see an **Enroll account** page, where you are prompted to add the `AWSControlTowerExecution` role to the account\. For some instructions, see [Manually add the required IAM role to an existing AWS account and enroll it](enroll-manually.md)\.
+ Next, select a registered OU from the drop down list\. If the account is already in a registered OU, this list will show the OU\.
+ Choose **Enroll account**\.
+ You’ll see a modal reminder to add the `AWSControlTowerExecution` role and confirm the action\.
+ Choose **Enroll**\.
+ AWS Control Tower begins the process of enrollment, and you are directed back to the **Account details** page\.

## Common causes for failure of enrollment<a name="common-causes-for-enrollment-failure"></a>
+ To enroll an existing account, the **AWSControlTowerExecution** role must be present in the account you're enrolling\.
+ Your IAM principal may lack the necessary permissions to provision an account\.
+ AWS Security Token Service \(AWS STS\) is disabled in your AWS account in your home region, or in any region supported by AWS Control Tower\.
+ You may be signed in to an account that needs to be added to the Account Factory Portfolio in AWS Service Catalog\. The account must be added before you'll have access to Account Factory so you can create or enroll an account in AWS Control Tower\. If the appropriate user or role is not added to the Account Factory Portfolio, you’ll receive an error when you attempt to add an account\. For instructions on how to grant access to AWS Service Catalog portfolios, see [Granting access to users](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/catalogs_portfolios_users.html)\.
+ You may be signed in as root\.
+ The account you're trying to enroll may have AWS Config settings that are residual\. In particular, the account may have a configuration recorder or delivery channel\. These must be deleted or modified through the AWS CLI before you can enroll an account\. For more information, see [Enroll accounts that have existing AWS Config resources](existing-config-resources.md) and [Interacting with AWS Control Tower using AWS CloudShell](using-aws-with-cloudshell.md#cshell-examples)\. 
+ If the account belongs to another OU with a management account, including another AWS Control Tower OU, you must terminate the account in its current OU before it can join another OU\. Existing resources must be removed in the original OU\. Otherwise, enrollment will fail\.
+ Account provisioning and enrollment fails if your destination OU’s SCPs don’t allow you to create all of the resources required for that account\. For example, an SCP in your destination OU may block resource creation without certain tags\. In this case, account provisioning or enrollment fails, because AWS Control Tower does not support tagging of resources\. For help, contact your account representative, or AWS Support\.

For more information about how AWS Control Tower works with roles when you're creating new accounts or enrolling existing accounts, see [How AWS Control Tower works with roles to create and manage accounts](roles-how.md)\.

**Tip**  
If you cannot confirm that an existing AWS account meets the enrollment prerequisites, you can set up an **Enrollment OU** and enroll the account into that OU\. After enrollment is successful, you can move the account to the desired OU\. If enrollment happens to fail, no other accounts or OUs are affected by the failure\.

If you have doubts that your existing accounts and their configurations are compatible with AWS Control Tower, you can follow the best practice recommended in the following section\. 

**Recommended: You can set up a two\-step approach to account enrollment**
+ First, use an AWS Config *conformance pack* to evaluate how your accounts may be affected by some AWS Control Tower controls\. To determine how enrollment into AWS Control Tower may affect your accounts, see **[ Extend AWS Control Tower governance using AWS Config conformance packs](http://aws.amazon.com/blogs/mt/extend-aws-control-tower-governance-using-aws-config-conformance-packs/)**\. 
+ Next, you may wish to enroll the account\. If the compliance results are satisfactory, the migration path is easier because you can enroll the account without unexpected consequences\.
+ After you've done your evaluation, if you decide to set up an AWS Control Tower landing zone, you may need to remove the AWS Config delivery channel and configuration recorder that were created for your evaluation\. Then you'll be able to set up AWS Control Tower successfully\.

**Note**  
The conformance pack also works in situations where the accounts are located in OUs registered by AWS Control Tower, but the workloads run within AWS Regions that don’t have AWS Control Tower support\. You can use the conformance pack to manage resources in accounts that exist in Regions where AWS Control Tower is not deployed\.