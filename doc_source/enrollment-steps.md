# Enroll an account<a name="enrollment-steps"></a>

After the **AdministratorAccess** permission is in place in your existing account, follow these steps to enroll the account:

**To enroll an individual account in AWS Control Tower**
+ Navigate to the AWS Control Tower **Accounts** page\.
+ Accounts that are eligible to be enrolled allow you to select **Enroll account** at the top of the section\. These accounts also show an **Enroll account** button when you view them on the **Account details** page\.
+ When you choose **Enroll account**, you’ll see an **Enroll account** page, where you are prompted to add the `AWSControlTowerExecution` role to the account\.
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
+ The account you're trying to enroll may have AWS Config settings that are residual\. In particular, the account must not have a configuration recorder or delivery channel\. These must be deleted or modified through the AWS CLI before you can enroll an account\. for more information, see [Enroll accounts that have existing AWS Config resources](existing-config-resources.md) and [Interacting with AWS Control Tower using AWS CloudShell](using-aws-with-cloudshell.md#cshell-examples)\.
+ If the account belongs to another OU with a management account, including another AWS Control Tower OU, you must terminate the account in its current OU before it can join another OU\. Existing resources must be removed in the original OU\. Otherwise, enrollment will fail\.
+ Account provisioning and enrollment fails if your destination OU’s SCPs don’t allow you to create all of the resources required for that account\. For example, an SCP in your destination OU may block resource creation without certain tags\. In this case, account provisioning or enrollment fails, because AWS Control Tower does not support tagging of resources\. For help, contact your account representative, or AWS Support\.

For more information about how AWS Control Tower works with roles when you're creating new accounts or enrolling existing accounts, see [How AWS Control Tower works with roles to create and manage accounts](roles-how.md)\.