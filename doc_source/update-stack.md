# Update the stack<a name="update-stack"></a>

 If you previously deployed *Customizations for AWS Control Tower* \(CfCT\), follow the procedure to update the AWS CloudFormation stack for the latest version of the CfCT framework\. 

**Important**  
 Before you can complete the following procedure, you must upload the [latest template from GitHub](  https://github.com/aws-solutions/aws-control-tower-customizations/blob/main/customizations-for-aws-control-tower.template) to an Amazon Simple Storage Service \(Amazon S3\) bucket\. For instructions on how to get started with Amazon S3, see [Getting started with Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/GetStartedWithS3.html) in the *Amazon Simple Storage Service User Guide*\. 

1.  Sign in to the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation)\. 

1.  Select your existing **Customizations for AWS Control Tower** \(CfCT\) CloudFormation stack, and then select **Update**\. 

1.  Under **Prerequisite — Prepare template**, select **Replace current template**\. 

1.  Under **Specify template**, do the following: 

   1.  For **Template source**, select **Replace current template**\. 

   1.  For **Amazon S3 URL**, enter the template URL for the template that you previously uploaded from GitGub to Amazon S3, and then choose **Next**\. 

   1.  Verify that the template URL is correct\. Then choose **Next** and **Next** again\. 

1. Under **Parameters**, review the parameters for the template and modify them as necessary\. Refer to [Step 1\. Launch the stack](step1.md) for details about the parameters\.

1. Choose **Next**\.

1. On the **Configure stack options** page, choose **Next**\.

1. On the **Review** page, review and confirm the settings\. Be sure to check the box acknowledging that the template might create AWS Identity and Access Management \(IAM\) resources\.

1. Choose **View change set** and verify the changes\.

1. Choose **Update stack** to deploy the stack\.

   You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **UPDATE\_COMPLETE** in approximately 15 minutes\.