# Automated deployment<a name="deployment"></a>

Before you launch the automated deployment, review the [considerations](cfct-considerations.md)\. Follow the step\-by\-step instructions in this section to configure and deploy the solution into your AWS Control Tower management account\.

**Time to deploy:** Approximately 15 minutes

## Prerequisites<a name="prerequisites"></a>

CfCT must be deployed in your AWS Control Tower management account, and in your AWS Control Tower home Region\. If you do not have a landing zone set up, see [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

## Deployment steps<a name="what-we-cover"></a>

The procedure for deploying CfCT consists of two major steps\. For detailed instructions, follow the links for each step\.

[Step 1\. Launch the stack](step1.md)
+ Launch the AWS CloudFormation template into your management account\.
+ Review the template parameters, and adjust if necessary\.

[Step 2\. Create a custom package](step2.md)
+ Create a custom configuration package\.

**Important**  
To download the correct AWS CloudFormation template and launch CfCT, follow the GitHub link given in this section\. Do not follow older links to any previously specified S3 buckets\.