# DynamoDB Accelerator controls<a name="dax-rules"></a>

**Topics**
+ [\[CT\.DAX\.PR\.1\] Require encryption at rest for all Amazon DynamoDB Accelerator \(DAX\) clusters](#ct-dax-pr-1-description)

## \[CT\.DAX\.PR\.1\] Require encryption at rest for all Amazon DynamoDB Accelerator \(DAX\) clusters<a name="ct-dax-pr-1-description"></a>

This control checks whether Amazon DynamoDB Accelerator \(DAX\) clusters are encrypted at rest\.

**Note**  
The control CT\.DAX\.PR\.1 cannot be activated from home Regions Canada \(Central\) Region, Europe \(Stockholm\) Region, and Asia Pacific \(Seoul\) Region, because the `AWS::DAX::Cluster` resource type is not available in those Regions\. If your home Region is not one of these three, you can activate the control for these three Regions from another home Region, if these three Regions are governed by AWS Control Tower in your landing zone\. For example, if your home Region is US West \(Oregon\) Region, you can deploy the control to Canada \(Central\) Region, if Canada \(Central\) Region is governed by AWS Control Tower\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::DAX::Cluster`
+ **AWS CloudFormation guard rule: ** [CT\.DAX\.PR\.1 rule specification](#ct-dax-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.DAX\.PR\.1 rule specification](#ct-dax-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.DAX\.PR\.1 example templates](#ct-dax-pr-1-templates) 

**Explanation**

Encrypting data at rest reduces the risk that data stored on disk may be accessible to a user who is not authenticated to AWS\. Encryption adds another set of access controls, which limits the ability of unauthorized users to gain access to the data\. For example, API permissions must decrypt the data before it can be read\.

### Remediation for rule failure<a name="ct-dax-pr-1-remediation"></a>

Provide an `SSESpecification` configuration with `SSEEnabled` set to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon DAX Cluster \- Example<a name="ct-dax-pr-1-remediation-1"></a>

Amazon DAX cluster configured with server\-side encryption enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DAXCluster": {
        "Type": "AWS::DAX::Cluster",
        "Properties": {
            "IAMRoleARN": {
                "Fn::GetAtt": [
                    "DAXServiceRole",
                    "Arn"
                ]
            },
            "NodeType": "dax.t3.small",
            "ReplicationFactor": 1,
            "SSESpecification": {
                "SSEEnabled": true
            }
        }
    }
}
```

**YAML example**

```
DAXCluster:
  Type: AWS::DAX::Cluster
  Properties:
    IAMRoleARN: !GetAtt 'DAXServiceRole.Arn'
    NodeType: dax.t3.small
    ReplicationFactor: 1
    SSESpecification:
      SSEEnabled: true
```

### CT\.DAX\.PR\.1 rule specification<a name="ct-dax-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   dax_cluster_encryption_enabled_check
# 
# Description:
#   This control checks whether Amazon DynamoDB Accelerator (DAX) clusters are encrypted at rest.
# 
# Reports on:
#   AWS::DAX::Cluster
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
#       And: The input document does not contain any DAX Cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains at least one DAX Cluster resource
#       And: 'SSESpecification' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains at least one DAX Cluster resource
#       And: 'SSESpecification' has been provided and 'SSESpecification.SSEEnabled' is missing or has been set to a
#            value other than bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains at least one DAX Cluster resource
#       And: 'SSESpecification' has been provided and 'SSESpecification.SSEEnabled' is present and has been set to
#            bool(true)
#      Then: PASS

#
# Constants
#
let DAX_CLUSTER_TYPE = "AWS::DAX::Cluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let dax_clusters = Resources.*[ Type == %DAX_CLUSTER_TYPE ]

#
# Primary Rules
#
rule dax_cluster_encryption_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %dax_clusters not empty {
    check(%dax_clusters.Properties)
        <<
        [CT.DAX.PR.1]: Require encryption at rest for all Amazon DynamoDB Accelerator (DAX) clusters
            [FIX]: Provide an 'SSESpecification' configuration with 'SSEEnabled' set to 'true'.
        >>
}

rule dax_cluster_encryption_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %DAX_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%DAX_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.DAX.PR.1]: Require encryption at rest for all Amazon DynamoDB Accelerator (DAX) clusters
            [FIX]: Provide an 'SSESpecification' configuration with 'SSEEnabled' set to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(dax_cluster) {
    %dax_cluster {
        # Scenario 2
        SSESpecification exists
        SSESpecification is_struct

        # Scenario 3 and 4
        SSESpecification {
            SSEEnabled exists
            SSEEnabled == true
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

### CT\.DAX\.PR\.1 example templates<a name="ct-dax-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DAXServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: dax.amazonaws.com
          Action: sts:AssumeRole
      Path: /
      Policies:
      - PolicyName: DynamoAccessPolicy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - dynamodb:DescribeTable
            - dynamodb:PutItem
            - dynamodb:GetItem
            - dynamodb:UpdateItem
            - dynamodb:DeleteItem
            - dynamodb:Query
            - dynamodb:Scan
            - dynamodb:BatchGetItem
            - dynamodb:BatchWriteItem
            - dynamodb:ConditionCheckItem
            Resource:
              Fn::Sub: arn:${AWS::Partition}:dynamodb:${AWS::Region}:${AWS::AccountId}:*
  DAXCluster:
    Type: AWS::DAX::Cluster
    Properties:
      IAMRoleARN:
        Fn::GetAtt: [DAXServiceRole, Arn]
      NodeType: dax.t3.small
      ReplicationFactor: 1
      SSESpecification:
        SSEEnabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DAXServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: dax.amazonaws.com
          Action: sts:AssumeRole
      Path: /
      Policies:
      - PolicyName: DynamoAccessPolicy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - dynamodb:DescribeTable
            - dynamodb:PutItem
            - dynamodb:GetItem
            - dynamodb:UpdateItem
            - dynamodb:DeleteItem
            - dynamodb:Query
            - dynamodb:Scan
            - dynamodb:BatchGetItem
            - dynamodb:BatchWriteItem
            - dynamodb:ConditionCheckItem
            Resource:
              Fn::Sub: arn:${AWS::Partition}:dynamodb:${AWS::Region}:${AWS::AccountId}:*
  DAXCluster:
    Type: AWS::DAX::Cluster
    Properties:
      IAMRoleARN:
        Fn::GetAtt: [DAXServiceRole, Arn]
      NodeType: dax.t3.small
      ReplicationFactor: 1
      SSESpecification:
        SSEEnabled: false
```