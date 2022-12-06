# Amazon Simple Queue Service \(SQS\) controls<a name="sqs-rules"></a>

**Topics**
+ [\[CT\.SQS\.PR\.1\] Require any Amazon SQS queue to have a dead\-letter queue configured](#ct-sqs-pr-1-description)
+ [\[CT\.SQS\.PR\.2\] Require any Amazon SQS queue to have encryption at rest configured](#ct-sqs-pr-2-description)

## \[CT\.SQS\.PR\.1\] Require any Amazon SQS queue to have a dead\-letter queue configured<a name="ct-sqs-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether an Amazon SQS queue is configured with a dead\-letter queue\.
+ **Control objective: **Improve resiliency
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::SQS::Queue`
+ **AWS CloudFormation guard rule: ** [CT\.SQS\.PR\.1 rule specification](#ct-sqs-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.SQS\.PR\.1 rule specification](#ct-sqs-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.SQS\.PR\.1 example templates](#ct-sqs-pr-1-templates) 

**Explanation**

The main task of a dead\-letter queue is to handle the lifecycle of unconsumed messages\. A dead\-letter queue lets you set aside and isolate messages that can`t be processed correctly, so you can determine why their processing didn`t succeed\.

**Usage considerations**  
This control applies only to Amazon SQS queues that are not configured as a dead\-letter queue with a `RedriveAllowPolicy` property\.

### Remediation for rule failure<a name="ct-sqs-pr-1-remediation"></a>

Create a `RedrivePolicy` with a `deadLetterTargetArn` value that`s set to the ARN of an Amazon SQS dead-letter queue. For Amazon SQS dead-letter queues, instead provide a redrive configuration in the `RedriveAllowPolicy' property\.

The examples that follow show how to implement this remediation\.

#### Amazon SQS Queue \- Example<a name="ct-sqs-pr-1-remediation-1"></a>

Amazon SQS queue configured to send messages to a dead\-letter queue, if the messages can't be processed \(consumed\) successfully\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "SQSQueue": {
        "Type": "AWS::SQS::Queue",
        "Properties": {
            "RedrivePolicy": {
                "deadLetterTargetArn": {
                    "Fn::GetAtt": [
                        "DLQQueue",
                        "Arn"
                    ]
                },
                "maxReceiveCount": 3
            }
        }
    }
}
```

**YAML example**

```
SQSQueue:
  Type: AWS::SQS::Queue
  Properties:
    RedrivePolicy:
      deadLetterTargetArn: !GetAtt 'DLQQueue.Arn'
      maxReceiveCount: 3
```

### CT\.SQS\.PR\.1 rule specification<a name="ct-sqs-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#    sqs_dlq_check
# 
# Description:
#   This control checks whether an Amazon SQS queue is configured with a dead-letter queue.
# 
# Reports on:
#    AWS::SQS::Queue
# 
# Evaluates:
#    AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#     None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any SQS queue resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SQS queue resource
#       And: 'RedriveAllowPolicy' has been provided on the SQS queue
#       And: 'RedriveAllowPolicy.redrivePermission' is set to 'allowAll' or 'byQueue'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SQS queue resource
#       And: 'RedriveAllowPolicy' has not been provided on the SQS queue or 'RedriveAllowPolicy.redrivePermission'
#            has been provided and is set to a value other than 'allowAll' or 'byQueue'
#       And: 'RedrivePolicy' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SQS queue resource
#       And: 'RedriveAllowPolicy' has not been provided on the SQS queue or 'RedriveAllowPolicy.redrivePermission'
#            has been provided and is set to a value other than 'allowAll' or 'byQueue'
#       And: 'RedrivePolicy' has been provided
#       And: 'RedrivePolicy.deadLetterTargetArn' has not been provided or has been provided as an empty string or
#            invalid local reference to an SQS queue
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SQS queue resource
#       And: 'RedriveAllowPolicy' has not been provided on the SQS queue or 'RedriveAllowPolicy.redrivePermission' has
#            been provided and is set to a value other than 'allowAll' or 'byQueue'
#       And: 'RedrivePolicy' has been provided
#       And: 'RedrivePolicy.deadLetterTargetArn' has been provided as a non-empty string or valid local reference to
#             an SQS queue
#      Then: PASS

#
# Constants
#
let SQS_QUEUE_TYPE = "AWS::SQS::Queue"
let INPUT_DOCUMENT = this
let DLQ_REDRIVE_PERMISSION = ["allowAll", "byQueue"]

#
# Assignments
#
let sqs_queues = Resources.*[ Type == %SQS_QUEUE_TYPE ]

#
# Primary Rules
#
rule sqs_dlq_check when is_cfn_template(%INPUT_DOCUMENT)
                        %sqs_queues not empty {
    check(%sqs_queues.Properties)
        <<
        [CT.SQS.PR.1]: Require any Amazon SQS queue to have a dead-letter queue configured
            [FIX]: Create a 'RedrivePolicy' with a 'deadLetterTargetArn' value that's set to the ARN of an Amazon SQS dead-letter queue. For Amazon SQS dead-letter queues, instead provide a redrive configuration in the 'RedriveAllowPolicy' property.
        >>
}

rule sqs_dlq_check when is_cfn_hook(%INPUT_DOCUMENT, %SQS_QUEUE_TYPE) {
    check(%INPUT_DOCUMENT.%SQS_QUEUE_TYPE.resourceProperties)
        <<
        [CT.SQS.PR.1]: Require any Amazon SQS queue to have a dead-letter queue configured
            [FIX]: Create a 'RedrivePolicy' with a 'deadLetterTargetArn' value that's set to the ARN of an Amazon SQS dead-letter queue. For Amazon SQS dead-letter queues, instead provide a redrive configuration in the 'RedriveAllowPolicy' property.
        >>
}

#
# Parameterized Rules
#
rule check(sqs_queues) {
    %sqs_queues [
        # Scenario 2
        RedriveAllowPolicy not exists or
        filter_is_not_dlq(this)
    ] {
        # Scenario 3
        RedrivePolicy exists
        RedrivePolicy is_struct

        # Scenario 4
        RedrivePolicy {
            deadLetterTargetArn exists

            check_is_string_and_not_empty(deadLetterTargetArn) or
            check_local_references(%INPUT_DOCUMENT, deadLetterTargetArn, %SQS_QUEUE_TYPE)
        }
    }
}

rule filter_is_not_dlq(sqs_queue) {
    RedriveAllowPolicy exists
    RedriveAllowPolicy is_struct

    RedriveAllowPolicy {
        redrivePermission exists
        redrivePermission not in %DLQ_REDRIVE_PERMISSION
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

rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
}

rule check_local_references(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        "Fn::GetAtt" {
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

### CT\.SQS\.PR\.1 example templates<a name="ct-sqs-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  SQSQueue:
    Type: AWS::SQS::Queue
    Properties:
      RedrivePolicy:
        deadLetterTargetArn:
          Fn::GetAtt: [DLQQueue, Arn]
        maxReceiveCount: 3
  DLQQueue:
    Type: AWS::SQS::Queue
    Properties:
      RedriveAllowPolicy:
        redrivePermission: allowAll
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  SQSQueue:
    Type: AWS::SQS::Queue
    Properties: {}
```

## \[CT\.SQS\.PR\.2\] Require any Amazon SQS queue to have encryption at rest configured<a name="ct-sqs-pr-2-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether an Amazon SQS queue is encrypted at rest\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::SQS::Queue`
+ **AWS CloudFormation guard rule: ** [CT\.SQS\.PR\.2 rule specification](#ct-sqs-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.SQS\.PR\.2 rule specification](#ct-sqs-pr-2-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.SQS\.PR\.2 example templates](#ct-sqs-pr-2-templates) 

**Explanation**

Server\-side encryption \(SSE\) allows you to transmit sensitive data in encrypted queues\. To protect the content of messages in queues, SSE uses KMS keys\.

### Remediation for rule failure<a name="ct-sqs-pr-2-remediation"></a>

Set `SqsManagedSseEnabled` to `true` or set an AWS KMS key identifier in the `KmsMasterKeyId` property\.

The examples that follow show how to implement this remediation\.

#### Amazon SQS Queue \- Example One<a name="ct-sqs-pr-2-remediation-1"></a>

Amazon SQS queue configured to encrypt data at rest with server\-side encryption enabled, by means of SQS managed encryption keys \(SSE\-SQS\)\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "SQSQueue": {
        "Type": "AWS::SQS::Queue",
        "Properties": {
            "SqsManagedSseEnabled": true
        }
    }
}
```

**YAML example**

```
SQSQueue:
  Type: AWS::SQS::Queue
  Properties:
    SqsManagedSseEnabled: true
```

The examples that follow show how to implement this remediation\.

#### Amazon SQS Queue \- Example Two<a name="ct-sqs-pr-2-remediation-2"></a>

Amazon SQS queue configured to encrypt data at rest with server\-side encryption enabled, by means of AWS KMS \(SSE\-KMS\)\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "SQSQueue": {
        "Type": "AWS::SQS::Queue",
        "Properties": {
            "KmsMasterKeyId": {
                "Ref": "KMSKey"
            }
        }
    }
}
```

**YAML example**

```
SQSQueue:
  Type: AWS::SQS::Queue
  Properties:
    KmsMasterKeyId: !Ref 'KMSKey'
```

### CT\.SQS\.PR\.2 rule specification<a name="ct-sqs-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#    sqs_queue_encrypted_check
# 
# Description:
#   This control checks whether an Amazon SQS queue is encrypted at rest.
# 
# Reports on:
#    AWS::SQS::Queue
# 
# Evaluates:
#    AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#     None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any SQS queue resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SQS queue resource
#       And: 'KmsMasterKeyId' or 'SqsManagedSseEnabled' have not been provided on the SQS queue resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SQS queue resource
#       And: 'KmsMasterKeyId' has not been provided
#       And: 'SqsManagedSseEnabled' has been provided and set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SQS queue resource
#       And: 'KmsMasterKeyId' has been provided as an empty string or invalid local reference to a KMS key or alias
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SQS queue resource
#       And: 'SqsManagedSseEnabled' is not provided or set to bool(false)
#       And: 'KmsMasterKeyId' is provided as a non-empty string or local reference to a KMS key
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SQS queue resource
#       And: 'KmsMasterKeyId' is not provided
#       And: 'SqsManagedSseEnabled' is provided and set to bool(true)
#      Then: PASS

#
# Constants
#
let SQS_QUEUE_TYPE = "AWS::SQS::Queue"
let INPUT_DOCUMENT = this
#
# Assignments
#
let sqs_queues = Resources.*[ Type == %SQS_QUEUE_TYPE ]

#
# Primary Rules
#
rule sqs_queue_encrypted_check when is_cfn_template(%INPUT_DOCUMENT)
                                    %sqs_queues not empty {
    check(%sqs_queues.Properties)
        <<
        [CT.SQS.PR.2]: Require any Amazon SQS queue to have encryption at rest configured
        [FIX]: Set 'SqsManagedSseEnabled' to 'true' or set an AWS KMS key identifier in the 'KmsMasterKeyId' property.
        >>
}

rule sqs_queue_encrypted_check when is_cfn_hook(%INPUT_DOCUMENT, %SQS_QUEUE_TYPE) {
    check(%INPUT_DOCUMENT.%SQS_QUEUE_TYPE.resourceProperties)
        <<
        [CT.SQS.PR.2]: Require any Amazon SQS queue to have encryption at rest configured
        [FIX]: Set 'SqsManagedSseEnabled' to 'true' or set an AWS KMS key identifier in the 'KmsMasterKeyId' property.
        >>
}

#
# Parameterized Rules
#
rule check(sqs_queue) {
    %sqs_queue{
        check_sse_enabled(this) or
        check_kms_valid(this)
    }
}

rule check_sse_enabled(sqs_queue) {
    # Scenario 2
    SqsManagedSseEnabled exists

    # Scenario 3, 6
    KmsMasterKeyId not exists
    SqsManagedSseEnabled == true
}

rule check_kms_valid(sqs_queue) {
    # Scenario 2
    KmsMasterKeyId exists

    # Scenario 4, 5
    check_is_string_and_not_empty(KmsMasterKeyId) or
    check_local_references(%INPUT_DOCUMENT, KmsMasterKeyId, "AWS::KMS::Key") or
    check_local_references(%INPUT_DOCUMENT, KmsMasterKeyId, "AWS::KMS::Alias")

    # Scenario 5
    SqsManagedSseEnabled not exists or
    SqsManagedSseEnabled == false
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

rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
}

rule check_local_references(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        "Fn::GetAtt" {
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

### CT\.SQS\.PR\.2 example templates<a name="ct-sqs-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  KMSKey:
    Type: AWS::KMS::Key
    Properties:
      KeyPolicy:
        Version: 2012-10-17
        Id: key-default-1
        Statement:
        - Sid: Enable IAM User Permissions
          Effect: Allow
          Principal:
            AWS: '*'
          Action: 'kms:*'
          Resource: '*'
  SQSQueue:
    Type: AWS::SQS::Queue
    Properties:
      KmsMasterKeyId:
        Ref: KMSKey
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  SQSQueue:
    Type: AWS::SQS::Queue
    Properties: {}
```