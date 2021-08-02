# Guardrails and compliance<a name="compliance"></a>

Within AWS Control Tower, compliance means that cloud administrators know when the accounts in their organization are compliant with established policies, while builders can provision new AWS accounts quickly in a few clicks\. AWS Control Tower guardrails embody the rules of compliance\.

When we talk about compliance in AWS Control Tower, we do not intend the same meaning as compliance with governmental regulations, such as data privacy or health information standards\. However, AWS Control Tower helps your organization to comply with many governmental regulations\.

**Examples of compliance rules \(guardrails\) in AWS Control Tower:**
+ Detect whether public write access to Amazon S3 buckets is allowed
+ Detect whether unrestricted incoming TCP traffic is allowed

**Examples of governmental compliance regulations:**
+ The U\.S\. Health Insurance Portability and Accountability Act of 1996 \(HIPAA\)
+ The European Union’s General Data Protection Regulation of 2016 \(GDPR\)

For more information about how AWS Control Tower helps you maintain compliance with governmental regulations and industry standards, see [Compliance Validation](https://docs.aws.amazon.com/controltower/latest/userguide/compliance-program-info.html)\.

 **How can administrators review compliance?** 

For ongoing governance, administrators can enable pre\-configured guardrails—clearly defined rules for security, operations, and compliance\. These guardrails can:
+ prevent deployment of resources that don’t conform to policies \(by means of preventive guardrails, implemented with SCPs\)
+ continuously monitor deployed resources for nonconformance \(by means of detective guardrails, implemented with AWS Config Rules\)

If an account has any non\-compliant resources, that account may be shown with **Non\-compliant** status on the **OU** or **Account** page in the AWS Control Tower console\. Details about the specific resources that have caused the non\-compliant status are shown on the **Account details** page\. If an account shows **Compliant** status, that means it has no resources that are non\-compliant; therefore, no resource details are shown on the **Account details** page, only an empty table\.

You can subscribe to SNS topics that send notifications when resource compliance status changes\. See [Drift prevention and notification](prevention-and-notification.md), later in this chapter\.

For more information on how AWS Control Tower collects information about resources, see the [AWS Config Aggregator Documentation](https://docs.aws.amazon.com/config/latest/developerguide/aggregate-data.html)\.

**Drift is related to compliance status for OU and account resources**

Drift is reported as **Unknown** in the **Compliance** status field of the AWS Control Tower console\. The **Unknown** state indicates that AWS Control Tower cannot determine the compliance status of the resource, because drift is present\. Drift is not necessarily a detective guardrail compliance violation\. For more information about drift, see [Detect and resolve drift in AWS Control Tower](drift.md)\.

## AWS Control Tower guardrail compliance status<a name="compliance-statuses"></a>

This section lists the possible categories of compliance and non\-compliance in AWS Control Tower\.

 **`In Violation`** – Denotes that resources are actively breaching a compliance rule\.
+ **Applies to:** Detective guardrails \(AWS Config Rules\)
+ **Reported for:** A guardrail across multiple accounts

 **`Enforced`** – Maximum level of protection\. Operations that would break this compliance rule are simply not allowed\. 
+ **Applies to:** Preventive guardrails \(SCPs\)
+ **Reported for:** A guardrail across multiple accounts

 **`Clear`** – Compliance rules are properly in place\. No violations have been detected\. 
+ **Applies to:** Detective guardrails \(AWS Config Rules\)
+ **Reported for:** A guardrail across multiple accounts

 **`Compliant`** – Compliance rules are properly in place\. No violations have been detected\.
+ **Applies to:** Detective guardrails \(AWS Config Rules\)
+ 

**Reported for:**
  + A guardrail for a single account
  + An account across multiple guardrails
  + An OU across multiple accounts 

 **`Non-Compliant`** – Compliance rules are properly in place\. However, non\-compliant resources have been detected\.
+ **Applies to:** Detective guardrails \(AWS Config Rules\)
+ 

**Reported for:**
  + A guardrail for a single account
  + An account across multiple guardrails
  + A OU across multiple accounts

 **`Unknown Status`** – A compliance rule is broken or compliance cannot be guaranteed\.
+ 

**Applies to:**
  + Detective Guardrails \(AWS Config Rules\)
  + Preventive guardrails \(SCPs\)
+ 

**Reported for:**
  + A guardrail across multiple accounts
  + A guardrail for a single account
  + An account across multiple guardrails
  + A OU across multiple accounts
  + Basically anything with a compliance status