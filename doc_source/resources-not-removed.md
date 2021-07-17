# Resources not removed during decommissioning<a name="resources-not-removed"></a>

Decommissioning a landing zone does not fully reverse the AWS Control Tower setup process\. Certain resources remain, which may be removed manually\.

**AWS Organizations**

For customers without existing AWS Organizations organizations, AWS Control Tower sets up an organization with two organizational units \(OUs\), named **Security** and **Sandbox**\. When you decommission your landing zone, the hierarchy of the organization is preserved, as follows:
+ Organizational Units \(OUs\) you created from the AWS Control Tower console are not removed\.
+ The Security and Sandbox OUs are not removed\.
+ The organization is not deleted from AWS Organizations\.
+ No accounts in AWS Organizations \(shared, provisioned, or management\) are moved or removed\.

**AWS Single Sign\-On \(SSO\)**

For customers without an existing AWS SSO directory, AWS Control Tower sets up AWS SSO and configures an initial directory\. When you decommission your landing zone, AWS Control Tower makes no changes to AWS SSO\. If needed, you can delete the AWS SSO information stored in your management account manually\. In particular, these areas are unchanged by decommissioning:
+ Users created with Account Factory are not removed\.
+ Groups created by AWS Control Tower setup are not removed\.
+ Permission sets created by AWS Control Tower are not removed\.
+ Associations between AWS accounts and AWS SSO permission sets are not removed\.
+ AWS SSO directories are not changed\. 

**Amazon S3 Buckets**

During setup, AWS Control Tower creates buckets in the logging account for logging and for logging access\. When you decommission your landing zone, the following resources are not removed:
+ Logging and logging access S3 buckets in the logging account are not removed\.
+ Contents of the logging and logging access buckets are not removed\.

**Shared Accounts**

Two shared accounts \(Audit and Log Archive\) are created in the Security OU during AWS Control Tower setup\. When you decommission your landing zone:
+ Shared accounts that were created during AWS Control Tower setup are not closed\.
+ The `OrganizationAccountAccessRole` IAM role is recreated to align with standard AWS Organizations configuration\.
+ The `AWSControlTowerExecution` role is removed\.

**Provisioned Accounts**

AWS Control Tower customers can use account factory to create new AWS accounts\. When you decommission your landing zone:
+ Provisioned accounts you created with Account Factory are not closed\.
+ Provisioned products in AWS Service Catalog are not removed\. If you clean those up by terminating them, their accounts are moved into the **Root OU**\.
+ The VPC that AWS Control Tower created is not removed, and the associated AWS CloudFormation stack set \(`BP_ACCOUNT_FACTORY_VPC`\) is not removed\.  
+ The `OrganizationAccountAccessRole` IAM role is recreated to align with standard AWS Organizations configuration\.
+ The `AWSControlTowerExecution` role is removed\.

**CloudWatch Logs Log Group**

A CloudWatch Logs log group, `aws-controltower/CloudTrailLogs`, is created as part of the blueprint named `AWSControlTowerBP-BASELINE-CLOUDTRAIL-MANAGEMENT`\. This log group is not removed\. Instead, the blueprint is deleted and the resources are retained\.
+ This log group must be deleted manually before you set up another landing zone\.