# Account Factory for Terraform \(AFT\) troubleshooting guide<a name="account-troubleshooting-guide"></a>

 This section can help you troubleshoot common issues that you might encounter when using Account Factory\. 

**Topics**
+ [General issues](#w32aac32c31c45b7)
+ [Issues related to account provisioning/registration](#w32aac32c31c45b9)
+ [Issues related to customizations invocation](#w32aac32c31c45c11)

## General issues<a name="w32aac32c31c45b7"></a>
+  **Exceeded AWS resource quotas** 

   If your log groups indicate that you exceeded AWS resource quotas, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\. Account Factory uses AWS services with resource quotas that include AWS CodeBuild, AWS Organizations, and AWS Systems Manager\. For more information, see the following: 
  +  [What is AWS CodeBuild?](https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html) in the *CodeBuild User Guide*\. 
  +  [What is AWS Organizations?](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html) in the *Organizations User Guide*\. 
  +  [What is AWS Systems Manager?](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) in the *Systems Manager User Guide*\. 
+  **Outdated version of Account Factory ** 

   If you encounter an issue and believe the issue is a bug, make sure that you have the latest version of Account Factory\. For more information, see [Updating the Account Factory version](https://docs.aws.amazon.com/controltower/latest/userguide/update-aft-version.html)\. 
+  **Local changes were made to the Account Factory source code** 

   Account Factory is an open source project\. AWS Control Tower supports the Account Factory core code\. If you make a local change to the Account Factory core code, AWS Control Tower only supports your Account Factory deployment on a best\-effort basis\. 
+ ** Insufficient Account Factory role permissions ** 

   Account Factory creates IAM roles and policies to manage vended account deployments and customizations\. If you change these roles or policies, the Account Factory pipeline may be unable to perform certain actions\. For more information, see [Required roles](https://docs.aws.amazon.com/controltower/latest/userguide/aft-required-roles.html)\. 
+  **Account repositories not populated correctly ** 

   Make sure that you follow the [post\-deployment steps](https://docs.aws.amazon.com/controltower/latest/userguide/aft-post-deployment.html) before provisioning accounts\. 
+  **Not detecting drift after changing the OU manually** 
**Note**  
 AWS Control Tower detects drift automatically\. For information about resolving drift, see [Detect and resolve drift in AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/drift.html#resolving-drift)\. 

   Drift isn't detected when the organizational unit \(OU\) is changed manually\. This is due to the event\-driven nature of Account Factory\. When an account request is submitted, the resource that Terraform manages is an Amazon DynamoDB item, not a direct account\. After an item is changed, the request is put in a queue, where AWS Control Tower processes them through Service Catalog \(the service that manages account details\)\. If you change the OU manually, drift isn't detected because the account request hasn't changed\. 

## Issues related to account provisioning/registration<a name="w32aac32c31c45b9"></a>
+  **Account request \(email address/name\) already exists** 

   The issue typically results in an Service Catalog product failure during provisioning or as `ConditionalCheckFailedException`\. 

   You can find more information about the issue by doing one of the following: 
  +  Review your Terraform or CloudWatch Logs log groups\. 
  +  Review the failures that are emitted to the Amazon SNS topic `aft-failure-notifications`\. 
+  ** Malformed account request ** 

   Make sure that your account request follows the expected schema\. For examples, see [terraform\-aws\-control\_tower\_account\_factory](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main/sources/aft-customizations-repos/aft-account-request/examples) on GitHub\. 
+  ** Exceeded AWS Organizations resource quotas** 

   Make sure that your account request doesn't exceed AWS Organizations resource quotas\. For more information, see [Quotas for AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html)\. 

## Issues related to customizations invocation<a name="w32aac32c31c45c11"></a>
+  ** Target account not onboarded to Account Factory ** 

   Make sure all accounts that are included in a customization request have been onboarded to Account Factory\. For more information, see [Update an existing account](https://docs.aws.amazon.com/controltower/latest/userguide/aft-update-account.html)\. 
+  **Account that customization request targets exists in the DynamoDB table `aft-request-metadata`, but not in account request repository ** 

   Format your customization invocation request to exclude the offending account by doing one of the following: 
  +  In the DynamoDB table `aft-request-metadata`, delete the entry referencing the account that's no longer in your account request repository\. 
  +  Not using "all" as the target\. 
  +  Not targeting the OU that the account belongs to\. 
  +  Not targeting the account directly\. 
+  ** Used incorrect token for Terraform Cloud ** 

   Make sure that you set up the correct token\. Terraform Cloud only supports team\-based tokens, not organization\-based tokens\. 
+  **Failed to create account before account customizations pipeline is created; can't customize account** 

   Make a change to the account specification in the account request repository\. When you make a change, such as changing a tag value for an account, Account Factory follows the path that tries to create the pipeline, even if the pipeline doesn't exist\. 