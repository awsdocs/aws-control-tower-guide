# Enroll an existing account<a name="quick-account-provisioning"></a>

The **Enroll account** feature is available in the AWS Control Tower console, for enrolling existing AWS accounts so that they are governed by AWS Control Tower\. For more information, see [Enroll an existing AWS account](enroll-account.md)\.

The **Enroll account** capability is available when your landing zone is not in a state of [drift](https://docs.aws.amazon.com/controltower/latest/userguide/drift.html)\. To view this capability:
+ Navigate to the **Accounts** page in AWS Control Tower\.
+ Follow the steps for enrolling an individual account, as shown on the [Enroll an account](enrollment-steps.md) page\.

You'll see a flashbar confirming that your account enrollment process has been successfully submitted\. If an error has occurred, AWS Control Tower may ask you for corrections\. The account provisioning process may take several minutes\.

**Note**  
When you are enrolling an existing AWS account, be sure to verify the existing email address\. Otherwise, a new account may be created\.

Certain errors may require that you refresh the page and try again\. If your landing zone is in a state of drift, you may not be able to use the **Enroll account** capability successfully\. You'll need to provision new accounts through Account Factory until your landing zone drift has been resolved\. 

When you enroll accounts from the AWS Control Tower console, you must be signed into an account with an IAM user that has the `AWSServiceCatalogEndUserFullAccess` policy enabled, along with permissions to use the AWS Control Tower console, and you cannot be signed in as the **Root** user\.

Accounts that you enroll must be updated by means of AWS Service Catalog and the AWS Control Tower account factory, as you would update any other account\. Update procedures are given in the section called [Update and move account factory accounts with AWS Control Tower or with AWS Service Catalog](updating-account-factory-accounts.md)\. 