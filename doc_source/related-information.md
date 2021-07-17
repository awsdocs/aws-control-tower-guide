# Related information<a name="related-information"></a>

This topic lists common use cases and best practices for AWS Control Tower capabilities and additional enhancements\. This topic also includes links to relevant blog posts, technical documentation, and related resources that can help you as you work with AWS Control Tower\.

## Tutorials and labs<a name="tutorials-and-labs"></a>
+ [AWS Control Tower lab](https://controltower.aws-management.tools/immersionday) – These labs provide a high\-level overview of common tasks related to AWS Control Tower\.
+ On the AWS Control Tower dashboard, choose **Get personalized guidance** if you have a use case in mind but you're not sure where to start\.

## Networking<a name="networking"></a>

Set up repeatable and manageable patterns for networks in AWS\. Learn more about design, automation, and appliances that are commonly used by customers\.
+ [AWS Quick Start VPC Architecture](http://aws.amazon.com/quickstart/architecture/vpc/)– This Quick Start guide provides a networking foundation based on AWS best practices for your AWS Cloud infrastructure\. It builds an AWS Virtual Private Network environment with public and private subnets where you can launch AWS services and other resources\. 
+ [Self\-service VPCs in AWS Control Tower using AWS Service Catalog](http://aws.amazon.com/blogs/mt/self-service-vpcs-in-aws-control-tower-using-aws-service-catalog/)– This blog post describes a way to set up Account Factory so you can provision accounts with customized VPCs\.
+ [Implementing Serverless Transit Network Orchestrator \(STNO\) in AWS Control Tower](http://aws.amazon.com/blogs/mt/serverless-transit-network-orchestrator-stno-in-control-tower/) – This blog post demonstrates how to automate network connectivity access across accounts\. This blog is intended for AWS Control Tower administrators, or those responsible for managing networks within their AWS environment\. 

## Security, identity, and logging<a name="security-identity-logging"></a>

Extend your security posture, integrate with external or existing identity providers, and centralize logging systems\.

<a name="resource-security"></a>*Security*
+ [Automating AWS Security Hub Alerts with AWS Control Tower lifecycle events](http://aws.amazon.com/blogs/mt/automating-aws-security-hub-alerts-with-aws-control-tower-lifecycle-events/) – This blog post describes how to automate Security Hub enablement and configuration in an AWS Control Tower multi\-account environment on existing and new accounts\.
+ [Enabling AWS Identity and Access Management](http://aws.amazon.com/blogs/mt/enabling-aws-identity-and-access-analyzer-on-aws-control-tower-accounts) – This blog post describes how to enhance your organizational security visibility by enabling and centralizing IAM Access Analyzer findings\.
+ [AWS Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html                     ) provides secure, hierarchical storage for configuration data management and secrets management\. You can use it to share configuration information in a secure location, for use by AWS Systems Manager and by AWS CloudFormation\. For example, you can store a list of Regions in which you want to deploy conformance packs\. 

<a name="identity"></a>*Identity*
+ [Link Azure AD user identity into AWS accounts and applications for single sign\-on](http://aws.amazon.com/blogs/aws/the-next-evolution-in-aws-single-sign-on/) – This blog post describes how to use Azure AD with AWS SSO and AWS Control Tower\.
+ [Manage access to AWS centrally for Okta users with AWS Single Sign\-On](http://aws.amazon.com/about-aws/whats-new/2020/05/manage-access-to-aws-centrally-for-okta-users-with-aws-single-sign-on/) – This blog post describes how to use Okta with AWS SSO and AWS Control Tower\. 

<a name="logging"></a>*Logging*
+ [AWS Centralized Logging Solution](http://aws.amazon.com/solutions/implementations/centralized-logging/) – This solutions post describes the Centralized Logging solution which enables organizations to collect, analyze, and display logs on AWS across multiple accounts and AWS Regions\. 

## Deploying resources and managing workloads<a name="deploy-resources"></a>

Deploy and manage resources and workloads\.
+ [Getting Started Library integration](http://aws.amazon.com/about-aws/whats-new/2020/04/aws-service-catalog-adds-three-new-getting-started-portfolios/) – This blog post describes Getting Started portfolios you can use\. 
+ [Continuous deployment of Cloud Custodian to AWS Control Tower](http://aws.amazon.com/blogs/opensource/continuous-deployment-of-cloud-custodian-to-aws-control-tower/)

## Working with existing organizations and accounts<a name="working-existing-organizations"></a>

Work with existing AWS organizations and accounts\.
+ [Enroll an account](https://docs.aws.amazon.com/controltower/latest/userguide/enroll-account) – This user guide topic describes how to enroll an existing AWS account in AWS Control Tower\. 
+ [Bring an account under AWS Control Tower](http://aws.amazon.com/blogs/architecture/field-notes-enroll-existing-aws-accounts-into-aws-control-tower/) – This blog post describes how to deploy AWS Control Tower into your existing AWS organizations\.
+  [Extend AWS Control Tower governance using AWS Config conformance packs](http://aws.amazon.com/blogs/mt/extend-aws-control-tower-governance-using-aws-config-conformance-packs/) – This blog post describes how to deploy AWS Config conformance packs to assist with bringing existing accounts and organizations into governance by AWS Control Tower\. 
+ [How to Detect and Mitigate Guardrail Violation with AWS Control Tower](http://aws.amazon.com/blogs/mt/how-to-detect-and-mitigate-guardrail-violation-with-aws-control-tower/) – This blog post describes how to add guardrails and how to subscribe to SNS notifications so that you can be notified by email of guardrail compliance violations\.

## Automation and integration<a name="automation-and-integration"></a>

Automate account creation and integrate lifecycle events with AWS Control Tower\.
+ [Lifecycle events](http://aws.amazon.com/blogs/mt/using-lifecycle-events-to-track-aws-control-tower-actions-and-trigger-automated-workflows) – This blog post describes how to use lifecycle events with AWS Control Tower\.
+ [Automate account creation](http://aws.amazon.com/blogs/mt/how-to-automate-the-creation-of-multiple-accounts-in-aws-control-tower/) – This blog post describes how to set up automated account creation in AWS Control Tower\. 
+ [Amazon VPC flow log automation](http://aws.amazon.com/blogs/mt/vpc-flow-log-with-aws-control-tower-lifecycle) – This blog post describes how to automate and centralize Amazon VPC Flow Logs in a multi\-account environment\.

## Migrating workloads<a name="migrating"></a>

Use other AWS services with AWS Control Tower to assist in workload migration\.
+ [CloudEndure migration](http://aws.amazon.com/blogs/mt/how-to-take-advantage-of-aws-control-tower-and-cloudendure-to-migrate-workloads-to-aws/) – This blog post describes how to combine CloudEndure and other AWS services with AWS Control Tower to assist in workload migration\.

## AWS Marketplace solutions<a name="aws-marketplace-solutions"></a>

Discover solutions from AWS Marketplace\. 
+ [AWS Control Tower Marketplace](http://aws.amazon.com/marketplace/solutions/control-tower) – AWS Marketplace offers a broad range of solutions for AWS Control Tower to help you integrate third\-party software\. These solutions help solve key infrastructure and operational use cases including identity management, security for a multi\-account environment, centralized networking, operational intelligence, and security information and event management \(SIEM\)\.