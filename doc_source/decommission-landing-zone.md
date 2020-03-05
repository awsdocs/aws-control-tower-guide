# Decommissioning a Landing Zone<a name="decommission-landing-zone"></a>

AWS Control Tower allows you to set up and govern secure multi\-account AWS environments, known as landing zones\. The process of cleaning up all of the resources allocated by AWS Control Tower is referred to as *decommissioning* a landing zone\. 

To decommission your landing zone with a process that is mostly automated, contact [AWS Support](https://aws.amazon.com/premiumsupport/), and they will assist you with further steps you need to follow\. For a complete list of actions performed during decommissioning, see [Overview of the Decommissioning Process](decommissioning-process-overview.md)\.

 Your data and your existing AWS Organizations are not changed by the decommissioning process, in the following ways\.
+ AWS Control Tower does not remove your data, it only removes parts of the landing zone that it created\.
+ After the decommissioning process is complete, a few resource artifacts remain, such as S3 buckets and Amazon CloudWatch Logs log groups\. These resources must be deleted manually before you set up another landing zone, and to avoid possible costs associated with maintaining certain resources\.

*Decommissioning a landing zone is a process with significant consequences, and it cannot be undone\.* The decommissioning actions taken by AWS Control Tower and the artifacts that remain after decommissioning are described in the following sections\.

**Important**  
 We strongly recommend that you perform this decommissioning process only if you intend to stop using your landing zone\. It is not possible to re\-create your existing landing zone after you've decommissioned it\.

## <a name="manual-cleanup-required"></a>

**Manual Cleanup Tasks Required After Decommissioning**
+ You must specify different email addresses for the logging and audit accounts if you create a new landing zone after decommissioning one\.
+ The CloudWatch Logs log group, `aws-controltower/CloudTrailLogs`, must be deleted manually, or renamed, before you set up another landing zone\.
+ The two S3 buckets with reserved names for logs must be removed, or renamed, manually\.
+ You must delete, or rename, the existing **Core** and **Custom** organizations manually\.
+  You may wish to delete the AWS Single Sign\-On \(AWS SSO\) configuration for AWS Control Tower manually, but you can proceed with the existing AWS SSO configuration\.