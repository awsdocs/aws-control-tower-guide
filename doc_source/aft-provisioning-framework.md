# AFT account provisioning pipeline<a name="aft-provisioning-framework"></a>

After the account provisioning stage of the pipeline is complete, the AFT framework continues\. It automatically runs a series of steps to ensure that the newly provisioned accounts have details in place, before the [Account customizations](aft-account-customization-options.md) stage begins\.

**Here are the next steps that the AFT pipeline runs\.**

1. Validates the account request input\.

1. Retrieves information about the account provisioned, for example, the account ID\.

1. Stores the account metadata in a DynamoDB table in the AFT management account\.

1. Creates the **AWSAFTExecution** IAM role in the newly provisioned account\. AFT assumes this role to perform the account customizations stage, because this role grants access to the account factory portfolio\. 

1. Applies the account tags that you provided as part of the account request input parameters\.

1. Applies the AFT feature options you chose at the time of AFT deployment\.

1. Applies the AFT account provisioning customizations you provided\. The next section tells more about how to set up these customizations with an AWS Step Functions state machine, in a `git` repository\. This stage is sometimes referred to as the *account provisioning framework* stage\. It is part of the core provisioning process, but you've previously set up a framework that delivers customized integrations as part of your account provisioning workflow, before additional customizations are added to the accounts in the next stage\.

1. For each account provisioned, it creates an AWS CodePipeline in the AFT management account, which will run to perform the \(next, global\) [Account customizations](aft-account-customization-options.md) stage\.

1. Invokes the account customizations pipeline for each account provisioned \(and targeted\)\.

1. Sends a success or failure notification to the SNS topic, from which you can retrieve the messages\.

## Set up the account provisioning framework customizations with a state machine<a name="aft-customizations"></a>

If you set up custom, non\-Terraform integrations before you provision your accounts, these customizations are included in your AFT account provisioning workflow\. For example, you may require certain customizations to ensure that all accounts created by AFT are compliant with the standards and policies of your organization, such as security standards, and these standards may be added to accounts before additional customization\. These *account provisioning framework* customizations are implemented on every provisioned account, before the global account customization stage begins next\.

**Note**  
The AFT feature described in this section is intended for advanced users who understand the functioning of AWS Step Functions\. As an alternative, we recommend that you work with the global helpers in the account customizations stage\.

The AFT account provisioning framework calls an AWS Step Functions state machine, which you define, to implement your customizations\. Refer to the [AWS Step Functions documentation](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html) to learn more about the possible state machine integrations\.

Here are some common integrations\.
+ AWS Lambda functions in the language of your choice
+ AWS ECS or AWS Fargate tasks, using Docker containers
+ AWS Step Functions activities using custom workers, hosted either in AWS or on\-premises
+ Amazon SNS or SQS integrations

If no AWS Step Functions state machine is defined, the stage passes with a no\-op\. To create an AFT account provisioning customizations state machine, follow the instructions in [Create your AFT account provisioning customizations state machine](#aft-create-customizations)\. Before you add customizations, be sure you have the prerequisites in place\.

These types of integrations are not part of AWS Control Tower, and they cannot be added during the global pre\-API stage of AFT account customization\. Instead, the AFT pipeline allows you to set up these customizations as part of the provisioning process, and they are run in the provisioning workflow\. You must implement these customizations by creating your state machine ahead of time, before you kick off the AFT account provisioning stage, as described in the following sections\. 

**Prerequisites for creating a state machine**
+ A fully deployed AFT\. See [Deploy AWS Control Tower Account Factory for Terraform \(AFT\)](aft-getting-started.md) for more information about AFT deployment\.
+ Set up a `git` repository in your environment for AFT account provisioning customizations\. See [Post\-deployment steps](aft-post-deployment.md) for more information\.

## Create your AFT account provisioning customizations state machine<a name="aft-create-customizations"></a>

**Step 1: Modify the state machine definition**

Modify the example `customizations.asl.json` state machine definition\. The example is available in the `git` repository you set up for storing AFT account provisioning customizations, in your [post\-deployment steps](https://docs.aws.amazon.com/controltower/latest/userguide/aft-post-deployment.html)\. Refer to the [AWS Step Functions Developer Guide](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html) to learn more about state machine definitions\.

**Step 2: Include the corresponding Terraform configuration**

Include Terraform files with the `.tf` extension in the same `git` repository with the state machine definition for your custom integration\. For example, if you choose to call a Lambda function in your state machine task definition, you’d include the `lambda.tf` file in the same directory\. Make sure you include the required IAM roles and permissions for your custom configurations\.

When you provide the appropriate input, the AFT pipeline automatically invokes your state machine and deploys your customizations as part of the AFT account provisioning framework stage\. 

## To re\-start the AFT account provisioning framework and customizations<a name="aft-provisioining-considerations"></a>

AFT runs the account provisioning framework and customizations steps for every account vended through the AFT pipeline\. To re\-start account provisioning customizations, you can use one of these two methods:

1. Make any change to an existing account in the account request repo\.

1. Provision a new account with AFT\.