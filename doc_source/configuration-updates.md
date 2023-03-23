# Configuration update management in AWS Control Tower<a name="configuration-updates"></a>

It is the responsibility of the members of your central cloud administrators' team to keep your landing zone updated\. Updating your landing zone ensures that AWS Control Tower is patched and updated\. In addition, to protect your landing zone from potential compliance issues, the members of the central cloud administrator team should resolve drift issues as soon as they're detected and reported\.

**Note**  
 The AWS Control Tower console indicates when your landing zone needs to be updated\. If you don't see an option to update, your landing zone is already up to date\.

The following table contains a list of AWS Control Tower landing zone update releases, with links to descriptions of each release\.


| **Version** | Release date | Description | 
| --- | --- | --- | 
| 3\.1 | 2\-09\-2023 | [Landing zone version 3\.1](2023-all.md#lz-3-1) | 
| 3\.0 | 7\-26\-2022 | [Landing zone version 3\.0](2022-all.md#version-3.0) | 
| 2\.9 | 4\-22\-2022 | [Landing zone version 2\.9](2022-all.md#version-2.9) | 
| 2\.8 | 2\-10\-2022 | [Landing zone version 2\.8](2022-all.md#version-2.8) | 
| 2\.7 | 4\-8\-2021 | [Landing zone version 2\.7](https://docs.aws.amazon.com/controltower/latest/userguide/2021-all.html#version-2.7) | 
| 2\.6 | 12\-29\-2020 | [Landing zone version 2\.6](https://docs.aws.amazon.com/controltower/latest/userguide/January-June-2020.html#config-aggregator-12-2020) | 
| 2\.5 | 11\-18\-2020 | [Landing zone version 2\.5](https://docs.aws.amazon.com/controltower/latest/userguide/January-June-2020.html#region-expansion-11-19-20)  | 
| 2\.4 | None | None | 
| 2\.3 | 3\-5\-2020 | [Landing zone version 2\.3](https://docs.aws.amazon.com/controltower/latest/userguide/January-June-2020.html#Available_in_Sydney) | 
| 2\.2 | 11\-13\-19 | [Landing zone version 2\.2](https://docs.aws.amazon.com/controltower/latest/userguide/January-December-2019.html#Version-2-2)  | 
| 2\.1 | 6\-24\-19 | [Landing zone version 2\.1](https://docs.aws.amazon.com/controltower/latest/userguide/January-December-2019.html#Version-2-1) | 

Each time you update your landing zone, you have the opportunity to modify your landing zone settings\.

**Benefits of updating**
+ You can change your governed Regions
+ You can change your log retention policy
+ You can add or remove the Region deny control
+ You can apply AWS KMS encryption keys
+ You can activate or deactivate your organization\-level CloudTrail trail\.
+ You can resolve [landing zone drift](governance-drift.md)

When you update your landing zone, you receive the latest features for AWS Control Tower, automatically\. View your current landing zone version on the **Landing zone settings** page\.

You have the opportunity to clear unused AWS Identity center \(formerly called AWS SSO\) mappings when you update your landing zone\. For more information, see [Field Notes: Clear Unused IAM Identity Center Mappings Automatically During AWS Control Tower Upgrades](http://aws.amazon.com/blogs/architecture/field-notes-clear-unused-aws-sso-mappings-automatically-during-aws-control-tower-upgrades/)\.

**Prerequisite for Update and Repair – turn off Requester Pays**  
Before you update or repair your landing zone, be sure that the Amazon S3 logging bucket for the Log Archive account does not have the **Requester Pays** feature enabled\. You must turn off that feature before you begin the **Update** or **Repair** process\. When AWS Control Tower sets up your logging bucket, this feature is not enabled\. Therefore, only the customers who have subesquently activated the Requester Pays feature must turn it off\. For more information, see [Amazon S3 bucket policy for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-s3-bucket-policy-for-cloudtrail.html) and [Using Requester Pays buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/RequesterPaysBuckets.html)\.