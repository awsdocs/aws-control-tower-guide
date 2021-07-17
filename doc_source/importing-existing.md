# Register an existing organizational unit with AWS Control Tower<a name="importing-existing"></a>

An efficient way to bring multiple, existing AWS accounts into AWS Control Tower is to *extend governance* by AWS Control Tower to an entire organizational unit \(OU\)\.

To enable AWS Control Tower governance over an existing OU that was created with AWS Organizations, and its accounts, *register* the OU with your AWS Control Tower landing zone\. You can register OUs that contain up to 300 accounts\. If an OU contains more than 300 accounts, you cannot register it in AWS Control Tower\.

When you register an OU, its member accounts are enrolled into the AWS Control Tower landing zone\. They are governed by the guardrails that apply to their OU\.

**Note**  
If you don't already have an AWS Control Tower landing zone, start by setting up a landing zone, either in a new organization created by AWS Control Tower, or in an existing AWS Organizations organization\. For more details about how to set up a landing zone, see [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

**What happens to my accounts when I register my OU?**

AWS Control Tower requires permission to establish trusted access between AWS CloudFormation and AWS Organizations on your behalf, so that AWS CloudFormation can deploy your stack to the accounts in your organization automatically\.
+ The `AWSControlTowerExecution` role is added to all accounts with status **Not enrolled**\.
+ Mandatory guardrails are enabled by default to your OU and all its accounts when you register your OU\.

**Partial enrollment of accounts after an OU is registered**

It's possible to register an OU successfully, yet certain accounts may remain unenrolled\. If so, these accounts do not meet some of the prerequisites for enrollment\. If an account enrollment as part of the **Register OU** process does not succeed, the account status on the accounts page shows **Enrollment failed**\. You may also see account information on your OU page such as **4 of 5**, in the accounts field\.

For example, if you see **4 of 5**, it means that your OU has 5 accounts in total, and 4 of them enrolled successfully, but one account failed to enroll during the **Register OU** process\. You can choose **Re\-Register OU** to bring accounts into enrollment, after you make sure the accounts meet the enrollment prerequisites\.

**IAM user prerequisites for registering an OU**

Your AWS Identity and Access Management \(IAM\) identity \(user or role\) must be included on the appropriate Account Factory portfolio when you perform the **Register OU** operation, even if you already have `Admin` permissions\. Otherwise, the creation of the provisioned products will fail during registration\. Failure occurs because AWS Control Tower relies upon the credentials of the IAM identity when registering an OU\.

The relevant portfolio is one created by AWS Control Tower, called **AWS Control Tower Account Factory Portfolio**\. Navigate to it by choosing **Service Catalog > Account Factory > AWS Control Tower Account Factory Portfolio**\. Then select the tab called **Groups, roles, and users** to view your IAM identity\. For more information on how to grant access, see [the documentation for AWS Service Catalog\.](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/catalogs_portfolios_users.html)

## Register an existing OU<a name="how-to-register-existing-ou"></a>

In the AWS Control Tower console, on the **Organizational units** page, you can view all of your organizations, including OUs that are registered with AWS Control Tower and those that are not registered\. You can register OUs that contain up to 300 accounts\. If an OU contains more than 300 accounts, you cannot register it in AWS Control Tower\.

**To register an existing OU**

1. Sign in to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\. 

1. In the left\-pane navigation menu, choose **Organizational units**\.

1. On the **Organizational units** page, select the radio button next to the OU you want to register\.

1. At the upper right, select **Register OU**\.

The registration process takes a minimum of 10 minutes to extend governance to the OU, and up to 2 additional minutes for each additional account\.

**Effects of registering an existing OU**

After you register an existing OU, the `AWSControlTowerExecution` role allows AWS Control Tower to extend governance to its individual accounts\. Guardrails are enforced, and information about account activities is reported to your audit and logging accounts\.

Other effects include the following:
+ `AWSControlTowerExecution` allows auditing by the AWS Control Tower audit account\.
+ `AWSControlTowerExecution` helps you configure your organization’s logging, so that all the logs for every account are sent to the logging account\.
+ `AWSControlTowerExecution` ensures that your selected AWS Control Tower guardrails apply automatically to every individual account in your OUs, as well as to every new account you create in AWS Control Tower\.

For a registered OU, you can provide compliance and security reports based on the auditing and logging features embodied by AWS Control Tower guardrails\. Your security and compliance teams can verify that all requirements are met, and that no organizational drift has occurred\. For more information about drift, see [Detect and resolve drift in AWS Control Tower](drift.md)\.

**Note**  
One unusual situation can occur when AWS Control Tower displays OUs and their accounts\. If you have created an account in a registered OU and then you subsequently move that enrolled account into another OU that’s not registered, particularly if you use AWS Organizations to move the account, you can see a result “1 of 0” accounts in your OU details page\. Furthermore, you may have created another unenrolled account in that unregistered OU\. If there’s an unregistered account, the console may read “1 of 1” for the OU\. It will seem that the single \(newly created\) account is enrolled, but in fact it is not\. You must enroll the new account\.

## Update existing OUs and accounts<a name="update-existing-accounts"></a>

When you perform a landing zone update, you must update your enrolled accounts to apply new guardrails to those accounts\. You can perform an update to all accounts under an OU using the **Re\-Register** option\. If you have more than one registered OU in your landing zone, re\-register all of your OUs to update all of your accounts\. To update a single account, select the **Update provisioned product** option in AWS Service Catalog\.

**To update multiple accounts**

1. Sign in to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\. 

1. In the left\-pane navigation menu, choose **Organizational units**\.

