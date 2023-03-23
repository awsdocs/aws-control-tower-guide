# Optional controls<a name="optional-controls"></a>

Optional controls in AWS Control Tower are applied at the OU level\. You can activate and deactivate these optional controls through the AWS Control Tower console, or by means of the [control APIs](https://docs.aws.amazon.com/controltower/latest/APIReference/Welcome.html)\.

**AWS Control Tower offers several types of optional controls:**
+ [Proactive controls](proactive-controls.md), which are based on AWS CloudFormation hooks\.
+ Security Hub controls, which are based on AWS Config rules – these controls are owned by Security Hub and integrated with AWS Control Tower, by means of the **Service\-Managed Standard: AWS Control Tower**\.
+ Data residency controls, which are elective controls based on SCPs and AWS Config rules, implemented within AWS Control Tower\.
+ Strongly recommended controls, which are based on SCPs and AWS Config rules, implemented within AWS Control Tower\.
+ Elective controls, which are based on SCPs and AWS Config rules, implemented within AWS Control Tower\.

The strongly recommended and elective controls owned by AWS Control Tower are optional, which means that you can customize the level of enforcement for OUs in your landing zone by choosing which ones to enable\. Optional controls are not enabled by default\. For more information about optional controls, see the following control reference pages: