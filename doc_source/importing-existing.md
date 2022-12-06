# Register an existing organizational unit with AWS Control Tower<a name="importing-existing"></a>

An efficient way to bring multiple, existing AWS accounts into AWS Control Tower is to *extend governance* by AWS Control Tower to an entire organizational unit \(OU\)\.

To enable AWS Control Tower governance over an existing OU that was created with AWS Organizations, and its accounts, *register* the OU with your AWS Control Tower landing zone\. You can register OUs that contain up to 300 accounts\. If an OU contains more than 300 accounts, you cannot register it in AWS Control Tower\.

When you register an OU, its member accounts are enrolled into the AWS Control Tower landing zone\. They are governed by the controls that apply to their OU\.

**Note**  
If you don't already have an AWS Control Tower landing zone, start by setting up a landing zone, either in a new organization created by AWS Control Tower, or in an existing AWS Organizations organization\. For more details about how to set up a landing zone, see [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

**What happens to my accounts when I register my OU?**

AWS Control Tower requires permission to establish trusted access between AWS CloudFormation and AWS Organizations on your behalf, so that AWS CloudFormation can deploy your stack to the accounts in your organization automatically\.
+ The `AWSControlTowerExecution` role is added to all accounts with status **Not enrolled**\.
+ Mandatory controls are enabled by default to your OU and all its accounts when you register your OU\.

**Partial enrollment of accounts after an OU is registered**

It's possible to register an OU successfully, yet certain accounts may remain unenrolled\. If so, these accounts do not meet some of the prerequisites for enrollment\. If an account enrollment as part of the **Register OU** process does not succeed, the account status on the accounts page shows **Enrollment failed**\. You may also see account information on your OU page such as **4 of 5**, in the accounts field\.

For example, if you see **4 of 5**, it means that your OU has 5 accounts in total, and 4 of them enrolled successfully, but one account failed to enroll during the **Register OU** process\. You can choose **Re\-Register OU** to bring accounts into enrollment, after you make sure the accounts meet the enrollment prerequisites\.

**IAM user prerequisites for registering an OU**

Your AWS Identity and Access Management \(IAM\) identity \(user or role\) must be included on the appropriate Account Factory portfolio when you perform the **Register OU** operation, even if you already have `Admin` permissions\. Otherwise, the creation of the provisioned products will fail during registration\. Failure occurs because AWS Control Tower relies upon the credentials of the IAM identity when registering an OU\.

The relevant portfolio is one created by AWS Control Tower, called **AWS Control Tower Account Factory Portfolio**\. Navigate to it by choosing **Service Catalog > Account Factory > AWS Control Tower Account Factory Portfolio**\. Then select the tab called **Groups, roles, and users** to view your IAM identity\. For more information on how to grant access, see [the documentation for AWS Service Catalog\.](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/catalogs_portfolios_users.html)