# AWS Control Tower quick start guide<a name="quick-start"></a>

If you are new to AWS, you can follow the steps in this section to get started quickly with AWS Control Tower\. If you prefer to customize your AWS Control Tower environment right away, see [Step 2\. Configure and launch your landing zone](step-two.md)\.

**Note**  
AWS Control Tower sets up paid services, such as AWS CloudTrail, AWS Config, Amazon CloudWatch, Amazon S3, and Amazon VPC\. When used, these services may incur costs, as shown on the [pricing page](http://aws.amazon.com/controltower/pricing/?loc=ft)\. The AWS management console shows you the usage of any paid services and the costs incurred\. No additional costs are created by AWS Control Tower itself\.

**Before you begin**

The most important decision to make before you begin the setup process is to *choose your home Region*\. Your home Region is the AWS Region in which you'll run most of your workloads or store most of your data\. It cannot be changed after you've set up your AWS Control Tower landing zone\. For more information about how to choose a home Region, see [Administrative tips for landing zone setup](tips-for-admin-setup.md)\.

**Note**  
By default, AWS Control Tower chooses the Region in which your account is operating currently as your home Region\. You can see your current Region in the upper right of your AWS management console screen\.

The quick start procedure assumes that you'll accept the default values for the resources in your AWS Control Tower environment\. Many of these choices can be changed later\. A few one\-time choices are listed in the section called [Expectations for landing zone configuration](getting-started-configure.md)\.

If you've created a new AWS account, it automatically meets the required prerequisites for setting up AWS Control Tower\. You can proceed through the steps that follow\.

**Quick start steps**

1. Sign in to the AWS management console with your administrator  user credentials\. 

1. Navigate to the **AWS Control Tower** console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. Verify that you are working in your desired home Region\.

1. Choose **Set up landing zone**\.

1. Follow the instructions in the console, accepting all the default values\. You will need to type in the email address for your account, a log archive account, and an audit account\.

1. Confirm your choices and choose **Set up landing zone**\.

1. AWS Control Tower takes about 30 to 45 minutes to set up all of the resources in your landing zone\.

For a more detailed version of how to set up AWS Control Tower, including ways to customize your environment, read and follow the procedures in the next few topics\.

**Note**  
If you are a first\-time customer and you encounter a setup issue, contact [AWS Support](https://aws.amazon.com/premiumsupport/) for diagnostic assistance\.  