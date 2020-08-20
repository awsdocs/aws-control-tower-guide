# Logging and monitoring in AWS Control Tower<a name="logging-and-monitoring"></a>

Monitoring allows you to plan for and respond to potential incidents\. Therefore, monitoring is an important part of the well\-architected nature of AWS Control Tower\. The results of monitoring activities are stored in log files; therefore, logging and monitoring are closely related concepts\.

When you set up your landing zone, one of the shared accounts created is the *log archive* account, dedicated to collecting all logs centrally, including logs for all of your other accounts\. These log files allow administrators and auditors to review actions and events that have occurred\. 

As a best practice, you should collect monitoring data from all of the parts of your AWS solution into your logs, so that you can more easily debug a multi\-point failure if one occurs\. AWS provides several tools for monitoring your resources and activity in your landing zone\.

For example, the status of your guardrails is monitored constantly\. You can see their status at a glance in the AWS Control Tower console\. The health and status of the accounts you provisioned in Account Factory also is monitored constantly\.

**Logging**

Logging of actions and events in AWS Control Tower is accomplished automatically through its integration with CloudWatch\. All actions are logged, including actions from the AWS Control Tower master accounts and from your organization's member accounts\. Master account actions and events are viewable on the **Activities** page in the console\. Member account actions and events are viewable in log archive files\.

**The **Activities** Page**

The **Activities** page provides an overview of AWS Control Tower master account actions\. To navigate to the AWS Control Tower **Activities** page, select **Activities** from the left navigation\.

The **Activities** page shows all AWS Control Tower actions initiated from the master account\. It includes actions that are logged automatically when you navigate through the AWS Control Tower console\. Here are the fields that the **Activities** page shows you: 
+ Date and time: The timestamp for the activity\.
+ User: The person or account that initiated the activity\.
+ Action: The activity that occurred\.
+ Resources: The resources affected by the activity\.
+ Status: Success, failure, or other state of the activity\.
+ Description: More details about the activity\.

The activities shown in the **Activities** page are the same ones reported in the AWS CloudTrail events log for AWS Control Tower, but they're shown in a table format\. To learn more about a specific activity, select the activity from the table and then choose **View details**\.

The following sections describe monitoring and logging in AWS Control Tower with more detail:

**Topics**
+ [Monitoring ](monitoring-overview.md)
+  [Logging AWS Control Tower Actions with AWS CloudTrail](logging-using-cloudtrail.md)
+ [Lifecycle Events in AWS Control Tower](lifecycle-events.md)