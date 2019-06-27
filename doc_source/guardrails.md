# Guardrails in AWS Control Tower<a name="guardrails"></a>

A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. It's expressed in plain language\.

When users perform work in an AWS account in your landing zone, they're subject to guardrails\. Currently, there are 20 guardrails that you can apply to an organizational unit \(OU\)\. There are 17 prevention guardrails, and 3 detection guardrails\.

The behavior of each guardrail is either prevention or detection\.
+ **Prevention** – A preventive guardrail ensures that your accounts maintain compliance\. The status of a preventive guardrail behavior is either **enforced** or **not\-enabled**\. A preventive guardrail prevents policy violations by using service control policies, AWS Config Rules, and AWS Lambda functions\. Preventive guardrails are supported in all AWS Regions\.
+ **Detection** – A detective guardrail detects noncompliance of resources within your accounts\. The status of a detective behavior is either **clear** or **in violation**\. A detective guardrail detects policy violations and provides alerts through the dashboard\. Detective guardrails only apply in the AWS Regions supported by AWS Control Tower\.

AWS Control Tower provides mandatory and strongly recommended guardrails\. When you create a new landing zone, all mandatory guardrails are enforced by default, and strongly recommended guardrails are not enabled\.

Guardrails enable you to express your policy intentions\. AWS Control Tower implements preventive or detective controls to govern and monitor compliance of your resources across AWS accounts\. For example, enable the **Disallow public read access to S3 buckets** guardrail to deny public read access to all S3 buckets for all accounts under an OU\. When you enable guardrails on organizational units, they are applied to all child accounts under the OU\.

## Considerations<a name="guardrail-considerations"></a>

When working with guardrails, consider the following:
+ After creating your landing zone, all resources in your landing zone are subject to guardrails\.
+ OUs created through AWS Control Tower have guardrails applied to them\. OUs created outside of a landing zone can't have guardrails applied to them, and they do not display in the AWS Control Tower console\.
+ Accounts created through Account Factory inherit their parent OU's guardrails\. Accounts created outside of a landing zone do not, and are not displayed in the AWS Control Tower console\.
+ The root user and any IAM administrators in the master account can perform work that guardrails would otherwise deny\. This is intentional\. This prevents the master account from entering into an unusable state\. While this is the case, all actions taken within the master account continue to be tracked in the logs in the log archive account\. The logs enable accountability and auditing\.

## Guardrail Details<a name="guardrail-details"></a>

In the guardrail details page of the console, you can find the following details for each guardrail:
+ **Name** – The name of the guardrail\.
+ **Description** – A description of the guardrail\.
+ **Guidance** – The guidance is either strongly recommended or mandatory\. Mandatory guardrails are automatically applied to all OUs by AWS Control Tower\. Strongly recommended guardrails can be enabled by central cloud administrators from the AWS Control Tower console\.
+ **Category** – The category can be Audit Logs, Monitoring, Data Security, Network, IAM, or Control Tower Setup\.
+ **Behavior** – A guardrail's behavior is set to either PREVENTATIVE or DETECTIVE\.
+ **Compliance Status** – A guardrail's compliance status can be clear, compliant, enforced, unknown, or in violation\.

On the guardrail details page, you can also see guardrail artifacts\. The guardrail is implemented by one or more artifacts\. These artifacts can include a baseline AWS CloudFormation template, a service control policy to prevent account\-level configuration changes or activity that may create configuration drift, and AWS Config Rules to detect account\-level policy violations\.

## Enabling Guardrails<a name="enable-guardrails"></a>

While most guardrails are enabled automatically according to an OU's configuration, some guardrails can be enabled manually on your OUs\. The following procedure describes the steps for enabling guardrails on an OU\.

**Important**  
When you enable guardrails with strongly recommended guidance, AWS Control Tower managed AWS resources are created in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so could result in the guardrails entering an unknown state\.

**To enable guardrails in an OU**

1. Using a web browser, navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. From the left navigation, choose **Guardrails**\.

1. Choose a guardrail that you want to enable; for example, **Guardrail: Enable encryption for EBS volumes attached to EC2 instances**\. This opens the guardrail's details page\.

1. From **Organizational units enabled**, choose **Enable guardrail on OU**\.

1. A new page is displayed that lists the names of your OUs\. Choose the OU that you want to enable this guardrail on\.

1. Choose **Enable guardrail on OU**\.

1. Your guardrail is now enabled\. It may take several minutes for the change to complete\. When it does, you'll see that this guardrail is enabled on the OU you selected\.