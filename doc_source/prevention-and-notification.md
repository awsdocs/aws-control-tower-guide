# Drift prevention and notification<a name="prevention-and-notification"></a>

You can enable certain guardrails and subscribe to certain SNS notifications that help you maintain compliance in AWS Control Tower\.

**Drift monitoring protection**

AWS Control Tower provides passive and active methods of drift monitoring protection for preventive guardrails\.
+ **Passive protection:** AWS CloudTrail monitors and logs preventive guardrail \(SCP\) drift\.
+ **Active protection:** The AWS Control Tower [drift monitoring service](drift.md#scp-invariance-scans) actively scans the preventive guardrail SCPs, on a regular basis\.

AWS Control Tower notifies you by means of SNS messaging, if drift is detected\.

**Drift prevention** 

Some guardrails prevent modification of compliance reporting mechanisms\.
+ [Disallow Changes to AWS Config Rules Set Up by AWS Control Tower](mandatory-guardrails.md#config-rule-disallow-changes) \(Mandatory, preventive guardrail\)
+ [Disallow Deletion of AWS Config Aggregation Authorizations Created by AWS Control Tower](mandatory-guardrails.md#config-aggregation-authorization-policy) \(Mandatory, preventive guardrail\)
+ [Disallow Changes to Tags Created by AWS Control Tower for AWS Config Resources](mandatory-guardrails.md#cloudwatch-disallow-config-changes) \(Mandatory, preventive guardrail\)
+ [Disallow Configuration Changes to AWS Config](mandatory-guardrails.md#config-disallow-changes) \(Mandatory, preventive guardrail\)

In contrast to preventive guardrails, detective guardrails notify you of resources that violate the associated AWS Config rule\.

**To receive SNS notifications about drift and guardrail compliance**

For information about how to receive appropriate drift and guardrail compliance notifications by Amazon SNS, see [Compliance notifications by SNS in the audit account](receive-notifications.md)\.

## Publishers and subscribers for SNS topics<a name="publishers-and-subscribers"></a>

**The `aws-controltower-AllConfigNotifications` topic:**
+ The `AWS::Config::DeliveryChannel` resource is configured to send notifications about configuration changes to this topic\.
+ The possible types of notifications that AWS Config can send are defined in the [**Amazon SNS Topic** section](https://docs.aws.amazon.com/config/latest/developerguide/how-does-config-work.html#delivery-channel) of the AWS Config documentation\.
+ The `AWS::CloudTrail::Trail` resource is configured to send notifications of log file delivery to this topic\.
+ You may subscribe to this topic\.

**The `aws-controltower-SecurityNotifications` topic:**
+ The `AWS::Events::Rule` resource is configured to send notifications about AWS Config Rule compliance changes \(one of the SNS notification types\) to this topic\.
+ The `aws-controltower-NotificationForwarder` Lambda function is subscribed to this topic, and it forwards the SNS notifications to the `aws-controltower-AggregateSecurityNotifications` topic\.

**The `aws-controltower-AggregateSecurityNotifications` topic:**
+ This topic receives notifications from `aws-controltower-SecurityNotifications`, forwarded by the Lambda function\.
+ It also receives drift notifications in the home Region\.
+ When AWS Control Tower creates the topic, a subscription is added for the audit account email address, and you must confirm the subscription\.

**Note**  
The endpoint, such as an email address, must confirm each subscription, SNS doesn’t send messages to an endpoint until the subscription is confirmed\.