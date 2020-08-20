# Walkthrough: Cleaning up AWS Control Tower Managed Resources<a name="walkthrough-delete"></a>

When your landing zone was set up, AWS Control Tower provisioned resources and services in your landing zone on your behalf\. For example, an AWS Organizations organization with multiple accounts and organizational units \(OUs\) were provisioned\. Additionally, guardrails were deployed in your accounts using AWS CloudFormation stacks, stack sets, and AWS Organizations policies\.

This document provides instructions for how to delete AWS Control Tower resources individually, as part of regular maintenance and administrative tasks\. The procedures given in this chapter are intended only for removing individual resources, or a few resources, when needed\. 

**Warning**  
If you intend to decommission your landing zone, contact AWS Support first, before manually deleting resources\. Manually deleting resources will not allow you to set up a new landing zone\. It is not the same as decommissioning\. Complete decommissioning requires assistance from AWS Support\.

 If you delete all of your landing zone resources manually, it is not the same as decommissioning the landing zone, and you may incur unexpected charges\.

## Manual Cleanup of AWS Control Tower Resources<a name="manual-decommissioning"></a>

The following procedures guide you through manual methods of cleaning up AWS Control Tower resources\. These procedures can be followed any time you need to delete resources from you landing zone\. Two types of tasks may require cleanup of resources:
+ To delete resources as you manage your landing zone in ordinary situations\.
+ To clean up resources that remain after automated decommissioning, in cooperation with AWS Support\.

Before performing these procedures, unless it's otherwise indicated, you must be signed in to the AWS Management Console in the home Region for your landing zone, and you must be signed in as an IAM user with administrative permissions for the master account that contains your landing zone\.

**Warning**  
These are destructive actions that can introduce governance drift into your AWS Control Tower setup\. They cannot be undone\.

