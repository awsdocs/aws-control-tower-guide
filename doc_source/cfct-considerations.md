# Deployment considerations<a name="cfct-considerations"></a>

Be sure to launch *Customizations for AWS Control Tower * \(CfCT\) in the same account and Region where your AWS Control Tower landing zone is deployed; that is, you must deploy it in the AWS Control Tower management account in your AWS Control Tower home Region\. By default, CfCT creates and runs the landing zone configuration package by setting up a configuration pipeline in that account and Region\.

## Prepare for deployment<a name="deploy-custom-aws-ct-deploy"></a>

You have some options when you prepare your AWS CloudFormation template for initial deployment\. You can choose the configuration source, and you can allow for manual approval of pipeline deployments\. The next two sections explain more about these options\.

### Choose your configuration source<a name="deploy-config-source"></a>

By default, the template creates an Amazon Simple Storage Service \(Amazon S3\) bucket to store the sample configuration package as a `.zip` file called `_custom-control-tower-configuration.zip`\. The Amazon S3 bucket is version controlled, and you can update the configuration package as needed\. For information about updating the configuration package, refer to [Using Amazon S3 as the Configuration Source](cfct-s3-source.md)\.

**Note**  
The sample configuration package filename begins with an underscore \(\_\) so that AWS CodePipeline is not initiated automatically\. When you have finished customizing the configuration package, be sure to upload the `custom-control-tower-configuration.zip` without the underscore \(\_\) in order to begin the deployment in AWS CodePipeline\.

You can change the storage location of the configuration package from the S3 bucket to an AWS CodeCommit Git repository by selecting the `AWS CodeCommit` option in the AWS CloudFormation parameter\. This option enables you to manage version control easily\.

**Note**  
When you're using the default S3 bucket, be sure that the configuration package is available as a `.zip` file\. When you're using the AWS CodeCommit repository, be sure that the configuration package is placed in the repository without zipping the files\. For information about creating and storing the configuration package in AWS CodeCommit, see [CfCT customization guide](cfct-customizations-dev-guide.md)\.

You can use the sample configuration package to create your own custom configuration source\. When you are ready to deploy your custom configurations, manually upload the configuration package, either to the Amazon S3 bucket or to the AWS CodeCommit repository\. The pipeline begins automatically when you upload the configuration file\.

**Note**  
When you're using AWS CodeCommit to store the configuration package, it is not necessary to zip the package\. For information about creating and storing the configuration package in AWS CodeCommit, refer to [CfCT customization guide](cfct-customizations-dev-guide.md)\.

### Choose your pipeline configuration approval parameters<a name="deploy-pipeline-config-parameters"></a>

The AWS CloudFormation template provides the option to approve the deployment of configuration changes manually\. By default, manual approval is not enabled\. For more information, refer to [Step 1\. Launch the stack](deployment.md#step1)\.

When manual approval is enabled, the configuration pipeline validates the customizations made to the AWS Control Tower file manifest and templates, then it pauses the process until manual approval is granted\. After approval, the deployment proceeds to run the remaining pipeline stages, as needed, to implement the *Customizations for AWS Control Tower* \(CfCT\) functionality\.

You can use the manual approval parameter to keep the customizations for the AWS Control Tower configuration from running, by rejecting the first attempt to run through the pipeline\. This parameter also allows you to validate customizations for the AWS Control Tower configuration changes manually, as a final control before implementation\.

## To update Customizations for AWS Control Tower<a name="update"></a>

If you have previously deployed CfCT, you must update the AWS CloudFormation stack to get the latest version of the CfCT framework\. For details, refer to [Update the Stack](deployment.md#update-stack)\.