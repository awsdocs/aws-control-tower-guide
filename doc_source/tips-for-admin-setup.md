# Administrative tips for landing zone setup<a name="tips-for-admin-setup"></a>
+ The AWS Region where you do the most work should be your home Region\.
+ Set up your landing zone and deploy your Account Factory accounts from within your home Region\.
+ If you’re investing in several AWS Regions, be sure that your cloud resources are in the Region where you’ll do most of your cloud administrative work and run your workloads\.
+ The audit and other Amazon S3 buckets are created in the same AWS Region from which you launch AWS Control Tower\. We recommend that you do not move these buckets\.
+ When launching, AWS Security Token Service \(STS\) endpoints must be activated in the management account, for all Regions supported by AWS Control Tower\. Otherwise, the launch may fail midway through the configuration process\.