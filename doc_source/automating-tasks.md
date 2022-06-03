# Automating tasks in AWS Control Tower<a name="automating-tasks"></a>

Many customers prefer to automate tasks in AWS Control Tower, such as account provisioning and auditing\. You can set up automated actions with calls to: 
+ [AWS Service Catalog APIs](https://docs.aws.amazon.com/servicecatalog/latest/dg/service-catalog-api-overview.html) 
+ [AWS Organizations APIs](https://docs.aws.amazon.com/organizations/latest/APIReference/Welcome.html)
+ [the AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/servicecatalog/index.html)

You can automate the closure of AWS Control Tower member accounts with an AWS Organizations API\. For more information, see [Close an AWS Control Tower member account through AWS Organizations](delete-account.md#close-account-with-orgs-api)\.

The [Related information](related-information.md) page contains links to many excellent technical blog posts that can help you automate tasks in AWS Control Tower\. The sections that follow provide links to areas in this *AWS Control Tower User Guide* that can assist you with automating tasks\.

**Automated account provisioning and updating**

AWS Control Tower Account Factory for Terraform \(AFT\) follows a GitOps model to automate the processes of account provisioning and account updating in AWS Control Tower\. For more information, see [Provision accounts with AWS Control Tower Account Factory for Terraform](taf-account-provisioning.md)\.

*Customizations for AWS Control Tower* \(CfCT\) helps you customize your AWS Control Tower landing zone and stay aligned with AWS best practices\. Customizations are implemented with AWS CloudFormation templates and service control policies \(SCPs\)\. For more information, see [Customizations for AWS Control Tower \(CfCT\) overview](cfct-overview.md)\.

For more information and a video about automated account provisioning, see [Walkthrough: Automated account provisioning in AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/automated-provisioning-walkthrough.html) and [Automated provisioning with IAM roles](https://docs.aws.amazon.com/controltower/latest/userguide/roles-how.html#automated-provisioning)\.

Also see [Update accounts by script](https://docs.aws.amazon.com/controltower/latest/userguide/configuration-updates.html#update-accounts-by-script)\.

**Programmatic auditing of accounts**

For more information about auditing accounts programmatically, see [Programmatic roles and trust relationships for the AWS Control Tower audit account](https://docs.aws.amazon.com/controltower/latest/userguide/roles-how.html#stacksets-and-roles)\.

**Automating other tasks**

For technical blogs that cover automation and integration use cases, see [Automation and integration](https://docs.aws.amazon.com/controltower/latest/userguide/related-information.html#automation-and-integration)\.

Two open source samples are available on GitHub to help you with certain automation tasks related to security\.
+ The sample called [aws\-control\-tower\-org\-setup\-sample](https://github.com/aws-samples/aws-control-tower-org-setup-sample) shows how to automate setting up the Audit account as the delegated administrator for security\-related services\.
+ The sample called [aws\-control\-tower\-account\-setup\-using\-step\-functions](https://github.com/aws-samples/aws-control-tower-account-setup-using-step-functions) shows how to automate security best practices using Step Functions, when provisioning and configuring new accounts\. This sample includes adding principals to organizationally\-shared AWS Service Catalog portfolios and associating organization\-wide AWS SSO groups to new accounts automatically\. It also illustrates how to delete the default VPC in every Region\.

The *AWS Security Reference Architecture* includes code examples for automating tasks related to AWS Control Tower\. For more information, see the [AWS Prescriptive Guidance pages](https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/welcome.html) and the [associated GitHub repository](https://github.com/aws-samples/aws-security-reference-architecture-examples/tree/main/aws_sra_examples)\.

For information about using AWS Control Tower with AWS CloudShell, an AWS service that facilitates working in the AWS CLI, see [AWS CloudShell and the AWS CLI](https://docs.aws.amazon.com/controltower/latest/userguide/using-aws-with-cloudshell.html)\.

Because AWS Control Tower is an orchestration layer for AWS Organizations, many other AWS services are available by means of APIs and the AWS CLI\. For more information, see [Related AWS services](https://docs.aws.amazon.com/controltower/latest/userguide/related-information.html#related-aws-services)\.