# AWS Key Management Service \(AWS KMS\) controls<a name="kms-rules"></a>

**Topics**
+ [\[CT\.KMS\.PR\.1\] Require any AWS KMS key to have rotation configured](#ct-kms-pr-1-description)

## \[CT\.KMS\.PR\.1\] Require any AWS KMS key to have rotation configured<a name="ct-kms-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether key rotation is enabled for AWS KMS customer managed keys\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::KMS::Key`
+ **AWS CloudFormation guard rule: ** [CT\.KMS\.PR\.1 rule specification](#ct-kms-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.KMS\.PR\.1 rule specification](#ct-kms-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.KMS.PR.1) 

**Explanation**

Key rotation minimizes the possibility of key exposure to malicious users\. Cryptographic best practices discourage extensive reuse of encryption keys\. Rotation of keys on regular basis helps you meet organizational security and compliance requirements\.

**Usage considerations**  
This control applies only to AWS KMS symmetric\-encryption, customer managed keys\.

### Remediation for rule failure<a name="ct-kms-pr-1-remediation"></a>

Set `EnableKeyRotation` to `true` for AWS KMS symmetric\-encryption keys\.

The examples that follow show how to implement this remediation\.

#### AWS KMS key \- Example<a name="ct-kms-pr-1-remediation-1"></a>

AWS KMS customer managed key configured with key rotation activated\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "KMSKey": {
        "Type": "AWS::KMS::Key",
        "Properties": {
            "PendingWindowInDays": 7,
            "KeyPolicy": {
                "Version": "2012-10-17",
                "Id": "sample-policy",
                "Statement": [
                    {
                        "Sid": "Enable IAM User Permissions",
                        "Effect": "Allow",
                        "Principal": {
                            "AWS": {
                                "Fn::Sub": "arn:${AWS::Partition}:iam::${AWS::AccountId}:root"
                            }
                        },
                        "Action": "kms:*",
                        "Resource": "*"
                    }
                ]
            },
            "EnableKeyRotation": true
        }
    }
}
```

**YAML example**

```
KMSKey:
  Type: AWS::KMS::Key
  Properties:
    PendingWindowInDays: 7
    KeyPolicy:
      Version: 2012-10-17
      Id: sample-policy
      Statement:
        - Sid: Enable IAM User Permissions
          Effect: Allow
          Principal:
            AWS: !Sub 'arn:${AWS::Partition}:iam::${AWS::AccountId}:root'
          Action: kms:*
          Resource: '*'
    EnableKeyRotation: true
```

### CT\.KMS\.PR\.1 rule specification<a name="ct-kms-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   kms_key_rotation_enabled_check
# 
# Description:
#   This control checks whether key rotation is enabled for AWS KMS customer managed keys.
# 
# Reports on:
#   AWS::KMS::Key
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
#       And: The input document does not contain any KMS key resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a KMS key resource
#       And: 'KeySpec' is provided and is a value other than 'SYMMETRIC_DEFAULT'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a KMS key resource
#       And: 'KeySpec' is not provided or is provided and is set to 'SYMMETRIC_DEFAULT'
#       And: 'EnableKeyRotation' is not provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a KMS key resource
#       And: 'KeySpec' is not provided or is provided and is set to 'SYMMETRIC_DEFAULT'
#       And: 'EnableKeyRotation' is provided and is set to bool(false)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a KMS key resource
#       And: 'KeySpec' is not provided or is provided and is set to 'SYMMETRIC_DEFAULT'
#       And: 'EnableKeyRotation' is provided and is set to bool(true)
#      Then: PASS

#
# Constants
#
let KMS_KEY_TYPE = "AWS::KMS::Key"
let INPUT_DOCUMENT = this

#
# Assignments
#
let kms_keys = Resources.*[ Type == %KMS_KEY_TYPE ]

#
# Primary Rules
#
rule kms_key_rotation_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                         %kms_keys not empty {
    check(%kms_keys.Properties)
        <<
        [CT.KMS.PR.1]: Require any AWS KMS key to have rotation configured
        [FIX]: Set 'EnableKeyRotation' to 'true' for AWS KMS symmetric-encryption keys.
        >>
}

rule kms_key_rotation_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %KMS_KEY_TYPE) {
    check(%INPUT_DOCUMENT.%KMS_KEY_TYPE.resourceProperties)
        <<
        [CT.KMS.PR.1]: Require any AWS KMS key to have rotation configured
        [FIX]: Set 'EnableKeyRotation' to 'true' for AWS KMS symmetric-encryption keys.
        >>
}

#
# Parameterized Rules
#
rule check(kms_keys) {
    %kms_keys[
        # Scenario 2
        filter_is_kms_cmk_symmetric_key(this)
    ] {
        # Scenario 3, 4 and 5
        EnableKeyRotation exists
        EnableKeyRotation == true
    }
}

rule filter_is_kms_cmk_symmetric_key(kms_key) {
    %kms_key {
        KeySpec not exists or
        KeySpec == "SYMMETRIC_DEFAULT"
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