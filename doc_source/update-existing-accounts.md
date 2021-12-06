# Update existing OUs and accounts<a name="update-existing-accounts"></a>

When you perform a landing zone update, you must update your enrolled accounts to apply new guardrails to those accounts\.
+ You can perform an update to all accounts under an OU using the **Re\-Register** option\.
+ If you have more than one registered OU in your landing zone, re\-register all of your OUs to update all of your accounts\.
+ To update a single account, select the **Update provisioned product** option in AWS Service Catalog\.

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