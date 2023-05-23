# Amazon SageMaker controls<a name="sagemaker-rules"></a>

**Topics**
+ [\[CT\.SAGEMAKER\.PR\.1\] Require an Amazon SageMaker notebook instance to prevent direct internet access](#ct-sagemaker-pr-1-description)
+ [\[CT\.SAGEMAKER\.PR\.2\] Require Amazon SageMaker notebook instances to be deployed within a custom Amazon VPC](#ct-sagemaker-pr-2-description)
+ [\[CT\.SAGEMAKER\.PR\.3\] Require Amazon SageMaker notebook instances to have root access disallowed](#ct-sagemaker-pr-3-description)

## \[CT\.SAGEMAKER\.PR\.1\] Require an Amazon SageMaker notebook instance to prevent direct internet access<a name="ct-sagemaker-pr-1-description"></a>

This control checks that direct internet access is not allowed for an Amazon SageMaker notebook instance\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::SageMaker::NotebookInstance`
+ **AWS CloudFormation guard rule: ** [CT\.SAGEMAKER\.PR\.1 rule specification](#ct-sagemaker-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.SAGEMAKER\.PR\.1 rule specification](#ct-sagemaker-pr-1-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.SAGEMAKER\.PR\.1 example templates](#ct-sagemaker-pr-1-templates) 

**Explanation**

When you configure your SageMaker notebook instance without a VPC, direct internet access is allowed for your instance, by default\. Instead, you should configure your instance with a VPC and change the default setting to `Disable - Access the internet through a VPC`\.

To train or host models from a notebook, you require internet access\. To set up internet access, make sure that your VPC has a NAT gateway, and that your security group allows outbound connections\.

Ensure that access to your SageMaker configuration is limited to authorized users\. Restrict users' IAM permissions for modifying SageMaker settings and resources\.

**Usage considerations**  
To set up outbound internet access for Amazon SageMaker notebook instances when this control is activated \- First, associate the notebook instance with a private subnet that has access to the internet, through a default route to a NAT gateway instance\. Also, be sure that the security groups assigned to the notebook instance, and the network access control list \(NACL\) of the private subnet, allow outbound traffic to the internet\.

### Remediation for rule failure<a name="ct-sagemaker-pr-1-remediation"></a>

Set `DirectInternetAccess` to `Disabled` and provide a `SubnetId` and one or more `SecurityGroupIds`\.

The examples that follow show how to implement this remediation\.

#### Amazon SageMaker Notebook Instance \- Example<a name="ct-sagemaker-pr-1-remediation-1"></a>

Amazon SageMaker notebook instance configured with direct internet access deactivated\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "SageMakerNoteBookInstance": {
        "Type": "AWS::SageMaker::NotebookInstance",
        "Properties": {
            "InstanceType": "ml.t2.medium",
            "RoleArn": {
                "Fn::GetAtt": [
                    "ExecutionRole",
                    "Arn"
                ]
            },
            "DirectInternetAccess": "Disabled",
            "SubnetId": {
                "Ref": "Subnet"
            },
            "SecurityGroupIds": [
                {
                    "Fn::GetAtt": [
                        "SecurityGroup",
                        "GroupId"
                    ]
                }
            ]
        }
    }
}
```

**YAML example**

```
SageMakerNoteBookInstance:
  Type: AWS::SageMaker::NotebookInstance
  Properties:
    InstanceType: ml.t2.medium
    RoleArn: !GetAtt 'ExecutionRole.Arn'
    DirectInternetAccess: Disabled
    SubnetId: !Ref 'Subnet'
    SecurityGroupIds:
      - !GetAtt 'SecurityGroup.GroupId'
```

### CT\.SAGEMAKER\.PR\.1 rule specification<a name="ct-sagemaker-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   sagemaker_notebook_no_direct_internet_access_check
# 
# Description:
#   This control checks that direct internet access is not allowed for an Amazon SageMaker notebook instance.
# 
# Reports on:
#   AWS::SageMaker::NotebookInstance
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
#       And: The input document does not contain any SageMaker notebook instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SageMaker notebook instance resource
#       And: 'DirectInternetAccess' has not been provided on the SageMaker notebook instance resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SageMaker notebook instance resource
#       And: 'DirectInternetAccess' has been provided on the SageMaker notebook instance resource
#       And: 'DirectInternetAccess' is set to 'Enabled'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SageMaker notebook instance resource
#       And: 'DirectInternetAccess' has been provided on the SageMaker notebook instance resource
#       And: 'DirectInternetAccess' is set to 'Disabled'
#       And: 'SecurityGroupIds' have been provided as a non-empty list with non-empty strings or valid local references
#       And: 'SubnetId' has been provided as an empty string or non-valid local reference
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SageMaker notebook instance resource
#       And: 'DirectInternetAccess' has been provided on the SageMaker notebook instance resource
#       And: 'DirectInternetAccess' is set to 'Disabled'
#       And: 'SubnetId' has been provided as a non-empty string or valid local reference
#       And: 'SecurityGroupIds' have been provided as an empty list or a list that contains empty string values or
#            non-valid local references
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an SageMaker notebook instance resource
#       And: 'DirectInternetAccess' has been provided on the SageMaker notebook instance resource
#       And: 'DirectInternetAccess' is set to 'Disabled'
#       And: 'SecurityGroupIds' have been provided as a non-empty list with non-empty strings or valid local references
#       And: 'SubnetId' has been provided as a non-empty string or valid local reference
#      Then: PASS

#
# Constants
#
let SAGEMAKER_NOTEBOOK_INSTANCE_TYPE = "AWS::SageMaker::NotebookInstance"
let INPUT_DOCUMENT = this

#
# Assignments
#
let sagemaker_notebook_instances = Resources.*[ Type == %SAGEMAKER_NOTEBOOK_INSTANCE_TYPE ]

#
# Primary Rules
#
rule sagemaker_notebook_no_direct_internet_access_check when is_cfn_template(this)
                                                             %sagemaker_notebook_instances not empty {
    check(%sagemaker_notebook_instances.Properties)
        <<
        [CT.SAGEMAKER.PR.1]: Require an Amazon SageMaker notebook instance to prevent direct internet access
        [FIX]: Set 'DirectInternetAccess' to 'Disabled' and provide a 'SubnetId' and one or more 'SecurityGroupIds'.
        >>
}

rule sagemaker_notebook_no_direct_internet_access_check when is_cfn_hook(%INPUT_DOCUMENT, %SAGEMAKER_NOTEBOOK_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%SAGEMAKER_NOTEBOOK_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.SAGEMAKER.PR.1]: Require an Amazon SageMaker notebook instance to prevent direct internet access
        [FIX]: Set 'DirectInternetAccess' to 'Disabled' and provide a 'SubnetId' and one or more 'SecurityGroupIds'.
        >>
}

#
# Parameterized Rules
#
rule check(sagemaker_notebook_instances) {
    %sagemaker_notebook_instances {
        # Scenario 2
        DirectInternetAccess exists

        # Scenario 3
        DirectInternetAccess is_string
        DirectInternetAccess == "Disabled"

        # Scenario 4,5 and 6
        check_is_string_and_not_empty(SubnetId) or
        check_local_references(%INPUT_DOCUMENT, SubnetId, "AWS::EC2::Subnet")

        SecurityGroupIds exists
        SecurityGroupIds is_list
        SecurityGroupIds not empty

        SecurityGroupIds[*] {
            check_is_string_and_not_empty(this) or
            check_local_references(%INPUT_DOCUMENT, this, "AWS::EC2::SecurityGroup")
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

rule is_cfn_hook(doc, SAGEMAKER_NOTEBOOK_INSTANCE_TYPE) {
    %doc.%SAGEMAKER_NOTEBOOK_INSTANCE_TYPE.resourceProperties exists
}

rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
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

### CT\.SAGEMAKER\.PR\.1 example templates<a name="ct-sagemaker-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: 'true'
      EnableDnsHostnames: 'true'
  Subnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Notebook SG1
      VpcId:
        Ref: VPC
  ExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - sagemaker.amazonaws.com
          Action:
          - sts:AssumeRole
      Path: /
      ManagedPolicyArns:
      - Fn::Sub: arn:${AWS::Partition}:iam::aws:policy/AmazonSageMakerFullAccess
  SageMakerNoteBookInstance:
    Type: AWS::SageMaker::NotebookInstance
    Properties:
      InstanceType: ml.t2.medium
      RoleArn:
        Fn::GetAtt:
        - ExecutionRole
        - Arn
      DirectInternetAccess: Disabled
      SubnetId:
        Fn::GetAtt:
        - Subnet
        - SubnetId
      SecurityGroupIds:
      - Fn::GetAtt:
        - SecurityGroup
        - GroupId
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - sagemaker.amazonaws.com
          Action:
          - sts:AssumeRole
      Path: /
      ManagedPolicyArns:
      - Fn::Sub: arn:${AWS::Partition}:iam::aws:policy/AmazonSageMakerFullAccess
  SageMakerNoteBookInstance:
    Type: AWS::SageMaker::NotebookInstance
    Properties:
      InstanceType: ml.t2.medium
      RoleArn:
        Fn::GetAtt:
        - ExecutionRole
        - Arn
      DirectInternetAccess: Enabled
```

## \[CT\.SAGEMAKER\.PR\.2\] Require Amazon SageMaker notebook instances to be deployed within a custom Amazon VPC<a name="ct-sagemaker-pr-2-description"></a>

This control checks whether an Amazon SageMaker notebook instance is configured to launch within a custom Amazon VPC\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::SageMaker::NotebookInstance`
+ **AWS CloudFormation guard rule: ** [CT\.SAGEMAKER\.PR\.2 rule specification](#ct-sagemaker-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.SAGEMAKER\.PR\.2 rule specification](#ct-sagemaker-pr-2-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.SAGEMAKER\.PR\.2 example templates](#ct-sagemaker-pr-2-templates) 

**Explanation**

As a best practice, we recommend that you keep your resources contained inside a VPC whenever possible, to ensure the secure network protection of your infrastructure\.

### Remediation for rule failure<a name="ct-sagemaker-pr-2-remediation"></a>

Set `SubnetId` to the identifier of an Amazon EC2 subnet and set `SecurityGroupIds` to a list containing one or more EC2 security group identifiers\.

The examples that follow show how to implement this remediation\.

#### Amazon SageMaker Notebook Instance \- Example<a name="ct-sagemaker-pr-2-remediation-1"></a>

Amazon SageMaker notebook instance configured with Amazon VPC connectivity\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "NotebookInstance": {
        "Type": "AWS::SageMaker::NotebookInstance",
        "Properties": {
            "InstanceType": "ml.t2.large",
            "RoleArn": {
                "Fn::GetAtt": [
                    "ExecutionRole",
                    "Arn"
                ]
            },
            "SubnetId": {
                "Fn::GetAtt": [
                    "Subnet",
                    "SubnetId"
                ]
            },
            "SecurityGroupIds": [
                {
                    "Fn::GetAtt": [
                        "SecurityGroup",
                        "GroupId"
                    ]
                }
            ]
        }
    }
}
```

**YAML example**

```
NotebookInstance:
  Type: AWS::SageMaker::NotebookInstance
  Properties:
    InstanceType: ml.t2.large
    RoleArn: !GetAtt 'ExecutionRole.Arn'
    SubnetId: !GetAtt 'Subnet.SubnetId'
    SecurityGroupIds:
      - !GetAtt 'SecurityGroup.GroupId'
```

### CT\.SAGEMAKER\.PR\.2 rule specification<a name="ct-sagemaker-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   sagemaker_notebook_instance_inside_vpc_check
# 
# Description:
#   This control checks whether an Amazon SageMaker notebook instance is configured to launch within a custom Amazon VPC.
# 
# Reports on:
#   AWS::SageMaker::NotebookInstance
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any SageMaker notebook instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a SageMaker notebook instance resource
#       And: 'SubnetId' has not been provided or provided as an empty string or non-valid local reference
#       And: 'SecurityGroupIds' has not been provided or provided as an empty list or a list that contains empty string
#            values or non-valid local references
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a SageMaker notebook instance resource
#       And: 'SubnetId' has been provided as a non-empty string or valid local reference
#       And: 'SecurityGroupIds' has not been provided or provided as an empty list or a list that contains empty string
#            values or non-valid local references
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a SageMaker notebook instance resource
#       And: 'SubnetId' has not been provided or provided as an empty string or non-valid local reference
#       And: 'SecurityGroupIds' have been provided as a non-empty list containing non-empty string values or
#            valid local references
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a SageMaker notebook instance resource
#       And: 'SubnetId' has been provided as a non-empty string or valid local reference
#       And: 'SecurityGroupIds' have been provided as a list containing one or more non-empty string values or
#            valid local references
#      Then: PASS

#
# Constants
#
let SAGEMAKER_NOTEBOOK_INSTANCE_TYPE = "AWS::SageMaker::NotebookInstance"
let INPUT_DOCUMENT = this

#
# Assignments
#
let sagemaker_notebook_instances = Resources.*[ Type == %SAGEMAKER_NOTEBOOK_INSTANCE_TYPE ]

#
# Primary Rules
#
rule sagemaker_notebook_instance_inside_vpc_check when is_cfn_template(%INPUT_DOCUMENT)
                                                       %sagemaker_notebook_instances not empty {
    check(%sagemaker_notebook_instances.Properties)
        <<
        [CT.SAGEMAKER.PR.2]: Require Amazon SageMaker notebook instances to be deployed within a custom Amazon VPC
            [FIX]: Set 'SubnetId' to the identifier of an Amazon EC2 subnet and set 'SecurityGroupIds' to a list containing one or more EC2 security group identifiers.
        >>
}

rule sagemaker_notebook_instance_inside_vpc_check when is_cfn_hook(%INPUT_DOCUMENT, %SAGEMAKER_NOTEBOOK_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%SAGEMAKER_NOTEBOOK_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.SAGEMAKER.PR.2]: Require Amazon SageMaker notebook instances to be deployed within a custom Amazon VPC
            [FIX]: Set 'SubnetId' to the identifier of an Amazon EC2 subnet and set 'SecurityGroupIds' to a list containing one or more EC2 security group identifiers.
        >>
}

rule check(sagemaker_notebook_instance) {
    %sagemaker_notebook_instance {
        # Scenario 2
        SubnetId exists

        # Scenario 3, 4 and 5
        check_is_string_and_not_empty(SubnetId) or
        check_local_references(%INPUT_DOCUMENT, SubnetId, "AWS::EC2::Subnet")

        SecurityGroupIds exists
        SecurityGroupIds is_list
        SecurityGroupIds not empty

        SecurityGroupIds[*] {
            check_is_string_and_not_empty(this) or
            check_local_references(%INPUT_DOCUMENT, this, "AWS::EC2::SecurityGroup")
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

rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
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

### CT\.SAGEMAKER\.PR\.2 example templates<a name="ct-sagemaker-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  NotebookInstance:
    Type: AWS::SageMaker::NotebookInstance
    Properties:
      InstanceType: ml.t2.large
      RoleArn: example-role-arn
      SubnetId: example-subnet-id
      SecurityGroupIds:
        - example-sg-id
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  NotebookInstance:
    Type: AWS::SageMaker::NotebookInstance
    Properties:
      InstanceType: ml.t2.large
      RoleArn: example-role-arn
```

## \[CT\.SAGEMAKER\.PR\.3\] Require Amazon SageMaker notebook instances to have root access disallowed<a name="ct-sagemaker-pr-3-description"></a>

This control checks whether Amazon SageMaker notebook instances allow root access\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::SageMaker::NotebookInstance`
+ **AWS CloudFormation guard rule: ** [CT\.SAGEMAKER\.PR\.3 rule specification](#ct-sagemaker-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.SAGEMAKER\.PR\.3 rule specification](#ct-sagemaker-pr-3-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.SAGEMAKER\.PR\.3 example templates](#ct-sagemaker-pr-3-templates) 

**Explanation**

By default, when you create a notebook instance, users that log into that notebook instance have root access\. Because users with root access have administrator privileges, users have access to edit all files on a notebook instance with root access enabled\. In adherence to the principle of least privilege, for security reasons, we recommend that you restrict root access to instance resources whenever possible, to avoid unintentional over\-provisioning of permissions\.

**Usage considerations**  
Lifecycle configurations associated with an Amazon SageMaker notebook instance always run with root access, even if you turn off root access for users\.

### Remediation for rule failure<a name="ct-sagemaker-pr-3-remediation"></a>

Set `RootAccess` to `Disabled`\.

The examples that follow show how to implement this remediation\.

#### Amazon SageMaker Notebook Instance \- Example<a name="ct-sagemaker-pr-3-remediation-1"></a>

Amazon SageMaker notebook instance configured with root access turned off\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "NotebookInstance": {
        "Type": "AWS::SageMaker::NotebookInstance",
        "Properties": {
            "InstanceType": "ml.t2.large",
            "RoleArn": {
                "Fn::GetAtt": [
                    "ExecutionRole",
                    "Arn"
                ]
            },
            "RootAccess": "Disabled"
        }
    }
}
```

**YAML example**

```
NotebookInstance:
  Type: AWS::SageMaker::NotebookInstance
  Properties:
    InstanceType: ml.t2.large
    RoleArn: !GetAtt 'ExecutionRole.Arn'
    RootAccess: Disabled
```

### CT\.SAGEMAKER\.PR\.3 rule specification<a name="ct-sagemaker-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   sagemaker_notebook_instance_root_access_check
# 
# Description:
#   This control checks whether Amazon SageMaker notebook instances allow root access.
# 
# Reports on:
#   AWS::SageMaker::NotebookInstance
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
#       And: The input document does not contain any SageMaker notebook instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a SageMaker notebook instance resource
#       And: 'RootAccess' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a SageMaker notebook instance resource
#       And: 'RootAccess' has been provided and is set to a value other than 'Disabled'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a SageMaker notebook instance resource
#       And: 'RootAccess' has been provided and is set to 'Disabled'
#      Then: PASS

#
# Constants
#
let SAGEMAKER_NOTEBOOK_INSTANCE_TYPE = "AWS::SageMaker::NotebookInstance"
let INPUT_DOCUMENT = this

#
# Assignments
#
let sagemaker_notebook_instances = Resources.*[ Type == %SAGEMAKER_NOTEBOOK_INSTANCE_TYPE ]

#
# Primary Rules
#
rule sagemaker_notebook_instance_root_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                                        %sagemaker_notebook_instances not empty {
    check(%sagemaker_notebook_instances.Properties)
        <<
        [CT.SAGEMAKER.PR.3]: Require Amazon SageMaker notebook instances to have root access disallowed
            [FIX]: Set 'RootAccess' to 'Disabled'.
        >>
}

rule sagemaker_notebook_instance_root_access_check when is_cfn_hook(%INPUT_DOCUMENT, %SAGEMAKER_NOTEBOOK_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%SAGEMAKER_NOTEBOOK_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.SAGEMAKER.PR.3]: Require Amazon SageMaker notebook instances to have root access disallowed
            [FIX]: Set 'RootAccess' to 'Disabled'.
        >>
}

rule check(sagemaker_notebook_instance) {
    %sagemaker_notebook_instance {
        # Scenarios 2, 3 and 4
        RootAccess exists
        RootAccess == "Disabled"
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

### CT\.SAGEMAKER\.PR\.3 example templates<a name="ct-sagemaker-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  NotebookInstance:
    Type: AWS::SageMaker::NotebookInstance
    Properties:
      InstanceType: ml.t2.large
      RoleArn: example-role-arn
      RootAccess: Disabled
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  NotebookInstance:
    Type: AWS::SageMaker::NotebookInstance
    Properties:
      InstanceType: ml.t2.large
      RoleArn: example-role-arn
      RootAccess: Enabled
```