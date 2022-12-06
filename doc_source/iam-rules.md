# AWS Identity and Access Management \(IAM\) controls<a name="iam-rules"></a>

**Topics**
+ [\[CT\.IAM\.PR\.1\] Require that an AWS Identity and Access Management \(IAM\) inline policy does not have a statement that includes "\*" in the Action and Resource elements](#ct-iam-pr-1-description)
+ [\[CT\.IAM\.PR\.2\] Require that AWS Identity and Access Management \(IAM\) customer\-managed policies do not contain a statement that includes "\*" in the Action and Resource elements](#ct-iam-pr-2-description)
+ [\[CT\.IAM\.PR\.3\] Require that AWS Identity and Access Management \(IAM\) customer\-managed policies do not have wildcard service actions](#ct-iam-pr-3-description)
+ [\[CT\.IAM\.PR\.4\] Require that an AWS Identity and Access Management\(IAM\) user does not have an inline or managed policy attached](#ct-iam-pr-4-description)
+ [\[CT\.IAM\.PR\.5\] Require that AWS Identity and Access Management \(IAM\) inline policies do not have wildcard service actions](#ct-iam-pr-5-description)

## \[CT\.IAM\.PR\.1\] Require that an AWS Identity and Access Management \(IAM\) inline policy does not have a statement that includes "\*" in the Action and Resource elements<a name="ct-iam-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks that AWS Identity and Access Management \(IAM\) inline policies do not include `Effect`: `Allow` with `Action`: `*` over `Resource`: `*`\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::IAM::Policy`, `AWS::IAM::Role`, `AWS::IAM::User`, `AWS::IAM::Group`
+ **AWS CloudFormation guard rule: ** [CT\.IAM\.PR\.1 rule specification](#ct-iam-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.IAM\.PR\.1 rule specification](#ct-iam-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.IAM\.PR\.1 example templates](#ct-iam-pr-1-templates) 

**Explanation**

IAM policies define a set of privileges that are granted to users, groups, or roles\. In alignment with industry\-standard security advice, AWS recommends that your policies grant least privilege, which means to grant only the permissions that are required to perform a task\. When you provide full administrative privileges instead of the minimum set of permissions that the user requires, you may expose the resources to unwanted actions\.

Instead of allowing full administrative privileges, determine the specific actions that your users must carry out, and then craft policies that let the users perform only those tasks\. It is more secure to start with a minimum set of permissions and grant additional permissions when necessary\. Do not start with lenient permissions and try to tighten them later\.

Remove IAM policies that have a statement with `Effect`: `Allow` that permit `Action`: `*` over `Resource`: `*`\.

**Usage considerations**  
This control applies only to IAM inline policies with statements that contain an `Effect` of `Allow` and that contain both the `Action` and the `Resource` element\.

### Remediation for rule failure<a name="ct-iam-pr-1-remediation"></a>

Remove IAM inline policy statements with `Effect`: `Allow` that permit `Action`: `*` over `Resource`: `*`\.

The examples that follow show how to implement this remediation\.

#### IAM Policy \- Example One<a name="ct-iam-pr-1-remediation-1"></a>

IAM inline policy configured to allow retrieval of objects from an Amazon S3 bucket\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMPolicy": {
        "Type": "AWS::IAM::Policy",
        "Properties": {
            "PolicyName": "sample-inline-policy",
            "Roles": [
                {
                    "Ref": "IAMRole"
                }
            ],
            "PolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Action": [
                            "s3:GetObject"
                        ],
                        "Resource": [
                            "arn:aws:s3:::samplebucket/*"
                        ]
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
IAMPolicy:
  Type: AWS::IAM::Policy
  Properties:
    PolicyName: sample-inline-policy
    Roles:
      - !Ref 'IAMRole'
    PolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Action:
            - s3:GetObject
          Resource:
            - arn:aws:s3:::samplebucket/*
```

The examples that follow show how to implement this remediation\.

#### IAM Role \- Example Two<a name="ct-iam-pr-1-remediation-2"></a>

IAM role configured with an inline policy allowing retrieval of objects from an Amazon S3 bucket\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMRole": {
        "Type": "AWS::IAM::Role",
        "Properties": {
            "AssumeRolePolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Principal": {
                            "AWS": {
                                "Ref": "AWS::AccountId"
                            }
                        },
                        "Action": [
                            "sts:AssumeRole"
                        ]
                    }
                ]
            },
            "Policies": [
                {
                    "PolicyName": "sample-inline-policy",
                    "PolicyDocument": {
                        "Version": "2012-10-17",
                        "Statement": [
                            {
                                "Effect": "Allow",
                                "Action": [
                                    "s3:GetObject"
                                ],
                                "Resource": [
                                    "arn:aws:s3:::samplebucket/*"
                                ]
                            }
                        ]
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
IAMRole:
  Type: AWS::IAM::Role
  Properties:
    AssumeRolePolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Principal:
            AWS: !Ref 'AWS::AccountId'
          Action:
            - sts:AssumeRole
    Policies:
      - PolicyName: sample-inline-policy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
            - Effect: Allow
              Action:
                - s3:GetObject
              Resource:
                - arn:aws:s3:::samplebucket/*
```

### CT\.IAM\.PR\.1 rule specification<a name="ct-iam-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Name:
#   iam_inline_policy_no_statements_with_admin_access_check
# 
# Description:
#   This control checks that AWS Identity and Access Management (IAM) inline policies do not include "Effect": "Allow" with "Action": "*" over "Resource": "*".
# 
# Reports on:
#   AWS::IAM::Policy, AWS::IAM::Role, AWS::IAM::User, AWS::IAM::Group
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
#       And: The input document does not contain any IAM policy, IAM role, IAM user or IAM group resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy resource
#       And: The policy has no statements with 'Effect' set to 'Allow'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy does not have both Action and resource statements
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM role, IAM user or IAM group resources
#       And: 'Policies' is not provided or is an empty list
#      Then: SKIP
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM role, IAM user or IAM group resources
#       And: 'Policies' is provided as a non-empty list
#       And: All IAM policy documents in 'Policies' have no statements with 'Effect' set to 'Allow'
#      Then: SKIP
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy statement has one or more Action statements and one or more Resource statements
#       And: At least one Action statement allows all actions (Action value of '*')
#       And: At least one Resource statement is a wildcard representing all resources (Resource value of '*')
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM role, IAM user or IAM group resources
#       And: 'Policies' is provided as a non-empty list
#       And: IAM policy document in 'Policies' has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'Action' statements
#       And: At least one Action statement allows all actions (Action value of '*')
#       And: At least one Resource statement is a wildcard representing all resources (Resource value of '*')
#      Then: FAIL
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more Action statements and one or more Resource statements
#       And: No Action statements allow administrator access (Action value of '*')
#       And: No Resources are wildcards representing all resources (Resource value of '*')
#      Then: PASS
#   Scenario: 9
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM role, IAM user or IAM group resources
#       And: 'Policies' is provided as a non-empty list
#       And: IAM policy document in 'Policies' has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'Action' statements
#       And: No Action statements allow administrator access (Action value of '*')
#       And: No Resources are wildcards representing all resources (Resource value of '*')
#      Then: PASS

#
# Constants
#
let AWS_IAM_POLICY_TYPE = "AWS::IAM::Policy"
let AWS_IAM_ROLE_TYPE = "AWS::IAM::Role"
let AWS_IAM_USER_TYPE = "AWS::IAM::User"
let AWS_IAM_GROUP_TYPE = "AWS::IAM::Group"
let INPUT_DOCUMENT = this

#
# Assignments
#
let iam_policies = Resources.*[ Type == %AWS_IAM_POLICY_TYPE ]
let iam_principals = Resources.*[
    Type == %AWS_IAM_ROLE_TYPE or
    Type == %AWS_IAM_USER_TYPE or
    Type == %AWS_IAM_GROUP_TYPE
]

#
# Primary Rules
#
rule iam_inline_policy_no_statements_with_admin_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                                                  %iam_policies not empty {
    check_policy(%iam_policies.Properties)
        <<
        [CT.IAM.PR.1]: Require that an AWS Identity and Access Management (IAM) inline policy does not have a statement that includes "*" in the Action and Resource elements
            [FIX]: Remove IAM inline policy statements with "Effect": "Allow" that permit "Action": "*" over "Resource": "*".
        >>
}

rule iam_inline_policy_no_statements_with_admin_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_POLICY_TYPE) {
    check_policy(%INPUT_DOCUMENT.%AWS_IAM_POLICY_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.1]: Require that an AWS Identity and Access Management (IAM) inline policy does not have a statement that includes "*" in the Action and Resource elements
            [FIX]: Remove IAM inline policy statements with "Effect": "Allow" that permit "Action": "*" over "Resource": "*".
        >>
}

rule iam_inline_policy_no_statements_with_admin_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                                                  %iam_principals not empty {
    check_principal(%iam_principals.Properties)
        <<
        [CT.IAM.PR.1]: Require that an AWS Identity and Access Management (IAM) inline policy does not have a statement that includes "*" in the Action and Resource elements
            [FIX]: Remove IAM inline policy statements with "Effect": "Allow" that permit "Action": "*" over "Resource": "*".
        >>
}

rule iam_inline_policy_no_statements_with_admin_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_ROLE_TYPE) {
    check_principal(%INPUT_DOCUMENT.%AWS_IAM_ROLE_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.1]: Require that an AWS Identity and Access Management (IAM) inline policy does not have a statement that includes "*" in the Action and Resource elements
            [FIX]: Remove IAM inline policy statements with "Effect": "Allow" that permit "Action": "*" over "Resource": "*".
        >>
}

rule iam_inline_policy_no_statements_with_admin_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_USER_TYPE) {
    check_principal(%INPUT_DOCUMENT.%AWS_IAM_USER_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.1]: Require that an AWS Identity and Access Management (IAM) inline policy does not have a statement that includes "*" in the Action and Resource elements
            [FIX]: Remove IAM inline policy statements with "Effect": "Allow" that permit "Action": "*" over "Resource": "*".
        >>
}

rule iam_inline_policy_no_statements_with_admin_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_GROUP_TYPE) {
    check_principal(%INPUT_DOCUMENT.%AWS_IAM_GROUP_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.1]: Require that an AWS Identity and Access Management (IAM) inline policy does not have a statement that includes "*" in the Action and Resource elements
            [FIX]: Remove IAM inline policy statements with "Effect": "Allow" that permit "Action": "*" over "Resource": "*".
        >>
}

#
# Parameterized Rules
#
rule check_policy(policy) {
    %policy [
        filter_policy_document_with_statement_provided(this)
    ] {
        PolicyDocument {
           check_statement(Statement)
       }
    }
}

rule check_principal(iam_principal) {
    %iam_principal [
        filter_iam_principal_with_inline_policy_provided(this)
    ] {
        Policies[*] {
            check_policy(this)
       }
    }
}

rule check_statement(statement) {
    %statement [
        filter_allow_on_action_and_resource(this)
    ] {
        Action exists
        Resource exists
        check_admin_access(Action, Resource)
    }
}

rule filter_allow_on_action_and_resource(statement) {
    %statement {
        Effect == "Allow"
        Action exists
        Resource exists
    }
}

rule filter_policy_document_with_statement_provided(policy) {
    %policy {
        PolicyDocument exists
        PolicyDocument is_struct
        PolicyDocument {
            Statement exists
            filter_statement_non_empty_list(Statement) or
            Statement is_struct
        }
    }
}

rule filter_iam_principal_with_inline_policy_provided(iam_principal) {
    %iam_principal {
        Policies exists
        Policies is_list
        Policies not empty
    }
}

rule filter_statement_non_empty_list(statement) {
    %statement {
        this is_list
        this not empty
    }
}

rule check_admin_access(actions, resources) {
    when some %actions[*] == "*" {
        %resources[*] != "*"
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

### CT\.IAM\.PR\.1 example templates<a name="ct-iam-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  IAMRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            AWS:
              Ref: AWS::AccountId
          Action:
          - sts:AssumeRole
  IAMPolicy:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName:
        Fn::Sub: ${AWS::StackName}-inline-policy
      Roles:
      - Ref: IAMRole
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Action:
          - s3:GetObject
          Resource:
          - arn:aws:s3:::examplebucket/*
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  IAMRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            AWS:
              Ref: AWS::AccountId
          Action:
          - sts:AssumeRole
  IAMPolicy:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName:
        Fn::Sub: ${AWS::StackName}-inline-policy
      Roles:
      - Ref: IAMRole
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Action: '*'
          Resource: '*'
```

## \[CT\.IAM\.PR\.2\] Require that AWS Identity and Access Management \(IAM\) customer\-managed policies do not contain a statement that includes "\*" in the Action and Resource elements<a name="ct-iam-pr-2-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether AWS Identity and Access Management \(IAM\) customer managed policies do not include `Effect`: `Allow` with `Action`: `*` over `Resource`: `*`\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::IAM::ManagedPolicy`
+ **AWS CloudFormation guard rule: ** [CT\.IAM\.PR\.2 rule specification](#ct-iam-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.IAM\.PR\.2 rule specification](#ct-iam-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.IAM.PR.2) 

**Explanation**

IAM policies define a set of privileges that are granted to users, groups, or roles\. In alignment with industry\-standard security advice, AWS recommends that your policies grant least privilege, which means to grant only the permissions that are required to perform a task\. When you provide full privileges instead of the minimum set of permissions that the user requires, you may expose the resources to unwanted actions\.

Instead of allowing full privileges, determine the specific actions that your users must carry out, and then craft policies that let the users perform only those tasks\. It is more secure to start with a minimum set of permissions and grant additional permissions when necessary\. Do not start with lenient permissions and try to tighten them later\.

Remove IAM policies that have a statement with `Effect`: `Allow` that permit `Action`: `*` over `Resource`: `*`\.

**Usage considerations**  
This control checks IAM customer\-managed policies only\. It does not check inline and AWS\-managed policies\.
This control applies only to IAM inline policies with statements that contain an `Effect` of `Allow` and that contain both the `Action` and the `Resource` element\.

### Remediation for rule failure<a name="ct-iam-pr-2-remediation"></a>

Remove IAM inline policy statements with `Effect`: `Allow` that permit `Action`: `*` over `Resource`: `*`\.

The examples that follow show how to implement this remediation\.

#### IAM Managed Policy \- Example<a name="ct-iam-pr-2-remediation-1"></a>

IAM managed policy configured to allow retrieval of objects from an Amazon S3 bucket\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMManagedPolicy": {
        "Type": "AWS::IAM::ManagedPolicy",
        "Properties": {
            "Roles": [
                {
                    "Ref": "IAMRole"
                }
            ],
            "PolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Action": [
                            "s3:GetObject"
                        ],
                        "Resource": [
                            "arn:aws:s3:::samplebucket/*"
                        ]
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
IAMManagedPolicy:
  Type: AWS::IAM::ManagedPolicy
  Properties:
    Roles:
      - !Ref 'IAMRole'
    PolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Action:
            - s3:GetObject
          Resource:
            - arn:aws:s3:::samplebucket/*
```

### CT\.IAM\.PR\.2 rule specification<a name="ct-iam-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Name:
#   iam_managed_policy_no_statements_with_admin_access_check
# 
# Description:
#   This control checks whether AWS Identity and Access Management (IAM) customer managed policies do not include "Effect": "Allow" with "Action": "*" over "Resource": "*".
# 
# Reports on:
#   AWS::IAM::ManagedPolicy
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
#       And: The input document does not contain any IAM managed policy resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM managed policy resource
#       And: The policy has no statements with 'Effect' set to 'Allow'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM managed policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy does not have both Action and Resource statements
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM managed policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy statement has one or more Action statements and one or more Resource statements
#       And: Within a single policy statement at least one Action statement allows all actions (Action value of '*')
#       And: Within the same policy statement at least one Resource statement is a wildcard representing all
#            resources (Resource value of '*')
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM managed policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more Action statements and one or more Resource statements
#       And: Within a single policy statement no Action statements allow all actions (Action value of '*')
#       And: Within the same policy statement no Resources are wildcards representing all
#            resources (Resource value of '*')
#      Then: PASS

#
# Constants
#
let AWS_IAM_MANAGED_POLICY_TYPE = "AWS::IAM::ManagedPolicy"
let INPUT_DOCUMENT = this

#
# Assignments
#
let iam_managed_policies = Resources.*[ Type == %AWS_IAM_MANAGED_POLICY_TYPE ]

#
# Primary Rules
#
rule iam_managed_policy_no_statements_with_admin_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                                                   %iam_managed_policies not empty {
    check(%iam_managed_policies.Properties)
        <<
        [CT.IAM.PR.2]: Require that AWS Identity and Access Management (IAM) customer-managed policies do not contain a statement that includes "*" in the Action and Resource elements
            [FIX]: Remove IAM inline policy statements with "Effect": "Allow" that permit "Action": "*" over "Resource": "*".
        >>
}

rule iam_managed_policy_no_statements_with_admin_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_MANAGED_POLICY_TYPE) {
    check(%INPUT_DOCUMENT.%AWS_IAM_MANAGED_POLICY_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.2]: Require that AWS Identity and Access Management (IAM) customer-managed policies do not contain a statement that includes "*" in the Action and Resource elements
            [FIX]: Remove IAM inline policy statements with "Effect": "Allow" that permit "Action": "*" over "Resource": "*".
        >>
}

#
# Parameterized Rules
#
rule check(policy) {
    %policy [
        filter_policy_document_with_statement_provided(this)
    ] {
        PolicyDocument {
           check_statement(Statement)
       }
    }
}

rule check_statement(statement) {
    %statement [
        filter_allow_on_action_and_resource(this)
    ] {
        Action exists
        Resource exists
        check_admin_access(Action, Resource)
    }
}

rule filter_allow_on_action_and_resource(statement) {
    %statement {
        Effect == "Allow"
        Action exists
        Resource exists
    }
}

rule filter_policy_document_with_statement_provided(policy) {
    %policy {
        PolicyDocument exists
        PolicyDocument is_struct
        PolicyDocument {
            Statement exists
            filter_statement_non_empty_list(Statement) or
            Statement is_struct
        }
    }
}

rule filter_statement_non_empty_list(statement) {
    %statement {
        this is_list
        this not empty
    }
}

rule check_admin_access(actions, resources) {
    when some %actions[*] == "*" {
        %resources[*] != "*"
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

### CT\.IAM\.PR\.2 example templates<a name="ct-iam-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  IAMRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            AWS:
              Ref: AWS::AccountId
          Action:
          - sts:AssumeRole
  IAMManagedPolicy:
    Type: AWS::IAM::ManagedPolicy
    Properties:
      Roles:
      - Ref: IAMRole
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Action:
          - s3:GetObject
          Resource:
          - arn:aws:s3:::examplebucket/*
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  IAMRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            AWS:
              Ref: AWS::AccountId
          Action:
          - sts:AssumeRole
  IAMManagedPolicy:
    Type: AWS::IAM::ManagedPolicy
    Properties:
      Roles:
      - Ref: IAMRole
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Action: '*'
          Resource: '*'
```

## \[CT\.IAM\.PR\.3\] Require that AWS Identity and Access Management \(IAM\) customer\-managed policies do not have wildcard service actions<a name="ct-iam-pr-3-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks that AWS Identity and Access Management \(IAM\) customer\-managed policies do not contain statements of `Effect`: `Allow` with `Action`: `Service:*` \(for example, s3:\*\) for individual AWS services, and that the policies do not use the combination of `NotAction` with an `Effect` of `Allow`\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::IAM::ManagedPolicy`
+ **AWS CloudFormation guard rule: ** [CT\.IAM\.PR\.3 rule specification](#ct-iam-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.IAM\.PR\.3 rule specification](#ct-iam-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.IAM.PR.3) 

**Explanation**

When you assign permissions to AWS services, it is important to scope the allowed IAM actions in your IAM policies\. We recommend that you provision least\-privilege permissions by restricting IAM policies to required actions only\. Overly permissive policies can lead to privilege escalation, if the policies are attached to an IAM principal that may not require the permission\.

**Usage considerations**  
This control checks IAM customer\-managed policies only\. It does not check inline and AWS\-managed policies\.
This control applies only to IAM customer\-managed policies with statements that contain an `Effect` of `Allow` with an `Action` or `NotAction` element present\.

### Remediation for rule failure<a name="ct-iam-pr-3-remediation"></a>

Remove statements from IAM customer\-managed policies with `Effect`: `Allow` and `Action`: `service:*` or `Effect`: `Allow` and `NotAction`\.

The examples that follow show how to implement this remediation\.

#### IAM Managed Policy \- Example<a name="ct-iam-pr-3-remediation-1"></a>

IAM managed policy configured to allow the Amazon S3 `ListBucket` action\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMManagedPolicy": {
        "Type": "AWS::IAM::ManagedPolicy",
        "Properties": {
            "Roles": [
                {
                    "Ref": "IAMRole"
                }
            ],
            "PolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Action": [
                            "s3:ListBucket"
                        ],
                        "Resource": [
                            "*"
                        ]
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
IAMManagedPolicy:
  Type: AWS::IAM::ManagedPolicy
  Properties:
    Roles:
      - !Ref 'IAMRole'
    PolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Action:
            - s3:ListBucket
          Resource:
            - '*'
```

### CT\.IAM\.PR\.3 rule specification<a name="ct-iam-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Name:
#   iam_managed_policy_no_statements_with_full_access_check
# 
# Description:
#   This control checks that AWS Identity and Access Management (IAM) customer-managed policies do not contain statements of "Effect": "Allow" with "Action": "Service:*" (for example, s3:*) for individual AWS services, and that the policies do not use the combination of "NotAction" with an "Effect" of "Allow".
# 
# Reports on:
#   AWS::IAM::ManagedPolicy
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any IAM Managed Policy resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM Managed Policy resource
#       And: The policy has no statements with 'Effect' set to 'Allow'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM Managed Policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'Action' statements
#       And: At least one 'Action' statement allows full access to a service ('Action' has a value 'service:*')
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM Managed Policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'NotAction' statements
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM Managed Policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'Action' statements
#       And: No 'Action' statements allow full access to a service ('Action' does not have a value 'service:*')
#      Then: PASS

#
# Constants
#
let AWS_IAM_MANAGED_POLICY_TYPE = "AWS::IAM::ManagedPolicy"
let WILDCARD_ACTION_PATTERN = /^[\w]*[:]*\*$/
let INPUT_DOCUMENT = this

#
# Assignments
#
let iam_managed_policies = Resources.*[ Type == %AWS_IAM_MANAGED_POLICY_TYPE ]

#
# Primary Rules
#
rule iam_managed_policy_no_statements_with_full_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                                                  %iam_managed_policies not empty {
    check(%iam_managed_policies.Properties)
        <<
        [CT.IAM.PR.3]: Require that AWS Identity and Access Management (IAM) customer-managed policies do not have wildcard service actions
            [FIX]: Remove statements from IAM customer-managed policies with "Effect": "Allow" and "Action": "service:*" or "Effect": "Allow" and "NotAction".
        >>
}

rule iam_managed_policy_no_statements_with_full_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_MANAGED_POLICY_TYPE) {
    check(%INPUT_DOCUMENT.%AWS_IAM_MANAGED_POLICY_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.3]: Require that AWS Identity and Access Management (IAM) customer-managed policies do not have wildcard service actions
            [FIX]: Remove statements from IAM customer-managed policies with "Effect": "Allow" and "Action": "service:*" or "Effect": "Allow" and "NotAction".
        >>
}

#
# Parameterized Rules
#
rule check(policy) {
    %policy [
        filter_policy_document_with_statement_provided(this)
    ] {
        PolicyDocument {
            check_statement_no_wildcard_actions(Statement)
            check_statement_no_not_action(Statement)
       }
    }
}

rule check_statement_no_wildcard_actions(statement) {
    %statement [
        filter_allow_on_action(this)
    ] {
        Action exists
        check_no_wildcard_action(Action)
    }
}

rule check_statement_no_not_action(statement) {
    %statement [
        filter_allow(this)
    ] {
        NotAction not exists
    }
}

rule filter_allow_on_action(statement) {
    %statement {
        Effect == "Allow"
        Action exists
    }
}

rule filter_allow(statement) {
    %statement {
        Effect == "Allow"
    }
}

rule filter_policy_document_with_statement_provided(policy) {
    %policy {
        PolicyDocument exists
        PolicyDocument is_struct
        PolicyDocument {
            Statement exists
            filter_statement_non_empty_list(Statement) or
            Statement is_struct
        }
    }
}

rule filter_statement_non_empty_list(statement) {
    %statement {
        this is_list
        this not empty
    }
}

rule check_no_wildcard_action(actions) {
    %actions[*] {
        this != %WILDCARD_ACTION_PATTERN
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

### CT\.IAM\.PR\.3 example templates<a name="ct-iam-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  IAMRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            AWS:
              Ref: AWS::AccountId
          Action:
          - sts:AssumeRole
  IAMManagedPolicy:
    Type: AWS::IAM::ManagedPolicy
    Properties:
      Roles:
      - Ref: IAMRole
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Action:
          - s3:ListBucket
          Resource:
          - '*'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  IAMRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            AWS:
              Ref: AWS::AccountId
          Action:
          - sts:AssumeRole
  IAMManagedPolicy:
    Type: AWS::IAM::ManagedPolicy
    Properties:
      Roles:
      - Ref: IAMRole
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Action: s3:*
          Resource: '*'
```

## \[CT\.IAM\.PR\.4\] Require that an AWS Identity and Access Management\(IAM\) user does not have an inline or managed policy attached<a name="ct-iam-pr-4-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your AWS Identity and Access Management \(IAM\) user has inline or managed \(AWS and customer\) policies directly attached\. Instead, IAM users should inherit permissions from IAM groups or roles\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::IAM::User`, `AWS::IAM::Policy`, `AWS::IAM::ManagedPolicy`
+ **AWS CloudFormation guard rule: ** [CT\.IAM\.PR\.4 rule specification](#ct-iam-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.IAM\.PR\.4 rule specification](#ct-iam-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.IAM\.PR\.4 example templates](#ct-iam-pr-4-templates) 

**Explanation**

By default, IAM users, groups, and roles have no access to AWS resources\. IAM policies grant privileges to users, groups, or roles\. We recommend that you apply IAM policies directly to groups and roles, but not to users\. As the number of users grows, assigning privileges at the group or role level reduces the complexity of access management\. Reducing access management complexity may in turn reduce the opportunity for a principal to receive or retain excessive privileges inadvertently\.

### Remediation for rule failure<a name="ct-iam-pr-4-remediation"></a>

Configure IAM users to inherit permissions from IAM groups\.

The examples that follow show how to implement this remediation\.

#### IAM User \- Example<a name="ct-iam-pr-4-remediation-1"></a>

IAM user configured with no IAM policy or managed policy attachments\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMUser": {
        "Type": "AWS::IAM::User"
    }
}
```

**YAML example**

```
IAMUser:
  Type: AWS::IAM::User
```

The examples that follow show how to implement this remediation\.

#### IAM Policy \- Example<a name="ct-iam-pr-4-remediation-2"></a>

IAM policy configured with no IAM user associations\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMPolicy": {
        "Type": "AWS::IAM::Policy",
        "Properties": {
            "PolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Action": [
                            "cloudformation:DescribeStacks"
                        ],
                        "Resource": "*"
                    }
                ]
            },
            "PolicyName": "sample-policy",
            "Roles": [
                {
                    "Ref": "IAMRole"
                }
            ]
        }
    }
}
```

**YAML example**

```
IAMPolicy:
  Type: AWS::IAM::Policy
  Properties:
    PolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Action:
            - cloudformation:DescribeStacks
          Resource: '*'
    PolicyName: sample-policy
    Roles:
      - !Ref 'IAMRole'
```

The examples that follow show how to implement this remediation\.

#### IAM Managed Policy \- Example<a name="ct-iam-pr-4-remediation-3"></a>

IAM managed policy configured with no IAM user associations\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMManagedPolicy": {
        "Type": "AWS::IAM::ManagedPolicy",
        "Properties": {
            "PolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Action": [
                            "cloudformation:DescribeStacks"
                        ],
                        "Resource": "*"
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
IAMManagedPolicy:
  Type: AWS::IAM::ManagedPolicy
  Properties:
    PolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Action:
            - cloudformation:DescribeStacks
          Resource: '*'
```

### CT\.IAM\.PR\.4 rule specification<a name="ct-iam-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   iam_user_no_policies_check
# 
# Description:
#   This control checks whether your AWS Identity and Access Management (IAM) user has inline or managed (AWS and customer) policies directly attached. Instead, IAM users should inherit permissions from IAM groups or roles.
# 
# Reports on:
#   AWS::IAM::User, AWS::IAM::Policy, AWS::IAM::ManagedPolicy
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
#       And: The input document does not contain any IAM user, policy or managed policy resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM user resource
#       And: 'Policies' or 'ManagedPolicyArns' have been specified as a non-empty list
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy or managed policy resource
#       And: 'Users' has been specified and is a non-empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM user resource
#       And: 'Policies' has not been been specified or is an empty list
#       And: 'ManagedPolicyArns' has not been been specified or is an empty list
#      Then: PASS
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy or managed policy resource
#       And: 'Users' has not been specified or is an empty list
#      Then: PASS

#
# Constants
#
let IAM_USER_TYPE = "AWS::IAM::User"
let IAM_POLICY_TYPE = "AWS::IAM::Policy"
let IAM_MANAGED_POLICY_TYPE = "AWS::IAM::ManagedPolicy"
let INPUT_DOCUMENT = this

#
# Assignments
#
let iam_users = Resources.*[ Type == %IAM_USER_TYPE ]
let iam_policies = Resources.*[ Type == %IAM_POLICY_TYPE ]
let iam_managed_policies = Resources.*[ Type == %IAM_MANAGED_POLICY_TYPE ]

#
# Primary Rules
#
rule iam_user_no_policies_check when is_cfn_template(%INPUT_DOCUMENT)
                                     %iam_users not empty {
    check_user(%iam_users.Properties)
        <<
        [CT.IAM.PR.4]: Require that an AWS Identity and Access Management (IAM) user does not have an inline or managed policy attached attached
            [FIX]: Configure IAM users to inherit permissions from IAM groups.
        >>
}

rule iam_user_no_policies_check when is_cfn_template(%INPUT_DOCUMENT)
                                     %iam_policies not empty {
    check_policy(%iam_policies.Properties)
        <<
        [CT.IAM.PR.4]: Require that an AWS Identity and Access Management (IAM) user does not have an inline or managed policy attached attached
            [FIX]: Configure IAM users to inherit permissions from IAM groups.
        >>
}

rule iam_user_no_policies_check when is_cfn_template(%INPUT_DOCUMENT)
                                     %iam_managed_policies not empty {
    check_policy(%iam_managed_policies.Properties)
        <<
        [CT.IAM.PR.4]: Require that an AWS Identity and Access Management (IAM) user does not have an inline or managed policy attached attached
            [FIX]: Configure IAM users to inherit permissions from IAM groups.
        >>
}

rule iam_user_no_policies_check when is_cfn_hook(%INPUT_DOCUMENT, %IAM_USER_TYPE) {
    check_user(%INPUT_DOCUMENT.%IAM_USER_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.4]: Require that an AWS Identity and Access Management (IAM) user does not have an inline or managed policy attached attached
            [FIX]: Configure IAM users to inherit permissions from IAM groups.
        >>
}

rule iam_user_no_policies_check when is_cfn_hook(%INPUT_DOCUMENT, %IAM_POLICY_TYPE) {
    check_policy(%INPUT_DOCUMENT.%IAM_POLICY_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.4]: Require that an AWS Identity and Access Management (IAM) user does not have an inline or managed policy attached attached
            [FIX]: Configure IAM users to inherit permissions from IAM groups.
        >>
}

rule iam_user_no_policies_check when is_cfn_hook(%INPUT_DOCUMENT, %IAM_MANAGED_POLICY_TYPE) {
    check_policy(%INPUT_DOCUMENT.%IAM_MANAGED_POLICY_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.4]: Require that an AWS Identity and Access Management (IAM) user does not have an inline or managed policy attached attached
            [FIX]: Configure IAM users to inherit permissions from IAM groups.
        >>
}

#
# Parameterized Rules
#
rule check_user(iam_user) {
    %iam_user {
        # Scenario 2 and 4
        Policies not exists or
        exists_and_is_empty_list(Policies)

        ManagedPolicyArns not exists or
        exists_and_is_empty_list(ManagedPolicyArns)
    }
}

rule check_policy(policy) {
    %policy {
        # Scenario 3 and 4
        Users not exists or
        exists_and_is_empty_list(Users)
    }
}

rule exists_and_is_empty_list(list_property) {
    %list_property {
        this is_list
        this empty
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

### CT\.IAM\.PR\.4 example templates<a name="ct-iam-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  IAMUser:
    Type: AWS::IAM::User
    Properties: {}
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  IAMUser:
    Type: AWS::IAM::User
    Properties:
      ManagedPolicyArns:
      - arn:aws:iam::aws:policy/AdministratorAccess
```

## \[CT\.IAM\.PR\.5\] Require that AWS Identity and Access Management \(IAM\) inline policies do not have wildcard service actions<a name="ct-iam-pr-5-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether AWS Identity and Access Management \(IAM\) inline policies do not include `Effect`: `Allow` with `Action`: `Service:*` \(e\.g\. s3:\*\) for individual AWS services or use the combination of `NotAction` with an `Effect` of `Allow`\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::IAM::Policy`, `AWS::IAM::Role`, `AWS::IAM::User`, `AWS::IAM::Group`
+ **AWS CloudFormation guard rule: ** [CT\.IAM\.PR\.5 rule specification](#ct-iam-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.IAM\.PR\.5 rule specification](#ct-iam-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.IAM.PR.5) 

**Explanation**

When you assign permissions to AWS services, it is important to scope the allowed IAM actions in your IAM policies\. You should restrict IAM actions to only those actions that are needed\. This helps you to provision least privilege permissions\. Overly permissive policies might lead to privilege escalation if the policies are attached to an IAM principal that might not require the permission\.

**Usage considerations**  
This control only applies to IAM inline policies with statements that contain an `Effect` of `Allow` with an `Action` or `NotAction` element present
This control only applies to IAM role, user or group resources with one or more inline policies and IAM policy resources with one or more statements configured

### Remediation for rule failure<a name="ct-iam-pr-5-remediation"></a>

Remove statements from IAM inline policies with `Effect`: `Allow` and `Action`: `service:*` or `Effect`: `Allow` and `NotAction`\.

The examples that follow show how to implement this remediation\.

#### IAM Inline Policy \- Example One<a name="ct-iam-pr-5-remediation-1"></a>

IAM role configured with an inline policy allowing the S3 `ListBucket` action\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMRole": {
        "Type": "AWS::IAM::Role",
        "Properties": {
            "AssumeRolePolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Principal": {
                            "AWS": {
                                "Ref": "AWS::AccountId"
                            }
                        },
                        "Action": [
                            "sts:AssumeRole"
                        ]
                    }
                ]
            },
            "Policies": [
                {
                    "PolicyDocument": {
                        "Version": "2012-10-17",
                        "Statement": [
                            {
                                "Effect": "Allow",
                                "Action": [
                                    "s3:ListBucket"
                                ],
                                "Resource": [
                                    "*"
                                ]
                            }
                        ]
                    },
                    "PolicyName": "sample-policy"
                }
            ]
        }
    }
}
```

**YAML example**

```
IAMRole:
  Type: AWS::IAM::Role
  Properties:
    AssumeRolePolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Principal:
            AWS: !Ref 'AWS::AccountId'
          Action:
            - sts:AssumeRole
    Policies:
      - PolicyDocument:
          Version: '2012-10-17'
          Statement:
            - Effect: Allow
              Action:
                - s3:ListBucket
              Resource:
                - '*'
        PolicyName: sample-policy
```

The examples that follow show how to implement this remediation\.

#### IAM Inline Policy \- Example Two<a name="ct-iam-pr-5-remediation-2"></a>

IAM user configured with an inline policy allowing the S3 `ListBucket` action\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMUser": {
        "Type": "AWS::IAM::User",
        "Properties": {
            "Policies": [
                {
                    "PolicyDocument": {
                        "Version": "2012-10-17",
                        "Statement": [
                            {
                                "Effect": "Allow",
                                "Action": [
                                    "s3:ListBucket"
                                ],
                                "Resource": [
                                    "*"
                                ]
                            }
                        ]
                    },
                    "PolicyName": "sample-policy"
                }
            ]
        }
    }
}
```

**YAML example**

```
IAMUser:
  Type: AWS::IAM::User
  Properties:
    Policies:
      - PolicyDocument:
          Version: '2012-10-17'
          Statement:
            - Effect: Allow
              Action:
                - s3:ListBucket
              Resource:
                - '*'
        PolicyName: sample-policy
```

The examples that follow show how to implement this remediation\.

#### IAM Inline Policy \- Example Three<a name="ct-iam-pr-5-remediation-3"></a>

IAM group configured with an inline policy allowing the S3 `ListBucket` action\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMGroup": {
        "Type": "AWS::IAM::Group",
        "Properties": {
            "Policies": [
                {
                    "PolicyDocument": {
                        "Version": "2012-10-17",
                        "Statement": [
                            {
                                "Effect": "Allow",
                                "Action": [
                                    "s3:ListBucket"
                                ],
                                "Resource": [
                                    "*"
                                ]
                            }
                        ]
                    },
                    "PolicyName": "sample-policy"
                }
            ]
        }
    }
}
```

**YAML example**

```
IAMGroup:
  Type: AWS::IAM::Group
  Properties:
    Policies:
      - PolicyDocument:
          Version: '2012-10-17'
          Statement:
            - Effect: Allow
              Action:
                - s3:ListBucket
              Resource:
                - '*'
        PolicyName: sample-policy
```

The examples that follow show how to implement this remediation\.

#### IAM Inline Policy \- Example Four<a name="ct-iam-pr-5-remediation-4"></a>

IAM policy associated with an IAM role as an inline policy and configured to allow the S3 `ListBucket` action\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "IAMPolicy": {
        "Type": "AWS::IAM::Policy",
        "Properties": {
            "PolicyName": "sample-policy",
            "Roles": [
                {
                    "Ref": "IAMRole"
                }
            ],
            "PolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Action": [
                            "s3:ListBucket"
                        ],
                        "Resource": [
                            "*"
                        ]
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
IAMPolicy:
  Type: AWS::IAM::Policy
  Properties:
    PolicyName: sample-policy
    Roles:
      - !Ref 'IAMRole'
    PolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Action:
            - s3:ListBucket
          Resource:
            - '*'
```

### CT\.IAM\.PR\.5 rule specification<a name="ct-iam-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Name:
#   iam_inline_policy_no_statements_with_full_access_check
# 
# Description:
#   This control checks whether AWS Identity and Access Management (IAM) inline policies do not include "Effect": "Allow" with "Action": "Service:*" (e.g. s3:*) for individual AWS services or use the combination of "NotAction" with an "Effect" of "Allow".
# 
# Reports on:
#   AWS::IAM::Policy, AWS::IAM::Role, AWS::IAM::User, AWS::IAM::Group
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any IAM policy, IAM role, IAM user or IAM group resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy resource
#       And: The policy has no statements with 'Effect' set to 'Allow'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM role, IAM user or IAM group resource
#       And: 'Policies' is not provided or is an empty list
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM role, IAM user or IAM group resource
#       And: 'Policies' is provided as a non-empty list
#       And: All IAM policy documents in 'Policies' have no statements with 'Effect' set to 'Allow'
#      Then: SKIP
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'Action' statements
#       And: 'Action' statement allows full access to a service ('Action' has a value 'service:*')
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'NotAction' statements
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM role, IAM user or IAM group resource
#       And: 'Policies' is provided as a non-empty list
#       And: IAM policy document in 'Policies' has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'Action' statements
#       And: 'Action' statement allows full access to a service ('Action' has a value 'service:*')
#      Then: FAIL
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM role, IAM user or IAM user resource
#       And: 'Policies' is provided as a non-empty list
#       And: At least one IAM policy document in 'Policies' has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'NotAction' statements
#      Then: FAIL
#   Scenario: 9
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM policy resource
#       And: The policy has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'Action' statements
#       And: No 'Action' statements allow full access to a service ('Action' does not have a value 'service:*')
#      Then: PASS
#   Scenario: 10
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an IAM role, IAM user or IAM user resource
#       And: 'Policies' is provided as a non-empty list
#       And: At least one IAM policy document in 'Policies' has a statement with 'Effect' set to 'Allow'
#       And: The policy has one or more 'Action' statements
#       And: No 'Action' statements allow full access to a service ('Action' does not have a value 'service:*')
#      Then: PASS

#
# Constants
#
let AWS_IAM_POLICY_TYPE = "AWS::IAM::Policy"
let AWS_IAM_ROLE_TYPE = "AWS::IAM::Role"
let AWS_IAM_USER_TYPE = "AWS::IAM::User"
let AWS_IAM_GROUP_TYPE = "AWS::IAM::Group"
let WILDCARD_ACTION_PATTERN = /^[\w]*[:]*\*$/
let INPUT_DOCUMENT = this

#
# Assignments
#
let iam_policies = Resources.*[ Type == %AWS_IAM_POLICY_TYPE ]
let iam_principals = Resources.*[
    Type == %AWS_IAM_ROLE_TYPE or
    Type == %AWS_IAM_USER_TYPE or
    Type == %AWS_IAM_GROUP_TYPE
]

#
# Primary Rules
#
rule iam_inline_policy_no_statements_with_full_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                                                 %iam_policies not empty {
    check_policy(%iam_policies.Properties)
        <<
        [CT.IAM.PR.5]: Require that AWS Identity and Access Management (IAM) inline policies do not have wildcard service actions
            [FIX]: Remove statements from IAM inline policies with "Effect": "Allow" and "Action": "service:*" or "Effect": "Allow" and "NotAction".
        >>
}

rule iam_inline_policy_no_statements_with_full_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_POLICY_TYPE) {
    check_policy(%INPUT_DOCUMENT.%AWS_IAM_POLICY_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.5]: Require that AWS Identity and Access Management (IAM) inline policies do not have wildcard service actions
            [FIX]: Remove statements from IAM inline policies with "Effect": "Allow" and "Action": "service:*" or "Effect": "Allow" and "NotAction".
        >>
}

rule iam_inline_policy_no_statements_with_full_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                                                 %iam_principals not empty {
    check_principal(%iam_principals.Properties)
        <<
        [CT.IAM.PR.5]: Require that AWS Identity and Access Management (IAM) inline policies do not have wildcard service actions
            [FIX]: Remove statements from IAM inline policies with "Effect": "Allow" and "Action": "service:*" or "Effect": "Allow" and "NotAction".
        >>
}

rule iam_inline_policy_no_statements_with_full_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_ROLE_TYPE) {
    check_principal(%INPUT_DOCUMENT.%AWS_IAM_ROLE_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.5]: Require that AWS Identity and Access Management (IAM) inline policies do not have wildcard service actions
            [FIX]: Remove statements from IAM inline policies with "Effect": "Allow" and "Action": "service:*" or "Effect": "Allow" and "NotAction".
        >>
}

rule iam_inline_policy_no_statements_with_full_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_USER_TYPE) {
    check_principal(%INPUT_DOCUMENT.%AWS_IAM_USER_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.5]: Require that AWS Identity and Access Management (IAM) inline policies do not have wildcard service actions
            [FIX]: Remove statements from IAM inline policies with "Effect": "Allow" and "Action": "service:*" or "Effect": "Allow" and "NotAction".
        >>
}

rule iam_inline_policy_no_statements_with_full_access_check when is_cfn_hook(%INPUT_DOCUMENT, %AWS_IAM_GROUP_TYPE) {
    check_principal(%INPUT_DOCUMENT.%AWS_IAM_GROUP_TYPE.resourceProperties)
        <<
        [CT.IAM.PR.5]: Require that AWS Identity and Access Management (IAM) inline policies do not have wildcard service actions
            [FIX]: Remove statements from IAM inline policies with "Effect": "Allow" and "Action": "service:*" or "Effect": "Allow" and "NotAction".
        >>
}


#
# Parameterized Rules
#
rule check_policy(policy) {
    %policy [
        filter_policy_document_with_statement_provided(this)
    ] {
        PolicyDocument {
            check_statement_no_wildcard_actions(Statement)
            check_statement_no_not_action(Statement)
       }
    }
}

rule check_principal(iam_principal) {
    %iam_principal [
        filter_iam_principal_with_inline_policy_provided(this)
    ] {
        Policies[*] {
            check_policy(this)
       }
    }
}

rule check_statement_no_wildcard_actions(statement) {
    %statement [
        filter_allow_on_action(this)
    ] {
        Action exists
        check_no_wildcard_action(Action)
    }
}

rule check_statement_no_not_action(statement) {
    %statement [
        filter_allow(this)
    ] {
        NotAction not exists
    }
}

rule filter_allow_on_action(statement) {
    %statement {
        Effect == "Allow"
        Action exists
    }
}

rule filter_allow(statement) {
    %statement {
        Effect == "Allow"
    }
}

rule filter_policy_document_with_statement_provided(policy) {
    %policy {
        PolicyDocument exists
        PolicyDocument is_struct
        PolicyDocument {
            Statement exists
            filter_statement_non_empty_list(Statement) or
            Statement is_struct
        }
    }
}

rule filter_iam_principal_with_inline_policy_provided(iam_principal) {
    %iam_principal {
        Policies exists
        Policies is_list
        Policies not empty
    }
}

rule filter_statement_non_empty_list(statement) {
    %statement {
        this is_list
        this not empty
    }
}

rule check_no_wildcard_action(actions) {
    %actions[*] {
        this != %WILDCARD_ACTION_PATTERN
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

### CT\.IAM\.PR\.5 example templates<a name="ct-iam-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  IAMRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            AWS:
              Ref: AWS::AccountId
          Action:
          - sts:AssumeRole
  IAMPolicy:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName:
        Fn::Sub: ${AWS::StackName}-example
      Roles:
      - Ref: IAMRole
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Action:
          - s3:ListBucket
          Resource:
          - '*'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  IAMRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            AWS:
              Ref: AWS::AccountId
          Action:
          - sts:AssumeRole
  IAMPolicy:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName:
        Fn::Sub: ${AWS::StackName}-example
      Roles:
      - Ref: IAMRole
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Action: s3:*
          Resource: '*'
```