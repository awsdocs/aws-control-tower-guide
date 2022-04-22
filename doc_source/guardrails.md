# Guardrails in AWS Control Tower<a name="guardrails"></a>

## <a name="guardrail-definition"></a>

A guardrail is a high\-level rule that provides ongoing governance for your overall AWS environment\. It's expressed in plain language\. Through guardrails, AWS Control Tower implements *preventive* or *detective* controls that help you govern your resources and monitor compliance across groups of AWS accounts\.

A guardrail applies to an entire organizational unit \(OU\), and every AWS account within the OU is affected by the guardrail\. Therefore, when users perform work in any AWS account in your landing zone, they're always subject to the guardrails that are governing their account's OU\.

**The purpose of guardrails**

Guardrails enable you to express your policy intentions\. For example, if you enable the detective guardrail **Detect Whether Public Read Access to Amazon S3 Buckets is Allowed** on an OU, you can determine whether a user would be permitted to have read access to any S3 buckets for any accounts under that OU\.

## Guardrail behavior and guidance<a name="guardrail-behavior"></a>

Guardrails are categorized according to their *behavior* and their *guidance*\.

The *behavior* of each guardrail is either preventive or detective\. Guardrail *guidance* refers to the recommended practice for how to apply each guardrail to your OUs\. The guidance of a guardrail is independent of whether its behavior is preventive or detective\.



**Guardrail behavior**
+ **Preventive** – A preventive guardrail ensures that your accounts maintain compliance, because it disallows actions that lead to policy violations\. The status of a preventive guardrail is either **enforced** or ** not enabled**\. Preventive guardrails are supported in all AWS Regions\.
+ **Detective** – A detective guardrail detects noncompliance of resources within your accounts, such as policy violations, and provides alerts through the dashboard\. The status of a detective guardrail is either **clear**, **in violation**, or **not enabled**\. Detective guardrails apply only in those AWS Regions supported by AWS Control Tower\.

**Implementation of guardrail behavior**
+ The preventive guardrails are implemented using Service Control Policies \(SCPs\), which are part of AWS Organizations\.
+ The detective guardrails are implemented using AWS Config rules\.
+ Certain mandatory guardrails are implemented by means of a single SCP that performs multiple actions, rather than as unique SCPs\. Therefore, the same SCP is shown in the guardrail reference, under each mandatory guardrail to which that SCP applies\.

**Guardrail guidance**

AWS Control Tower provides three categories of guidance: *mandatory*, *strongly recommended*, and *elective* guardrails\.
+ Mandatory guardrails are always enforced\.
+ Strongly recommended guardrails are designed to enforce some common best practices for well\-architected, multi\-account environments\.
+ Elective guardrails enable you to track or lock down actions that are commonly restricted in an AWS enterprise environment\.

**Defaults:** When you create a new landing zone, AWS Control Tower enables all mandatory guardrails by default and applies them to your top\-level OUs\. When you extend governance to an OU, AWS Control Tower applies mandatory guardrails to the OU by default\. Strongly recommended and elective guardrails are not enabled by default\.

## Considerations for guardrails and OUs<a name="guardrail-considerations"></a>

When working with guardrails and OUs, consider the following properties:

**Guardrails, landing zones, and OUs**
+ After you create your landing zone, all resources in your landing zone, for example, Amazon S3 buckets, are subject to guardrails\.
+ OUs created through AWS Control Tower have mandatory guardrails applied to them automatically, and optional guardrails applied at the discretion of administrators\.
+ OUs created outside of an AWS Control Tower landing zone \(that is,* unregistered OUs*\) are displayed in the AWS Control Tower console, but AWS Control Tower guardrails do not apply to them, unless they become registered OUs\.
+ When you enable guardrails on an organizational unit \(OU\) that is registered with AWS Control Tower, preventive guardrails apply to all member accounts under the OU, enrolled and unenrolled\. Detective guardrails apply to enrolled accounts only\.

For more information about how guardrails are applied to nested OUs, in AWS Control Tower, see [Nested OUs and guardrails](nested-ous.md#nested-ous-and-guardrails)\.

## Exception to guardrails for the management account<a name="exception-to-guardrails"></a>

The root user and any IAM administrators in the management account can perform work that guardrails would otherwise deny\. This exception is intentional\. It prevents the management account from entering into an unusable state\. All actions taken within the management account continue to be tracked in the logs contained within the log archive account, for purposes of accountability and auditing\.

## Considerations for guardrails and accounts<a name="guardrails-and-accounts"></a>

When working with guardrails and accounts, consider the following properties:

**Guardrails and accounts**
+ Accounts created through the Account Factory in AWS Control Tower inherit the guardrails of the parent OU, and the associated resources are created\.
+ Accounts created outside of an AWS Control Tower landing zone do not inherit AWS Control Tower guardrails\. These are called * unenrolled* accounts\.
+ Accounts created outside of AWS Control Tower won't inherit guardrails in AWS Control Tower until you enroll them\. However, these unenrolled accounts *are* displayed in AWS Control Tower\.

+ Accounts inherit guardrails from an OU upon enrollment in that OU\.
+ An OU can contain enrolled or unenrolled *member accounts*\.
+ Guardrails do not apply to an unenrolled account unless it becomes a member account of a registered AWS Control Tower OU\. In that case, preventive guardrails for the OU will apply to the unenrolled account\. Detective guardrails will not apply\.
+ When you enable optional guardrails, AWS Control Tower creates and manages certain additional AWS resources in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so could result in the guardrails entering an unknown state\. For more information, see [Guardrail reference](guardrails-reference.md)\.
