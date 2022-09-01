# Enable feature options<a name="aft-feature-options"></a>

AFT offers feature options based on best practices\. You can opt\-in to these features, by means of feature flags, during AFT deployment\. Refer to [Provision a new account with AFT](aft-provision-account.md) for more information about AFT input configuration parameters\.

These features are not enabled by default\. You must explicitly enable each one in your environment\.

**Topics**
+ [AWS CloudTrail data events](#cloudtrail-data-event-option)
+ [AWS Enterprise Support plan](#enterprise-support-option)
+ [Delete the AWS default VPC](#delete-default-vpc-option)

## AWS CloudTrail data events<a name="cloudtrail-data-event-option"></a>

When enabled, the AWS CloudTrail data events option configures these capabilities\.
+ Creates an Organization Trail in the AWS Control Tower management account, for CloudTrail
+ Turns on logging for Amazon S3 and Lambda data events
+ Encrypts and exports all the CloudTrail data events to an `aws-aft-logs-*` S3 bucket in the AWS Control Tower Log Archive account, with AWS KMS encryption
+ Turns on the **Log file validation** setting

To enable this option, set the following feature flag to **True** in your AFT deployment input configuration\.

```
aft_feature_cloudtrail_data_events
```

**Prerequisite**

Before you enable this feature option, be sure that trusted access for AWS CloudTrail is enabled in your organization\. 

**To check the status of trusted access for CloudTrail :**

1. Navigate to the AWS Organizations console\.

1. Choose **Services > CloudTrail**\.

1. Then select **Enable trusted access** in the upper right, if needed\.

You may receive a warning message that advises you to use the AWS CloudTrail console, but in this case, disregard the warning\. AFT creates the trail as part of enabling this feature option, after you allow trusted access\. If trusted access is not enabled, you will receive an error message when AFT attempts to create your trail for data events\.

**Note**  
This setting works at the organization level\. Enabling this setting affects all accounts in AWS Organizations, whether they are managed by AFT or not\. All buckets in the AWS Control Tower Log Archive account at the time of enabling are excluded from Amazon S3 data events\. Refer to [the AWS CloudTrail User Guide](https://docs.aws.amazon.com/                 /awscloudtrail/latest/userguide/cloudtrail-user-guide.html) to learn more about CloudTrail\.

## AWS Enterprise Support plan<a name="enterprise-support-option"></a>

When this option is enabled, the AFT pipeline turns on the AWS Enterprise Support plan for accounts provisioned by AFT\.

AWS accounts by default come with the AWS Basic Support plan enabled\. AFT provides automated enrollment into the enterprise support level, for accounts that AFT provisions\. The provisioning process opens a support ticket for the account, requesting it to be added to the AWS Enterprise Support plan\.

To enable the Enterprise Support option, set the following feature flag to **True** in your AFT deployment input configuration\.

```
aft_feature_enterprise_support=false
```

Refer to [Compare AWS Support Plans](http://aws.amazon.com/aws.amazon.com/premiumsupport/plans/) to learn more about AWS Support Plans\.

**Note**  
To allow this feature to operate, you must enroll the payer account into the Enterprise Support plan\.

## Delete the AWS default VPC<a name="delete-default-vpc-option"></a>

When this option is enabled, the AFT pipeline deletes the associated AWS default VPCs for the AFT management account in all AWS Regions, even if you don’t have AWS Control Tower resources deployed in those Regions\.

The AFT pipeline does not delete the AWS default VPCs automatically for any AWS Control Tower accounts that AFT provisions, or for existing AWS accounts that you enroll in AWS Control Tower by means of AFT\.

New AWS accounts are created with a VPC set up in each AWS Region, by default\. Your enterprise may have standard practices for creating VPCs, which require you to delete the AWS default VPC and avoid enabling it, especially for the AFT management account\. 

To enable this option, set the following feature flag to **True** in your AFT deployment input configuration\.

```
aft_feature_delete_default_vpcs_enabled
```

Refer to [Default VPC and default subnets](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) to learn more about default VPCs\.