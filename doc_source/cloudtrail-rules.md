# AWS CloudTrail controls<a name="cloudtrail-rules"></a>

**Topics**
+ [\[CT\.CLOUDTRAIL\.PR\.1\] Require an AWS CloudTrail trail to have encryption at rest activated](#ct-cloudtrail-pr-1-description)
+ [\[CT\.CLOUDTRAIL\.PR\.2\] Require an AWS CloudTrail trail to have log file validation activated](#ct-cloudtrail-pr-2-description)
+ [\[CT\.CLOUDTRAIL\.PR\.3\] Require an AWS CloudTrail trail to have an Amazon CloudWatch Logs log group configuration](#ct-cloudtrail-pr-3-description)

## \[CT\.CLOUDTRAIL\.PR\.1\] Require an AWS CloudTrail trail to have encryption at rest activated<a name="ct-cloudtrail-pr-1-description"></a>

This control checks whether your AWS CloudTrail is configured to use the server\-side encryption \(SSE\) AWS KMS key encryption\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudTrail::Trail`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDTRAIL\.PR\.1 rule specification](#ct-cloudtrail-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDTRAIL\.PR\.1 rule specification](#ct-cloudtrail-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDTRAIL\.PR\.1 example templates](#ct-cloudtrail-pr-1-templates) 

**Explanation**

For an added layer of security for your sensitive CloudTrail log files, you should use server\-side encryption with AWS KMS keys \(SSE\-KMS\) for your CloudTrail log files for encryption at rest\. Note that by default, the log files delivered by CloudTrail to your buckets are encrypted by Amazon server\-side encryption with Amazon S3\-managed encryption keys \(SSE\-S3\)\.

### Remediation for rule failure<a name="ct-cloudtrail-pr-1-remediation"></a>

Set the `KMSKeyId` property to a valid KMS key\.

The examples that follow show how to implement this remediation\.

#### AWS CloudTrail trail \- Example<a name="ct-cloudtrail-pr-1-remediation-1"></a>

AWS CloudTrail Trail configured to use server\-side encryption with AWS KMS keys \(SSE\-KMS\)\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudTrail": {
        "Type": "AWS::CloudTrail::Trail",
        "Properties": {
            "IsLogging": true,
            "KMSKeyId": {
                "Ref": "KMSKey"
            },
            "S3BucketName": {
                "Ref": "LoggingBucket"
            }
        }
    }
}
```

**YAML example**

```
CloudTrail:
  Type: AWS::CloudTrail::Trail
  Properties:
    IsLogging: true
    KMSKeyId: !Ref 'KMSKey'
    S3BucketName: !Ref 'LoggingBucket'
```

### CT\.CLOUDTRAIL\.PR\.1 rule specification<a name="ct-cloudtrail-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloud_trail_encryption_enabled_check
# 
# Description:
#   This rule checks whether AWS CloudTrail is configured to use the server-side encryption (SSE) AWS KMS key encryption.
# 
# Reports on:
#   AWS::CloudTrail::Trail
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
#       And: The input document does not contain any AWS CloudTrailtrails
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains a CloudTrail trail resource
#       And: 'KMSKeyId' is not present
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains a CloudTrail trail resource
#       And: 'KMSKeyId' has been provided and is set to an empty string or a non-valid local reference to a KMS key or
#            Alias
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains a CloudTrail trail resource
#       And: 'KMSKeyId' has been provided and is a non-empty string or a valid reference to a KMS key or Alias.
#      Then: PASS

#
# Constants
#
let CLOUDTRAIL_TRAIL_TYPE = "AWS::CloudTrail::Trail"
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudtrail_trails = Resources.*[ Type == %CLOUDTRAIL_TRAIL_TYPE ]

#
# Primary Rules
#
rule cloud_trail_encryption_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %cloudtrail_trails not empty {
    check_cloudtrail_kms_key_configuration(%cloudtrail_trails.Properties)
        < <
        [CT.CLOUDTRAIL.PR.1]: Require an AWS CloudTrail trail to have encryption at rest activated
        [FIX]: Set the 'KMSKeyId' property to a valid KMS key.
        >>
}

rule cloud_trail_encryption_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDTRAIL_TRAIL_TYPE) {
    check_cloudtrail_kms_key_configuration(%INPUT_DOCUMENT.%CLOUDTRAIL_TRAIL_TYPE.resourceProperties)
        <<
        [CT.CLOUDTRAIL.PR.1]: Require an AWS CloudTrail trail to have encryption at rest activated
        [FIX]: Set the 'KMSKeyId' property to a valid KMS key.
        >>
}

#
# Parameterized Rules
#
rule check_cloudtrail_kms_key_configuration(cloudtrail_trail){
    %cloudtrail_trail {
        # Scenario 2
        KMSKeyId exists
        # Scenario 3 and 4
        check_is_string_and_not_empty(KMSKeyId) or
        check_kms_key_id_local_ref(KMSKeyId)
    }
}

rule check_kms_key_id_local_ref(key_ref) {
    %key_ref {
      check_local_references(%INPUT_DOCUMENT, this, "AWS::KMS::Key") or
      check_local_references(%INPUT_DOCUMENT, this, "AWS::KMS::Alias")
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

rule check_local_references(doc, reference_properties, referenced_RESOURCE_TYPE) {
    %reference_properties {
        'Fn::GetAtt' {
            query_for_resource(%doc, this[0], %referenced_RESOURCE_TYPE)
                <<Local Stack reference was invalid>>
        } or Ref {
            query_for_resource(%doc, this, %referenced_RESOURCE_TYPE)
                <<Local Stack reference was invalid>>
        }
    }
}

rule query_for_resource(doc, resource_key, referenced_RESOURCE_TYPE) {
    let referenced_resource = %doc.Resources[ keys == %resource_key ]
    %referenced_resource not empty
    %referenced_resource {
        Type == %referenced_RESOURCE_TYPE
    }
}
```

### CT\.CLOUDTRAIL\.PR\.1 example templates<a name="ct-cloudtrail-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  KMSKey:
    Type: AWS::KMS::Key
    Properties:
      KeyPolicy:
        Version: 2012-10-17
        Id: example-cloudtrail-key-policy
        Statement:
        - Sid: Enable IAM User Permissions
          Effect: Allow
          Principal:
            AWS:
              Fn::Sub: arn:${AWS::Partition}:iam::${AWS::AccountId}:root
          Action: kms:*
          Resource: '*'
        - Sid: Allow CloudTrail to encrypt logs
          Effect: Allow
          Action: "kms:GenerateDataKey*"
          Principal:
            Service: "cloudtrail.amazonaws.com"
          Resource: '*'
          Condition:
            StringLike:
              "kms:EncryptionContext:aws:cloudtrail:arn": [
                Fn::Sub: "arn:aws:cloudtrail:*:${AWS::AccountId}:trail/*"
              ]
            StringEquals:
              "aws:SourceArn": 
                Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
        - Sid: Allow CloudTrail to describe key
          Effect: Allow
          Principal:
            Service: "cloudtrail.amazonaws.com"
          Action: kms:DescribeKey
          Resource: '*'
        - Sid: Allow principals in the account to decrypt log files
          Effect: Allow
          Principal:
            AWS: "*"
          Action:
           - "kms:Decrypt"
           - "kms:ReEncryptFrom"
          Resource: "*"
          Condition:
            StringEquals: 
              "kms:CallerAccount":
                Ref: AWS::AccountId
              "kms:EncryptionContext:aws:cloudtrail:arn":
                Fn::Sub: "arn:aws:cloudtrail:*:${AWS::AccountId}:trail/*"
  LoggingBucket:
    Type: AWS::S3::Bucket
  LoggingBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: LoggingBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetBucketAcl'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
          - Action:
              - 's3:PutObject'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
                  - /AWSLogs/
                  - Ref: AWS::AccountId
                  - /*
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                's3:x-amz-acl': 'bucket-owner-full-control'
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
  CloudTrail:
    Type: AWS::CloudTrail::Trail
    Properties:
      IsLogging: true
      TrailName:
        Fn::Sub: ${AWS::StackName}-example-trail
      KMSKeyId:
        Ref: KMSKey
      S3BucketName:
        Ref: LoggingBucket
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  LoggingBucket:
    Type: AWS::S3::Bucket
  LoggingBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: LoggingBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetBucketAcl'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
          - Action:
              - 's3:PutObject'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
                  - /AWSLogs/
                  - Ref: AWS::AccountId
                  - /*
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                's3:x-amz-acl': 'bucket-owner-full-control'
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
  CloudTrail:
    Type: AWS::CloudTrail::Trail
    Properties:
      IsLogging: true
      TrailName:
        Fn::Sub: ${AWS::StackName}-example-trail
      S3BucketName:
        Ref: LoggingBucket
```

## \[CT\.CLOUDTRAIL\.PR\.2\] Require an AWS CloudTrail trail to have log file validation activated<a name="ct-cloudtrail-pr-2-description"></a>

This control checks whether log file integrity validation is enabled on an AWS CloudTrail trail\.
+ **Control objective: **Manage secrets
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudTrail::Trail`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDTRAIL\.PR\.2 rule specification](#ct-cloudtrail-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDTRAIL\.PR\.2 rule specification](#ct-cloudtrail-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDTRAIL\.PR\.2 example templates](#ct-cloudtrail-pr-2-templates) 

**Explanation**

CloudTrail log file validation creates a digitally\-signed digest file that contains a hash of each log that CloudTrail writes to Amazon S3\. You can use these digest files to determine whether a log file was changed, deleted, or unchanged after CloudTrail delivered the log\.

AWS Control Tower recommends that you enable file validation on all trails\. Log file validation provides additional integrity checks of CloudTrail logs\.

### Remediation for rule failure<a name="ct-cloudtrail-pr-2-remediation"></a>

Set the CloudTrail resource `EnableLogFileValidation` property to true\.

The examples that follow show how to implement this remediation\.

#### AWS CloudTrail trail \- Example<a name="ct-cloudtrail-pr-2-remediation-1"></a>

AWS CloudTrail trail configured with log file validation\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudTrail": {
        "Type": "AWS::CloudTrail::Trail",
        "Properties": {
            "IsLogging": true,
            "S3BucketName": {
                "Ref": "LoggingBucket"
            },
            "KMSKeyId": {
                "Ref": "KMSKey"
            },
            "EnableLogFileValidation": true
        }
    }
}
```

**YAML example**

```
CloudTrail:
  Type: AWS::CloudTrail::Trail
  Properties:
    IsLogging: true
    S3BucketName: !Ref 'LoggingBucket'
    KMSKeyId: !Ref 'KMSKey'
    EnableLogFileValidation: true
```

### CT\.CLOUDTRAIL\.PR\.2 rule specification<a name="ct-cloudtrail-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloud_trail_log_file_validation_enabled_check
# 
# Description:
#   This control checks whether log file integrity validation is enabled on an AWS CloudTrail trail.
# 
# Reports on:
#   AWS::CloudTrail::Trail
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
#       And: The input document does not contain any CloudTrail trails
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains a CloudTrail trail resource
#       And: 'EnableLogFileValidation' is not present
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains a CloudTrail trail resource
#       And: 'EnableLogFileValidation' is present and and is set to a value other than bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains a CloudTrail trail resource
#       And: 'EnableLogFileValidation' is present and set to bool(true)
#      Then: PASS

#
# Constants
#
let CLOUDTRAIL_TRAIL_TYPE = "AWS::CloudTrail::Trail"
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudtrail_trails = Resources.*[ Type == %CLOUDTRAIL_TRAIL_TYPE ]

#
# Primary Rules
#
rule cloud_trail_log_file_validation_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                        %cloudtrail_trails not empty {
    check(%cloudtrail_trails.Properties)
        <<
        [CT.CLOUDTRAIL.PR.2]: Require an AWS CloudTrail trail to have log file validation activated
        [FIX]: Set the CloudTrail resource 'EnableLogFileValidation' property to true.
        >>
}

rule cloud_trail_log_file_validation_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDTRAIL_TRAIL_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDTRAIL_TRAIL_TYPE.resourceProperties)
        <<
        [CT.CLOUDTRAIL.PR.2]: Require an AWS CloudTrail trail to have log file validation activated
        [FIX]: Set the CloudTrail resource 'EnableLogFileValidation' property to true.
        >>
}

#
# Parameterized Rules
#
rule check(cloudtrail_trail){
    %cloudtrail_trail {
        # Scenario 2
        EnableLogFileValidation exists
        # Scenario 3 and 4
        EnableLogFileValidation == true
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

### CT\.CLOUDTRAIL\.PR\.2 example templates<a name="ct-cloudtrail-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  LoggingBucket:
    Type: AWS::S3::Bucket
  LoggingBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: LoggingBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetBucketAcl'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
          - Action:
              - 's3:PutObject'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
                  - /AWSLogs/
                  - Ref: AWS::AccountId
                  - /*
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                's3:x-amz-acl': 'bucket-owner-full-control'
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
  CloudTrail:
    Type: AWS::CloudTrail::Trail
    Properties:
      IsLogging: true
      TrailName:
        Fn::Sub: ${AWS::StackName}-example-trail
      S3BucketName:
        Ref: LoggingBucket
      EnableLogFileValidation: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  LoggingBucket:
    Type: AWS::S3::Bucket
  LoggingBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: LoggingBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetBucketAcl'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
          - Action:
              - 's3:PutObject'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
                  - /AWSLogs/
                  - Ref: AWS::AccountId
                  - /*
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                's3:x-amz-acl': 'bucket-owner-full-control'
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
  CloudTrail:
    Type: AWS::CloudTrail::Trail
    Properties:
      IsLogging: true
      TrailName:
        Fn::Sub: ${AWS::StackName}-example-trail
      S3BucketName:
        Ref: LoggingBucket
      EnableLogFileValidation: false
```

## \[CT\.CLOUDTRAIL\.PR\.3\] Require an AWS CloudTrail trail to have an Amazon CloudWatch Logs log group configuration<a name="ct-cloudtrail-pr-3-description"></a>

This control checks whether your AWS CloudTrail trail is configured to send logs to Amazon CloudWatch Logs Logs\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudTrail::Trail`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDTRAIL\.PR\.3 rule specification](#ct-cloudtrail-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDTRAIL\.PR\.3 rule specification](#ct-cloudtrail-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDTRAIL\.PR\.3 example templates](#ct-cloudtrail-pr-3-templates) 

**Explanation**

CloudTrail records AWS API calls that are made in a given account\. The recorded information includes: the identity of the API caller, the time of the API call, the source IP address of the API caller, the request parameters and the response elements returned by the AWS service\.

CloudTrail uses Amazon S3 for log file storage and delivery\. You can capture CloudTrail logs in a specified S3 bucket for long\-term analysis\. To perform real\-time analysis, you can configure CloudTrail to send logs to CloudWatch Logs\.

For a trail that is enabled in all AWS Regions in an account, CloudTrail sends log files from all of those Regions to a CloudWatch Logs log group\.

AWS CloudTrail recommends that you send CloudTrail logs to CloudWatch Logs\. Note that this recommendation is intended to ensure that account activity is captured, monitored, and appropriately alarmed on\. You can use CloudWatch Logs to set this up with your AWS services\. This recommendation does not preclude the use of a different solution\.

Sending CloudTrail logs to CloudWatch Logs facilitates real\-time and historic activity logging based on user, API, resource, and IP address\. You can use this approach to establish alarms and notifications for anomalous or sensitivity account activity\.

### Remediation for rule failure<a name="ct-cloudtrail-pr-3-remediation"></a>

Set the `CloudWatchLogsLogGroupArn` and `CloudWatchLogsRoleArn` properties\.

The examples that follow show how to implement this remediation\.

#### AWS CloudTrail trail \- Example<a name="ct-cloudtrail-pr-3-remediation-1"></a>

AWS CloudTrail trail configured to send events to Amazon CloudWatch Logs\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudTrail": {
        "Type": "AWS::CloudTrail::Trail",
        "Properties": {
            "IsLogging": true,
            "S3BucketName": {
                "Ref": "LoggingBucket"
            },
            "CloudWatchLogsRoleArn": {
                "Fn::GetAtt": [
                    "LogRole",
                    "Arn"
                ]
            },
            "CloudWatchLogsLogGroupArn": {
                "Fn::GetAtt": [
                    "LogGroup",
                    "Arn"
                ]
            }
        }
    }
}
```

**YAML example**

```
CloudTrail:
  Type: AWS::CloudTrail::Trail
  Properties:
    IsLogging: true
    S3BucketName: !Ref 'LoggingBucket'
    CloudWatchLogsRoleArn: !GetAtt 'LogRole.Arn'
    CloudWatchLogsLogGroupArn: !GetAtt 'LogGroup.Arn'
```

### CT\.CLOUDTRAIL\.PR\.3 rule specification<a name="ct-cloudtrail-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloud_trail_cloud_watch_logs_enabled_check
# 
# Description:
#   This rule checks whether AWS CloudTrail trails are configured to send logs to Amazon CloudWatch Logs.
# 
# Reports on:
#   AWS::CloudTrail::Trail
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
#       And: The input document does not contain any AWS CloudTrail trails
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an AWS CloudTrail trail resource
#       And: 'CloudWatchLogsLogGroupArn' or 'CloudWatchLogsRoleArn' is not present
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an AWS CloudTrail trail resource
#       And: 'CloudWatchLogsLogGroupArn' is set to a non-empty string or a valid local reference to a log group
#       And: 'CloudWatchLogsRoleArn' is set to an empty string or a non-valid local reference
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an AWS CloudTrail trail resource
#       And: 'CloudWatchLogsLogGroupArn' is set to an empty string or an invalid local reference
#       And: 'CloudWatchLogsRoleArn' is set to a non-empty string or a valid local reference to an IAM role
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an AWS CloudTrail trail resource
#       And: 'CloudWatchLogsRoleArn' is set to a non-empty string or a valid local reference to an IAM role
#       And: 'CloudWatchLogsLogGroupArn' is set to a non-empty string or a valid local reference to a log group
#      Then: PASS

#
# Constants
#
let CLOUDTRAIL_TRAIL_TYPE = "AWS::CloudTrail::Trail"
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudtrail_trails = Resources.*[ Type == %CLOUDTRAIL_TRAIL_TYPE ]

#
# Primary Rules
#
rule cloud_trail_cloud_watch_logs_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                     %cloudtrail_trails not empty {
    check_cloudtrail_log_group_configuration(%cloudtrail_trails.Properties)
        <<
        [CT.CLOUDTRAIL.PR.3]: Require an AWS CloudTrail trail to have an CloudTrail log group configuration
        [FIX]: Set the 'CloudWatchLogsLogGroupArn' and 'CloudWatchLogsRoleArn' properties.
        >>
}

rule cloud_trail_cloud_watch_logs_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDTRAIL_TRAIL_TYPE) {
    check_cloudtrail_log_group_configuration(%INPUT_DOCUMENT.%CLOUDTRAIL_TRAIL_TYPE.resourceProperties)
        <<
        [CT.CLOUDTRAIL.PR.3]: Require an AWS CloudTrail trail to have an CloudTrail log group configuration
        [FIX]: Set the 'CloudWatchLogsLogGroupArn' and 'CloudWatchLogsRoleArn' properties.
        >>
}

#
# Parameterized Rules
#
rule check_cloudtrail_log_group_configuration(cloudtrail_trail) {
    %cloudtrail_trail {
        # Scenario 2
        CloudWatchLogsLogGroupArn exists
        CloudWatchLogsRoleArn exists

        # Scenario 3, 4 and 5
        check_cloudwatch_log_group_arn(CloudWatchLogsLogGroupArn)
        check_cloudwatch_log_role_arn(CloudWatchLogsRoleArn)
    }
}

rule check_cloudwatch_log_group_arn(log_group) {
   %log_group {
      check_is_string_and_not_empty(this) or
      check_local_references(%INPUT_DOCUMENT, this, "AWS::Logs::LogGroup")
    }
}

rule check_cloudwatch_log_role_arn(log_role) {
   %log_role {
      check_is_string_and_not_empty(this) or
      check_local_references(%INPUT_DOCUMENT, this, "AWS::IAM::Role")
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

rule check_local_references(doc, reference_properties, referenced_RESOURCE_TYPE) {
    %reference_properties {
        'Fn::GetAtt' {
            query_for_resource(%doc, this[0], %referenced_RESOURCE_TYPE)
                <<Local Stack reference was invalid>>
        } or Ref {
            query_for_resource(%doc, this, %referenced_RESOURCE_TYPE)
                <<Local Stack reference was invalid>>
        }
    }
}

rule query_for_resource(doc, resource_key, referenced_RESOURCE_TYPE) {
    let referenced_resource = %doc.Resources[ keys == %resource_key ]
    %referenced_resource not empty
    %referenced_resource {
        Type == %referenced_RESOURCE_TYPE
    }
}
```

### CT\.CLOUDTRAIL\.PR\.3 example templates<a name="ct-cloudtrail-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  LoggingBucket:
    Type: AWS::S3::Bucket
  LoggingBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: LoggingBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetBucketAcl'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
          - Action:
              - 's3:PutObject'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
                  - /AWSLogs/
                  - Ref: AWS::AccountId
                  - /*
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                's3:x-amz-acl': 'bucket-owner-full-control'
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
  CloudWatchLogsRole:
    Type: "AWS::IAM::Role"
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Sid: AssumeRole
          Effect: Allow
          Principal:
            Service: 'cloudtrail.amazonaws.com'
          Action: 'sts:AssumeRole'
      Policies:
      - PolicyName: 'cloudtrail-policy'
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - 'logs:CreateLogStream'
            - 'logs:PutLogEvents'
            Resource: 
              Fn::GetAtt: [LogGroup, Arn]
  LogGroup:
    Type: AWS::Logs::LogGroup
    Properties: {}
  CloudTrail:
    Type: AWS::CloudTrail::Trail
    Properties:
      IsLogging: true
      TrailName:
        Fn::Sub: ${AWS::StackName}-example-trail
      S3BucketName:
        Ref: LoggingBucket
      CloudWatchLogsRoleArn:
        Fn::GetAtt:
        - CloudWatchLogsRole
        - Arn
      CloudWatchLogsLogGroupArn:
        Fn::GetAtt:
        - LogGroup
        - Arn
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  LoggingBucket:
    Type: AWS::S3::Bucket
  LoggingBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: LoggingBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetBucketAcl'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
          - Action:
              - 's3:PutObject'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - 'arn:aws:s3:::'
                  - Ref: LoggingBucket
                  - /AWSLogs/
                  - Ref: AWS::AccountId
                  - /*
            Principal:
              Service: "cloudtrail.amazonaws.com"
            Condition:
              StringEquals:
                's3:x-amz-acl': 'bucket-owner-full-control'
                "aws:SourceArn": 
                  Fn::Sub: "arn:aws:cloudtrail:${AWS::Region}:${AWS::AccountId}:trail/${AWS::StackName}-example-trail"
  CloudTrail:
    Type: AWS::CloudTrail::Trail
    Properties:
      IsLogging: true
      TrailName:
        Fn::Sub: ${AWS::StackName}-example-trail
      S3BucketName:
        Ref: LoggingBucket
```