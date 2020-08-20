# Guardrails in AWS Control Tower<a name="guardrails"></a>

## <a name="guardrail-definition"></a>

A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. It's expressed in plain language\. Through guardrails, AWS Control Tower implements *preventive* or *detective* controls that help you govern your resources and monitor compliance across groups of AWS accounts\.

A guardrail applies to an entire organizational unit \(OU\), and every AWS account within the OU is affected by the guardrail\. Therefore, when users perform work in any AWS account in your landing zone, they're always subject to the guardrails that are governing their account's OU\.

**The purpose of guardrails**

Guardrails enable you to express your policy intentions\. For example, if you enable the detective **Disallow public read access to S3 buckets** guardrail on an OU, you can determine whether a user has attempted public read access to any S3 buckets for any accounts under that OU\.

## Guardrail Behavior and Guidance<a name="guardrail-behavior"></a>

Guardrails are categorized according to their *behavior* and their *guidance*\.

The *behavior* of each guardrail is either preventive or detective\. Guardrail *guidance* refers to the recommended practice for how to apply each guardrail to your OUs\. The guidance of a guardrail is independent of whether its behavior is preventive or detective\.

**Guardrail behavior**
+ **Prevention** – A preventive guardrail ensures that your accounts maintain compliance, because it disallows actions that lead to policy violations\. The status of a preventive guardrail is either **enforced** or ** not enabled**\. Preventive guardrails are supported in all AWS Regions\.
+ **Detection** – A detective guardrail detects noncompliance of resources within your accounts, such as policy violations, and provides alerts through the dashboard\. The status of a detective guardrail is either **clear**, **in violation**, or **not enabled**\. Detective guardrails apply only in those AWS Regions supported by AWS Control Tower\.

**Implementation of guardrail behavior**
+ The preventive guardrails are implemented using Service Control Policies \(SCPs\), which are part of AWS Organizations\.
+ The detective guardrails are implemented using AWS Config rules and AWS Lambda functions\.
+ Certain mandatory guardrails are implemented by means of a single SCP that performs multiple actions, rather than as unique SCPs\. Therefore, the same SCP is shown in the guardrail reference, under each mandatory guardrail to which that SCP applies\.

**Guardrail guidance**

AWS Control Tower provides three categories of guidance: *mandatory*, *strongly recommended*, and *elective* guardrails\.
+ Mandatory guardrails are always enforced\.
+ Strongly recommended guardrails are designed to enforce some common best practices for well\-architected, multi\-account environments\.
+ Elective guardrails enable you to track or lock down actions that are commonly restricted in an AWS enterprise environment\.

**Defaults:** When you create a new landing zone, all mandatory guardrails are enabled by default\. Strongly recommended and elective guardrails are not enabled by default\.

## Considerations for Guardrails and OUs<a name="guardrail-considerations"></a>

When working with guardrails and OUs, consider the following properties:
+ When you enable guardrails on an organizational unit, those guardrails apply to all child accounts under the OU\.
+ After you create your landing zone, all resources in your landing zone, for example, S3 buckets, are subject to guardrails\.
+ OUs created through AWS Control Tower have mandatory guardrails applied to them automatically, and other guardrails applied at the discretion of administrators\.
+ OUs created outside of an AWS Control Tower landing zone don't have guardrails applied to them automatically\.
+ Accounts created through Account Factory inherit their parent OU's guardrails\.
+  Accounts created outside of a landing zone do not inherit guardrails\.
+ *Unregistered* OUs are displayed in the AWS Control Tower console, but guardrails do not apply to them\.
+ *Unenrolled* accounts are displayed in the AWS Control Tower console, but guardrails do not apply to them\.

**Exceptions to guardrails**
+ The root user and any IAM administrators in the master account can perform work that guardrails would otherwise deny\. This exception is intentional\. It prevents the master account from entering into an unusable state\. All actions taken within the master account continue to be tracked in the logs contained within the log archive account, for purposes of accountability and auditing\.

## Optional Guardrails<a name="optional-guardrails"></a>

Strongly recommended and elective guardrails are optional, which means that you can customize the level of enforcement for your landing zone by choosing which ones to enable\. Optional guardrails are not enabled by default\. For more information about optional guardrails, see the following guardrail references:
+ [Strongly Recommended Guardrails](strongly-recommended-guardrails.md)
+ [Elective Guardrails](elective-guardrails.md)

## Viewing Guardrail Details<a name="guardrail-details"></a>

In the guardrail details page of the console, you can find the following details for each guardrail:
+ **Name** – The name of the guardrail\.
+ **Description** – A description of the guardrail\.
+ **Guidance** – The guidance is either mandatory, strongly recommended, or elective\.
+ **Category** – The guardrail category can be Audit Logs, Monitoring, Data Security, Network, IAM, or Control Tower Setup\.
+ **Behavior** – A guardrail's behavior is set to either preventive or detective\.
+ **Compliance Status** – A guardrail's compliance status can be clear, compliant, enforced, unknown, or in violation\.

On the guardrail details page, you can also see guardrail artifacts\. The guardrail is implemented by one or more artifacts\. These artifacts can include a baseline AWS CloudFormation template, a service control policy \(SCP\) to prevent account\-level configuration changes or activity that may create configuration drift, and AWS Config Rules to detect account\-level policy violations\.

## Enabling Guardrails<a name="enable-guardrails"></a>

Most guardrails are enabled automatically according to an OU's configuration, and some guardrails can be enabled manually on your OUs\. The following procedure describes the steps for enabling guardrails on an OU\.

**Important**  
When you enable guardrails with strongly recommended guidance, AWS Control Tower managed AWS resources are created in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so could result in the guardrails entering an unknown state\.

**To enable guardrails in an OU**

1. Using a web browser, navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. From the left navigation, choose **Guardrails**\.

1. Choose a guardrail that you want to enable; for example, **Guardrail: Enable encryption for EBS volumes attached to EC2 instances**\. This choice opens the guardrail's details page\.

1. From **Organizational units enabled**, choose **Enable guardrail on OU**\.

1. A new page is displayed that lists the names of your OUs\. Identify the OU on which you want to enable this guardrail\.

1. Choose **Enable guardrail on OU**\.

1. Your guardrail is now enabled\. It may take several minutes for the change to complete\. When it does, you'll see that this guardrail is enabled on the OU you selected\. You can enable only one guardrail at a time\.