# What is Compliance?<a name="compliance"></a>

With AWS Control Tower, compliance means that cloud administrators know the accounts in their organization are compliant with established policies, while builders can provision new AWS accounts quickly in a few clicks\.

**Examples of compliance rules \(guardrails\) in AWS Control Tower:**
+ Disallow public read access to S3 buckets
+  Disallow internet connection through RDP

**Examples of governmental compliance regulations:**
+ The U\.S\. Health Insurance Portability and Accountability Act of 1996 \(HIPAA\)
+ The European Union’s General Data Protection Regulation of 2016 \(GDPR\)

For more information about how AWS Control Tower helps you maintain compliance with governmental regulations and industry standards, see [Compliance Validation](https://docs.aws.amazon.com/controltower/latest/userguide/compliance-program-info.html)\.

 **How can administrators review compliance?** 

For ongoing governance, administrators can enable pre\-configured guardrails—clearly defined rules for security, operations, and compliance—that prevent deployment of resources that don’t conform to policies, and they can continuously monitor deployed resources for nonconformance\.

Compliance status of resources associated with OUs and accounts are shown on the **Organizational unit details** page and **Account details** page, respectively, in the AWS Control Tower console\.

You can subscribe to SNS topics that send notifications when resource compliance status changes\. See [Prevention and notification](#prevention-and-notification), later in this chapter\.

For more information on how AWS Control Tower collects information about resources, see the [AWS Config Aggregator Documentation](https://docs.aws.amazon.com/config/latest/developerguide/aggregate-data.html)\.

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

## Prevention and notification<a name="prevention-and-notification"></a>

You can enable certain guardrails and subscribe to certain SNS notifications that help you maintain compliance in AWS Control Tower\.

**Drift prevention** 

Some guardrails prevent modification of compliance reporting mechanisms\.
+ [Disallow Changes to AWS Config Rules Set Up by AWS Control Tower](mandatory-guardrails.md#config-rule-disallow-changes) 

  \(Mandatory, preventive guardrail\)
+ [Disallow Deletion of AWS Config Aggregation Authorization](mandatory-guardrails.md#config-aggregation-authorization-policy)

  \(Mandatory, preventive guardrail\)
+ [Disallow Changes to AWS Config Aggregation Set Up by AWS Control Tower](mandatory-guardrails.md#cloudwatch-disallow-config-changes)

  \(Mandatory, preventive guardrail\)
+ [Disallow Configuration Changes to AWS Config](mandatory-guardrails.md#config-disallow-changes) 

  \(Mandatory, preventive guardrail\)

 **Receive guardrail compliance notifications** 

Subscribe to topic `arn:aws:sns:homeRegion:AuditAccount:aws-controltower-AggregateSecurityNotifications` to receive compliance change notifications in email sent to your audit account\.

Substitute your actual AWS Control Tower home region and audit account information into the topic name shown\.

**Additional SNS topics and notifications you can receive**
+ `aws-controltower-SecurityNotifications`: One of these topics exists for each supported AWS Region\. It receives compliance, noncompliance, and change notifications from AWS Config in that Region\. It forwards all incoming notifications to `aws-controltower-AggregateSecurityNotifications`
+ `aws-controltower-AggregateSecurityNotifications`: This topic exists only in the home AWS Region\. It receives AWS Config notifications from the region\-specific `aws-controltower-SecurityNotifications` topics, and drift notifications generated by AWS Control Tower\.
+ `aws-controltower-AllConfigNotifications`: It receives notifications from AWS Config regarding compliance, noncompliance, and change\.

**Other considerations about SNS topics:**
+ All of these topics exist and receive notifications in the audit account\.
+  By default, only the audit account email address is subscribed to these notifications\.
+ SNS topics in AWS Control Tower are extremely noisy, by design\. For example, AWS Config sends a notification every time AWS Config discovers a new resource\.
+ Administrators who wish to filter out specific types of notifications from an SNS topic can create a Lambda function and subscribe it to the SNS topic\.
+ AWS Config notifications contain a JSON object\.
+ AWS Control Tower drift notifications appear in plain text\.