# Amazon Simple Storage Service \(Amazon S3\) controls<a name="s3-rules"></a>

**Topics**
+ [\[CT\.S3\.PR\.1\] Require an Amazon S3 bucket to have block public access settings configured](#ct-s3-pr-1-description)
+ [\[CT\.S3\.PR\.2\] Require an Amazon S3 bucket to have server access logging configured](#ct-s3-pr-2-description)
+ [\[CT\.S3\.PR\.3\] Require an Amazon S3 buckets to have versioning configured and a lifecycle policy](#ct-s3-pr-3-description)
+ [\[CT\.S3\.PR\.4\] Require an Amazon S3 bucket to have event notifications configured](#ct-s3-pr-4-description)
+ [\[CT\.S3\.PR\.5\] Require that an Amazon S3 bucket does not manage user access with an access control list \(ACL\)](#ct-s3-pr-5-description)
+ [\[CT\.S3\.PR\.6\] Require an Amazon S3 bucket to have lifecycle policies configured](#ct-s3-pr-6-description)
+ [\[CT\.S3\.PR\.7\] Require an Amazon S3 bucket to have server\-side encryption configured](#ct-s3-pr-7-description)
+ [\[CT\.S3\.PR\.8\] Require that Amazon S3 bucket requests use Secure Socket Layer](#ct-s3-pr-8-description)

## \[CT\.S3\.PR\.1\] Require an Amazon S3 bucket to have block public access settings configured<a name="ct-s3-pr-1-description"></a>

This control checks whether your Amazon Simple Storage Service \(Amazon S3\) bucket has a bucket\-level Block Public Access \(BPA\) configuration\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::S3::Bucket`
+ **AWS CloudFormation guard rule: ** [CT\.S3\.PR\.1 rule specification](#ct-s3-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.S3\.PR\.1 rule specification](#ct-s3-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.S3\.PR\.1 example templates](#ct-s3-pr-1-templates) 

**Explanation**

Block Public Access at the Amazon S3 bucket level provides controls to ensure that objects never have public access\. Public access is granted to buckets and objects through access control lists \(ACLs\), bucket policies, or both\.

Unless you intend to have your S3 buckets publicly accessible, you should configure the bucket level Amazon S3 Block Public Access feature\.

**Usage considerations**  
This control is incompatible with Amazon S3 buckets that require a public access configuration\.

### Remediation for rule failure<a name="ct-s3-pr-1-remediation"></a>

The parameters `BlockPublicAcls`, `BlockPublicPolicy`, `IgnorePublicAcls`, `RestrictPublicBuckets` must be set to true under the bucket\-level `PublicAccessBlockConfiguration`\.

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket \- Example<a name="ct-s3-pr-1-remediation-1"></a>

Amazon S3 bucket with a bucket level Block Public Access configuration that ensures objects never have public access\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3Bucket": {
        "Type": "AWS::S3::Bucket",
        "Properties": {
            "PublicAccessBlockConfiguration": {
                "BlockPublicAcls": true,
                "BlockPublicPolicy": true,
                "IgnorePublicAcls": true,
                "RestrictPublicBuckets": true
            }
        }
    }
}
```

**YAML example**

```
S3Bucket:
  Type: AWS::S3::Bucket
  Properties:
    PublicAccessBlockConfiguration:
      BlockPublicAcls: true
      BlockPublicPolicy: true
      IgnorePublicAcls: true
      RestrictPublicBuckets: true
```

### CT\.S3\.PR\.1 rule specification<a name="ct-s3-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   s3_bucket_level_public_access_prohibited_check
# 
# Description:
#   Checks whether Amazon Simple Storage Service (Amazon S3) buckets have a bucket-level Block Public Access (BPA)
#   configuration.
# 
# Reports on:
#   AWS::S3::Bucket
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Amazon S3 bucket resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'PublicAccessBlockConfiguration' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'PublicAccessBlockConfiguration' has been provided
#       And: 'BlockPublicAcls' or 'BlockPublicPolicy' or 'IgnorePublicAcls' or 'RestrictPublicBuckets'
#            have not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket Resource
#       And: 'PublicAccessBlockConfiguration' has been provided
#       And: Any of 'BlockPublicAcls' or 'BlockPublicPolicy' or 'IgnorePublicAcls' or 'RestrictPublicBuckets'
#            have been set to a value other than bool(true) (e.g. bool(false), str(false), other)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket Resource
#       And: 'PublicAccessBlockConfiguration' has been provided
#       And: 'BlockPublicAcls' or 'BlockPublicPolicy' or 'IgnorePublicAcls' or 'RestrictPublicBuckets'
#            have all been set to bool(true)
#      Then: PASS

#
# Constants
#
let S3_BUCKET_TYPE = "AWS::S3::Bucket"
let INPUT_DOCUMENT = this

#
# Assignments
#
let s3_buckets = Resources.*[ Type == %S3_BUCKET_TYPE ]

#
# Primary Rules
#
rule s3_bucket_level_public_access_prohibited_check when is_cfn_template(%INPUT_DOCUMENT)
                                                         %s3_buckets not empty {
    check(%s3_buckets.Properties)
        <<
        [CT.S3.PR.1]: Require an Amazon S3 bucket to have block public access settings configured
        [FIX]: The parameters 'BlockPublicAcls', 'BlockPublicPolicy', 'IgnorePublicAcls', 'RestrictPublicBuckets' must be set to true under the bucket-level 'PublicAccessBlockConfiguration'.
        >>
}

rule s3_bucket_level_public_access_prohibited_check when is_cfn_hook(%INPUT_DOCUMENT, %S3_BUCKET_TYPE) {
    check(%INPUT_DOCUMENT.%S3_BUCKET_TYPE.resourceProperties)
        <<
        [CT.S3.PR.1]: Require an Amazon S3 bucket to have block public access settings configured
        [FIX]: The parameters 'BlockPublicAcls', 'BlockPublicPolicy', 'IgnorePublicAcls', 'RestrictPublicBuckets' must be set to true under the bucket-level 'PublicAccessBlockConfiguration'.
        >>
}

#
# Parameterized Rules
#
rule check(s3_bucket) {
    %s3_bucket {
        # Scenario 2
        PublicAccessBlockConfiguration exists
        PublicAccessBlockConfiguration is_struct

        PublicAccessBlockConfiguration {
            # Scenario 3
            BlockPublicAcls exists
            BlockPublicPolicy exists
            IgnorePublicAcls exists
            RestrictPublicBuckets exists

            # Scenarios 4 and 5
            BlockPublicAcls == true
            BlockPublicPolicy == true
            IgnorePublicAcls == true
            RestrictPublicBuckets == true
        }
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.S3\.PR\.1 example templates<a name="ct-s3-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      PublicAccessBlockConfiguration:
        BlockPublicAcls: false
        BlockPublicPolicy: false
        IgnorePublicAcls: false
        RestrictPublicBuckets: false
```

## \[CT\.S3\.PR\.2\] Require an Amazon S3 bucket to have server access logging configured<a name="ct-s3-pr-2-description"></a>

This control checks whether server access logging is enabled for your Amazon S3 bucket\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::S3::Bucket`
+ **AWS CloudFormation guard rule: ** [CT\.S3\.PR\.2 rule specification](#ct-s3-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.S3\.PR\.2 rule specification](#ct-s3-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.S3\.PR\.2 example templates](#ct-s3-pr-2-templates) 

**Explanation**

Server access logging provides detailed records of requests made to a bucket\. Server access logs can assist in security and access audits\.

### Remediation for rule failure<a name="ct-s3-pr-2-remediation"></a>

Set a `LoggingConfiguration` on the S3 bucket and optionally set `DestinationBucketName` to an S3 bucket configured to receive S3 Access Logs\.

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket \- Example<a name="ct-s3-pr-2-remediation-1"></a>

Amazon S3 bucket with a server access logging configuration\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3Bucket": {
        "Type": "AWS::S3::Bucket",
        "Properties": {
            "LoggingConfiguration": {
                "DestinationBucketName": {
                    "Ref": "LoggingBucket"
                }
            }
        }
    }
}
```

**YAML example**

```
S3Bucket:
  Type: AWS::S3::Bucket
  Properties:
    LoggingConfiguration:
      DestinationBucketName: !Ref 'LoggingBucket'
```

### CT\.S3\.PR\.2 rule specification<a name="ct-s3-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   s3_bucket_logging_enabled_check
# 
# Description:
#   This control checks whether server access logging is enabled for Amazon S3 buckets.
# 
# Reports on:
#   AWS::S3::Bucket
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Amazon S3 bucket resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'LoggingConfiguration' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'LoggingConfiguration' has been provided
#       And: 'LoggingConfiguration.DestinationbucketName' has been provided with an empty string or non-valid local
#            reference
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'LoggingConfiguration' has been provided
#      Then: PASS
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'LoggingConfiguration' has been provided
#       And: 'LoggingConfiguration.DestinationBucketName' has been provided with a non-empty string or valid local
#            reference
#      Then: PASS

#
# Constants
#
let S3_BUCKET_TYPE = "AWS::S3::Bucket"
let INPUT_DOCUMENT = this

#
# Assignments
#
let s3_buckets = Resources.*[ Type == %S3_BUCKET_TYPE ]

#
# Primary Rules
#
rule s3_bucket_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                          %s3_buckets not empty {
    check(%s3_buckets.Properties)
        <<
        [CT.S3.PR.2]: Require an Amazon S3 bucket to have server access logging configured
        [FIX]: Set a 'LoggingConfiguration' on the S3 Bucket and optionally set 'DestinationBucketName' to an S3 bucket configured to receive S3 Access Logs.
        >>
}

rule s3_bucket_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %S3_BUCKET_TYPE) {
    check(%INPUT_DOCUMENT.%S3_BUCKET_TYPE.resourceProperties)
        <<
        [CT.S3.PR.2]: Require an Amazon S3 bucket to have server access logging configured
        [FIX]: Set a 'LoggingConfiguration' on the S3 bucket and optionally set 'DestinationBucketName' to an S3 bucket configured to receive S3 Access Logs.
        >>
}

#
# Parameterized Rules
#
rule check(s3_bucket) {
    %s3_bucket {
        # Scenario 2 and 4
        LoggingConfiguration exists
        LoggingConfiguration is_struct

        LoggingConfiguration {
            when DestinationBucketName exists {
                # Scenario 3, 4 and 5
                check_is_string_and_not_empty(DestinationBucketName) or
                check_local_references(%INPUT_DOCUMENT, DestinationBucketName, %S3_BUCKET_TYPE)
            }
        }
    }
}


#
# Utility Rules
#
rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
}

rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}

rule check_local_references(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        'Fn::GetAtt' {
            query_for_resource(%doc, this[0], %referenced_resource_type)
                <<Local Stack reference was invalid>>
        } or Ref {
            query_for_resource(%doc, this, %referenced_resource_type)
                <<Local Stack reference was invalid>>
        }
    }
}

rule query_for_resource(doc, resource_key, referenced_resource_type) {
    let referenced_resource = %doc.Resources[ keys == %resource_key ]
    %referenced_resource not empty
    %referenced_resource {
        Type == %referenced_resource_type
    }
}
```

### CT\.S3\.PR\.2 example templates<a name="ct-s3-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties:
      LoggingConfiguration: {}
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties: {}
```

## \[CT\.S3\.PR\.3\] Require an Amazon S3 buckets to have versioning configured and a lifecycle policy<a name="ct-s3-pr-3-description"></a>

This control checks whether your Amazon Simple Storage Service \(Amazon S3\) version\-enabled bucket has a lifecycle policy configured\.
+ **Control objective: **Optimize costs
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::S3::Bucket`
+ **AWS CloudFormation guard rule: ** [CT\.S3\.PR\.3 rule specification](#ct-s3-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.S3\.PR\.3 rule specification](#ct-s3-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.S3\.PR\.3 example templates](#ct-s3-pr-3-templates) 

**Explanation**

We recommend that you configure lifecycle rules on your Amazon S3 bucket, because these rules help you define actions that you want Amazon S3 to take during an object's lifetime\.

**Usage considerations**  
This control applies only to Amazon S3 buckets with versioning enabled\.

### Remediation for rule failure<a name="ct-s3-pr-3-remediation"></a>

Configure versioning\-enabled buckets with at least one active lifecycle rule\.

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket \- Example One<a name="ct-s3-pr-3-remediation-1"></a>

Amazon S3 bucket with versioning enabled and an active lifecycle rule\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3Bucket": {
        "Type": "AWS::S3::Bucket",
        "Properties": {
            "VersioningConfiguration": {
                "Status": "Enabled"
            },
            "LifecycleConfiguration": {
                "Rules": [
                    {
                        "Status": "Enabled",
                        "ExpirationInDays": 1,
                        "Id": "FirstRule"
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
S3Bucket:
  Type: AWS::S3::Bucket
  Properties:
    VersioningConfiguration:
      Status: Enabled
    LifecycleConfiguration:
      Rules:
        - Status: Enabled
          ExpirationInDays: 1
          Id: FirstRule
```

### CT\.S3\.PR\.3 rule specification<a name="ct-s3-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   s3_version_lifecycle_policy_check
# 
# Description:
#   Checks whether Amazon Simple Storage Service (Amazon S3) version-enabled buckets have lifecycle policy configured.
# 
# Reports on:
#   AWS::S3::Bucket
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Amazon S3 bucket resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket Resource
#       And: The S3 bucket does not have versioning enabled (VersioningConfiguration is missing or
#            VersioningConfiguration.Status is set to Suspended)
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket Resource
#       And: The S3 bucket has versioning enabled (VersioningConfiguration.Status is set to 'Enabled')
#       And: 'LifecycleConfiguration' has been been provided and there are no 'Rules' with 'Status' set to 'Enabled'
#            present in the 'LifecycleConfiguration'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket Resource
#       And: The S3 bucket has versioning enabled (VersioningConfiguration.Status is set to 'Enabled')
#       And: 'LifecycleConfiguration' has been been provided and there is at least one 'Rule' with 'Status' set to
#            'Enabled' in the 'LifecycleConfiguration'
#      Then: PASS

#
# Constants
#
let S3_BUCKET_TYPE = "AWS::S3::Bucket"
let INPUT_DOCUMENT = this

#
# Assignments
#
let s3_buckets = Resources.*[ Type == %S3_BUCKET_TYPE ]

#
# Primary Rules
#
rule s3_version_lifecycle_policy_check when is_cfn_template(%INPUT_DOCUMENT)
                                            %s3_buckets not empty {
    check(%s3_buckets.Properties)
        <<
        [CT.S3.PR.3]: Require an Amazon S3 buckets to have versioning configured and a lifecycle policy
        [FIX]: Configure versioning-enabled buckets with at least one active lifecycle rule.
        >>
}

rule s3_version_lifecycle_policy_check when is_cfn_hook(%INPUT_DOCUMENT, %S3_BUCKET_TYPE) {
    check(%INPUT_DOCUMENT.%S3_BUCKET_TYPE.resourceProperties)
        <<
        [CT.S3.PR.3]: Require an Amazon S3 buckets to have versioning configured and a lifecycle policy
        [FIX]: Configure versioning-enabled buckets with at least one active lifecycle rule.
        >>
}

#
# Parameterized Rules
#
rule check(s3_bucket) {
    %s3_bucket [
        filter_s3_buckets_with_versioning_enabled(this)
    ] {
        # Scenario 2
        LifecycleConfiguration exists
        LifecycleConfiguration is_struct

        LifecycleConfiguration {
            # Scenario 3 and 4
            Rules exists
            Rules is_list
            Rules not empty

            some Rules[*] {
                Status exists
                Status == "Enabled"
            }
        }
    }
}

rule filter_s3_buckets_with_versioning_enabled(s3_bucket) {
    %s3_bucket {
        VersioningConfiguration exists
        VersioningConfiguration is_struct

        VersioningConfiguration {
            Status exists
            Status == "Enabled"
        }
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.S3\.PR\.3 example templates<a name="ct-s3-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties:
      VersioningConfiguration:
        Status: Enabled
      LifecycleConfiguration:
        Rules:
        - Status: Enabled
          ExpirationInDays: 1
          Id: FirstRule
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties:
      VersioningConfiguration:
        Status: Enabled
      LifecycleConfiguration:
        Rules:
        - Status: Disabled
          ExpirationInDays: 1
          Id: FirstRule
```

## \[CT\.S3\.PR\.4\] Require an Amazon S3 bucket to have event notifications configured<a name="ct-s3-pr-4-description"></a>

This control checks whether Amazon S3 events notifications are enabled on your Amazon S3 bucket\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::S3::Bucket`
+ **AWS CloudFormation guard rule: ** [CT\.S3\.PR\.4 rule specification](#ct-s3-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.S3\.PR\.4 rule specification](#ct-s3-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.S3\.PR\.4 example templates](#ct-s3-pr-4-templates) 

**Explanation**

By enabling event notifications, you receive alerts on your Amazon S3 buckets when specific events occur\. For example, you can be notified of object creation, object removal, and object restoration\. These notifications can alert relevant teams to accidental or intentional modifications that may lead to unauthorized data access\.

### Remediation for rule failure<a name="ct-s3-pr-4-remediation"></a>

Set a `NotificationConfiguration` parameter on your bucket with one of `EventBridgeConfiguration`, `LambdaConfigurations`, `QueueConfigurations` or `TopicConfigurations.`

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket \- Example One<a name="ct-s3-pr-4-remediation-1"></a>

Amazon S3 bucket with Amazon EventBridge notifications configured\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3Bucket": {
        "Type": "AWS::S3::Bucket",
        "Properties": {
            "NotificationConfiguration": {
                "EventBridgeConfiguration": {
                    "EventBridgeEnabled": true
                }
            }
        }
    }
}
```

**YAML example**

```
S3Bucket:
  Type: AWS::S3::Bucket
  Properties:
    NotificationConfiguration:
      EventBridgeConfiguration:
        EventBridgeEnabled: true
```

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket \- Example Two<a name="ct-s3-pr-4-remediation-2"></a>

Amazon S3 bucket with SNS topic notifications configured\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3Bucket": {
        "Type": "AWS::S3::Bucket",
        "Properties": {
            "NotificationConfiguration": {
                "TopicConfigurations": [
                    {
                        "Topic": {
                            "Ref": "SnsTopic"
                        },
                        "Event": "s3:ReducedRedundancyLostObject"
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
S3Bucket:
  Type: AWS::S3::Bucket
  Properties:
    NotificationConfiguration:
      TopicConfigurations:
        - Topic: !Ref 'SnsTopic'
          Event: s3:ReducedRedundancyLostObject
```

### CT\.S3\.PR\.4 rule specification<a name="ct-s3-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   s3_event_notifications_enabled_check
# 
# Description:
#   This control checks whether Amazon S3 event notifications are enabled on an S3 bucket.
# 
# Reports on:
#   AWS::S3::Bucket
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Amazon S3 bucket resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'NotificationConfiguration' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'NotificationConfiguration' has been provided
#       And:  At least one of 'EventBridgeConfiguration.EventBridgeEnabled', 'LambdaConfigurations',
#             'QueueConfigurations', or 'TopicConfigurations' have not been provided or provided as empty lists.
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'NotificationConfiguration' has been provided
#       And: 'EventBridgeConfiguration.EventBridgeEnabled' is set to bool(true) or 'LambdaConfigurations',
#            'QueueConfigurations', or 'TopicConfigurations' have been provided with at least one configuration
#      Then: PASS

#
# Constants
#
let S3_BUCKET_TYPE = "AWS::S3::Bucket"
let INPUT_DOCUMENT = this

#
# Assignments
#
let s3_buckets = Resources.*[ Type == %S3_BUCKET_TYPE ]

#
# Primary Rules
#
rule s3_event_notifications_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %s3_buckets not empty {
    check(%s3_buckets.Properties)
        <<
        [CT.S3.PR.4]: Require an Amazon S3 bucket to have event notifications configured
        [FIX]: Set a 'NotificationConfiguration' parameter on your bucket with one of 'EventBridgeConfiguration', 'LambdaConfigurations', 'QueueConfigurations' or 'TopicConfigurations.'
        >>
}

rule s3_event_notifications_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %S3_BUCKET_TYPE) {
    check(%INPUT_DOCUMENT.%S3_BUCKET_TYPE.resourceProperties)
        <<
        [CT.S3.PR.4]: Require an Amazon S3 bucket to have event notifications configured
        [FIX]: Set a 'NotificationConfiguration' parameter on your bucket with one of 'EventBridgeConfiguration', 'LambdaConfigurations', 'QueueConfigurations' or 'TopicConfigurations.'
        >>
}

#
# Parameterized Rules
#
rule check(s3_bucket) {
    %s3_bucket {
       # Scenario 2
       NotificationConfiguration exists
       NotificationConfiguration is_struct
       NotificationConfiguration {
            # Scenario 3 and 4
            EventBridgeConfiguration exists or
            LambdaConfigurations exists or
            QueueConfigurations exists or
            TopicConfigurations exists

            check_event_bridge_notifications(EventBridgeConfiguration) or
            check_other_notifications(LambdaConfigurations) or
            check_other_notifications(QueueConfigurations) or
            check_other_notifications(TopicConfigurations)
       }
    }
}

rule check_event_bridge_notifications(configuration) {
    %configuration {
        this is_struct
        EventBridgeEnabled exists
        EventBridgeEnabled == true
    }
}

rule check_other_notifications(configuration) {
    %configuration {
        this is_list
        this not empty
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.S3\.PR\.4 example templates<a name="ct-s3-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties:
      NotificationConfiguration:
        EventBridgeConfiguration:
          EventBridgeEnabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties:
      NotificationConfiguration: {}
```

## \[CT\.S3\.PR\.5\] Require that an Amazon S3 bucket does not manage user access with an access control list \(ACL\)<a name="ct-s3-pr-5-description"></a>

This control checks whether your Amazon Simple Storage Service \(Amazon S3\) bucket allows user permissions through access control lists\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::S3::Bucket`
+ **AWS CloudFormation guard rule: ** [CT\.S3\.PR\.5 rule specification](#ct-s3-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.S3\.PR\.5 rule specification](#ct-s3-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.S3\.PR\.5 example templates](#ct-s3-pr-5-templates) 

**Explanation**

ACLs are legacy access control mechanisms that predate IAM\. Instead of ACLs, we recommend using IAM policies or Amazon S3 bucket policies to more easily manage access to your S3 buckets\.

### Remediation for rule failure<a name="ct-s3-pr-5-remediation"></a>

Manage access to Amazon S3 buckets with bucket resource policies and IAM identity policies instead\.

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket \- Example<a name="ct-s3-pr-5-remediation-1"></a>

Amazon S3 bucket that does not allow user permissions through access control lists by omitting the `AccessControl` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3Bucket": {
        "Type": "AWS::S3::Bucket",
        "Properties": {}
    }
}
```

**YAML example**

```
S3Bucket:
  Type: AWS::S3::Bucket
  Properties: {}
```

### CT\.S3\.PR\.5 rule specification<a name="ct-s3-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   s3_bucket_acl_prohibited_check
# 
# Description:
#   Checks whether Amazon Simple Storage Service (Amazon S3) buckets allow user permissions through access control lists.
# 
# Reports on:
#   AWS::S3::Bucket
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Amazon S3 bucket resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'AccessControl' has been provided on the S3 bucket resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'AccessControl' has not been provided on the S3 bucket resource
#      Then: PASS

#
# Constants
#
let S3_BUCKET_TYPE = "AWS::S3::Bucket"
let INPUT_DOCUMENT = this

#
# Assignments
#
let s3_buckets = Resources.*[ Type == %S3_BUCKET_TYPE ]

#
# Primary Rules
#
rule s3_bucket_acl_prohibited_check when is_cfn_template(%INPUT_DOCUMENT)
                                         %s3_buckets not empty {
    check(%s3_buckets.Properties)
        <<
        [CT.S3.PR.5]: Require that an Amazon S3 bucket does not manage user access with an access control list (ACL)
        [FIX]: Manage access to Amazon S3 buckets with bucket resource policies and IAM identity policies instead.
        >>
}

rule s3_bucket_acl_prohibited_check when is_cfn_hook(%INPUT_DOCUMENT, %S3_BUCKET_TYPE) {
    check(this.%S3_BUCKET_TYPE.resourceProperties)
        <<
        [CT.S3.PR.5]: Require that an Amazon S3 bucket does not manage user access with an access control list (ACL)
        [FIX]: Manage access to Amazon S3 buckets with bucket resource policies and IAM identity policies instead.
        >>
}

#
# Parameterized Rules
#
rule check(s3_bucket) {
    %s3_bucket {
       # Scenario 2 and 3
       AccessControl not exists
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.S3\.PR\.5 example templates<a name="ct-s3-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties: {}
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties:
      AccessControl: Private
```

## \[CT\.S3\.PR\.6\] Require an Amazon S3 bucket to have lifecycle policies configured<a name="ct-s3-pr-6-description"></a>

This control checks whether a lifecycle rule is configured for Amazon S3 buckets\.
+ **Control objective: **Optimize costs, Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::S3::Bucket`
+ **AWS CloudFormation guard rule: ** [CT\.S3\.PR\.6 rule specification](#ct-s3-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.S3\.PR\.6 rule specification](#ct-s3-pr-6-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.S3\.PR\.6 example templates](#ct-s3-pr-6-templates) 

**Explanation**

Configuring lifecycle rules on your Amazon S3 bucket defines actions that you want S3 to take during an object's lifetime\. For example, you can transition objects to another storage class, archive them, or delete them after a specified period of time\.

### Remediation for rule failure<a name="ct-s3-pr-6-remediation"></a>

Configure at least one active lifecycle rule in `LifecycleConfiguration.Rules` by setting `Status` on a rule to `Enabled`\.

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket \- Example<a name="ct-s3-pr-6-remediation-1"></a>

Amazon S3 bucket configured with an active lifecycle rule\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3Bucket": {
        "Type": "AWS::S3::Bucket",
        "Properties": {
            "LifecycleConfiguration": {
                "Rules": [
                    {
                        "Status": "Enabled",
                        "ExpirationInDays": 1,
                        "Id": "FirstRule"
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
S3Bucket:
  Type: AWS::S3::Bucket
  Properties:
    LifecycleConfiguration:
      Rules:
        - Status: Enabled
          ExpirationInDays: 1
          Id: FirstRule
```

### CT\.S3\.PR\.6 rule specification<a name="ct-s3-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   s3_lifecycle_policy_check
# 
# Description:
#   This control checks whether a lifecycle rule is configured for Amazon S3 buckets.
# 
# Reports on:
#   AWS::S3::Bucket
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any S3 bucket resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an S3 bucket resource
#       And: 'LifecycleConfiguration.Rules' has not been been provided or has been provided where 'Rules' is an
#             empty list
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an S3 bucket resource
#       And: The S3 bucket has versioning enabled (VersioningConfiguration.Status is set to 'Enabled')
#       And: 'LifecycleConfiguration.Rules' has been been provided as a non-empty list
#       And: There are no 'Rules' with 'Status' set to 'Enabled' present in the 'LifecycleConfiguration'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an S3 bucket resource
#       And: The S3 bucket has versioning enabled (VersioningConfiguration.Status is set to 'Enabled')
#       And: 'LifecycleConfiguration.Rules' has been been provided as a non-empty list
#       And: There is at least one entry in 'LifecycleConfiguration.Rules' with 'Status' set to 'Enabled'
#      Then: PASS

#
# Constants
#
let S3_BUCKET_TYPE = "AWS::S3::Bucket"
let INPUT_DOCUMENT = this

#
# Assignments
#
let s3_buckets = Resources.*[ Type == %S3_BUCKET_TYPE ]

#
# Primary Rules
#
rule s3_lifecycle_policy_check when is_cfn_template(%INPUT_DOCUMENT)
                                    %s3_buckets not empty {
    check(%s3_buckets.Properties)
        <<
        [CT.S3.PR.6]: Require an Amazon S3 bucket to have lifecycle policies configured
        [FIX]: Configure at least one active lifecycle rule in 'LifecycleConfiguration.Rules' by setting 'Status' on a rule to 'Enabled'.
        >>
}

rule s3_lifecycle_policy_check when is_cfn_hook(%INPUT_DOCUMENT, %S3_BUCKET_TYPE) {
    check(%INPUT_DOCUMENT.%S3_BUCKET_TYPE.resourceProperties)
        <<
        [CT.S3.PR.6]: Require an Amazon S3 bucket to have lifecycle policies configured
        [FIX]: Configure at least one active lifecycle rule in 'LifecycleConfiguration.Rules' by setting 'Status' on a rule to 'Enabled'.
        >>
}

#
# Parameterized Rules
#
rule check(s3_bucket) {
    %s3_bucket {
        # Scenario 2
        LifecycleConfiguration exists
        LifecycleConfiguration is_struct

        LifecycleConfiguration {
            # Scenario 3 and 4
            Rules exists
            Rules is_list
            Rules not empty

            some Rules[*] {
                Status exists
                Status == "Enabled"
            }
        }
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.S3\.PR\.6 example templates<a name="ct-s3-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      LifecycleConfiguration:
        Rules:
        - Status: Enabled
          ExpirationInDays: 1
          Id: FirstRule
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      LifecycleConfiguration:
        Rules:
        - Status: Disabled
          ExpirationInDays: 1
          Id: FirstRule
```

## \[CT\.S3\.PR\.7\] Require an Amazon S3 bucket to have server\-side encryption configured<a name="ct-s3-pr-7-description"></a>

This control checks whether default server\-side encryption is enabled on your Amazon S3 bucket\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::S3::Bucket`
+ **AWS CloudFormation guard rule: ** [CT\.S3\.PR\.7 rule specification](#ct-s3-pr-7-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.S3\.PR\.7 rule specification](#ct-s3-pr-7-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.S3\.PR\.7 example templates](#ct-s3-pr-7-templates) 

**Explanation**

For an added layer of security for your sensitive data in Amazon S3 buckets, you should configure your buckets with server\-side encryption to protect your data at rest\. Amazon S3 encrypts each object with a unique key\. As an additional safeguard, Amazon S3 encrypts the key itself with a root key that it rotates regularly\. Amazon S3 server\-side encryption uses one of the strongest block ciphers available to encrypt your data, 256\-bit Advanced Encryption Standard \(AES\-256\)\.

### Remediation for rule failure<a name="ct-s3-pr-7-remediation"></a>

Set an encryption rule in `BucketEncryption.ServerSideEncryptionConfiguration` with a `ServerSideEncryptionByDefault.SSEAlgorithm` configuration of `AES256` or `aws:kms`\.

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket \- Example One<a name="ct-s3-pr-7-remediation-1"></a>

Amazon S3 bucket configured with Amazon S3 managed keys \(SSE\-S3\) default server\-side encryption\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3Bucket": {
        "Type": "AWS::S3::Bucket",
        "Properties": {
            "BucketEncryption": {
                "ServerSideEncryptionConfiguration": [
                    {
                        "ServerSideEncryptionByDefault": {
                            "SSEAlgorithm": "AES256"
                        }
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
S3Bucket:
  Type: AWS::S3::Bucket
  Properties:
    BucketEncryption:
      ServerSideEncryptionConfiguration:
        - ServerSideEncryptionByDefault:
            SSEAlgorithm: AES256
```

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket \- Example Two<a name="ct-s3-pr-7-remediation-2"></a>

Amazon S3 bucket configured with AWS Key Management Service \(AWS KMS\) \(SSE\-KMS\) default server\-side encryption\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3Bucket": {
        "Type": "AWS::S3::Bucket",
        "Properties": {
            "BucketEncryption": {
                "ServerSideEncryptionConfiguration": [
                    {
                        "ServerSideEncryptionByDefault": {
                            "SSEAlgorithm": "aws:kms"
                        }
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
S3Bucket:
  Type: AWS::S3::Bucket
  Properties:
    BucketEncryption:
      ServerSideEncryptionConfiguration:
        - ServerSideEncryptionByDefault:
            SSEAlgorithm: aws:kms
```

### CT\.S3\.PR\.7 rule specification<a name="ct-s3-pr-7-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   s3_bucket_default_encryption_enabled_check
# 
# Description:
#   This control checks whether default server-side encryption is enabled on Amazon S3 buckets.
# 
# Reports on:
#   AWS::S3::Bucket
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Amazon S3 bucket resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'BucketEncryption.ServerSideEncryptionConfiguration' has not been provided or provided as an empty list
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'BucketEncryption.ServerSideEncryptionConfiguration' has been provided as a non empty list
#       And: 'BucketEncryption.ServerSideEncryptionConfiguration' does not contain an encryption rule with
#            a 'ServerSideEncryptionByDefault' configuration
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'BucketEncryption.ServerSideEncryptionConfiguration' has been provided as a non empty list
#       And: 'BucketEncryption.ServerSideEncryptionConfiguration' contains an encryption rule with
#            a 'ServerSideEncryptionByDefault' configuration
#       And: For an encryption rule, 'SSEAlgorithm' in 'ServerSideEncryptionByDefault' is not not provided or has been
#            provided and set to an invalid SSE Algorithm
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon S3 bucket resource
#       And: 'BucketEncryption.ServerSideEncryptionConfiguration' has been provided as a non empty list
#       And: 'BucketEncryption.ServerSideEncryptionConfiguration' contains an encryption rule with
#            a 'ServerSideEncryptionByDefault' configuration
#       And: For all encryption rules, 'SSEAlgorithm' in 'ServerSideEncryptionByDefault' is provided and set to a valid
#            SSE Algorithm
#      Then: PASS

#
# Constants
#
let S3_BUCKET_TYPE = "AWS::S3::Bucket"
let SUPPORTED_SSE_ALGORITHMS = [ "AES256", "aws:kms" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let s3_buckets = Resources.*[ Type == %S3_BUCKET_TYPE ]

#
# Primary Rules
#
rule s3_bucket_default_encryption_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                     %s3_buckets not empty {
    check(%s3_buckets.Properties)
        <<
        [CT.S3.PR.7]: Require an Amazon S3 bucket to have server-side encryption configured
        [FIX]: Set an encryption rule in 'BucketEncryption.ServerSideEncryptionConfiguration' with a 'ServerSideEncryptionByDefault.SSEAlgorithm' configuration of 'AES256' or 'aws:kms'.
        >>
}

rule s3_bucket_default_encryption_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %S3_BUCKET_TYPE) {
    check(%INPUT_DOCUMENT.%S3_BUCKET_TYPE.resourceProperties)
        <<
        [CT.S3.PR.7]: Require an Amazon S3 bucket to have server-side encryption configured
        [FIX]: Set an encryption rule in 'BucketEncryption.ServerSideEncryptionConfiguration' with a 'ServerSideEncryptionByDefault.SSEAlgorithm' configuration of 'AES256' or 'aws:kms'.
        >>
}

#
# Parameterized Rules
#
rule check(s3_bucket) {
    %s3_bucket {
        # Scenario 2
        BucketEncryption exists
        BucketEncryption is_struct

        BucketEncryption {
            ServerSideEncryptionConfiguration exists
            ServerSideEncryptionConfiguration is_list
            ServerSideEncryptionConfiguration not empty

            # Scenario 3, 4 and 5
            some ServerSideEncryptionConfiguration[*] {
                ServerSideEncryptionByDefault exists
                ServerSideEncryptionByDefault is_struct

                ServerSideEncryptionByDefault {
                    SSEAlgorithm exists
                    SSEAlgorithm in %SUPPORTED_SSE_ALGORITHMS
                }
            }
        }
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.S3\.PR\.7 example templates<a name="ct-s3-pr-7-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketEncryption:
        ServerSideEncryptionConfiguration:
        - ServerSideEncryptionByDefault:
            SSEAlgorithm: AES256
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties: {}
```

## \[CT\.S3\.PR\.8\] Require that Amazon S3 bucket requests use Secure Socket Layer<a name="ct-s3-pr-8-description"></a>

This control checks whether Amazon S3 bucket policies require requests to use Secure Socket Layer \(SSL\)\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::S3::BucketPolicy`
+ **AWS CloudFormation guard rule: ** [CT\.S3\.PR\.8 rule specification](#ct-s3-pr-8-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.S3\.PR\.8 rule specification](#ct-s3-pr-8-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.S3\.PR\.8 example templates](#ct-s3-pr-8-templates) 

**Explanation**

Amazon S3 buckets should have policies that require all requests \(`Action: S3:*`\) to accept transmission of data over HTTPS in the S3 resource policy only, as indicated by the condition key `aws:SecureTransport`\.

### Remediation for rule failure<a name="ct-s3-pr-8-remediation"></a>

Configure an Amazon S3 bucket policy statement that denies access to all principals and actions for the S3 bucket and bucket objects when a secure transport protocol is not in use\.

The examples that follow show how to implement this remediation\.

#### Amazon S3 Bucket Policy \- Example<a name="ct-s3-pr-8-remediation-1"></a>

Amazon S3 bucket policy configured to deny all access to the bucket and bucket objects when transmission of data is not over HTTPS\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "S3BucketPolicy": {
        "Type": "AWS::S3::BucketPolicy",
        "Properties": {
            "Bucket": {
                "Ref": "S3Bucket"
            },
            "PolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Action": "s3:*",
                        "Resource": [
                            {
                                "Fn::GetAtt": [
                                    "S3Bucket",
                                    "Arn"
                                ]
                            },
                            {
                                "Fn::Join": [
                                    "",
                                    [
                                        {
                                            "Fn::GetAtt": [
                                                "S3Bucket",
                                                "Arn"
                                            ]
                                        },
                                        "/*"
                                    ]
                                ]
                            }
                        ],
                        "Principal": "*",
                        "Condition": {
                            "Bool": {
                                "aws:SecureTransport": "false"
                            }
                        }
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
S3BucketPolicy:
  Type: AWS::S3::BucketPolicy
  Properties:
    Bucket: !Ref 'S3Bucket'
    PolicyDocument:
      Version: 2012-10-17
      Statement:
        - Effect: Deny
          Action: s3:*
          Resource:
            - !GetAtt 'S3Bucket.Arn'
            - !Join
              - ''
              - - !GetAtt 'S3Bucket.Arn'
                - /*
          Principal: '*'
          Condition:
            Bool:
              aws:SecureTransport: 'false'
```

### CT\.S3\.PR\.8 rule specification<a name="ct-s3-pr-8-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   s3_bucket_policy_ssl_requests_only_check
# 
# Description:
#   This control checks whether Amazon S3 bucket policies require requests to use Secure Socket Layer (SSL).
# 
# Reports on:
#   AWS::S3::BucketPolicy
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any S3 bucket policies
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an S3 bucket policy
#       And: 'Policydocument' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an S3 bucket policy
#       And: 'Policydocument' does not include a statement that denies Principal  ('*', AWS: '*')
#            all Actions ('s3:*', '*') over resource ('*' or bucketArn, bucketObjectArn) when the condition
#            "aws:SecureTransport" is "false"
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an S3 bucket policy
#       And: 'Policydocument' includes a statement that denies Principal  ('*', AWS: '*')
#            all Actions ('s3:*', '*') over resource ('*' or bucketArn, bucketObjectArn) when the condition
#            "aws:SecureTransport" is "false"
#      Then: PASS

#
# Constants
#
let S3_BUCKET_POLICY_TYPE = "AWS::S3::BucketPolicy"
let INPUT_DOCUMENT = this

let S3_BUCKET_ARN_PATTERN = /^arn:aws[a-z0-9\-]*:s3:::[a-z0-9][a-z0-9\.-]*[a-z0-9]$/
let S3_BUCKET_OBJECT_ARN_PATTERN = /^arn:aws[a-z0-9\-]*:s3:::[a-z0-9][a-z0-9\.-]*[a-z0-9]\/\*$/

#
# Assignments
#
let s3_bucket_policies = Resources.*[ Type == %S3_BUCKET_POLICY_TYPE ]

#
# Primary Rules
#
rule s3_bucket_policy_ssl_requests_only_check when is_cfn_template(%INPUT_DOCUMENT)
                                                   %s3_bucket_policies not empty {
    check(%s3_bucket_policies.Properties)
        <<
        [CT.S3.PR.8]: Require that Amazon S3 buckets request to use Secure Socket Layer
            [FIX]: Configure an Amazon S3 bucket policy statement that denies access to all principals and actions for the S3 bucket and bucket objects when a secure transport protocol is not in use.
        >>
}

rule s3_bucket_policy_ssl_requests_only_check when is_cfn_hook(%INPUT_DOCUMENT, %S3_BUCKET_POLICY_TYPE) {
    check(%INPUT_DOCUMENT.%S3_BUCKET_POLICY_TYPE.resourceProperties)
        <<
        [CT.S3.PR.8]: Require that Amazon S3 buckets request to use Secure Socket Layer
            [FIX]: Configure an Amazon S3 bucket policy statement that denies access to all principals and actions for the S3 bucket and bucket objects when a secure transport protocol is not in use.
        >>
}

#
# Parameterized Rules
#
rule check(s3_bucket_policy) {
    %s3_bucket_policy {
        # Scenario 2
        PolicyDocument exists
        PolicyDocument is_struct

        PolicyDocument {
            Statement exists
            Statement is_list or
            Statement is_struct

            #Scenario 3 and 4
            some Statement[*] {
                check_statement_ssl_requests_only(this)
            }
        }
    }
}

rule check_statement_ssl_requests_only(statement) {
    %statement{
        check_all_required_statement_properties(this)

        Effect == "Deny"
        Action[*] in ["s3:*", "*"]

        Principal == "*" or
        Principal {
            AWS exists
            AWS == "*"
        }

        Resource[*] == "*" or
        check_resource_for_bucket_arns(Resource) or
        check_resource_for_bucket_arn_refs(Resource)

        Condition is_struct
        Condition == {
            "Bool": {
                "aws:SecureTransport": "false"
            }
        }

    }
}

rule check_all_required_statement_properties(statement) {
    %statement {
        Effect exists
        Action exists
        Principal exists
        Condition exists
        Resource exists
    }
}

rule check_resource_for_bucket_arns(resource) {
    %resource {
        this is_list
        this not empty
        some this[*] == %S3_BUCKET_ARN_PATTERN
        some this[*] == %S3_BUCKET_OBJECT_ARN_PATTERN
    }
}

rule check_resource_for_bucket_arn_refs(resource) {
    %resource {
        this is_list
        this not empty
        some this[*] {
            check_local_bucket_arn_reference(%INPUT_DOCUMENT, this, "AWS::S3::Bucket")
        }
        some this[*] {
            check_local_bucket_object_arn_reference(%INPUT_DOCUMENT, this, "AWS::S3::Bucket")
        }
    }
}

rule check_local_bucket_arn_reference(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        'Fn::GetAtt' {
            check_get_att_bucket_arn(this)
        }
    }
}

rule check_local_bucket_object_arn_reference(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        'Fn::Join' {
            this is_list
            this not empty
            this[1][0] {
                'Fn::GetAtt' {
                    check_get_att_bucket_arn(this)
                }
            }
            this[1][1] == "/*"
        }
    }
}

rule check_get_att_bucket_arn(get_att){
    %get_att {
        this is_list
        this not empty
        this[1] == "Arn"
        query_for_resource(%doc, this[0], %referenced_resource_type)
            <<Local Stack reference was invalid>>
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}

rule query_for_resource(doc, resource_key, referenced_resource_type) {
    let referenced_resource = %doc.Resources[ keys == %resource_key ]
    %referenced_resource not empty
    %referenced_resource {
        Type == %referenced_resource_type
    }
}
```

### CT\.S3\.PR\.8 example templates<a name="ct-s3-pr-8-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
  S3BucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: S3Bucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
        - Effect: Deny
          Action: s3:*
          Resource:
          - Fn::GetAtt:
            - S3Bucket
            - Arn
          - Fn::Join:
            - ''
            - - Fn::GetAtt:
                - S3Bucket
                - Arn
              - /*
          Principal: '*'
          Condition:
            Bool:
              aws:SecureTransport: 'false'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
  S3BucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: S3Bucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
        - Effect: Allow
          Action: s3:*
          Resource:
          - Fn::GetAtt:
            - S3Bucket
            - Arn
          - Fn::Join:
            - ''
            - - Fn::GetAtt:
                - S3Bucket
                - Arn
              - /*
          Principal:
            AWS:
            - Ref: AWS::AccountId
          Condition:
            Bool:
              aws:SecureTransport: 'false'
```