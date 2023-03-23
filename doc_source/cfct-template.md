# Template and source code<a name="cfct-template"></a>

 Customizations for AWS Control Tower \(CfCT\) are deployed in your management account after you launch your AWS CloudFormation template\. You can download [the template](  https://github.com/aws-solutions/aws-control-tower-customizations/blob/main/customizations-for-aws-control-tower.template) from GitHub and then launch it from [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/home?region=us-east-1)\. 

 The **customizations\-for\-aws\-control\-tower\.template** deploys the following: 
+  An AWS CodeBuild project 
+  An AWS CodePipeline project 
+  An Amazon EventBridge rule 
+  AWS Lambda functions 
+  An Amazon Simple Queue Service queue 
+  An Amazon Simple Storage Service bucket with a sample configuration package 
+  AWS Step Functions 

**Note**  
 You can customize the template based on your specific requirements\. 

## Source code repository<a name="cfct-source-code"></a>

You can visit our [GitHub repository](https://github.com/aws-solutions/aws-control-tower-customizations) to download the templates and scripts for CfCT, and to share your landing zone customizations with others\.