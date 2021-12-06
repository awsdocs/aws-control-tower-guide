# Enroll an account<a name="enrollment-steps"></a>

After the **AdministratorAccess** permission is in place in your existing account, follow these steps to enroll the account:

**To enroll an individual account in AWS Control Tower**
+ Navigate to the AWS Control Tower Account Factory page and select **Enroll account**\.
+ Specify the current email address of the existing account you'd like to enroll in AWS Control Tower\.
+ Specify the first and last name of the account owner\.
+ Specify the organizational unit \(OU\) in which you'd like to enroll the account\.
+ Choose **Enroll account**\.

**Common Causes for Failure of Enrollment**
+ Your IAM principal may lack the necessary permissions to provision an account\. To enroll an existing account, the **AWSControlTowerExecution** role must be present in the account you're enrolling\.
+ AWS Security Token Service \(AWS STS\) is disabled in your AWS account in your home region, or in any region supported by AWS Control Tower\.
+ You may be signed in to an account that needs to be added to the Account Factory Portfolio in AWS Service Catalog\. The account must be added before you'll have access to Account Factory so you can create or enroll an account in AWS Control Tower\. If the appropriate user or role is not added to the Account Factory Portfolio, you’ll receive an error when you attempt to add an account\. For instructions on how to grant access to AWS Service Catalog portfolios, see [Granting access to users](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/catalogs_portfolios_users.html)\.
+ You may be signed in as root\.
+ The account you're trying to enroll may have AWS Config settings that are residual\. In particular, the account must not have a configuration recorder or delivery channel, so these must be deleted through the AWS CLI before you can enroll an account\. for more information, see [Interacting with AWS Control Tower using AWS CloudShell](using-aws-with-cloudshell.md#cshell-examples)\.
+ If the account belongs to another OU with a management account, including another AWS Control Tower OU, you must terminate the account in its current OU before it can join another OU\. Existing resources must be removed in the original OU\. Otherwise, enrollment will fail\.

For more information about how AWS Control Tower works with roles when you're creating new accounts or enrolling existing accounts, see [How AWS Control Tower works with roles to create and manage accounts](roles-how.md)\.