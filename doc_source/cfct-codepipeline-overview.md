# Code pipeline overview<a name="cfct-codepipeline-overview"></a>

The configuration package requires Amazon Simple Storage Service \(Amazon S3\) and AWS CodePipeline\. The configuration package contains these items:
+ A manifest file
+ An accompanying set of templates
+ Other JSON files for describing and implementing your AWS Control Tower environment customizations

By default, the `_custom-control-tower-configuration.zip` configuration package is loaded in an Amazon S3 bucket with the following naming convention:

`custom-control-tower-configuration-accountID-region`\.

**Note**  
 By default, CfCT creates an Amazon S3 bucket to store the pipeline source, but you can change the source location to an AWS CodeCommit repository\. For more information, see [Edit a pipeline in CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-edit.html) in the *AWS CodePipeline User Guide*\.

The *manifest file* is a text file that describes the AWS resources you can deploy to customize your landing zone\. CodePipeline does these tasks: 
+ extracts the manifest file, accompanying set of templates, and other JSON files
+ performs manifest and template validations
+ invokes sections in the manifest file to run specific [pipeline stages](#code-pipeline-stages)\.

When you update the configuration package by customizing the manifest file and removing the underscore \(\_\) from the configuration package filename, it automatically initiates AWS CodePipeline\.

**Note**  
The sample configuration package filename begins with an underscore \(\_\) so that AWS CodePipeline is not automatically triggered\. When you have completed the customization of the configuration package, upload the file `custom-control-tower-configuration.zip` without the underscore \(\_\) in order to trigger the deployment in AWS CodePipeline\.

## AWS CodePipeline stages<a name="code-pipeline-stages"></a>

The CfCT pipeline requires several AWS CodePipeline stages to implement and update your AWS Control Tower environment\.

1. **Source stage**

   The source stage is the initial stage\. Your customized configuration package initiates this pipeline stage\. The source for the AWS CodePipeline can be either an Amazon S3 bucket or an AWS CodeCommit repository, in which the configuration package can be hosted\.

1. **Build stage**

   The build stage requires AWS CodeBuild to validate the contents of the configuration package\. These checks include testing the `manifest.yaml` file syntax and schema, along with all AWS CloudFormation templates included in the package or remotely hosted, using AWS CloudFormation `validate-template` and `cfn_nag`\. If the manifest file and AWS CloudFormation templates pass the tests, the pipeline continues to the next stage\. If the tests fail, you can review the CodeBuild logs to identify the issue and edit the configuration source file as needed\.

1. **Manual approval stage \(optional\)**

   The manual approval stage is optional\. If you enable this stage, it provides additional control over the configuration pipeline\. It pauses the pipeline during deployment, until an approval is given\. You can opt into manual approval by editing the **Pipeline Approval Stage** parameter to **Yes** when you launch the stack\.

1. **Service control policy stage**

   The service control policy stage invokes the service control policy state machine to call AWS Organizations APIs that create service control policies \(SCPs\)\.

1. **AWS CloudFormation resource stage**

   The AWS CloudFormation resource stage invokes the stack set state machine to deploy the resources specified in the list of accounts or organizational units \(OUs\), which you provided in the manifest file\. The state machine creates the AWS CloudFormation resources in the order that they are specified in the manifest file, unless a resource dependency is specified\.