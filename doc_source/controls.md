# The AWS Control Tower control library<a name="controls"></a>

## <a name="control-definition"></a>

A control is a high\-level rule that provides ongoing governance for your overall AWS environment\. It's expressed in plain language\. AWS Control Tower implements *preventive* or *detective* controls that help you govern your resources and monitor compliance across groups of AWS accounts\.

A control applies to an entire organizational unit \(OU\), and every AWS account within the OU is affected by the control\. Therefore, when users perform work in any AWS account in your landing zone, they're always subject to the controls that are governing their account's OU\.

**Note**  
We are transitioning our terminology to align better with industry usage and with other AWS services\. During this time, you may see the previous term, *guardrail*, as well as the new term, *control*, in our documentation, console, blogs, and videos\. These terms are synonymous for our purposes\.

**The purpose of controls**

Controls enable you to express your policy intentions\. For example, if you enable the detective control **Detect Whether Public Read Access to Amazon S3 Buckets is Allowed** on an OU, you can determine whether a user would be permitted to have read access to any S3 buckets for any accounts under that OU\.

## Control behavior and guidance<a name="control-behavior"></a>

Controls are categorized according to their *behavior* and their *guidance*\.

The *behavior* of each control is either preventive or detective\. Control *guidance* refers to the recommended practice for how to apply each control to your OUs\. The guidance of a control is independent of whether its behavior is preventive or detective\.



**Control behavior**
+ **Preventive** – A preventive control ensures that your accounts maintain compliance, because it disallows actions that lead to policy violations\. The status of a preventive control is either **enforced** or **not enabled**\. Preventive controls are supported in all AWS Regions\.
+ **Detective** – A detective control detects noncompliance of resources within your accounts, such as policy violations, and provides alerts through the dashboard\. The status of a detective control is either **clear**, **in violation**, or **not enabled**\. Detective controls apply only in those AWS Regions supported by AWS Control Tower\.

**Implementation of control behavior**
+ The preventive controls are implemented using Service Control Policies \(SCPs\), which are part of AWS Organizations\.
+ The detective controls are implemented using AWS Config rules\.
+ Certain mandatory controls are implemented by means of a single SCP that performs multiple actions, rather than as unique SCPs\. Therefore, the same SCP is shown in the control reference, under each mandatory control to which that SCP applies\.

**Control guidance**

AWS Control Tower provides three categories of guidance: *mandatory*, *strongly recommended*, and *elective* controls\.
+ Mandatory controls are always enforced\.
+ Strongly recommended controls are designed to enforce some common best practices for well\-architected, multi\-account environments\.
+ Elective controls enable you to track or lock down actions that are commonly restricted in an AWS enterprise environment\.

**Defaults:** When you create a new landing zone, AWS Control Tower enables all mandatory controls by default and applies them to your top\-level OUs\. When you extend governance to an OU, AWS Control Tower applies mandatory controls to the OU by default\. Strongly recommended and elective controls are not enabled by default\.

## Considerations for controls and OUs<a name="control-considerations"></a>

When working with controls and OUs, consider the following properties:

**Controls, landing zones, and OUs**
+ After you create your landing zone, all resources in your landing zone are subject to controls\. For example, certain controls apply to Amazon S3 buckets\.
+ OUs created through AWS Control Tower have mandatory controls applied to them automatically\. Optional controls are applied at the discretion of administrators\.
+ OUs created outside of an AWS Control Tower landing zone \(that is,* unregistered OUs*\) are displayed in the AWS Control Tower console, but AWS Control Tower controls do not apply to them, unless they become registered OUs\.
+ When you enable controls on an organizational unit \(OU\) that is registered with AWS Control Tower, preventive controls apply to all member accounts under the OU, enrolled and unenrolled\. Detective controls apply to enrolled accounts only\.

For more information about how controls are applied to nested OUs, in AWS Control Tower, see [Nested OUs and controls](nested-ous.md#nested-ous-and-controls)\.

## Exception to controls for the management account<a name="exception-to-controls"></a>

The root user and any IAM administrators in the management account can perform work that controls would otherwise deny\. This exception is intentional\. It prevents the management account from entering into an unusable state\. All actions taken within the management account continue to be tracked in the logs contained within the log archive account, for purposes of accountability and auditing\.

## Considerations for controls and accounts<a name="controls-and-accounts"></a>

When working with controls and accounts, consider the following properties:

**Controls and accounts**
+ Accounts created through the Account Factory in AWS Control Tower inherit the controls of the parent OU, and the associated resources are created\.
+ Accounts created outside of an AWS Control Tower landing zone do not inherit AWS Control Tower controls\. These are called * unenrolled* accounts\.
+ Accounts created outside of AWS Control Tower won't inherit controls in AWS Control Tower until you enroll them\. However, these unenrolled accounts *are* displayed in AWS Control Tower\.

  Accounts inherit controls from an OU upon enrollment in that OU\.
+ An OU can contain enrolled or unenrolled *member accounts*\.
+ Controls do not apply to an unenrolled account unless it becomes a member account of a registered AWS Control Tower OU\. In that case, preventive controls for the OU will apply to the unenrolled account\. Detective controls will not apply\.
+ When you enable optional controls, AWS Control Tower creates and manages certain additional AWS resources in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so could result in the controls entering an unknown state\. For more information, see [Control reference](controls-reference.md)\.