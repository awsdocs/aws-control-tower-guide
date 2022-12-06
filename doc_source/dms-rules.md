# AWS Database Migration Service \(DMS\) controls<a name="dms-rules"></a>

**Topics**
+ [\[CT\.DMS\.PR\.1\] Require that a public AWS DMS replication instance is not public](#ct-dms-pr-1-description)

## \[CT\.DMS\.PR\.1\] Require that a public AWS DMS replication instance is not public<a name="ct-dms-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your AWS DMS replication instance is public\.
+ **Control objective: **Limit network access, Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::DMS::ReplicationInstance`
+ **AWS CloudFormation guard rule: ** [CT\.DMS\.PR\.1 rule specification](#ct-dms-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.DMS\.PR\.1 rule specification](#ct-dms-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.DMS.PR.1) 

**Explanation**

A private replication instance has a private IP address that you cannot access outside of the replication network\. You use a private instance when both source and target databases are in the same network that is connected to the replication instance's VPC\. The network can be connected to the VPC by using a VPN, AWS Direct Connect, or VPC peering\.

### Remediation for rule failure<a name="ct-dms-pr-1-remediation"></a>

Set `PubliclyAccessible` to `false`\.

The examples that follow show how to implement this remediation\.

#### AWS DMS Replication Instance \- Example<a name="ct-dms-pr-1-remediation-1"></a>

AWS DMS replication instance configured with public access disabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DMSReplicationInstance": {
        "Type": "AWS::DMS::ReplicationInstance",
        "Properties": {
            "ReplicationInstanceClass": "dms.t3.micro",
            "PubliclyAccessible": false
        }
    }
}
```

**YAML example**

```
DMSReplicationInstance:
  Type: AWS::DMS::ReplicationInstance
  Properties:
    ReplicationInstanceClass: dms.t3.micro
    PubliclyAccessible: false
```

### CT\.DMS\.PR\.1 rule specification<a name="ct-dms-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# Rule Identifier:
#   dms_replication_instance_not_public_check
# 
# Description:
#   This control checks whether your AWS DMS replication instance is public.
# 
# Reports on:
#   AWS::DMS::ReplicationInstance
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
#       And: The input document does not contain any AWS DMS replication instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a AWS DMS replication instance resource
#       And: 'PubliclyAccessible' is not present on the AWS DMS replication instance
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a AWS DMS replication instance resource
#       And: 'PubliclyAccessible' is present on the AWS DMS replication instance
#            and is set to bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a AWS DMS replication instance resource
#       And: 'PubliclyAccessible' is present on the AWS DMS replication instance
#            and is set to bool(false)
#      Then: PASS

#
# Constants
#
let DMS_REPLICATION_INSTANCE_TYPE = "AWS::DMS::ReplicationInstance"
let INPUT_DOCUMENT = this

#
# Assignments
#
let dms_replication_instances = Resources.*[ Type == %DMS_REPLICATION_INSTANCE_TYPE ]

#
# Primary Rules
#
rule dms_replication_instance_not_public_check when is_cfn_template(%INPUT_DOCUMENT)
                                                    %dms_replication_instances not empty {
    check(%dms_replication_instances.Properties)
        <<
        [CT.DMS.PR.1]: Require that a public AWS DMS replication instance is not public
        [FIX]: Set 'PubliclyAccessible' to 'false'.
        >>
}

rule dms_replication_instance_not_public_check when is_cfn_hook(%INPUT_DOCUMENT, %DMS_REPLICATION_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%DMS_REPLICATION_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.DMS.PR.1]: Require that a public AWS DMS replication instance is not public
        [FIX]: Set 'PubliclyAccessible' to 'false'.
        >>
}

#
# Parameterized Rules
#
rule check(dms_replication_instances) {
    %dms_replication_instances {
        # Scenario 2
        PubliclyAccessible exists
        # Scenario 3 and 4
        PubliclyAccessible == false
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