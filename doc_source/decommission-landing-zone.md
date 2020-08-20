# Walkthrough: Decommission a landing zone<a name="decommission-landing-zone"></a>

AWS Control Tower allows you to set up and govern secure multi\-account AWS environments, known as landing zones\. The process of cleaning up all of the resources allocated by AWS Control Tower is referred to as *decommissioning* a landing zone\. 

To decommission your landing zone with a process that is mostly automated, contact [AWS Support](https://aws.amazon.com/premiumsupport/), and they will assist you with further steps you need to follow\. For a complete list of actions performed during decommissioning, see [Overview of the decommissioning process](decommissioning-process-overview.md)\.

**Warning**  
If you intend to decommission your landing zone, contact AWS Support first, before manually deleting resources\. Manually deleting resources will not allow you to set up a new landing zone\. It is not the same as decommissioning\. Complete decommissioning requires assistance from AWS Support\.

 Your data and your existing AWS Organizations are not changed by the decommissioning process, in the following ways\.
+ AWS Control Tower does not remove your data, it only removes parts of the landing zone that it created\.
+ After the decommissioning process is complete, a few resource artifacts remain, such as S3 buckets and Amazon CloudWatch Logs log groups\. These resources must be deleted manually before you set up another landing zone, and to avoid possible costs associated with maintaining certain resources\.
+ You can’t use automated decommissioning to remove a landing zone that’s partially set up\. If your landing zone setup process fails, you must resolve the failure state and set it up all the way to make automated decommissioning possible, or you must manually delete the resources individually\.

*Decommissioning a landing zone is a process with significant consequences, and it cannot be undone\.* The decommissioning actions taken by AWS Control Tower and the artifacts that remain after decommissioning are described in the following sections\.

**Important**  
 We strongly recommend that you perform this decommissioning process only if you intend to stop using your landing zone\. It is not possible to re\-create your existing landing zone after you've decommissioned it\.

## <a name="manual-cleanup-required"></a>

**Manual Cleanup tasks required after decommissioning**
+ You must specify different email addresses for the logging and audit accounts if you create a new landing zone after decommissioning one\.
+ The CloudWatch Logs log group, `aws-controltower/CloudTrailLogs`, must be deleted manually, or renamed, before you set up another landing zone\.
+ The two S3 buckets with reserved names for logs must be removed, or renamed, manually\.
+ You must delete, or rename, the existing **Core** and **Custom** organizations manually\.
**Note**  
Before you can delete the AWS Control Tower **Core OU** organization, you must first delete the logging and audit accounts, but not the master account\. To delete these accounts, you must [Sign in as a Root User](best-practices.md#root-login) to the audit account and to the logging account and delete them individually\. 
+  You may wish to delete the AWS Single Sign\-On \(AWS SSO\) configuration for AWS Control Tower manually, but you can proceed with the existing AWS SSO configuration\.
+ You may wish to remove the VPC created by AWS Control Tower, and remove the associated AWS CloudFormation stack set\.