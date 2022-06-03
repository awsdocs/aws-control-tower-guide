# Administrative tips for landing zone setup<a name="tips-for-admin-setup"></a>
+ The AWS Region where you do the most work should be your home Region\.
+ Set up your landing zone and deploy your Account Factory accounts from within your home Region\.
+ If you’re investing in several AWS Regions, be sure that your cloud resources are in the Region where you’ll do most of your cloud administrative work and run your workloads\.
+ By keeping your workloads and logs in the same AWS Region, you reduce the cost that would be associated with moving and retrieving log information across regions\.
+ The audit and other Amazon S3 buckets are created in the same AWS Region from which you launch AWS Control Tower\. We recommend that you do not move these buckets\.
+ You can make your own log buckets in the Log Archive account, but it is not recommended\. Be sure to leave the buckets created by AWS Control Tower\.
+ Your Amazon S3 access logs must be in the same AWS Region as the source buckets\. 
+ When launching, AWS Security Token Service \(STS\) endpoints must be activated in the management account, for all Regions supported by AWS Control Tower\. Otherwise, the launch may fail midway through the configuration process\.
+ *Do not add tags* to resources in your AWS Control Tower landing zone, because setup will fail\. AWS Control Tower does not support tagging\.
+ We recommend enabling multi\-factor authentication \(MFA\) for every account that AWS Control Tower manages\.

**Considerations about VPCs**
+ The VPC created by AWS Control Tower is limited to the AWS Regions in which AWS Control Tower is available\. Some customers whose workloads run in non\-supported Regions may want to disable the VPC that is created with your Account Factory account\. They may prefer to create a new VPC using the AWS Service Catalog portfolio, or to create a custom VPC that runs in only the required Regions\.
+ The VPC created by AWS Control Tower is not the same as the default VPC that is created for all AWS accounts\. In Regions where AWS Control Tower is supported, AWS Control Tower deletes the default AWS VPC when it creates the AWS Control Tower VPC\.
+  If you delete your default VPC in your home AWS Region, it's best to delete it in all other AWS Regions\. 