1. On the **Organizational units** page, choose a Registered OU to view the details page\.

1. Under **Details** in the upper right, select **Re\-Register OU**\.

Effects of re\-registering an OU:
+ The **State** field indicates whether the account currently is enrolled with AWS Control Tower \(**Enrolled**\), whether the account has never been enrolled \(**Not enrolled**\), or whether enrollment failed previously \(**Enrollment failed**\)\.
+ When you re\-register the OU, the `AWSControlTowerExecution` role is added to all accounts with status **Not enrolled** or **Enrollment failed**\.
+ AWS Control Tower creates a single sign\-on \(SSO\) login for those new enrolled accounts\.
+ **Enrolled** accounts are re\-enrolled into AWS Control Tower\.
+ Drift on any preventive guardrails applied to the OU is fixed\.
+ All accounts are updated to reflect the latest landing zone changes\.

For more information, see [Enroll an existing AWS account](enroll-account.md)\.

**To update a single account**

1. Go to AWS Service Catalog\.

1. In the left\-pane navigation menu, choose **Provisioned products**\.

1. On the **Provisioned products** page, select the radio button next to the provisioned product you want to update\.

1. In the upper right, choose the **Actions** dropdown to **Update**\.

To learn more about updating in AWS Service Catalog, see [https://docs.aws.amazon.com/servicecatalog/latest/adminguide/productmgmt-update.html](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/productmgmt-update.html)\.

## Common causes of failure during registration or re\-registration<a name="common-eg-failures"></a>

If registration \(or re\-registration\) of an OU or any of its member accounts fails, you can download a file containing a detailed report that shows which pre\-checks did not pass\. This section lists the types of errors you may receive if pre\-checks fail, and how to correct the errors\.

In general, when you register or re\-register an OU, all accounts within that OU are enrolled in AWS Control Tower\. However, it is possible that some accounts may fail to enroll, even if the OU as a whole is registered successfully\. In these cases, you must resolve the pre\-check failure related to the account and then try re\-enrolling that account, by using the **Enroll account** form in the AWS Control Tower console\.

**Landing Zone error**
+ **Landing zone not ready**

  Repair your current landing zone, or update it to the latest version\.

**OU errors**
+ **Nested OU detected**

  AWS Control Tower does not support nested OUs\. Recreate the nested OUs at the root level, move the accounts from the nested OUs into the new OUs, and then delete the nested OUs\. Then, try registering this OU again\.
+ **Exceeds maximum number of SCPs**

  You may be over the limit for service control policies \(SCPs\) per OU, or you may have reached another quota\. A limit of 5 SCPs per OU applies to all OUs in your AWS Control Tower landing zone\. If you have more SCPs than the quota allows, you must delete or combine the SCPs\.
+ **Conflicting SCPs**

  Existing SCPs may be applied to the account, which prevent AWS Control Tower from enrolling the account\. Check the applied SCPs for any policy that may prevent AWS Control Tower from working\.
+ **Exceeds stack set quota**

  The stack set quota may have been exceeded\. You can have up to 2,000 instances per stack\. If you have more instances than the quota allows, you must delete some stack instances\. For more information, see [AWS CloudFormation quotas](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html) in the *AWS CloudFormation User Guide*\.
+ **Exceeds account limit**

  AWS Control Tower limits each OU to 300 accounts during registration\.

**Account errors**
+ **Pre\-checks prevented on accounts**

  An existing SCP on the OU prevents AWS Control Tower from conducting pre\-checks on your OU member accounts\. To resolve this pre\-check failure, update or remove the SCP from the OU\.
+ **Email address error**

  The email address you specified for the account does not conform to the naming standards\. Here is the regular expression \(regex\) that specifies which characters are allowed: `[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+[.]+[A-Za-z]+`
+ **Config recorder or delivery channel enabled**

  The account may have an existing AWS Config configuration recorder or delivery channel\. These must be deleted through the AWS CLI in all AWS Regions where the AWS Control Tower management account has governed resources, before you can enroll an account\.
+ **STS disabled**

  AWS Security Token Service \(AWS STS\) may be disabled in the account\. AWS STS endpoints must be activated in the accounts for all Regions supported by AWS Control Tower\.
+ **SSO conflict**

  The AWS Control Tower home Region is not the same as the AWS Single Sign\-On \(AWS SSO\) Region\. If AWS SSO is already set up, the AWS Control Tower home region must be the same as the AWS SSO Region\.
+ **Conflicting SNS topic**

  The account has an Amazon Simple Notification Service \(Amazon SNS\) topic name that AWS Control Tower needs to use\. AWS Control Tower creates resources \(such as SNS topics\) with specific names\. If these names are already taken, AWS Control Tower setup fails\. This situation could occur if you are reusing an account previously enrolled in AWS Control Tower\.
+ **Suspended account detected**

  This account has been suspended\. It cannot be enrolled into AWS Control Tower\. Remove the account from this OU, and try again\.
+ **IAM user not in portfolio**

  Add the AWS Identity and Access Management \(IAM\) user to the AWS Service Catalog portfolio before registering your OU\.
+ **Account does not meet prerequisites**

  The account doesn’t meet prerequisites for account enrollment\. For example, the account may be missing roles and permissions required to enroll it in AWS Control Tower\. Instructions for adding a role are available in [Manually add the required IAM role to an existing AWS account and enroll it](enroll-account.md#enroll-manually)\.

As a reminder, AWS CloudTrail is auto\-enabled on all of your AWS accounts when you enroll them in AWS Control Tower\. If CloudTrail is enabled on an account previous to enrollment, you could experience double\-billing unless you deactivate CloudTrail before you begin the enrollment process\.