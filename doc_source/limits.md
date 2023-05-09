# Limitations and quotas in AWS Control Tower<a name="limits"></a>

This chapter covers the AWS service limitations and quotas that you should keep in mind as you use AWS Control Tower\. If you're unable to set up your landing zone due to a service quota issue, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

For more information about limitations that are specific to controls see [Control limitations](#control-limitations)\.

## Limitations in AWS Control Tower<a name="controltower-limits"></a>

This section describes known limitations and unsupported use cases in AWS Control Tower\.
+ AWS Control Tower has overall concurrency limitations\. In general, one operation at a time is permitted\. Two exceptions to this limitation are allowed:
  + Optional controls can be activated and deactivated concurrently, through an asynchronous process\. Up to ten \(10\) control\-related operations at a time can be in progress, in total, no matter if they are called from the console or from an API\.
  + Accounts can be provisioned, updated, and enrolled concurrently in Account Factory, through an asynchronous process, with up to five \(5\) account\-related operations in progress simultaneously\. Unmanaging accounts must be performed one account at a time\.
+ Email addresses of shared accounts in the Security OU can be changed, but you must update your landing zone to see these changes in the AWS Control Tower console\.
+ A limit of five \(5\) SCPs per OU applies to OUs in your AWS Control Tower landing zone\.
+ Existing OUs with over 300 directly nested accounts cannot be registered or re\-registered in AWS Control Tower\.
+ If all 22 commercial Regions where AWS Control Tower is available are activated, including opt\-in Regions, the upper limit on the number of accounts per organizational unit \(OU\), when extending governance to an OU, is reduced\. The limit is 220 accounts, instead of 300\. This reduction is due to StackSet limitations\. If you require to extend governance to OUs with more than 220 accounts, reduce the number of activated Regions\.
+ CfCT is not available in opt\-in Regions\.
+ When calling a control API to activate or deactivate a control, the limit for `EnableControl` and `DisableControl` updates in AWS Control Tower is ten \(10\) concurrent operations\. You may need to adjust your code to wait for completions\.

You can contact [AWS Support](http://aws.amazon.com/premiumsupport/) to request a limit increase for some resources in AWS Control Tower\. For example, you can request a limit increase from five of up to ten concurrent account\-related operations\. Some AWS Control Tower performance characteristics may change after a limit increase\. For example, it may take longer to update an OU when you have more accounts in it\. Or it may take longer to complete an action on OU with five SCPs than with three SCPs\.

**Video: Automate requests for service limit increase**

This video \(7:24\) describes how to automate service limit increases for deployments in AWS Control Tower\. It also shows how to automate enrolllment of new accounts into AWS Enterprise support for your organization\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.

When provisioning new accounts in this environment, you can use lifecycle events to trigger automated requests for service limit increases in specified AWS Regions\. 

## Control limitations<a name="control-limitations"></a>

If you modify AWS Control Tower resources, such as an SCP, or remove any AWS Config resource, such as a Config recorder or aggregator, AWS Control Tower can no longer guarantee that the controls are functioning as designed\. Therefore, the security of your multi\-account environment may be compromised\. The AWS [shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model) of security is applicable to any such changes you may make\.

**Note**  
 AWS Control Tower helps maintain the integrity of your environment by resetting the SCPs of the controls to their standard configuration when you update your landing zone\. Changes that you may have made to SCPs are replaced by the standard version of the control, by design\.

Some controls in AWS Control Tower do not operate in certain AWS Regions where AWS Control Tower is available, because those Regions do not support the required underlying functionality\.  This limitation affects certain detective controls, certain proactive controls, and certain controls in the **Security Hub Service\-managed Standard: AWS Control Tower**\. For more information about Regional availability, see the [Regional services list documentation](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/) and the [Security Hub controls reference documentation](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-controls-reference.html)\.

For more information about how AWS Control Tower manages the limitations of Regions and controls, see [Considerations for activating AWS opt\-in Regions](opt-in-region-considerations.md)\.

You can view the Regions for each control in the AWS Control Tower console\.

**The following opt\-in AWS Regions do not support controls that are part of the Security Hub Service\-managed Standard: AWS Control Tower, and they do not support CfCT\.**
+ Asia Pacific \(Hong Kong\) Region, ap\-east\-1
+ Asia Pacific \(Jakarta\) Region, ap\-southeast\-3
+ Asia Pacific \(Osaka\) Region, ap\-northeast\-3 
+ Europe \(Milan\) Region, eu\-south\-1 
+ Africa \(Cape Town\) Region, af\-south\-1
+ Middle East \(Bahrain\) Region, me\-south\-1 

**The following table shows 16 detective controls that are not supported in certain AWS Regions\. **


| **Control identifier** | **Unsupported regions** | 
| --- | --- | 
| `AWS-GR_AUTOSCALING_LAUNCH_CONFIG_PUBLIC_IP_DISABLED` | ap\-northeast\-3, ap\-southeast\-3 | 
| `AWS-GR_REDSHIFT_CLUSTER_PUBLIC_ACCESS_CHECK` | ap\-northeast\-3, ap\-southeast\-3 | 
| `AWS-GR_LAMBDA_FUNCTION_PUBLIC_ACCESS_PROHIBITED` | ap\-northeast\-3 | 
| `AWS-GR_EMR_MASTER_NO_PUBLIC_IP` | ap\-northeast\-3, ap\-southeast\-3, af\-south\-1, eu\-south\-1 | 
| `AWS-GR_EBS_SNAPSHOT_PUBLIC_RESTORABLE_CHECK` | ap\-northeast\-3, ap\-southeast\-3 | 
| `AWS-GR_NO_UNRESTRICTED_ROUTE_TO_IGW` | ap\-northeast\-3, ap\-southeast\-3 | 
| `AWS-GR_SAGEMAKER_NOTEBOOK_NO_DIRECT_INTERNET_ACCESS` | ap\-northeast\-3, ap\-southeast\-3, af\-south\-1, eu\-south\-1 | 
| `AWS-GR_EC2_INSTANCE_NO_PUBLIC_IP` | ap\-northeast\-3, ap\-southeast\-3 | 
| `AWS-GR_EKS_ENDPOINT_NO_PUBLIC_ACCESS` | ap\-northeast\-3, ap\-southeast\-3, af\-south\-1, eu\-south\-1, us\-west\-1 | 
| `AWS-GR_ELASTICSEARCH_IN_VPC_ONLY` | ap\-northeast\-3, ap\-southeast\-3 | 
| `AWS-GR_RESTRICTED_SSH` | ap\-northeast\-3, ap\-southeast\-3, af\-south\-1, eu\-south\-1 | 
| `AWS-GR_DMS_REPLICATION_NOT_PUBLIC` | ap\-northeast\-3, ap\-southeast\-3, af\-south\-1, eu\-south\-1 | 
| `AWS-GR_RDS_SNAPSHOTS_PUBLIC_PROHIBITED` | ap\-northeast\-3, ap\-southeast\-3, af\-south\-1, eu\-south\-1 | 
| `AWS-GR_SUBNET_AUTO_ASSIGN_PUBLIC_IP_DISABLED` | ap\-northeast\-3, ap\-southeast\-3 | 
| `AWS-GR_ENCRYPTED_VOLUMES` | ap\-northeast\-3, ap\-southeast\-3, af\-south\-1, eu\-south\-1 | 
| `AWS-GR_RESTRICTED_COMMON_PORTS` | ap\-northeast\-3, ap\-southeast\-3, af\-south\-1, eu\-south\-1 | 

More information about AWS quotas is available in the [AWS General Reference](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_config)\.