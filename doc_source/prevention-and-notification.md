# Drift prevention and notification<a name="prevention-and-notification"></a>

You can enable certain guardrails and subscribe to certain SNS notifications that help you maintain compliance in AWS Control Tower\.

**Drift prevention** 

Some guardrails prevent modification of compliance reporting mechanisms\.
+ [Disallow Changes to AWS Config Rules Set Up by AWS Control Tower](mandatory-guardrails.md#config-rule-disallow-changes) 

  \(Mandatory, preventive guardrail\)
+ [Disallow Deletion of AWS Config Aggregation Authorizations Created by AWS Control Tower](mandatory-guardrails.md#config-aggregation-authorization-policy)

  \(Mandatory, preventive guardrail\)
+ [Disallow Changes to Tags Created by AWS Control Tower for AWS Config Resources](mandatory-guardrails.md#cloudwatch-disallow-config-changes)

  \(Mandatory, preventive guardrail\)
+ [Disallow Configuration Changes to AWS Config](mandatory-guardrails.md#config-disallow-changes) 

  \(Mandatory, preventive guardrail\)

In contrast to preventive guardrails, detective guardrails notify you of resources that violate the associated AWS Config rule\.

For information about how to receive appropriate guardrail compliance notifications by Amazon SNS, see [Guardrail compliance notifications](receive-notifications.md)\.