# Update the stack<a name="update-stack"></a>

If you have previously deployed *Customizations for AWS Control Tower* \(CfCT\), follow this procedure to update the AWS CloudFormation stack for the latest version of the CfCT framework\.

1. Sign in to the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation), select your existing **Customizations for AWS Control Tower** \(CfCT\) CloudFormation stack, and select **Update**\.

1. Select **Replace current template**\.

1. Under **Specify template**: 

   1. Copy the link of the [latest template from GitHub](  https://github.com/aws-solutions/aws-control-tower-customizations/blob/main/customizations-for-aws-control-tower.template)\.

   1. Paste the link in the **URL** box\.

   1. Verify that the correct template URL shows in the ** URL** text box and choose **Next**\. Choose **Next** again\.

1. Under **Parameters**, review the parameters for the template and modify them as necessary\. Refer to [Step 1\. Launch the stack](step1.md) for details about the parameters\.

1. Choose **Next**\.

1. On the **Configure stack options** page, choose **Next**\.

1. On the **Review** page, review and confirm the settings\. Be sure to check the box acknowledging that the template might create AWS Identity and Access Management \(IAM\) resources\.

1. Choose **View change set** and verify the changes\.

1. Choose **Update stack** to deploy the stack\.

   You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **UPDATE\_COMPLETE** in approximately 15 minutes\.