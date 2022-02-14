# Delete Amazon S3 Buckets in the Log Archive Account<a name="controltower-walkthrough-delete-s3-buckets"></a>

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