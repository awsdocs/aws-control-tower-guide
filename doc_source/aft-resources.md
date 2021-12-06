# Resource considerations for AWS Control Tower Account Factory for Terraform<a name="aft-resources"></a>

When you set up your landing zone using AWS Control Tower Account Factory for Terraform, several types of AWS resources are created within your AWS accounts\.

**Search for resources**
+ You can use tags to search for the most updated list of AFT resources\. The key\-value pair for your search is:

  ```
  Key: managed_by | Value: AFT
  ```
+ For component services that do not support tags, you can locate resources with a search for `aft` in the resource names\.

**Tables of resources initially created, by account**


**AWS Control Tower Account Factory for Terraform management account**  

| **AWS service** | **Resource type** | **Resource name** | 
| --- | --- | --- | 
| AWS Identity and Access Management | Roles |  AWSAFTAdministratorRole AWSAFTExecutionRole aws\-ct\-aft\-\* | 
| AWS Identity and Access Management | Policies | aws\-ct\-aft\-\* | 
| CodeCommit | Repositories | aws\-ct\-aft\-\* | 
| CodeBuild | Build Projects | aws\-ct\-aft\-\* | 
| Code Pipeline | Pipelines | \*\-baseline\-\* | 
| Amazon S3 | Buckets | \*\-aws\-ct\-aft\-\* aws\-ct\-aft\-\* | 
| Lambda | Functions | aws\-ct\-aft\-\* | 
| Lambda | Layers | aws\-ct\-aft\-common\-layer | 
| DynamoDB | Tables | aws\-ct\-aft\-request aws\-ct\-aft\-request\-audit aws\-ct\-aft\-request\-metadata aws\-ct\-aft\-controltower\-events | 
| Step Functions | State Machines | aws\-ct\-aft\-prebaseline aws\-ct\-aft\-prebaseline\-customizations aws\-ct\-aft\-trigger\-baseline aws\-ct\-aft\-features | 
| VPC | VPC | aws\-ct\-aft\-vpc | 
| Amazon SNS | Topics | aws\-ct\-aft\-notifications aws\-ct\-aft\-failure\-notifications | 
| Amazon EventBridge | Event buses | aws\-ct\-aft\-events\-from\-ct\-management | 
| Amazon EventBridge | Event rules | aws\-ct\-aft\-capture\-ct\-events aws\-ct\-aft\-lambda\-account\-request\-processor | 
| Key Management Service \(KMS\) | Customer Managed Keys | \*\-aws\-ct\-aft\-\* aws\-ct\-aft\-\* | 
| AWS Systems Manager | Parameter store | /aws\-ct\-aft/account/\* /aws/ct\-aft/config/\* | 
| Amazon SQS | Queues | aws\-ct\-aft\-account\-request\.fifo aws\-ct\-aft\-account\-request\-dlg\.fifo | 
| CloudWatch | Log groups | /aws/\*/aws\-ct\-aft\-\* aws\-ct\-aft\-\* | 
| AWS Support Center \(Optional\) | Support plans | Enterprise | 


**AWS accounts provisioned through AWS Control Tower Account Factory for Terraform**  

| **AWS service** | **Resource type** | **Resource name** | 
| --- | --- | --- | 
| AWS Identity and Access Management | Roles | AWSAFTExecutionRole | 
| AWS Support Center \(Optional\) | Support plans | Enterprise | 


**AWS Control Tower management account**  

| **AWS service** | **Resource type** | **Resource name** | 
| --- | --- | --- | 
| AWS Identity and Access Management | Roles | AWSAFTExecutionRole aws\-ct\-aft\-controltower\-events\-rule | 
| AWS Systems Manager | Parameter store | /aws\-ct\-aft/account/aws\-ct\-aft\-management/account\-id | 
| AWS Organizations \(Optional\) | Service Control Policies | aws\-ct\-aft\-protect\-resources | 
| CloudTrail \(Optional\) | Trails | aws\-ct\-aft\-BaselineCloudTrail | 
| AWS Support Center \(Optional\) | Support plans | Enterprise | 


**AWS Control Tower log archive account**  

| **AWS service** | **Resource type** | **Resource name** | 
| --- | --- | --- | 
| AWS Identity and Access Management | Roles | AWSAFTExecutionRole aws\-ct\-aft\-cloudtrail\-data\-events\-role | 
| Key Management Service \(KMS\) | Customer Managed Keys | \*\-aws\-ct\-aft\-kms\-gd\-findings | 
| Amazon S3 | Buckets | \*\-aws\-ct\-aft\-logs\* aws\-ct\-aft\-s3\-access\-logs\* | 
| AWS Support Center \(Optional\) | Support plans | Enterprise | 


**AWS Control Tower audit account**  

| **AWS service** | **Resource type** | **Resource name** | 
| --- | --- | --- | 
| AWS Identity and Access Management | Roles | AWSAFTExecutionRole | 
| Amazon S3 | Buckets | \*\-aws\-ct\-aft\-logs\-\* aws\-ct\-aft\-s3\-access\-logs\* | 
| AWS Support Center \(Optional\) | Support plans | Enterprise | 