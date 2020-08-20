# Tracking Alerts Through Amazon Simple Notification Service<a name="sns"></a>

Amazon Simple Notification Service \(Amazon SNS\) is a web service that enables applications, end\-users, and devices to send and receive notifications instantly from the cloud\. For more information, see *[Amazon Simple Notification Service Developer Guide](https://docs.aws.amazon.com/sns/latest/dg/)*\.

AWS Control Tower uses Amazon SNS to send programmatic alerts to the email addresses of your master account and your audit account\. These alerts help you prevent drift within your landing zone\. For more information, see [Detect and resolve drift in AWS Control Tower](drift.md)\. 

We also use Amazon Simple Notification Service to send compliance notifications from AWS Config\.

**Tip**  
One of the best ways to receive AWS Control Tower guardrail compliance notifications \(in your audit account\) is to subcribe to `AggregateConfigurationNotifications`\. It is a service that helps you inspect compliance\. It gives you real data about AWS Config rules going out of compliance\. AWS Config automatically maintains the list of accounts in your OU\.  
You must subscribe manually, using email or any type of subscription that SNS allows\. The statement `arn:aws:sns:homeregion:account:aws-controltower-AggregateSecurityNotifications` leads to your audit account\. 