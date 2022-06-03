# Automated deployment<a name="deployment"></a>

Before you launch the automated deployment, review the [considerations](cfct-considerations.md)\. Follow the step\-by\-step instructions in this section to configure and deploy the solution into your AWS Control Tower management account\.

**Time to deploy:** Approximately 15 minutes

## Prerequisites<a name="prerequisites"></a>

CfCT must be deployed in your AWS Control Tower management account, and in your AWS Control Tower home Region\. If you do not have a landing zone set up, see [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

## Deployment steps<a name="what-we-cover"></a>

The procedure for deploying CfCT consists of two major steps\. For detailed instructions, follow the links for each step\.

[Step 1\. Launch the stack](#step1)
+ Launch the AWS CloudFormation template into your management account\.
+ Review the template parameters, and adjust if necessary\.

[Step 2\. Create a custom package](#step2)
+ Create a custom configuration package\.

**Important**  
To download the correct AWS CloudFormation template and launch CfCT, follow the GitHub link given in this section\. Do not follow older links to any previously specified S3 buckets\.

## Step 1\. Launch the stack<a name="step1"></a>

The AWS CloudFormation template in this section deploys *Customizations for AWS Control Tower* \(CfCT\) in your account\.

**Note**  
You are responsible for the cost of the AWS services used while you run CfCT\. For more details, see [Cost](cost.md)\.

1. To launch *Customizations for AWS Control Tower*, [download the template from GitHub](  https://github.com/aws-solutions/aws-control-tower-customizations/blob/main/customizations-for-aws-control-tower.template) and then launch it from [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/home?region=us-east-1)\.

1. The template launches in the US East \(N\. Virginia\) Region by default\. To launch CfCT in a different AWS Region, use the Region selector in the console navigation bar\.
**Note**  
CfCT must be launched in the same Region and account where you deployed your AWS Control Tower landing zone, which is your home Region\.

1. On the **Create stack** page, verify that the correct template URL shows in the ** URL** text box and choose **Next**\.

1. On the **Specify stack details** page, assign a name to your CfCT stack\.

1. Under **Parameters**, review the following parameters and modify them in the template, if necessary\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/controltower/latest/userguide/deployment.html)

1. Choose **Next**\.

1. On the **Configure stack options** page, choose **Next**\.

1. On the **Review** page, review and confirm the settings\. Be sure to check the box acknowledging that the template will create AWS Identity and Access Management \(IAM\) resources\.

1. Choose **Create stack** to deploy the stack\.

   You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **CREATE\_COMPLETE** in approximately 15 minutes\.

## Step 2\. Create a custom package<a name="step2"></a>

With the launched stack, you can add customizations to your AWS Control Tower landing zone and service control policies \(SCPs\) by customizing the included configuration package\. For detailed instructions on creating a custom package, refer to the [CfCT customization guide](cfct-customizations-dev-guide.md)\.

**Note**  
The pipeline does not run without uploading the custom configuration package\.

## Update the stack<a name="update-stack"></a>

If you have previously deployed *Customizations for AWS Control Tower* \(CfCT\), follow this procedure to update the AWS CloudFormation stack for the latest version of the CfCT framework\.

1. Sign in to the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation), select your existing **Customizations for AWS Control Tower** \(CfCT\) CloudFormation stack, and select **Update**\.

1. Select **Replace current template**\.

1. Under **Specify template**: 

   1. Copy the link of the [latest template from GitHub](  https://github.com/aws-solutions/aws-control-tower-customizations/blob/main/customizations-for-aws-control-tower.template)\.

   1. Paste the link in the **URL** box\.

   1. Verify that the correct template URL shows in the ** URL** text box and choose **Next**\. Choose **Next** again\.

1. Under **Parameters**, review the parameters for the template and modify them as necessary\. Refer to [Step 1\. Launch the stack](#step1) for details about the parameters\.

1. Choose **Next**\.

1. On the **Configure stack options** page, choose **Next**\.

1. On the **Review** page, review and confirm the settings\. Be sure to check the box acknowledging that the template might create AWS Identity and Access Management \(IAM\) resources\.

1. Choose **View change set** and verify the changes\.

1. Choose **Update stack** to deploy the stack\.

   You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **UPDATE\_COMPLETE** in approximately 15 minutes\.