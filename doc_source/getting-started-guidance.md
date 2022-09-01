# Guidance for creating and modifying AWS Control Tower resources<a name="getting-started-guidance"></a>

We recommend the following practices as you create and modify resources in AWS Control Tower\. This guidance might change as the service is updated\.

**General Guidance**
+ Do not modify or delete resources created by AWS Control Tower in the management account or in the shared accounts\. Modification of these resources can require you to update your landing zone or re\-register an OU\.
+ Do not modify or delete the AWS Identity and Access Management \(IAM\) roles created within the shared accounts in the Security organizational unit \(OU\)\. Modification of these roles can require an update to your landing zone\.
+ For more information about the resources created by AWS Control Tower, see [What Are the Shared Accounts?](how-control-tower-works.md#what-shared)
+ Do not disallow usage of any AWS Regions through either SCPs or AWS Security Token Service \(STS\)\. Doing so will cause AWS Control Tower to enter an undefined state\. If you disallow Regions with AWS STS, your functionality will fail in those Regions, because authentication would be unavailable in those Regions\. Instead, rely on the AWS Control Tower Region deny capability, as shown in the guardrail, [Deny access to AWS based on the requested AWS Region](data-residency-guardrails.md#primary-region-deny-policy)\.
+ The AWS Organizations **FullAWSAccess** SCP must be applied and should not be merged with other SCPs\. Change to this SCP is not reported as drift; however, some changes may affect AWS Control Tower functionality in unpredictable ways, if access to certain resources is denied\. For example, if the SCP is detached, or modified, an account may lose access to an AWS Config recorder or create a gap in CloudTrail logging\.
+ In general, AWS Control Tower performs a single action at a time, which must be completed before another action can begin\. For example, if you attempt to provision an account while the process of enabling a guardrail is already in operation, account provisioning will fail\.

**Exception:**
  + AWS Control Tower allows concurrent actions to deploy optional preventive and detective guardrails\. See [Concurrent deployment for optional guardrails](enable-guardrails.md#concurrent-optional-guardrails)\.
+  Keep an active AWS Config recorder\. If you delete your Config recorder, detective guardrails cannot detect and report drift\. Non\-compliant resources may be reported as **Compliant** due to insufficient information\. 
+ Do not delete the **AWSControlTowerExecution** role from your member accounts, even in unenrolled accounts\. If you do, you will not be able to enroll these accounts with AWS Control Tower, or register their immediate parent OUs\.

**Tips about accounts and OUs**
+ We recommend that you keep each registered OU to a maximum of 300 accounts, so that you can update those accounts with the **Re\-register OU** capability whenever account updates are required, such as when you configure new Regions for governance\.
+ To reduce the time required when registering an OU, we recommend that you keep the number of accounts per OU to around 150, even though the limit is 300 accounts per OU\. As a general rule, the time required to register an OU increases according to the number of Regions in which your OU is operating, multiplied by the number of accounts in the OU\. 
+ As an estimate, an OU with 150 accounts requires approximately 2 hours to register and enable guardrails, and about 1 hour to re\-register\. Also, an OU that has many guardrails takes longer to register than an OU with few guardrails\.
+ One concern about allowing a longer timeframe for registering an OU is that this process blocks other actions\. Some customers are comfortable allowing longer times to register or re\-register an OU, because they prefer to allow more accounts in each OU\.