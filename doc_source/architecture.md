# Architecture overview<a name="architecture"></a>

Deploying CfCT builds the following environment in the AWS Cloud\.

![\[Customizations for AWS Control Tower architecture diagram\]](http://docs.aws.amazon.com/controltower/latest/userguide/images/customizations-for-aws-control-tower-architecture-diagram.png)

CfCT includes an AWS CloudFormation template that you deploy in your AWS Control Tower management account\. The template launches all the components necessary to build the workflows, so you can customize your AWS Control Tower landing zone\.

**Note**  
CfCT must be deployed in the AWS Control Tower home Region and in the AWS Control Tower management account, because that is where your AWS Control Tower landing zone is deployed\. For information about setting up an AWS Control Tower landing zone, refer to [Getting started with AWS Control Tower](getting-started-with-control-tower.md)\.

As you deploy CfCT, it packages and uploads the custom resources to the code pipeline source, by means of [Amazon Simple Storage Service](https://aws.amazon.com/s3/) \(Amazon S3\)\. The upload process automatically invokes the service control policies \(SCPs\) state machine and the [AWS CloudFormation StackSets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/what-is-cfnstacksets.html) state machine to deploy the SCPs at the OU level, or to deploy stack instances at the OU or account level\.

**Note**  
By default, CfCT creates an Amazon S3 bucket to store the pipeline source, but you can change the location to an [AWS CodeCommit](https://aws.amazon.com/codecommit/) repository\. For more information, refer to [[Set up Amazon S3 as the configuration source](cfct-s3-source.md)](cfct-s3-source.md)\.

**CfCT deploys two workflows:**
+ an [AWS CodePipeline](https://aws.amazon.com/codepipeline/) workflow
+ and an AWS Control Tower lifecycle event workflow\. 

**The AWS CodePipeline workflow**

The AWS CodePipeline workflow configures AWS CodePipeline, [AWS CodeBuild](https://aws.amazon.com/codebuild/) projects, and [AWS Step Functions](https://aws.amazon.com/step-functions/) that orchestrate the management of AWS CloudFormation StackSets and SCPs in your organization\. 

When you upload the configuration package, CfCT invokes the code pipeline to run three stages\.
+ **Build Stage** – validates the contents of the configuration package using AWS CodeBuild\.
+ **SCP Stage** – invokes the service control policy state machine, which calls the AWS Organizations API to create SCPs\.
+ **AWS CloudFormation Stage** – invokes the stack set state machine to deploy the resources specified in the list of accounts or OUs, which you've provided in [the manifest file](the-manifest-file.md)\.

At each stage, the code pipeline invokes the stack set and SCP step functions, which deploy custom stack sets and SCPs to the targeted individual accounts, or to an entire organizational unit\.

**Note**  
For detailed information about customizing the configuration package, refer to [CfCT customization guide](cfct-customizations-dev-guide.md)\.

**The AWS Control Tower lifecycle event workflow**

When a new account is created in AWS Control Tower, a [lifecycle event](lifecycle-events.md) can invoke the AWS CodePipeline workflow\. You can customize the configuration package through this workflow, which consists of an [Amazon EventBridge](https://aws.amazon.com/eventbridge/) event rule, an [Amazon Simple Queue Service](https://aws.amazon.com/sqs/) \(Amazon SQS\) first\-in first\-out \(FIFO\) queue, and an [AWS Lambda](https://aws.amazon.com/lambda/) function\.

When the Amazon EventBridge event rule detects a matching lifecycle event, it passes the event to the Amazon SQS FIFO queue, invokes the AWS Lambda function, and invokes the code pipeline to perform downstream deployment of stack sets and SCPs\.