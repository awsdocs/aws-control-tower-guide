# Logging and Monitoring in AWS Control Tower<a name="logging-and-monitoring"></a>

Monitoring is an important part of the well\-architected nature of AWS Control Tower\. When you set up your landing zone, cross\-account monitoring is set up as well\. Of the shared accounts created, one is the log archive account, dedicated to centrally collecting all logs across all of your other accounts\. The health and status of your guardrails are monitored constantly and you can see them at\-a\-glance in the AWS Control Tower console\. This is also true for the health and status of the accounts you provisioned in Account Factory\.

You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multi\-point failure if one occurs\. AWS provides several tools for monitoring your resources and activity in your landing zone, allowing you to plan for and respond to potential incidents\.

For more information, see [Monitoring AWS Control Tower](monitoring-overview.md)\.