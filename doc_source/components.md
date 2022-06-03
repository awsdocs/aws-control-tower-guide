# Component services<a name="components"></a>

The following AWS services are components of *Customizations for AWS Control Tower* \(CfCT\)\.

## Amazon Simple Storage Service<a name="comp-amazon-s3"></a>

When you deploy CfCT, it creates an Amazon Simple Storage Service \(Amazon S3\) bucket with a unique name of this form: 

`custom-control-tower-configuration-accountID-region`

The bucket contains a sample configuration file called:

`_custom-control-tower-configuration.zip`

Notice the leading underscore in the file name\.

This zip file provides a sample manifest and the related sample templates that describe the necessary folder structure\. These examples help you develop a configuration package to customize your AWS Control Tower landing zone\. The sample manifest identifies the required configurations for stack sets and service control policies \(SCPs\) you'll need, when you implement your customizations\.

You can use this sample configuration package as a model, to develop and upload your custom package, which triggers the CfCT configuration pipeline automatically\.

For information about customizing the configuration file, see [CfCT customization guide](cfct-customizations-dev-guide.md)\.

## AWS CodeCommit<a name="comp-aws-codecommit"></a>

Based on your input to the AWS CloudFormation template, CfCT can create an [AWS CodeCommit](https://aws.amazon.com/codecommit/) repository with the same sample configuration that's explained in the Amazon Simple Storage Service section\.

To clone the CfCT AWS CodeCommit repository to your local computer, you must create credentials that give you temporary access to the repository, as explained in the [AWS CodeCommit User Guide](https://docs.aws.amazon.com/codecommit/latest/userguide/temporary-access.html#temporary-access-configure-credentials)\. For information about version compatibility, see [Setting up for AWS CodeCommit](https://docs.aws.amazon.com/https://docs.aws.amazon.com/console/codecommit/connect-tc-alert-np)\.

## Amazon Simple Queue Service<a name="comp-amazon-sqs"></a>

CfCT uses an Amazon Simple Queue Service \(Amazon SQS\) FIFO queue to capture lifecycle events from Amazon EventBridge\. It triggers an AWS Lambda function, which invokes AWS CodePipeline to deploy AWS CloudFormation StackSets or SCPs\. For more information about SCPs, see [AWS Organizations](https://aws.amazon.com/organizations/)\.

## AWS CodePipeline<a name="comp-aws-codepipeline"></a>

AWS CodePipeline validates, tests, and implements changes based on updates to the configuration package, which you'll make in either the default Amazon S3 bucket or the AWS CodeCommit repository\. For more information about changing the configuration source control to AWS CodeCommit, refer to [Using Amazon S3 as the Configuration Source](cfct-s3-source.md)\. The pipeline includes stages to validate and manage the configuration files and templates, core accounts, AWS Organizations service control policies, and AWS CloudFormation StackSets\. For more information about the pipeline stages, refer to [CfCT customization guide](cfct-customizations-dev-guide.md)

## AWS Key Management Service<a name="comp-aws-kms"></a>

CfCT creates an [AWS Key Management Service](https://aws.amazon.com/kms/) \(AWS KMS\) `CustomControlTowerKMSKey` encryption key\. This key is used to encrypt objects in the Amazon S3 configuration bucket, Amazon SQS queue, and sensitive parameters in the AWS Systems Manager Parameter Store\. By default, only roles provisioned by CfCT have permission to perform encryption or decryption operations with this key\. For access to the configuration file, FIFO queue, or Parameter Store `SecureString` values, administrators must be added to the `CustomControlTowerKMSKey` policy\. Automatic key rotation is enabled by default\.

## AWS Lambda<a name="comp-aws-lambda"></a>

CfCT uses AWS Lambda functions to invoke the installation components during the initial installation and deployment of AWS CloudFormation StackSets or AWS Organizations SCPs during an AWS Control Tower lifecycle event\.

## AWS Systems Manager Parameter Store<a name="comp-aws-sm-parameter-store"></a>

[AWS Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) stores the CfCT configuration parameters\. These parameters allow you to integrate related configuration templates\. For example, you can configure each account to log AWS CloudTrail data to a centralized Amazon S3 bucket\. Also, the Systems Manager Parameter Store provides a centralized location where administrators can view CfCT inputs and parameters\.

## Amazon Simple Notification Service<a name="comp-amazon-sns"></a>

CfCT may publish notifications, such as pipeline approval, to [Amazon Simple Notification Service](https://aws.amazon.com/sns/) \(Amazon SNS\) topics during the workflow\. Amazon SNS is launched only when you choose to receive pipeline approval notifications\.