**Topics**
+ [Manual Cleanup of AWS Control Tower Resources](#manual-decommissioning)
+ [Delete SCPs](#controltower-walkthrough-delete-scps)
+ [Delete StackSets and Stacks](#controltower-walkthrough-delete-stacksets)
+ [Delete Amazon S3 Buckets in the Log Archive Account](#controltower-walkthrough-delete-s3-buckets)
+ [Clean Up Account Factory](#controltower-walkthrough-cleanup-account-factory)
+ [Clean Up Roles and Polices](#controltower-walkthrough-cleanup-identity)
+ [AWS Control Tower Clean Up Help](#control-tower-cleanup-help)

## Delete SCPs<a name="controltower-walkthrough-delete-scps"></a>

AWS Control Tower uses service control policies \(SCPs\) for its guardrails\. This procedure walks through how to delete the SCPs specifically related to AWS Control Tower\.

**To delete AWS Organizations SCPs**

1. Open the Organizations console at [https://console\.aws\.amazon\.com/organizations/](https://console.aws.amazon.com/organizations/)\.

1. Open the **Policies** tab, and find the Service Control Policies \(SCPs\) that have the prefix **aws\-guardrails\-** and do the following for each SCP:

   1. Detach the SCP from the associated OU\.

   1. Delete the SCP\.

## Delete StackSets and Stacks<a name="controltower-walkthrough-delete-stacksets"></a>

AWS Control Tower uses StackSets and stacks to deploy AWS Config Rules related to guardrails in your landing zone\. The following procedures walk through how to delete these specific resources\.

**To delete AWS CloudFormation StackSets**

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. From the left navigation menu, choose **StackSets**\.

1. For each StackSet with the prefix **AWSControlTower**, do the following\. If you have many accounts in a StackSet, this can take some time\.

   1. Choose the specific StackSet from the table in the dashboard\. This opens the properties page for that StackSet\.

   1. At the bottom of the page, in the **Stacks** table, make a record of the AWS account IDs for all the accounts in the table\. Copy the list of all accounts\.

   1. Choose **Manage StackSet** to open the management wizard\.

   1. From **Select action**, choose **Delete stacks**, and choose **Next**\.

   1. On **Set deployment options**, from **Specify accounts**, choose **Delete stacks from account**\.

   1. In the text field, enter the AWS account IDs you made a record of in step 3\.b, separated by commas\. For example: *123456789012*, *098765431098*, and so on\.

   1. From **Specify regions**, choose **Add all**, leave the rest of the parameters on the page set to their defaults, and choose **Next**\.

   1. On the **Review** page, review your choices, and then choose **Delete stacks**\.

   1. On the **StackSet properties** page, you can begin this procedure again for your other StackSets\.

1. The process is complete when the records in the **Stacks** table of the different **StackSets properties** pages are empty\.

1. When the records in the **Stacks** table are empty, choose **Delete StackSet**\.

**To delete AWS CloudFormation stacks**

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. From the **Stacks** dashboard, search for all of the stacks with the prefix **AWSControlTower**\.

1. For each stack in the table, do the following:

   1. Choose the check box next to the name of the stack\.

   1. From the **Actions** menu, choose **Delete Stack**\.

   1. In the dialog box that opens, review the information to make sure it's accurate, and choose **Yes, Delete**\.

## Delete Amazon S3 Buckets in the Log Archive Account<a name="controltower-walkthrough-delete-s3-buckets"></a>

The following procedures guide you through how to sign in to the log archive account as an AWS SSO user in the **AWSControlTowerExecution** group and then delete the Amazon S3 buckets in your log archive account\.

**To sign in to your log archive account with the right permissions**

1. Open the Organizations console at [https://console\.aws\.amazon\.com/organizations/](https://console.aws.amazon.com/organizations/)\.

1. From the **Accounts** tab, find the **Log archive** account\.

1. From the right pane that opens, make a record of the log archive account number\.

1. From the navigation bar, choose your account name to open your account menu\.

1. Choose **Switch Role**\.

1. On the page that opens, provide the account number for the log archive account in **Account**\.

1. For **Role**, enter **AWSControlTowerExecution**\.

1. The **Display Name** populates with text\.

1. Choose your favorite **Color**\.

1. Choose **Switch Role**\.

**To delete Amazon S3 buckets**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Search for bucket names that contain **aws\-controltower**\.

1. For each bucket in the table, do the following:

   1. Choose the check box for the bucket in the table\.

   1. Choose **Delete**\.

   1. In the dialog box that opens, review the information to make sure it's accurate, enter the name of the bucket to confirm, and then choose **Confirm**\.

## Clean Up Account Factory<a name="controltower-walkthrough-cleanup-account-factory"></a>

The following procedure guides you through how to sign in as an AWS SSO user in the **AWSServiceCatalogAdmins** group and then clean up your Account Factory accounts\.

**To sign in to your master account with the right permissions**

1. Go to your user portal URL at *directory\-id*\.awsapps\.com/start

1. From **AWS Account**, find the **Master** account\.

1. From **AWSServiceCatalogAdminFullAccess**, choose **Management console** to sign in to the AWS Management Console as this role\.

**To clean up Account Factory**

1. Open the AWS Service Catalog console at [https://console\.aws\.amazon\.com/servicecatalog/](https://console.aws.amazon.com/servicecatalog/)\.

1. From the left navigation menu, choose **Portfolios list**\.

1. In the **Local Portfolios** table, search for a portfolio named **AWS Control Tower Account Factory Portfolio**\.

1. Choose the name of that portfolio to go to its details page\.

1. Expand the **Constraints** section of the page, and choose the radio button for the constraint with the product name **AWS Control Tower Account Factory**\.

1. Choose **REMOVE CONSTRAINTS**\.

1. In the dialog box that opens, review the information to make sure it's accurate, and then choose **CONTINUE**\.

1. From the **Products** section of the page, choose the radio button for the product named **AWS Control Tower Account Factory**\.

1. Choose **REMOVE PRODUCT**\.

1. In the dialog box that opens, review the information to make sure it's accurate, and then choose **CONTINUE**\.

1. Expand the **Users, Groups, and Roles** section of the page, and choose the check boxes for all the records in this table\.

1. Choose **REMOVE USERS, GROUP OR ROLE**\.

1. In the dialog box that opens, review the information to make sure it's accurate, and then choose **CONTINUE**\.

1. From the left navigation menu, choose **Portfolios list**\.

1. In the **Local Portfolios** table, search for a portfolio named **AWS Control Tower Account Factory Portfolio**\.

1. Choose the radio button for that portfolio, and then choose **DELETE PORTFOLIO**\.

1. In the dialog box that opens, review the information to make sure it's accurate, and then choose **CONTINUE**\.

1. From the left navigation menu, choose **Product list**\.

1. On the **Admin products** page, search for the product named **AWS Control Tower Account Factory**\.

1. Choose the product to open the **Admin product details** page\.

1. From **Actions**, choose **Delete product**\.

1. In the dialog box that opens, review the information to make sure it's accurate, and then choose **CONTINUE**\.

## Clean Up Roles and Polices<a name="controltower-walkthrough-cleanup-identity"></a>

These procedures walk you through how to clean up the roles and policies that were created when your landing zone was set up\.

**To delete the AWS SSO AWSServiceCatalogEndUserAccess role**

1. Open the AWS Single Sign\-On console at [https://console\.aws\.amazon\.com/singlesignon/](https://console.aws.amazon.com/singlesignon/)\.

1. Change your AWS Region to your home Region, which is the Region where you initially set up AWS Control Tower\.

1. From the left navigation menu, choose **AWS accounts**\.

1. Choose your master account link\.

1. Choose the dropdown for **Permission sets**, select **AWSServiceCatalogEndUserAccess**, and then choose **Remove**\.

1. Choose **AWS accounts** from the left panel\.

1. Open the **Permission sets** tab\.

1. Select **AWSServiceCatalogEndUserAccess** and delete it\.

**To delete IAM roles**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. From the left navigation menu, choose **Roles**\.

1. From the table, search for roles with the name **AWSControlTower**\.

1. For each role in the table, do the following:

   1. Choose the check box for the role\.

   1. Choose **Delete role**\.

   1. In the dialog box that opens, review the information to make sure it's accurate, and then choose **Yes, delete**\.

**To delete IAM policies**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. From the left navigation menu, choose **Policies**\.

1. From the table, search for policies with the name **AWSControlTower**\.

1. For each policy in the table, do the following:

   1. Choose the check box for the policy\.

   1. Choose **Policy actions**, and **Delete** from the dropdown menu\.

   1. In the dialog box that opens, review the information to make sure it's accurate, and then choose **Delete**\.

## AWS Control Tower Clean Up Help<a name="control-tower-cleanup-help"></a>

If you encounter any issues that you can't resolve during this clean up process, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.