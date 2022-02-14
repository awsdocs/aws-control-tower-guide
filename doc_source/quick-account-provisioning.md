# Create or Enroll An Individual Account<a name="quick-account-provisioning"></a>

The **Enroll account** feature is available in the AWS Control Tower console, for provisioning new accounts in your landing zone and for enrolling existing AWS accounts so that they are governed by AWS Control Tower\.

The **Enroll account** capability is available when your landing zone is not in a state of [drift](https://docs.aws.amazon.com/controltower/latest/userguide/drift.html)\. To view this capability:
+ Navigate to the **Account Factory** page in AWS Control Tower\.
+ Select the **Enroll account** item near the top of the page\.
+ You'll then see a **Create account** section, where you can fill in the required fields: *account email*, *account name*, *SSO user name*, and *organizational unit*\.
+ When you've filled in the information, select **Enroll account**\.

You'll see a flashbar confirming that your account enrollment process has been successfully submitted\. If an error has occurred, AWS Control Tower may ask you for corrections\. The account provisioning process may take several minutes\.

**Note**  
If you are enrolling an existing AWS account, be sure to type the existing email address correctly\. Otherwise, a new account will be created\.

Certain errors may require that you refresh the page and try again\. If your landing zone is in a state of drift, you may not be able to use the **Enroll account** capability successfully\. You'll need to provision new accounts through AWS Service Catalog until your landing zone drift has been resolved\. 

When you enroll accounts from the AWS Control Tower console, you must be signed into an account with an IAM user that has the `AWSServiceCatalogEndUserFullAccess` policy enabled, along with permissions to use the AWS Control Tower console, and you cannot be signed in as **Root**\.

Accounts that you enroll must be updated by means of AWS Service Catalog and the AWS Control Tower account factory, as you would update any other account\. Update procedures are given in the section called [Update and move account factory accounts with AWS Service Catalog](updating-account-factory-accounts.md)\. 