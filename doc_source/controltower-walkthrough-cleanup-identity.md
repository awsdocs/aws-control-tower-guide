# Remove AWS Control Tower Roles and Policies<a name="controltower-walkthrough-cleanup-identity"></a>

These procedures walk you through how to clean up the roles and policies that AWS Control Tower created when your landing zone was set up, or later\.

**To delete the AWS SSO AWSServiceCatalogEndUserAccess role**

1. Open the AWS Single Sign\-On console at [https://console\.aws\.amazon\.com/singlesignon/](https://console.aws.amazon.com/singlesignon/)\.

1. Change your AWS Region to your home Region, which is the Region where you initially set up AWS Control Tower\.

1. From the left navigation menu, choose **AWS accounts**\.

1. Choose your management account link\.

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