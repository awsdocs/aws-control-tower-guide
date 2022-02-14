# Guardrails and compliance<a name="compliance"></a>

Within AWS Control Tower, compliance means that cloud administrators know when the accounts in their organization are compliant with established policies, while builders can provision new AWS accounts quickly in a few clicks\. AWS Control Tower guardrails embody the rules of compliance, so you can identify compliant and non\-compliant resources\. This page describes guardrail compliance status in detail\.

When we talk about compliance in AWS Control Tower, we do not intend the same meaning as compliance with governmental regulations, such as data privacy or health information standards\. However, AWS Control Tower can assist your organization to comply with many governmental regulations\.

For more information about how AWS Control Tower helps you maintain compliance with governmental regulations and industry standards, see [Compliance Validation](https://docs.aws.amazon.com/controltower/latest/userguide/compliance-program-info.html)\.

**Examples of compliance rules \(guardrails\) in AWS Control Tower:**
+ [Detect Whether Public Write Access to Amazon S3 Buckets is Allowed](strongly-recommended-guardrails.md#s3-disallow-public-write)
+ [Detect Whether Unrestricted Incoming TCP Traffic is Allowed](strongly-recommended-guardrails.md#rdp-disallow-internet)

**Examples of governmental compliance regulations:**
+ The U\.S\. Health Insurance Portability and Accountability Act of 1996 \(HIPAA\)
+ The European Union’s General Data Protection Regulation of 2016 \(GDPR\)

For ongoing governance, administrators can enable pre\-configured guardrails—clearly defined rules for security, operations, and compliance\. These guardrails can:
+ prevent deployment of resources that don’t conform to policies \(by means of preventive guardrails, implemented with SCPs\)
+ continuously monitor deployed resources for nonconformance \(by means of detective guardrails, implemented with AWS Config Rules\)

## How can administrators review compliance?<a name="review-compliance"></a>

Compliance with detective guardrails is determined according to data retrieved from the AWS Config aggregator in the AWS Control Tower Audit account\.

**Detective guardrail status**

To view the compliance status of detective guardrails, navigate to the **Enrolled accounts** table in the AWS Control Tower console\. Accounts may show a compliance status of **Unknown** if any detective guardrails are misconfigured, which occurs most often due to **Moved account** drift\.

**Preventive guardrail status**

The compliance status of preventive guardrails on an OU may be viewed on the **OU** page\. If any preventive guardrails are misconfigured for an OU, the **State** field shows **Registration failed** status\. Preventive guardrail misconfiguration is caused most often by SCP drift, which can occur if the guardrail's SCP is modified or detached from the OU by means of the AWS Organizations console\.

The status also can be viewed in the organizational units table, on any page that has an organizational units table:
+ the AWS Control Tower dashboard
+ the OUs list
+ the guardrail details page

**Nested OUs and compliance**

When an OU shows a status of **Noncompliant**, it means that one of the accounts directly under the OU contains noncompliant resources\. The compliance status of an OU is not influenced by the compliance status of nested OUs under the OU, or the compliance status of any accounts that are not directly under the OU\.

**Other resources**

If an account has any non\-compliant resources, that account may be shown with **Noncompliant** status on the **OU** or **Account** page in the AWS Control Tower console\. Details about the specific resources that have caused the non\-compliant status are shown on the **Account details** page\.

If an account shows **Compliant** status, that means it has no resources that are non\-compliant; therefore, no resource details are shown on the **Account details** page, only an empty table\.

**Receive compliance status updates**

To receive updates about compliance, you can subscribe to SNS topics that send notifications when resource compliance status changes\. See [Guardrail compliance notifications by SNS](receive-notifications.md), later in this chapter\.

For more information on how AWS Control Tower collects information about resources, see the [AWS Config Aggregator Documentation](https://docs.aws.amazon.com/config/latest/developerguide/aggregate-data.html)\.

**Drift changes the compliance status for OU and account resources**  
Drifted resources may be shown with status **Unknown** in the **Compliance** status field of the AWS Control Tower console\. The **Unknown** state indicates that AWS Control Tower cannot determine the compliance status of the resource, because drift is present\. Drift is not necessarily a detective guardrail compliance violation\. For more information about drift, see [Detect and resolve drift in AWS Control Tower](drift.md)\.

## AWS Control Tower guardrail compliance status<a name="compliance-statuses"></a>

This section lists the possible categories of compliance and non\-compliance in AWS Control Tower\.

 **`In violation`** – Denotes that resources are actively breaching a compliance rule\.
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

 **`Noncompliant`** – Compliance rules are properly in place\. However, non\-compliant resources have been detected\.
+ **Applies to:** Detective guardrails \(AWS Config Rules\)
+ 

**Reported for:**
  + A guardrail for a single account
  + An account across multiple guardrails
  + A OU across multiple accounts

 **`Unknown`** – A compliance rule is broken or compliance cannot be guaranteed\.
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