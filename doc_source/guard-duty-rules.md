# Amazon GuardDuty controls<a name="guard-duty-rules"></a>

**Topics**
+ [\[CT\.GUARDDUTY\.PR\.1\] Require an Amazon GuardDuty detector to have Amazon S3 protection activated](#ct-guardduty-pr-1-description)

## \[CT\.GUARDDUTY\.PR\.1\] Require an Amazon GuardDuty detector to have Amazon S3 protection activated<a name="ct-guardduty-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether Amazon S3 protection is enabled on an Amazon GuardDuty detector\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::GuardDuty::Detector`
+ **AWS CloudFormation guard rule: ** [CT\.GUARDDUTY\.PR\.1 rule specification](#ct-guardduty-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.GUARDDUTY\.PR\.1 rule specification](#ct-guardduty-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.GUARDDUTY.PR.1) 

**Explanation**

Amazon GuardDuty monitors threats against your Amazon S3 resources by analyzing AWS CloudTrail management events and CloudTrail S3 data events\. These data sources monitor different kinds of activity\. For example, CloudTrail management events for S3 include operations that list or configure S3 buckets, such as `ListBuckets`, `DeleteBuckets`, and `PutBucketReplication`\. Examples of data events for S3 include object\-level API operations, such as `GetObject`, `ListObjects`, `DeleteObject`, and `PutObject`\.

Amazon GuardDuty monitoring of AWS CloudTrail management events is on by default for all accounts that have enabled GuardDuty, and it is not configurable\. Amazon S3 data event logs are a configurable data source in GuardDuty\.

AWS Control Tower recommends that you enable Amazon S3 protection in GuardDuty\. If the feature is not enabled, GuardDuty cannot fully monitor or generate findings for suspicious access to data stored in your Amazon S3 buckets\.

### Remediation for rule failure<a name="ct-guardduty-pr-1-remediation"></a>

Set `DataSources.S3Logs` to true\.

The examples that follow show how to implement this remediation\.

#### GuardDuty Detector \- Example<a name="ct-guardduty-pr-1-remediation-1"></a>

Amazon GuardDuty detector with Amazon S3 protection enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "GuardDutyDetector": {
        "Type": "AWS::GuardDuty::Detector",
        "Properties": {
            "Enable": true,
            "DataSources": {
                "S3Logs": {
                    "Enable": true
                }
            }
        }
    }
}
```

**YAML example**

```
GuardDutyDetector:
  Type: AWS::GuardDuty::Detector
  Properties:
    Enable: true
    DataSources:
      S3Logs:
        Enable: true
```

### CT\.GUARDDUTY\.PR\.1 rule specification<a name="ct-guardduty-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   guardduty_s3_protection_enabled_check
# 
# Description:
#   Checks if Amazon S3 protection is enabled on an Amazon GuardDuty detector.
# 
# Reports on:
#   AWS::GuardDuty::Detector
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#      Given: The input document is an AWS CloudFormation or CloudFormation hook document
#        And: The input document does not contain any Amazon GuardDuty detector resources
#       Then: SKIP
#   Scenario: 2
#      Given: The input document is an AWS CloudFormation or CloudFormation hook document
#        And: The input document contains a GuardDuty detector resource
#        And: 'Enable' has not been specified or specified with value is bool(false)
#       Then: FAIL
#   Scenario: 3
#      Given: The input document is an AWS CloudFormation or CloudFormation hook document
#        And: The input document contains a GuardDuty detector resource
#        And: 'Enable' is specified with a value of bool(true)
#        And: 'DataSources.S3Logs' has not been specified
#       Then: FAIL
#   Scenario: 4
#      Given: The input document is an AWS CloudFormation or CloudFormation hook document
#        And: The input document contains a GuardDuty detector resource
#        And: 'Enable' is specified and value is bool(true)
#        And: 'DataSources.S3Logs' has been specified
#        And: 'Enable' has not been specified within 'S3Logs' or has been specified with a value of bool(false)
#       Then: FAIL
#   Scenario: 5
#      Given: The input document is an AWS CloudFormation or CloudFormation hook document
#        And: The input document contains a GuardDuty detector resource
#        And: 'Enable' is specified and value is bool(true)
#        And: 'DataSources.S3Logs' has been specified
#        And: 'Enable' has been specified within 'S3Logs' with a value of bool(true)
#       Then: PASS

#
# Constants
#
let GUARDDUTY_DETECTOR_TYPE = "AWS::GuardDuty::Detector"
let INPUT_DOCUMENT = this

#
# Assignments
#
let guardduty_detectors = Resources.*[ Type == %GUARDDUTY_DETECTOR_TYPE ]

#
# Primary Rules
#
rule guardduty_s3_protection_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                %guardduty_detectors not empty {
    check(%guardduty_detectors.Properties)
        <<
        [CT.GUARDDUTY.PR.1]: Require an Amazon GuardDuty detector to have Amazon S3 protection activated
        [FIX]: Set 'DataSources.S3Logs' to true.
        >>
}

rule guardduty_s3_protection_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %GUARDDUTY_DETECTOR_TYPE) {
    check(%INPUT_DOCUMENT.%GUARDDUTY_DETECTOR_TYPE.resourceProperties)
        <<
        [CT.GUARDDUTY.PR.1]: Require an Amazon GuardDuty detector to have Amazon S3 protection activated
        [FIX]: Set 'DataSources.S3Logs' to true.
        >>
}

#
# Parameterized Rules
#
rule check(guardduty_detector) {
    %guardduty_detector {
        # Scenario: 2
        Enable exists
        Enable == true
        # Scenario: 3
        DataSources exists
        DataSources is_struct
        DataSources {
            # Scenario: 4 and 5
            S3Logs exists
            S3Logs is_struct
            S3Logs {
                Enable exists
                Enable == true
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