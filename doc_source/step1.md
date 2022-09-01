# Step 1\. Launch the stack<a name="step1"></a>

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
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/controltower/latest/userguide/step1.html)

1. Choose **Next**\.

1. On the **Configure stack options** page, choose **Next**\.

1. On the **Review** page, review and confirm the settings\. Be sure to check the box acknowledging that the template will create AWS Identity and Access Management \(IAM\) resources\.

1. Choose **Create stack** to deploy the stack\.

   You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **CREATE\_COMPLETE** in approximately 15 minutes\.