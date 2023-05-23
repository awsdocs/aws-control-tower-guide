# Amazon Elastic Compute Cloud \(Amazon EC2\) Auto Scaling controls<a name="ec2-auto-scaling-rules"></a>

**Topics**
+ [\[CT\.AUTOSCALING\.PR\.1\] Require an Amazon EC2 Auto Scaling group to have multiple Availability Zones](#ct-autoscaling-pr-1-description)
+ [\[CT\.AUTOSCALING\.PR\.2\] Require an Amazon EC2 Auto Scaling group launch configuration to configure Amazon EC2 instances for IMDSv2](#ct-autoscaling-pr-2-description)
+ [\[CT\.AUTOSCALING\.PR\.3\] Require an Amazon EC2 Auto Scaling launch configuration to have a single\-hop metadata response limit](#ct-autoscaling-pr-3-description)
+ [\[CT\.AUTOSCALING\.PR\.4\] Require an Amazon EC2 Auto Scaling group associated with an AWS Elastic Load Balancing \(ELB\) to have ELB health checks activated](#ct-autoscaling-pr-4-description)
+ [\[CT\.AUTOSCALING\.PR\.5\] Require than an Amazon EC2 Auto Scaling group launch configuration does not have Amazon EC2 instances with public IP addresses](#ct-autoscaling-pr-5-description)
+ [\[CT\.AUTOSCALING\.PR\.6\] Require any Amazon EC2 Auto Scaling groups to use multiple instance types](#ct-autoscaling-pr-6-description)
+ [\[CT\.AUTOSCALING\.PR\.8\] Require an Amazon EC2 Auto Scaling group to have EC2 launch templates configured](#ct-autoscaling-pr-8-description)

## \[CT\.AUTOSCALING\.PR\.1\] Require an Amazon EC2 Auto Scaling group to have multiple Availability Zones<a name="ct-autoscaling-pr-1-description"></a>

This control checks whether your Amazon EC2 Auto Scaling group spans multiple Availability Zones\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::AutoScaling::AutoScalingGroup`
+ **AWS CloudFormation guard rule: ** [CT\.AUTOSCALING\.PR\.1 rule specification](#ct-autoscaling-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.AUTOSCALING\.PR\.1 rule specification](#ct-autoscaling-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.AUTOSCALING\.PR\.1 example templates](#ct-autoscaling-pr-1-templates) 

**Explanation**

Amazon EC2 Auto Scaling groups can be configured to use multiple Availability Zones\. An Auto Scaling group with a single Availability Zone is preferred in some use cases, such as batch\-jobs or when inter\-AZ transfer costs need to be kept to a minimum\. However, an Auto Scaling group that does not span multiple Availability Zones will not launch instances in another Availability Zone to compensate if the configured single Availability Zone becomes unavailable\.

### Remediation for rule failure<a name="ct-autoscaling-pr-1-remediation"></a>

Configure Auto Scaling groups with multiple Availability Zones\.

The examples that follow show how to implement this remediation\.

#### Auto Scaling group \- Example<a name="ct-autoscaling-pr-1-remediation-1"></a>

Auto Scaling group configured with multiple Availability Zones\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingGroup": {
        "Type": "AWS::AutoScaling::AutoScalingGroup",
        "Properties": {
            "LaunchTemplate": {
                "LaunchTemplateId": {
                    "Ref": "LaunchTemplate"
                },
                "Version": {
                    "Fn::GetAtt": "LaunchTemplate.LatestVersionNumber"
                }
            },
            "MaxSize": "1",
            "MinSize": "0",
            "DesiredCapacity": "1",
            "AvailabilityZones": [
                {
                    "Fn::Select": [
                        0,
                        {
                            "Fn::GetAZs": ""
                        }
                    ]
                },
                {
                    "Fn::Select": [
                        1,
                        {
                            "Fn::GetAZs": ""
                        }
                    ]
                }
            ]
        }
    }
}
```

**YAML example**

```
AutoScalingGroup:
  Type: AWS::AutoScaling::AutoScalingGroup
  Properties:
    LaunchTemplate:
      LaunchTemplateId: !Ref 'LaunchTemplate'
      Version: !GetAtt 'LaunchTemplate.LatestVersionNumber'
    MaxSize: '1'
    MinSize: '0'
    DesiredCapacity: '1'
    AvailabilityZones:
      - !Select
        - 0
        - !GetAZs ''
      - !Select
        - 1
        - !GetAZs ''
```

### CT\.AUTOSCALING\.PR\.1 rule specification<a name="ct-autoscaling-pr-1-rule"></a>

```
#####################################
##       Rule Specification        ##
#####################################

Rule Identifier:
  autoscaling_multiple_az_check

Description:
  Checks if Auto Scaling groups span multiple Availability Zones.

Reports on:
   AWS::AutoScaling::AutoScalingGroup

Evaluates:
  AWS CloudFormation, AWS CloudFormation hook

Rule Parameters:
  None

Scenarios:
  Scenario: 1
    Given: The input document is an AWS CloudFormation or CloudFormation hook document
      And: The input document does not contain any Auto Scaling groups
     Then: SKIP
  Scenario: 2
    Given: The input document is an AWS CloudFormation or CloudFormation hoo document
      And: The input document contains an Auto Scaling group resource
      And: 'AvailabilityZones' is not present on the Auto Scaling group resource
     Then: FAIL
  Scenario: 3
    Given: The input document is an AWS CloudFormation or CloudFormation hook document
      And: The input document contains an Auto Scaling group resource
      And: 'AvailabilityZones' is present on the Auto Scaling group resource
      And: The number of 'AvailabilityZones' present is less than 2 (< 2) or the number of
           unique 'AvailabilityZones' provided is less than 2 (< 2)
     Then: FAIL
  Scenario: 4
    Given: The input document is an AWS CloudFormation or CloudFormation Hook Document
      And: The input document contains an Auto Scaling group resource
      And: 'AvailabilityZones' is present on the Auto Scaling group resource
      And: The number of 'AvailabilityZones' present is greater than or equal to 2 (>= 2)
      And: At least two unique 'AvailabilityZones' have been provided
     Then: PASS
```

### CT\.AUTOSCALING\.PR\.1 example templates<a name="ct-autoscaling-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        ImageId:
          Ref: LatestAmiId
        InstanceType: t3.micro
  AutoScalingGroup:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      LaunchTemplate:
        LaunchTemplateId:
          Ref: LaunchTemplate
        Version:
          Fn::GetAtt: LaunchTemplate.LatestVersionNumber
      MaxSize: '1'
      MinSize: '0'
      DesiredCapacity: '1'
      AvailabilityZones:
      - Fn::Select:
        - 0
        - Fn::GetAZs: ""
      - Fn::Select:
        - 1
        - Fn::GetAZs: ""
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        ImageId:
          Ref: LatestAmiId
        InstanceType: t3.micro
  AutoScalingGroup:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      LaunchTemplate:
        LaunchTemplateId:
          Ref: LaunchTemplate
        Version:
          Fn::GetAtt: LaunchTemplate.LatestVersionNumber
      MaxSize: '1'
      MinSize: '0'
      DesiredCapacity: '1'
      AvailabilityZones:
      - Fn::Select:
        - 0
        - Fn::GetAZs: ""
```

## \[CT\.AUTOSCALING\.PR\.2\] Require an Amazon EC2 Auto Scaling group launch configuration to configure Amazon EC2 instances for IMDSv2<a name="ct-autoscaling-pr-2-description"></a>

This control checks whether an Amazon EC2 Auto Scaling launch configuration is configured to require the use of Instance Metadata Service Version 2 \(IMDSv2\)\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::AutoScaling::LaunchConfiguration`
+ **AWS CloudFormation guard rule: ** [CT\.AUTOSCALING\.PR\.2 rule specification](#ct-autoscaling-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.AUTOSCALING\.PR\.2 rule specification](#ct-autoscaling-pr-2-rule) 
+ For examples of PASS and FAIL CloudFront Templates related to this control, see: [CT\.AUTOSCALING\.PR\.2 example templates](#ct-autoscaling-pr-2-templates) 

**Explanation**

IMDS provides data about your instance, which you can use to configure or manage the running instance\.

Version 2 of the IMDS adds protections that weren't available in IMDSv1, to safeguard your EC2 instances further\.

**Usage considerations**  
This control applies only to Amazon EC2 Auto Scaling launch configurations that allow access to instance metadata\.

### Remediation for rule failure<a name="ct-autoscaling-pr-2-remediation"></a>

Provide a `MetadataOptions` configuration and set the value of `HttpTokens` to `required`\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Auto Scaling Launch Configuration \- Example<a name="ct-autoscaling-pr-2-remediation-1"></a>

Amazon EC2 Auto Scaling launch configuration with IMDSv2 enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingLaunchConfiguration": {
        "Type": "AWS::AutoScaling::LaunchConfiguration",
        "Properties": {
            "ImageId": {
                "Ref": "LatestAmiId"
            },
            "InstanceType": "t3.micro",
            "MetadataOptions": {
                "HttpTokens": "required"
            }
        }
    }
}
```

**YAML example**

```
AutoScalingLaunchConfiguration:
  Type: AWS::AutoScaling::LaunchConfiguration
  Properties:
    ImageId: !Ref 'LatestAmiId'
    InstanceType: t3.micro
    MetadataOptions:
      HttpTokens: required
```

### CT\.AUTOSCALING\.PR\.2 rule specification<a name="ct-autoscaling-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   autoscaling_launch_config_requires_imdsv2_check
# 
# Description:
#   This control checks whether an Amazon EC2 Auto Scaling launch configuration is configured to require the use of Instance Metadata Service Version 2 (IMDSv2).
# 
# Reports on:
#   AWS::AutoScaling::LaunchConfiguration
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#  Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any Autoscaling launch configuration resources
#      Then: SKIP
#  Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Autoscaling launch configuration resource
#       And: 'MetadataOptions' has been provided.
#       And: 'MetadataOptions.HttpEndpoint' has been provided is equal to 'disabled'
#      Then: SKIP
#  Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Autoscaling launch configuration resource
#       And: 'MetadataOptions.HttpEndpoint' has not been provided or has been provided and is equal to 'enabled'
#       And: 'MetadataOptions.HttpTokens' has not been provided
#      Then: FAIL
#  Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Autoscaling launch configuration resource
#       And: 'MetadataOptions' has been provided.
#       And: 'MetadataOptions.HttpEndpoint' has not been provided or has been provided and is equal to 'enabled'
#       And: 'MetadataOptions.HttpTokens' has been provided and set to a value other than 'required'
#      Then: FAIL
#  Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Autoscaling launch configuration resource
#       And: 'MetadataOptions' has been provided.
#       And: 'MetadataOptions.HttpEndpoint' has not been provided or has been provided and is equal to 'enabled'
#       And: 'MetadataOptions.HttpTokens' has been provided and set to 'required'
#      Then: PASS

#
# Constants
#
let AUTOSCALING_LAUNCH_CONFIGURATION_TYPE = "AWS::AutoScaling::LaunchConfiguration"
let INPUT_DOCUMENT = this

#
# Assignments
#
let autoscaling_launch_configurations = Resources.*[ Type == %AUTOSCALING_LAUNCH_CONFIGURATION_TYPE ]

#
# Primary Rules
#
rule autoscaling_launch_config_requires_imdsv2_check when is_cfn_template(%INPUT_DOCUMENT)
                                                          %autoscaling_launch_configurations not empty {
    check(%autoscaling_launch_configurations.Properties)
        <<
        [CT.AUTOSCALING.PR.2]: Require an Amazon EC2 Auto Scaling group launch configuration to configure Amazon EC2 instances for IMDSv2
            [FIX]: Provide a 'MetadataOptions' configuration and set the value of 'HttpTokens' to 'required'.
        >>
}

rule autoscaling_launch_config_requires_imdsv2_check when is_cfn_hook(%INPUT_DOCUMENT, %AUTOSCALING_LAUNCH_CONFIGURATION_TYPE) {
    check(%INPUT_DOCUMENT.%AUTOSCALING_LAUNCH_CONFIGURATION_TYPE.resourceProperties)
        <<
        [CT.AUTOSCALING.PR.2]: Require an Amazon EC2 Auto Scaling group launch configuration to configure Amazon EC2 instances for IMDSv2
            [FIX]: Provide a 'MetadataOptions' configuration and set the value of 'HttpTokens' to 'required'.
        >>
}

#
# Parameterized Rules
#
rule check(autoscaling_launch_configuration) {
    %autoscaling_launch_configuration [
        # Scenario 2
        filter_autoscaling_launch_configurations(this)
    ] {
        # Scenario 3, 4 and 5
        MetadataOptions exists
        MetadataOptions is_struct

        MetadataOptions {
            HttpTokens exists
            HttpTokens == "required"
        }
    }
}

rule filter_autoscaling_launch_configurations(autoscaling_launch_configurations) {
    %autoscaling_launch_configurations {
        MetadataOptions not exists or
        filter_metadata_options(this)
    }
}

rule filter_metadata_options(metadata_options) {
    %metadata_options {
        MetadataOptions is_struct
        MetadataOptions {
            HttpEndpoint not exists or
            HttpEndpoint == "enabled"
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

### CT\.AUTOSCALING\.PR\.2 example templates<a name="ct-autoscaling-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  AutoScalingLaunchConfiguration:
    Type: AWS::AutoScaling::LaunchConfiguration
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      MetadataOptions:
        HttpTokens: required
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  AutoScalingLaunchConfiguration:
    Type: AWS::AutoScaling::LaunchConfiguration
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      MetadataOptions:
        HttpTokens: optional
```

## \[CT\.AUTOSCALING\.PR\.3\] Require an Amazon EC2 Auto Scaling launch configuration to have a single\-hop metadata response limit<a name="ct-autoscaling-pr-3-description"></a>

This control checks whether an Amazon EC2 Auto Scaling launch configuration has a metadata token hop limit set to `1`\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::AutoScaling::LaunchConfiguration`
+ **AWS CloudFormation guard rule: ** [CT\.AUTOSCALING\.PR\.3 rule specification](#ct-autoscaling-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.AUTOSCALING\.PR\.3 rule specification](#ct-autoscaling-pr-3-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.AUTOSCALING\.PR\.3 example templates](#ct-autoscaling-pr-3-templates) 

**Explanation**

The Instance Metadata Service \(IMDS\) provides metadata information about an EC2 instance, which is useful for application configuration\. Restricting the HTTP `PUT` response for the metadata service to the EC2 instance protects the IMDS from unauthorized use\.

The Time To Live \(TTL\) field in the IP packet is reduced by one on every hop\. This reduction can be used to ensure that the packet does not travel outside EC2\. IMDSv2 protects EC2 instances that may have been misconfigured as open routers, layer 3 firewalls, VPNs, tunnels, or NAT devices, which prevents unauthorized users from retrieving metadata\. With IMDSv2, the `PUT` response that contains the secret token cannot travel outside the instance, because the default metadata response hop limit is set to `1`\. However, if this value is greater than `1`, the token can leave the EC2 instance\.

**Usage considerations**  
This control applies only to Amazon EC2 Auto Scaling launch configurations that allow access to instance metadata\.
This control is incompatible with Amazon EC2 Auto Scaling launch configurations that require a token hop limit of `2`\.

### Remediation for rule failure<a name="ct-autoscaling-pr-3-remediation"></a>

Provide a `MetadataOptions` configuration with `HttpPutResponseLimit` set to `1`\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Auto Scaling Launch Configuration \- Example One<a name="ct-autoscaling-pr-3-remediation-1"></a>

Amazon EC2 Auto Scaling launch configuration configured with access to instance metadata enabled by means of AWS CloudFormation defaults with a token hop limit of `1`\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingLaunchConfiguration": {
        "Type": "AWS::AutoScaling::LaunchConfiguration",
        "Properties": {
            "ImageId": {
                "Ref": "LatestAmiId"
            },
            "InstanceType": "t3.micro",
            "MetadataOptions": {
                "HttpPutResponseHopLimit": 1
            }
        }
    }
}
```

**YAML example**

```
AutoScalingLaunchConfiguration:
  Type: AWS::AutoScaling::LaunchConfiguration
  Properties:
    ImageId: !Ref 'LatestAmiId'
    InstanceType: t3.micro
    MetadataOptions:
      HttpPutResponseHopLimit: 1
```

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Auto Scaling Launch Configuration \- Example Two<a name="ct-autoscaling-pr-3-remediation-2"></a>

Amazon EC2 Auto Scaling launch configuration configured with access to instance metadata enabled by means of the `MetadataOptions` property with a token hop limit of `1`\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingLaunchConfiguration": {
        "Type": "AWS::AutoScaling::LaunchConfiguration",
        "Properties": {
            "ImageId": {
                "Ref": "LatestAmiId"
            },
            "InstanceType": "t3.micro",
            "MetadataOptions": {
                "HttpEndpoint": "enabled",
                "HttpPutResponseHopLimit": 1
            }
        }
    }
}
```

**YAML example**

```
AutoScalingLaunchConfiguration:
  Type: AWS::AutoScaling::LaunchConfiguration
  Properties:
    ImageId: !Ref 'LatestAmiId'
    InstanceType: t3.micro
    MetadataOptions:
      HttpEndpoint: enabled
      HttpPutResponseHopLimit: 1
```

### CT\.AUTOSCALING\.PR\.3 rule specification<a name="ct-autoscaling-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   autoscaling_launch_config_hop_limit_check
# 
# Description:
#   This control checks whether an Amazon EC2 Auto Scaling launch configuration has a metadata token hop limit set to '1'.
# 
# Reports on:
#   AWS::AutoScaling::LaunchConfiguration
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document does not contain any Amazon EC2 Auto Scaling launch configuration resources
#       Then: SKIP
#   Scenario: 2
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an Amazon EC2 Auto Scaling launch configuration resource
#        And: 'MetadataOptions' has been provided.
#        And: 'MetadataOptions.HttpEndpoint' has been provided is equal to 'disabled'
#       Then: SKIP
#   Scenario: 3
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an Amazon EC2 Auto Scaling launch configuration resource
#        And: 'MetadataOptions' has not been provided.
#       Then: FAIL
#   Scenario: 4
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an Amazon EC2 Auto Scaling launch configuration resource
#        And: 'MetadataOptions' has been provided.
#        And: 'MetadataOptions.HttpEndpoint' has not been provided or has been provided and is equal to 'enabled'
#        And: 'MetadataOptions.HttpPutResponseHopLimit' has not been provided.
#       Then: FAIL
#   Scenario: 5
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an Amazon EC2 Auto Scaling launch configuration resource
#        And: 'MetadataOptions' has been provided.
#        And: 'MetadataOptions.HttpEndpoint' has not been provided or has been provided and is equal to 'enabled'
#        And: 'MetadataOptions.HttpPutResponseHopLimit' has been provided but is not equal to an integer of '1'.
#       Then: FAIL
#   Scenario: 6
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an Amazon EC2 Auto Scaling launch configuration resource
#        And: 'MetadataOptions' has been provided.
#        And: 'MetadataOptions.HttpEndpoint' has not been provided or has been provided and is equal to 'enabled'
#        And: 'MetadataOptions.HttpPutResponseHopLimit' has been provided and is equal to an integer of '1'.
#       Then: PASS

#
# Constants
#
let AUTOSCALING_LAUNCH_CONFIG_TYPE = "AWS::AutoScaling::LaunchConfiguration"
let INPUT_DOCUMENT = this

#
# Assignments
#
let autoscaling_launch_configurations = Resources.*[ Type == %AUTOSCALING_LAUNCH_CONFIG_TYPE]

#
# Primary Rules
#
rule autoscaling_launch_config_hop_limit_check when is_cfn_template(this)
                                                    %autoscaling_launch_configurations not empty {
    check(%autoscaling_launch_configurations.Properties)
        <<
        [CT.AUTOSCALING.PR.3]: Require an Amazon EC2 Auto Scaling launch configuration to have a single-hop metadata response limit
        [FIX]: Provide a 'MetadataOptions' configuration with 'HttpPutResponseLimit' set to '1'.
        >>
}

rule autoscaling_launch_config_hop_limit_check when is_cfn_hook(%INPUT_DOCUMENT, %AUTOSCALING_LAUNCH_CONFIG_TYPE) {
    check(%INPUT_DOCUMENT.%AUTOSCALING_LAUNCH_CONFIG_TYPE.resourceProperties)
        <<
        [CT.AUTOSCALING.PR.3]: Require an Amazon EC2 Auto Scaling launch configuration to have a single-hop metadata response limit
        [FIX]: Provide a 'MetadataOptions' configuration with 'HttpPutResponseLimit' set to '1'.
        >>
}

#
# Parameterized Rules
#
rule check(autoscaling_launch_configurations) {
    %autoscaling_launch_configurations[
        # Scenario 2, 3 and 4
        filter_launch_configuration(this)
    ] {
        # Scenario 5 and 6
        MetadataOptions exists
        MetadataOptions is_struct

        MetadataOptions {
            HttpPutResponseHopLimit exists
            HttpPutResponseHopLimit == 1
        }
    }
}

rule filter_launch_configuration(autoscaling_launch_configurations) {
    %autoscaling_launch_configurations {
        MetadataOptions not exists or
        filter_metadata_options_provided(this)
    }
}

rule filter_metadata_options_provided(options) {
    %options {
        MetadataOptions is_struct
        MetadataOptions {
            HttpEndpoint not exists or
            HttpEndpoint == "enabled"
        }
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

### CT\.AUTOSCALING\.PR\.3 example templates<a name="ct-autoscaling-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  AutoScalingLaunchConfiguration:
    Type: AWS::AutoScaling::LaunchConfiguration
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  AutoScalingLaunchConfiguration:
    Type: AWS::AutoScaling::LaunchConfiguration
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      MetadataOptions:
        HttpEndpoint: enabled
        HttpPutResponseHopLimit: 2
```

## \[CT\.AUTOSCALING\.PR\.4\] Require an Amazon EC2 Auto Scaling group associated with an AWS Elastic Load Balancing \(ELB\) to have ELB health checks activated<a name="ct-autoscaling-pr-4-description"></a>

This control checks whether your Amazon EC2 Auto Scaling groups that are associated with a load balancer are using Elastic Load Balancing health checks\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::AutoScaling::AutoScalingGroup`
+ **AWS CloudFormation guard rule: ** [CT\.AUTOSCALING\.PR\.4 rule specification](#ct-autoscaling-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.AUTOSCALING\.PR\.4 rule specification](#ct-autoscaling-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.AUTOSCALING.PR.4) 

**Explanation**

This configuration requirement ensures that the group can determine an instance's health based on additional tests provided by the load balancer\. Using Elastic Load Balancing health checks can help support the availability of applications that use EC2 Auto Scaling groups\.

**Usage considerations**  
This control only applies to Auto Scaling groups associated with a Classic Load Balancer or Target Group

### Remediation for rule failure<a name="ct-autoscaling-pr-4-remediation"></a>

Configure Amazon EC2 Auto Scaling groups associated with an Elastic Load Balancing to use Elastic Load Balancing health checks\.

The examples that follow show how to implement this remediation\.

#### Auto Scaling group \- Example One<a name="ct-autoscaling-pr-4-remediation-1"></a>

Auto Scaling group with a Classic Load Balancer association and Elastic Load Balancing health checks\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingGroup": {
        "Type": "AWS::AutoScaling::AutoScalingGroup",
        "Properties": {
            "LaunchTemplate": {
                "LaunchTemplateId": {
                    "Ref": "LaunchTemplate"
                },
                "Version": {
                    "Fn::GetAtt": "LaunchTemplate.LatestVersionNumber"
                }
            },
            "MaxSize": "1",
            "MinSize": "0",
            "DesiredCapacity": "1",
            "LoadBalancerNames": [
                {
                    "Ref": "ElasticLoadBalancer"
                }
            ],
            "HealthCheckType": "ELB",
            "VPCZoneIdentifier": [
                {
                    "Ref": "Subnet"
                }
            ]
        }
    }
}
```

**YAML example**

```
AutoScalingGroup:
  Type: AWS::AutoScaling::AutoScalingGroup
  Properties:
    LaunchTemplate:
      LaunchTemplateId: !Ref 'LaunchTemplate'
      Version: !GetAtt 'LaunchTemplate.LatestVersionNumber'
    MaxSize: '1'
    MinSize: '0'
    DesiredCapacity: '1'
    LoadBalancerNames:
      - !Ref 'ElasticLoadBalancer'
    HealthCheckType: ELB
    VPCZoneIdentifier:
      - !Ref 'Subnet'
```

The examples that follow show how to implement this remediation\.

#### Auto Scaling group \- Example Two<a name="ct-autoscaling-pr-4-remediation-2"></a>

Auto Scaling group with a Target Group association and Elastic Load Balancing health checks\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingGroup": {
        "Type": "AWS::AutoScaling::AutoScalingGroup",
        "Properties": {
            "LaunchTemplate": {
                "LaunchTemplateId": {
                    "Ref": "LaunchTemplate"
                },
                "Version": {
                    "Fn::GetAtt": "LaunchTemplate.LatestVersionNumber"
                }
            },
            "MaxSize": "1",
            "MinSize": "0",
            "DesiredCapacity": "1",
            "TargetGroupARNs": [
                {
                    "Ref": "ELBv2TargetGroup"
                }
            ],
            "HealthCheckType": "ELB",
            "VPCZoneIdentifier": [
                {
                    "Ref": "Subnet"
                }
            ]
        }
    }
}
```

**YAML example**

```
AutoScalingGroup:
  Type: AWS::AutoScaling::AutoScalingGroup
  Properties:
    LaunchTemplate:
      LaunchTemplateId: !Ref 'LaunchTemplate'
      Version: !GetAtt 'LaunchTemplate.LatestVersionNumber'
    MaxSize: '1'
    MinSize: '0'
    DesiredCapacity: '1'
    TargetGroupARNs:
      - !Ref 'ELBv2TargetGroup'
    HealthCheckType: ELB
    VPCZoneIdentifier:
      - !Ref 'Subnet'
```

### CT\.AUTOSCALING\.PR\.4 rule specification<a name="ct-autoscaling-pr-4-rule"></a>

```
#####################################
##       Rule Specification        ##
#####################################

Rule Identifier:
  autoscaling_group_elb_healthcheck_required_check

Description:
  This control checks whether your Auto Scaling groups that are associated with a load balancer are using
  Elastic Load Balancing health checks.

Reports on:
  AWS::AutoScaling::AutoScalingGroup

Evaluates:
  AWS CloudFormation, AWS CloudFormation hook

Rule Parameters:
  None

Scenarios:
  Scenario: 1
    Given: The input document is an AWS CloudFormation or CloudFormation hook document
      And: The input document does not contain any Auto Scaling group
     Then: SKIP
  Scenario: 2
    Given: The input document is an AWS CloudFormation or CloudFormation hook document
      And: The input document contains an Auto Scaling group resource
      And: 'LoadBalancerNames' or 'TargetGroupARNs' are not present on the Auto Scaling group resource or empty lists
     Then: SKIP
  Scenario: 3
    Given: The input document is an AWS CloudFormation or CloudFormation hook document
      And: The input document contains an Auto Scaling group resource
      And: 'LoadBalancerNames' or 'TargetGroupARNs' are present on the Auto Scaling group with at least
           one configuration
      And: 'HealthCheckType' is not present
     Then: FAIL
  Scenario: 4
    Given: The input document is an AWS CloudFormation or CloudFormation hook document
      And: The input document contains an Auto Scaling group resource
      And: 'LoadBalancerNames' or 'TargetGroupARNs' are present on the Auto Scaling group with at least
           one configuration
      And: 'HealthCheckType' is present and set to a value other than 'ELB' (e.g. 'EC2')
     Then: FAIL
  Scenario: 5
    Given: The input document is an AWS CloudFormation or CloudFormation hook document
      And: The input document contains an Auto Scaling group resource
      And: 'LoadBalancerNames' or 'TargetGroupARNs' are present on the Auto Scaling group with at least
           one configuration
      And: 'HealthCheckType' is present and set to 'ELB'
     Then: PASS
```

## \[CT\.AUTOSCALING\.PR\.5\] Require than an Amazon EC2 Auto Scaling group launch configuration does not have Amazon EC2 instances with public IP addresses<a name="ct-autoscaling-pr-5-description"></a>

This control checks whether Amazon EC2 Auto Scaling groups have public IP addresses configured through Launch Configurations\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::AutoScaling::LaunchConfiguration`
+ **AWS CloudFormation guard rule: ** [CT\.AUTOSCALING\.PR\.5 rule specification](#ct-autoscaling-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.AUTOSCALING\.PR\.5 rule specification](#ct-autoscaling-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.AUTOSCALING\.PR\.5 example templates](#ct-autoscaling-pr-5-templates) 

**Explanation**

Amazon EC2 instances in an Auto Scaling group launch configuration should not have an associated public IP address, except for in limited edge cases\. Amazon EC2 instances should only be accessible from behind a load balancer instead of being directly exposed to the internet\.

### Remediation for rule failure<a name="ct-autoscaling-pr-5-remediation"></a>

Set `AssociatePublicIpAddress` to false on Auto Scaling Launch Configurations\.

The examples that follow show how to implement this remediation\.

#### Auto Scaling Launch Configuration \- Example<a name="ct-autoscaling-pr-5-remediation-1"></a>

Auto Scaling Launch Configuration configured to disable public IP address association\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingLaunchConfiguration": {
        "Type": "AWS::AutoScaling::LaunchConfiguration",
        "Properties": {
            "ImageId": {
                "Ref": "LatestAmiId"
            },
            "InstanceType": "t3.micro",
            "AssociatePublicIpAddress": false
        }
    }
}
```

**YAML example**

```
AutoScalingLaunchConfiguration:
  Type: AWS::AutoScaling::LaunchConfiguration
  Properties:
    ImageId: !Ref 'LatestAmiId'
    InstanceType: t3.micro
    AssociatePublicIpAddress: false
```

### CT\.AUTOSCALING\.PR\.5 rule specification<a name="ct-autoscaling-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   autoscaling_launch_config_public_ip_disabled_check
# 
# Description:
#   Checks if Auto Scaling Launch Configurations have been configured to disable public IP address association.
# 
# Reports on:
#   AWS::Auto Scaling::LaunchConfiguration
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation Hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Auto Scaling Launch Configuration Resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Auto Scaling Launch Configuration Resource
#       And: 'AssociatePublicIpAddress' is not present on the Auto Scaling Launch Configuration Resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Auto Scaling Launch Configuration Resource
#       And: 'AssociatePublicIpAddress' is present on the Auto Scaling Launch Configuration Resource
#            and is set to bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Auto Scaling Launch Configuration Resource
#       And: 'AssociatePublicIpAddress' is present on the Auto Scaling Launch Configuration Resource
#            and is set to bool(false)
#      Then: PASS

#
# Constants
#
let AUTOSCALING_LAUNCH_CONFIGURATION_TYPE = 'AWS::AutoScaling::LaunchConfiguration'
let INPUT_DOCUMENT = this

#
# Assignments
#
let autoscaling_launch_configurations = Resources.*[ Type == %AUTOSCALING_LAUNCH_CONFIGURATION_TYPE ]

#
# Primary Rules
#
rule autoscaling_launch_config_public_ip_disabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                             %autoscaling_launch_configurations not empty {

    check(%autoscaling_launch_configurations.Properties)
        <<
        [CT.AUTOSCALING.PR.5]: Require than an Amazon EC2 Auto Scaling group launch configuration does not have EC2 instances with public IP addresses
        [FIX]: Set 'AssociatePublicIpAddress' to false on Auto Scaling Launch Configurations.
        >>

}

rule autoscaling_launch_config_public_ip_disabled_check when is_cfn_hook(%INPUT_DOCUMENT, %AUTOSCALING_LAUNCH_CONFIGURATION_TYPE) {

    check(%INPUT_DOCUMENT.%AUTOSCALING_LAUNCH_CONFIGURATION_TYPE.resourceProperties)
        <<
        [CT.AUTOSCALING.PR.5]: Require than an Amazon EC2 Auto Scaling group launch configuration does not have EC2 instances with public IP addresses
        [FIX]: Set 'AssociatePublicIpAddress' to false on Auto Scaling Launch Configurations.
        >>
}

#
# Parameterized Rules
#
rule check(launch_configuration) {
    %launch_configuration {
        # Scenario 2
        AssociatePublicIpAddress exists
        # Scenarios 3 and 4
        AssociatePublicIpAddress == false
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

### CT\.AUTOSCALING\.PR\.5 example templates<a name="ct-autoscaling-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  AutoScalingLaunchConfiguration:
    Type: AWS::AutoScaling::LaunchConfiguration
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      AssociatePublicIpAddress: false
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  AutoScalingLaunchConfiguration:
    Type: AWS::AutoScaling::LaunchConfiguration
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      AssociatePublicIpAddress: true
```

## \[CT\.AUTOSCALING\.PR\.6\] Require any Amazon EC2 Auto Scaling groups to use multiple instance types<a name="ct-autoscaling-pr-6-description"></a>

This control checks whether an Amazon EC2 Auto Scaling group uses multiple instance types through a mixed instance policy and explicit instance type overrides\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::AutoScaling::AutoScalingGroup`
+ **AWS CloudFormation guard rule: ** [CT\.AUTOSCALING\.PR\.6 rule specification](#ct-autoscaling-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.AUTOSCALING\.PR\.6 rule specification](#ct-autoscaling-pr-6-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.AUTOSCALING\.PR\.6 example templates](#ct-autoscaling-pr-6-templates) 

**Explanation**

You can enhance availability by deploying your application across multiple instance types running in multiple Availability Zones\. AWS Control Tower recommends using multiple instance types so that the Auto Scaling group can launch another instance type if there is insufficient instance capacity in your chosen Availability Zones\.

**Usage considerations**  
This control applies only to Amazon EC2 Auto Scaling groups that do not use attribute\-based instance type selection within a mixed instances policy \(configured by means of the `InstanceRequirements` property within mixed instances policy `Overrides`\)\.

### Remediation for rule failure<a name="ct-autoscaling-pr-6-remediation"></a>

Within a `MixedInstancePolicy` configuration, provide a `LaunchTemplate` configuration with two entries in the `Overrides` property\. Within each override, set the `InstanceType` property to a different Amazon EC2 instance type\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Auto Scaling Group \- Example<a name="ct-autoscaling-pr-6-remediation-1"></a>

Amazon EC2 Auto Scaling group configured with multiple instance types\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingGroup": {
        "Type": "AWS::AutoScaling::AutoScalingGroup",
        "Properties": {
            "VPCZoneIdentifier": [
                {
                    "Ref": "Subnet"
                }
            ],
            "MaxSize": "2",
            "MinSize": "1",
            "MixedInstancesPolicy": {
                "LaunchTemplate": {
                    "LaunchTemplateSpecification": {
                        "LaunchTemplateId": {
                            "Ref": "EC2LaunchTemplate"
                        },
                        "Version": {
                            "Fn::GetAtt": [
                                "EC2LaunchTemplate",
                                "LatestVersionNumber"
                            ]
                        }
                    },
                    "Overrides": [
                        {
                            "InstanceType": "t3.micro"
                        },
                        {
                            "InstanceType": "m5.large"
                        }
                    ]
                }
            }
        }
    }
}
```

**YAML example**

```
AutoScalingGroup:
  Type: AWS::AutoScaling::AutoScalingGroup
  Properties:
    VPCZoneIdentifier:
      - !Ref 'Subnet'
    MaxSize: '2'
    MinSize: '1'
    MixedInstancesPolicy:
      LaunchTemplate:
        LaunchTemplateSpecification:
          LaunchTemplateId: !Ref 'EC2LaunchTemplate'
          Version: !GetAtt 'EC2LaunchTemplate.LatestVersionNumber'
        Overrides:
          - InstanceType: t3.micro
          - InstanceType: m5.large
```

### CT\.AUTOSCALING\.PR\.6 rule specification<a name="ct-autoscaling-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   autoscaling_mixed_instances_policy_multiple_instance_types_check
# 
# Description:
#   This control checks whether an Amazon EC2 Auto Scaling group uses multiple instance types through a mixed instance policy and explicit instance type overrides.
# 
# Reports on:
#   AWS::AutoScaling::AutoscalingGroup
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#  Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any Autoscaling Group resources
#      Then: SKIP
#  Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains Autoscaling Group resources
#       And: 'MixedInstancesPolicy.LaunchTemplate.Overrides' has been provided as a list
#       And: There exists any 'Overrides' entry where 'InstanceRequirements' is present
#       And: There exists no 'Overrides' entry where 'InstanceType' is present
#      Then: SKIP
#  Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains Autoscaling Group resources
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has been provided
#       And: 'MixedInstancesPolicy.LaunchTemplate.Overrides' has not been provided
#      Then: FAIL
#  Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains Autoscaling Group resources
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has been provided
#       And: 'MixedInstancesPolicy.LaunchTemplate.Overrides' has been provided as a list
#       And: 'InstanceType' is not present or is present as a empty string in 'MixedInstancesPolicy.LaunchTemplate.Overrides'
#      Then: FAIL
#  Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains Autoscaling Group resources
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has been provided
#       And: 'MixedInstancesPolicy.LaunchTemplate.Overrides' has been provided as a list
#       And: There exists any 'Overrides' entry where 'InstanceRequirements' is present
#       And: There exists any 'Overrides' entry where 'InstanceType' is present
#      Then: FAIL
#  Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains Autoscaling Group resources
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has been provided
#       And: 'MixedInstancesPolicy.LaunchTemplate.Overrides' has been provided as a list
#       And: 'InstanceType' is present in 'MixedInstancesPolicy.LaunchTemplate.Overrides' as a non empty string
#       And: Length of 'MixedInstancesPolicy.LaunchTemplate.Overrides' is less than or equal to 1
#      Then: FAIL
#  Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains Autoscaling Group resources
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has been provided
#       And: 'MixedInstancesPolicy.LaunchTemplate.Overrides' has been provided as a list
#       And: 'InstanceType' is present in 'MixedInstancesPolicy.LaunchTemplate.Overrides' as a non empty string
#       And: Length of 'MixedInstancesPolicy.LaunchTemplate.Overrides' is greater than 1
#      Then: PASS

#
# Constants
#
let AUTOSCALING_GROUP_TYPE = "AWS::AutoScaling::AutoScalingGroup"
let INPUT_DOCUMENT = this

#
# Assignments
#
let autoscaling_groups = Resources.*[ Type == %AUTOSCALING_GROUP_TYPE ]

#
# Primary Rules
#
rule autoscaling_mixed_instances_policy_multiple_instance_types_check when is_cfn_template(%INPUT_DOCUMENT)
                                                                           %autoscaling_groups not empty {
    check(%autoscaling_groups.Properties)
        <<
        [CT.AUTOSCALING.PR.6]: Require any Amazon EC2 Auto Scaling groups to use multiple instance types
            [FIX]: Within a 'MixedInstancePolicy' configuration, provide a 'LaunchTemplate' configuration with two entries in the 'Overrides' property. Within each override, set the 'InstanceType' property to a different Amazon EC2 instance type.
        >>
}

rule autoscaling_mixed_instances_policy_multiple_instance_types_check when is_cfn_hook(%INPUT_DOCUMENT, %AUTOSCALING_GROUP_TYPE) {
    check(%INPUT_DOCUMENT.%AUTOSCALING_GROUP_TYPE.resourceProperties)
        <<
        [CT.AUTOSCALING.PR.6]: Require any Amazon EC2 Auto Scaling groups to use multiple instance types
            [FIX]: Within a 'MixedInstancePolicy' configuration, provide a 'LaunchTemplate' configuration with two entries in the 'Overrides' property. Within each override, set the 'InstanceType' property to a different Amazon EC2 instance type.
        >>
}

#
# Parameterized Rules
#
rule check(autoscaling_group) {
    %autoscaling_group [
        # Scenario 2
        filter_asg_no_instance_requirement_overrides(this)
    ] {
        # Scenario 4, 5, 6
        MixedInstancesPolicy exists
        MixedInstancesPolicy is_struct

        MixedInstancesPolicy {
            LaunchTemplate exists
            LaunchTemplate is_struct

            LaunchTemplate {
                LaunchTemplateSpecification exists
                LaunchTemplateSpecification is_struct

                Overrides exists
                Overrides is_list
                Overrides not empty

                Overrides[0] exists
                Overrides[1] exists

                Overrides[*] {
                    InstanceType exists
                    check_is_string_and_not_empty(InstanceType)
                }

                Overrides[0].InstanceType not in Overrides[1].InstanceType
            }
        }
    }

    %autoscaling_group [
        # Scenario 2
        filter_asg_conflicting_overrides(this)
    ] {
        MixedInstancesPolicy {
            LaunchTemplate {
                Overrides[*] {
                    check_mutually_exclusive_property_combination(InstanceType, InstanceRequirements) or
                    check_mutually_exclusive_property_combination(InstanceRequirements, InstanceType)
                }
            }
        }
    }
}

rule filter_asg_no_instance_requirement_overrides(autoscaling_group) {
    %autoscaling_group {
        MixedInstancesPolicy not exists or
        filter_mixed_instances_policy_no_instance_requirement_overrides(this)
    }
}

rule filter_mixed_instances_policy_no_instance_requirement_overrides(autoscaling_group) {
    %autoscaling_group {
        MixedInstancesPolicy is_struct
        MixedInstancesPolicy {
            LaunchTemplate not exists or
            filter_launch_templates_no_instance_requirement_overrides(this)
        }
    }
}

rule filter_launch_templates_no_instance_requirement_overrides(launch_template) {
    %launch_template {
        LaunchTemplate is_struct
        LaunchTemplate {
            Overrides not exists or
            filter_overrides_no_instance_requirement_overrides(this)
        }
    }
}

rule filter_overrides_no_instance_requirement_overrides(overrides) {
    %overrides {
        Overrides is_list
        Overrides empty or
        Overrides[*] {
            InstanceRequirements not exists
        }
    }
}

rule filter_asg_conflicting_overrides(autoscaling_group) {
    %autoscaling_group {
        MixedInstancesPolicy not exists or
        filter_mixed_instances_policy_conflicting_overrides(this)
    }
}

rule filter_mixed_instances_policy_conflicting_overrides(autoscaling_group) {
    %autoscaling_group {
        MixedInstancesPolicy is_struct
        MixedInstancesPolicy {
            LaunchTemplate not exists or
            filter_launch_templates_conflicting_overrides(this)
        }
    }
}

rule filter_launch_templates_conflicting_overrides(launch_template) {
    %launch_template {
        LaunchTemplate is_struct
        LaunchTemplate {
            Overrides not exists or
            filter_overrides_conflicting_overrides(this)
        }
    }
}

rule filter_overrides_conflicting_overrides(overrides) {
    %overrides {
        Overrides is_list
        Overrides empty or
        some Overrides[*] {
            InstanceRequirements exists
            InstanceType exists
        }
    }
}

rule check_mutually_exclusive_property_combination(property1, property2) {
    %property1 not exists
    %property2 exists
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

rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
}
```

### CT\.AUTOSCALING\.PR\.6 example templates<a name="ct-autoscaling-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateName:
        Fn::Sub: ${AWS::StackName}-example
      LaunchTemplateData:
        InstanceType: t3.micro
        ImageId:
          Ref: LatestAmiId
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
  AutoScalingGroup:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      VPCZoneIdentifier:
      - Ref: Subnet
      MaxSize: '2'
      MinSize: '1'
      MixedInstancesPolicy:
        LaunchTemplate:
          LaunchTemplateSpecification:
            LaunchTemplateId:
              Ref: EC2LaunchTemplate
            Version:
              Fn::GetAtt:
              - EC2LaunchTemplate
              - LatestVersionNumber
          Overrides:
          - InstanceType: t3.micro
          - InstanceType: m5.large
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateName:
        Fn::Sub: ${AWS::StackName}-example
      LaunchTemplateData:
        InstanceType: t3.micro
        ImageId:
          Ref: LatestAmiId
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
  AutoScalingGroup:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      VPCZoneIdentifier:
      - Ref: Subnet
      MaxSize: '2'
      MinSize: '1'
      MixedInstancesPolicy:
        LaunchTemplate:
          LaunchTemplateSpecification:
            LaunchTemplateId:
              Ref: EC2LaunchTemplate
            Version:
              Fn::GetAtt:
              - EC2LaunchTemplate
              - LatestVersionNumber
          Overrides:
          - InstanceType: t3.micro
```

## \[CT\.AUTOSCALING\.PR\.8\] Require an Amazon EC2 Auto Scaling group to have EC2 launch templates configured<a name="ct-autoscaling-pr-8-description"></a>

This control checks whether an Amazon EC2 Auto Scaling group is configured to use an EC2 launch template\.
+ **Control objective: **Manage vulnerabilities
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::AutoScaling::AutoScalingGroup`
+ **AWS CloudFormation guard rule: ** [CT\.AUTOSCALING\.PR\.8 rule specification](#ct-autoscaling-pr-8-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.AUTOSCALING\.PR\.8 rule specification](#ct-autoscaling-pr-8-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.AUTOSCALING\.PR\.8 example templates](#ct-autoscaling-pr-8-templates) 

**Explanation**

An Auto Scaling group can be created from an EC2 launch template or from a launch configuration\. If you use a launch template to create an Auto Scaling group, you have access to the latest features and improvements\.

### Remediation for rule failure<a name="ct-autoscaling-pr-8-remediation"></a>

Provide a `LaunchTemplate` or `MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification` configuration with a valid `Version` and a `LaunchTemplateId` or `LaunchTemplateName`\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Auto Scaling Group \- Example One<a name="ct-autoscaling-pr-8-remediation-1"></a>

Amazon EC2 Auto Scaling group configured with an EC2 launch template\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingGroup": {
        "Type": "AWS::AutoScaling::AutoScalingGroup",
        "Properties": {
            "VPCZoneIdentifier": [
                {
                    "Ref": "Subnet"
                }
            ],
            "MaxSize": "2",
            "MinSize": "1",
            "LaunchTemplate": {
                "LaunchTemplateName": "SampleLaunchTemplate",
                "Version": {
                    "Fn::GetAtt": [
                        "EC2LaunchTemplate",
                        "LatestVersionNumber"
                    ]
                }
            }
        }
    }
}
```

**YAML example**

```
AutoScalingGroup:
  Type: AWS::AutoScaling::AutoScalingGroup
  Properties:
    VPCZoneIdentifier:
      - !Ref 'Subnet'
    MaxSize: '2'
    MinSize: '1'
    LaunchTemplate:
      LaunchTemplateName: SampleLaunchTemplate
      Version: !GetAtt 'EC2LaunchTemplate.LatestVersionNumber'
```

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Auto Scaling Group \- Example Two<a name="ct-autoscaling-pr-8-remediation-2"></a>

Amazon EC2 Auto Scaling group configured with a mixed instances policy and EC2 launch template\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "AutoScalingGroup": {
        "Type": "AWS::AutoScaling::AutoScalingGroup",
        "Properties": {
            "VPCZoneIdentifier": [
                {
                    "Ref": "Subnet"
                }
            ],
            "MaxSize": "2",
            "MinSize": "1",
            "MixedInstancesPolicy": {
                "LaunchTemplate": {
                    "LaunchTemplateSpecification": {
                        "LaunchTemplateId": {
                            "Ref": "EC2LaunchTemplate"
                        },
                        "Version": {
                            "Fn::GetAtt": [
                                "EC2LaunchTemplate",
                                "LatestVersionNumber"
                            ]
                        }
                    }
                }
            }
        }
    }
}
```

**YAML example**

```
AutoScalingGroup:
  Type: AWS::AutoScaling::AutoScalingGroup
  Properties:
    VPCZoneIdentifier:
      - !Ref 'Subnet'
    MaxSize: '2'
    MinSize: '1'
    MixedInstancesPolicy:
      LaunchTemplate:
        LaunchTemplateSpecification:
          LaunchTemplateId: !Ref 'EC2LaunchTemplate'
          Version: !GetAtt 'EC2LaunchTemplate.LatestVersionNumber'
```

### CT\.AUTOSCALING\.PR\.8 rule specification<a name="ct-autoscaling-pr-8-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   autoscaling_launch_template_check
# 
# Description:
#   This control checks whether an Amazon EC2 Auto Scaling group is configured to use an EC2 launch template.
# 
# Reports on:
#   AWS::AutoScaling::AutoscalingGroup
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#  Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contains any Amazon EC2 Auto Scaling group resources
#      Then: SKIP
#  Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 Auto Scaling group resource
#       And: 'LaunchTemplate' has not been provided
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has not been provided
#      Then: FAIL
#  Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 Auto Scaling group resource
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has not been provided
#       And: 'LaunchTemplate' has been provided
#       And: 'LaunchTemplate' has an invalid configuration ('Version' has not been provided and one of 'LaunchTemplateId'
#            or 'LaunchTemplateName' has not been provided or 'Version' has been provided as an empty string or invalid local
#            reference and one of 'LaunchTemplateId' or 'LaunchTemplateName' has been provided as an empty string or an
#            invalid local reference)
#      Then: FAIL
#  Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 Auto Scaling group resource
#       And: 'LaunchTemplate' has not been provided
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has been provided
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has an invalid configuration ('Version' has
#            not been provided and one of 'LaunchTemplateId' or 'LaunchTemplateName' has not been provided or 'Version' has
#            been provided as an empty string or invalid local reference and one of 'LaunchTemplateId' or 'LaunchTemplateName'
#            has been provided as an empty string or an invalid local reference)
#      Then: FAIL
#  Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 Auto Scaling group resource
#       And: 'LaunchTemplate' has been provided
#       And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has
#            been provided.
#       Then: FAIL
#   Scenario: 6
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an Amazon EC2 Auto Scaling group resource
#        And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has not been provided
#        And: 'LaunchTemplate' has been provided
#        And: 'LaunchTemplate' has a valid configuration ('Version' has been provided and one of 'LaunchTemplateId' or
#             'LaunchTemplateName' has been provided as a non-empty string or valid local reference)
#       Then: PASS
#   Scenario: 7
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an Amazon EC2 Auto Scaling group resource
#        And: 'LaunchTemplate' has not been provided
#        And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has been provided
#        And: 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' has a valid configuration ('Version' has
#             been provided and one of 'LaunchTemplateId' or 'LaunchTemplateName' has been provided as a non-empty string
#             or valid local reference)
#       Then: PASS

#
# Constants
#
let AUTOSCALING_GROUP_TYPE = "AWS::AutoScaling::AutoScalingGroup"
let INPUT_DOCUMENT = this

#
# Assignments
#
let autoscaling_groups = Resources.*[ Type == %AUTOSCALING_GROUP_TYPE ]

#
# Primary Rules
#
rule autoscaling_launch_template_check when is_cfn_template(%INPUT_DOCUMENT)
                                            %autoscaling_groups not empty {
    check(%autoscaling_groups.Properties)
        <<
        [CT.AUTOSCALING.PR.8]: Require an Amazon EC2 Auto Scaling group to have EC2 launch templates configured
            [FIX]: Provide a 'LaunchTemplate' or 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' configuration with a valid 'Version' and a 'LaunchTemplateId' or 'LaunchTemplateName'.
        >>
}

rule autoscaling_launch_template_check when is_cfn_hook(%INPUT_DOCUMENT, %AUTOSCALING_GROUP_TYPE) {
    check(%INPUT_DOCUMENT.%AUTOSCALING_GROUP_TYPE.resourceProperties)
        <<
        [CT.AUTOSCALING.PR.8]: Require an Amazon EC2 Auto Scaling group to have EC2 launch templates configured
            [FIX]: Provide a 'LaunchTemplate' or 'MixedInstancesPolicy.LaunchTemplate.LaunchTemplateSpecification' configuration with a valid 'Version' and a 'LaunchTemplateId' or 'LaunchTemplateName'.
        >>
}

#
# Parameterized Rules
#
rule check(autoscaling_groups) {
    %autoscaling_groups {
        # Scenario 3 and 6
        check_launch_template(this) or

        # Scenario 4 and 7
        check_mixed_instances_policy(this)
    }
}

rule check_launch_template(autoscaling_groups) {
    %autoscaling_groups {
        check_mutually_exclusive_property_combination(LaunchTemplate, MixedInstancesPolicy)
        LaunchTemplate is_struct
        LaunchTemplate {
            check_valid_launch_template_config(this)
        }
    }
}

rule check_mixed_instances_policy(autoscaling_groups) {
    %autoscaling_groups {
        check_mutually_exclusive_property_combination(MixedInstancesPolicy, LaunchTemplate)
        MixedInstancesPolicy is_struct
        MixedInstancesPolicy {
            LaunchTemplate exists
            LaunchTemplate is_struct
            LaunchTemplate {
                LaunchTemplateSpecification exists
                LaunchTemplateSpecification is_struct
                check_valid_launch_template_config(LaunchTemplateSpecification)
            }
        }
    }
}

rule check_valid_launch_template_config(launch_template_specification) {
    %launch_template_specification {
        check_valid_launch_template_property(Version)
        check_valid_prop_combination(LaunchTemplateId, LaunchTemplateName) or
        check_valid_prop_combination(LaunchTemplateName, LaunchTemplateId)
    }
}

rule check_valid_prop_combination(valid_property, invalid_property) {
    check_mutually_exclusive_property_combination(%valid_property, %invalid_property)
    check_valid_launch_template_property(%valid_property)
}

rule check_mutually_exclusive_property_combination(valid_property, invalid_property) {
    %invalid_property not exists
    %valid_property exists
}

rule check_valid_launch_template_property(property) {
    %property {
        check_is_string_and_not_empty(this) or
        check_local_references(%INPUT_DOCUMENT, this, "AWS::EC2::LaunchTemplate")
    }
}

rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
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

### CT\.AUTOSCALING\.PR\.8 example templates<a name="ct-autoscaling-pr-8-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateName:
        Fn::Sub: ${AWS::StackName}-example
      LaunchTemplateData:
        InstanceType: t3.micro
        ImageId:
          Ref: LatestAmiId
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
  AutoScalingGroup:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      VPCZoneIdentifier:
      - Ref: Subnet
      MaxSize: '2'
      MinSize: '1'
      LaunchTemplate:
        LaunchTemplateId:
          Ref: EC2LaunchTemplate
        Version:
          Fn::GetAtt:
          - EC2LaunchTemplate
          - LatestVersionNumber
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
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
  AutoScalingLaunchConfiguration:
    Type: AWS::AutoScaling::LaunchConfiguration
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      LaunchConfigurationName: "AutoScalingLaunchConfiguration"
  AutoScalingGroup:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      VPCZoneIdentifier:
      - Ref: Subnet
      MaxSize: '2'
      MinSize: '1'
      LaunchConfigurationName: "AutoScalingLaunchConfiguration"
```