# Amazon Elastic Compute Cloud \(Amazon EC2\) controls<a name="ec2-rules"></a>

**Topics**
+ [\[CT\.EC2\.PR\.1\] Require an Amazon EC2 launch template to have IMDSv2 configured](#ct-ec2-pr-1-description)
+ [\[CT\.EC2\.PR\.2\] Require that Amazon EC2 launch templates restrict the token hop limit to a maximum of one](#ct-ec2-pr-2-description)
+ [\[CT\.EC2\.PR\.3\] Require that any Amazon EC2 security group rule does not use the source IP range `0.0.0.0/0` or `::/0` for ports other than 80 and 443](#ct-ec2-pr-3-description)
+ [\[CT\.EC2\.PR\.4\] Require that any Amazon EC2 security group rule does not use the source IP range `0.0.0.0/0` or `::/0` for specific high\-risk ports](#ct-ec2-pr-4-description)
+ [\[CT\.EC2\.PR\.5\] Require any Amazon EC2 network ACL to prevent ingress from 0\.0\.0\.0/0 to port 22 or port 3389](#ct-ec2-pr-5-description)
+ [\[CT\.EC2\.PR\.6\] Require that Amazon EC2 transit gateways refuse automatic Amazon VPC attachment requests](#ct-ec2-pr-6-description)
+ [\[CT\.EC2\.PR\.7\] Require that an Amazon EBS volume attached to an Amazon EC2 instance is encrypted at rest](#ct-ec2-pr-7-description)
+ [\[CT\.EC2\.PR\.8\] Require any Amazon EC2 instance to have a non\-public IP address](#ct-ec2-pr-8-description)
+ [\[CT\.EC2\.PR\.9\] Require any Amazon EC2 launch template not to auto\-assign public IP addresses to network interfaces](#ct-ec2-pr-9-description)
+ [\[CT\.EC2\.PR\.10\] Require Amazon EC2 launch templates to have Amazon CloudWatch detailed monitoring activated](#ct-ec2-pr-10-description)
+ [\[CT\.EC2\.PR\.11\] Require that an Amazon EC2 subnet does not automatically assign public IP addresses](#ct-ec2-pr-11-description)
+ [\[CT\.EC2\.PR\.12\] Require an Amazon EC2 instance to configure one ENI only](#ct-ec2-pr-12-description)

## \[CT\.EC2\.PR\.1\] Require an Amazon EC2 launch template to have IMDSv2 configured<a name="ct-ec2-pr-1-description"></a>

This control checks whether your Amazon EC2 launch templates are configured with Instance Metadata Service Version 2 \(IMDSv2\)\.
+ **Control objective: **Enforce least privilege, Protect configurations
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::LaunchTemplate`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.1 rule specification](#ct-ec2-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.1 rule specification](#ct-ec2-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.1 example templates](#ct-ec2-pr-1-templates) 

**Explanation**

Instance metadata configures and manages your running instances\. The IMDS provides access to temporary, frequently rotated credentials, so you don't need to distribute sensitive credentials to instances, either manually or programmatically\. The IMDS is attached locally to every EC2 instance\. It runs on a special IP address of 169\.254\.169\.254\. This IP address is accessible only to software that runs on the instance\.

Version 2 of the IMDS adds protections for vulnerabilities that can be used to gain access to the IMDS: `Open website application firewalls`, `Open reverse proxies`, `Server-side request forgery (SSRF) vulnerabilities` and `'Open Layer 3 firewalls and network address translation (NAT)`\.

AWS Control Tower recommends that you configure your EC2 instances with IMDSv2\.

**Usage considerations**  
This control applies only to Amazon EC2 launch templates that allow access to instance metadata\.

### Remediation for rule failure<a name="ct-ec2-pr-1-remediation"></a>

Within the `LaunchTemplateData` property, provide a `MetadataOptions` configuration and set the value of `HttpTokens` to `required`\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Launch Template \- Example<a name="ct-ec2-pr-1-remediation-1"></a>

Amazon EC2 launch template configured with IMDSv2 activated\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EC2LaunchTemplate": {
        "Type": "AWS::EC2::LaunchTemplate",
        "Properties": {
            "LaunchTemplateData": {
                "InstanceType": "t3.micro",
                "ImageId": {
                    "Ref": "LatestAmiId"
                },
                "MetadataOptions": {
                    "HttpTokens": "required"
                }
            }
        }
    }
}
```

**YAML example**

```
EC2LaunchTemplate:
  Type: AWS::EC2::LaunchTemplate
  Properties:
    LaunchTemplateData:
      InstanceType: t3.micro
      ImageId: !Ref 'LatestAmiId'
      MetadataOptions:
        HttpTokens: required
```

### CT\.EC2\.PR\.1 rule specification<a name="ct-ec2-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ec2_launch_template_imdsv2_check
# 
# Description:
#   This control checks whether your Amazon EC2 launch templates are configured with Instance Metadata Service Version 2 (IMDSv2).
# 
# Reports on:
#   AWS::EC2::LaunchTemplate
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
#       And: The input document does not contain any EC2 launch template resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 launch template resource
#       And: 'LaunchTemplateData' has not been provided or 'LaunchTemplateData.MetadataOptions.HttpEndpoint' has
#             been provided and is equal to 'disabled'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 launch template resource
#       And: 'LaunchTemplateData' has been provided
#       And: 'MetadataOptions.HttpEndpoint' in 'LaunchTemplateData' has not been provided or has been provided and
#            is equal to 'enabled'
#       And: 'MetadataOptions.HttpTokens' in 'LaunchTemplateData' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 launch template resource
#       And: 'LaunchTemplateData' has been provided
#       And: 'MetadataOptions.HttpEndpoint' in 'LaunchTemplateData' has not been provided or has been provided and
#            is equal to 'enabled'
#       And: 'MetadataOptions.HttpTokens' in 'LaunchTemplateData' has been provided and set to a value other than 'required'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 launch template resource
#       And: 'LaunchTemplateData' has been provided
#       And: 'MetadataOptions.HttpEndpoint' in 'LaunchTemplateData' has not been provided or has been provided and
#            is equal to 'enabled'
#       And: 'MetadataOptions.HttpTokens' in 'LaunchTemplateData' has been provided and set to 'required'
#      Then: PASS

#
# Constants
#
let EC2_LAUNCH_TEMPLATE_TYPE = "AWS::EC2::LaunchTemplate"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_launch_templates = Resources.*[ Type == %EC2_LAUNCH_TEMPLATE_TYPE ]

#
# Primary Rules
#
rule ec2_launch_template_imdsv2_check when is_cfn_template(%INPUT_DOCUMENT)
                                           %ec2_launch_templates not empty {
    check(%ec2_launch_templates.Properties)
        <<
        [CT.EC2.PR.1]: Require an Amazon EC2 launch template to have IMDSv2 configured
            [FIX]: Within the 'LaunchTemplateData' property, provide a 'MetadataOptions' configuration and set the value of 'HttpTokens' to 'required'.
        >>
}

rule ec2_launch_template_imdsv2_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_LAUNCH_TEMPLATE_TYPE) {
    check(%INPUT_DOCUMENT.%EC2_LAUNCH_TEMPLATE_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.1]: Require an Amazon EC2 launch template to have IMDSv2 configured
            [FIX]: Within the 'LaunchTemplateData' property, provide a 'MetadataOptions' configuration and set the value of 'HttpTokens' to 'required'.
        >>
}

#
# Parameterized Rules
#
rule check(launch_template) {
    %launch_template [
        # Scenario 2
        filter_launch_template_imds_enabled(this)
    ] {
        LaunchTemplateData exists
        LaunchTemplateData is_struct

        LaunchTemplateData {
            # Scenario 3, 4 and 5
            MetadataOptions exists
            MetadataOptions is_struct
            MetadataOptions {
                HttpTokens exists
                HttpTokens == "required"
            }
        }
    }
}

rule filter_launch_template_imds_enabled(launch_template) {
    %launch_template {
        LaunchTemplateData exists
        LaunchTemplateData is_struct
        LaunchTemplateData {
            MetadataOptions not exists or
            filter_metadata_options_imds_enabled(this)
        }
    }
}

rule filter_metadata_options_imds_enabled(metadata_options) {
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

### CT\.EC2\.PR\.1 example templates<a name="ct-ec2-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Parameters:
  LatestAmiId:
    Description: Region specific latest AMI ID from the Parameter Store
    Type: AWS::SSM::Parameter::Value<WS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
Resources:
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        InstanceType: t3.micro
        ImageId:
          Ref: LatestAmiId
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
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        InstanceType: t3.micro
        ImageId:
          Ref: LatestAmiId
        MetadataOptions:
          HttpTokens: optional
```

## \[CT\.EC2\.PR\.2\] Require that Amazon EC2 launch templates restrict the token hop limit to a maximum of one<a name="ct-ec2-pr-2-description"></a>

This control checks whether an Amazon EC2 launch template has a metadata token hop limit set to `1`\.
+ **Control objective: **Enforce least privilege, Protect configurations
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::LaunchTemplate`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.2 rule specification](#ct-ec2-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.2 rule specification](#ct-ec2-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.2 example templates](#ct-ec2-pr-2-templates) 

**Explanation**

The Amazon Instance Metadata Service \(IMDS\) provides metadata information about an Amazon EC2 instance, which is useful for application configuration\. Restricting the HTTP PUT response for the metadata service to the EC2 instance protects the IMDS from unauthorized use\.

The Time To Live \(TTL\) field in the IP packet is reduced by one on every hop\. This reduction can be used to ensure that the packet does not travel outside EC2\. IMDSv2 protects EC2 instances that may have been misconfigured as open routers, layer 3 firewalls, VPNs, tunnels, or NAT devices, which prevents unauthorized users from retrieving metadata\. With IMDSv2, the PUT response that contains the secret token cannot travel outside the instance, because the default metadata response hop limit is set to 1\. However, if this value is greater than 1, the token can leave the EC2 instance\.

**Usage considerations**  
This control applies only to Amazon EC2 launch templates that allow access to instance metadata\.
This control is incompatible with Amazon EC2 launch templates that require a token hop limit of 2\.

### Remediation for rule failure<a name="ct-ec2-pr-2-remediation"></a>

Within the `LaunchTemplateData` property, provide a `MetadataOptions` configuration with the value of `HttpPutResponseLimit` set to `1`, or omit the `HttpPutResponseLimit` property to adopt the AWS CloudFormation default value of `1`\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Launch Template \- Example One<a name="ct-ec2-pr-2-remediation-1"></a>

Amazon EC2 launch template configured with access to instance metadata enabled and a token hop limit of `1`, set by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EC2LaunchTemplate": {
        "Type": "AWS::EC2::LaunchTemplate",
        "Properties": {
            "LaunchTemplateData": {
                "MetadataOptions": {
                    "HttpEndpoint": "enabled"
                }
            }
        }
    }
}
```

**YAML example**

```
EC2LaunchTemplate:
  Type: AWS::EC2::LaunchTemplate
  Properties:
    LaunchTemplateData:
      MetadataOptions:
        HttpEndpoint: enabled
```

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Launch Template \- Example Two<a name="ct-ec2-pr-2-remediation-2"></a>

Amazon EC2 launch template configured with access to instance metadata enabled and a token hop limit of `1`, set by means of the `MetadataOptions` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EC2LaunchTemplate": {
        "Type": "AWS::EC2::LaunchTemplate",
        "Properties": {
            "LaunchTemplateData": {
                "MetadataOptions": {
                    "HttpEndpoint": "enabled",
                    "HttpPutResponseHopLimit": 1
                }
            }
        }
    }
}
```

**YAML example**

```
EC2LaunchTemplate:
  Type: AWS::EC2::LaunchTemplate
  Properties:
    LaunchTemplateData:
      MetadataOptions:
        HttpEndpoint: enabled
        HttpPutResponseHopLimit: 1
```

### CT\.EC2\.PR\.2 rule specification<a name="ct-ec2-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ec2_launch_template_token_hop_limit_check
# 
# Description:
#   This control checks whether an Amazon EC2 launch template has a metadata token hop limit set to '1'.
# 
# Reports on:
#   AWS::EC2::LaunchTemplate
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
#        And: The input document does not contain any EC2 launch template resources
#       Then: SKIP
#   Scenario: 2
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 launch template resource
#        And: 'LaunchTemplateData.MetadataOptions' has been provided
#        And: 'LaunchTemplateData.MetadataOptions.HttpEndpoint' has been provided and is equal to 'disabled'
#       Then: SKIP
#   Scenario: 3
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 launch template resource
#        And: 'LaunchTemplateData.MetadataOptions' has been provided
#        And: 'LaunchTemplateData.MetadataOptions.HttpEndpoint' has not been provided or has been provided and is
#             equal to 'enabled'
#        And: 'LaunchTemplateData.MetadataOptions.HttpPutResponseHopLimit' has been provided and is not equal to
#             an integer of 1.
#       Then: FAIL
#   Scenario: 4
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 launch template resource
#        And: 'LaunchTemplateData.MetadataOptions' has not been provided
#       Then: PASS
#   Scenario: 5
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 launch template resource
#        And: 'LaunchTemplateData.MetadataOptions' has been provided
#        And: 'LaunchTemplateData.MetadataOptions.HttpEndpoint' has not been provided or has been provided and is
#             equal to 'enabled'
#        And: 'LaunchTemplateData.MetadataOptions.HttpPutResponseHopLimit' has not been provided
#       Then: PASS
#   Scenario: 6
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 launch template resource
#        And: 'LaunchTemplateData.MetadataOptions' has been provided
#        And: 'LaunchTemplateData.MetadataOptions.HttpEndpoint' has not been provided or has been provided and is
#             equal to 'enabled'
#        And: 'LaunchTemplateData.MetadataOptions.HttpPutResponseHopLimit' has been provided and is equal to an
#             integer of 1.
#       Then: PASS

#
# Constants
#
let EC2_LAUNCH_TEMPLATE_TYPE = "AWS::EC2::LaunchTemplate"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_launch_templates = Resources.*[ Type == %EC2_LAUNCH_TEMPLATE_TYPE ]

#
# Primary Rules
#
rule ec2_launch_template_token_hop_limit_check when is_cfn_template(this)
                                                    %ec2_launch_templates not empty {
    check(%ec2_launch_templates.Properties)
        <<
        [CT.EC2.PR.2]: Require that Amazon EC2 launch templates restrict the token hop limit to a maximum of one
            [FIX]: Within the 'LaunchTemplateData' property, provide a 'MetadataOptions' configuration with the value of 'HttpPutResponseLimit' set to '1', or omit the 'HttpPutResponseLimit' property to adopt the AWS CloudFormation default value of '1'.
        >>
}

rule ec2_launch_template_token_hop_limit_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_LAUNCH_TEMPLATE_TYPE) {
    check(%INPUT_DOCUMENT.%EC2_LAUNCH_TEMPLATE_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.2]: Require that Amazon EC2 launch templates restrict the token hop limit to a maximum of one
            [FIX]: Within the 'LaunchTemplateData' property, provide a 'MetadataOptions' configuration with the value of 'HttpPutResponseLimit' set to '1', or omit the 'HttpPutResponseLimit' property to adopt the AWS CloudFormation default value of '1'.
        >>
}

#
# Parameterized Rules
#
rule check(ec2_launch_template) {
  %ec2_launch_template[
      # Scenario 2, 3 and 4
      filter_launch_template(this)
  ] {
      # Scenario 5 and 6
      LaunchTemplateData {
          MetadataOptions not exists or
          MetadataOptions {
              HttpPutResponseHopLimit not exists or
              HttpPutResponseHopLimit == 1
          }
      }
  }
}

rule filter_launch_template(ec2_launch_template) {
  %ec2_launch_template {
      LaunchTemplateData exists
      LaunchTemplateData is_struct
      LaunchTemplateData {
          MetadataOptions not exists or
          filter_metadata_options_provided(this)
      }
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
    AWSTemplateFormatVersion exists  or
    Resources exists
  }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.EC2\.PR\.2 example templates<a name="ct-ec2-pr-2-templates"></a>

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
      LaunchTemplateData:
        InstanceType: t3.micro
        ImageId:
          Ref: LatestAmiId
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        MetadataOptions:
          HttpPutResponseHopLimit: 2
```

## \[CT\.EC2\.PR\.3\] Require that any Amazon EC2 security group rule does not use the source IP range `0.0.0.0/0` or `::/0` for ports other than 80 and 443<a name="ct-ec2-pr-3-description"></a>

This control checks whether an Amazon EC2 security group rule contains the string `0.0.0.0/0` or `::/0` as a source IP range\. This control is not triggered if a rule allows connection to port 80 or 443 with TCP, UDP, ICMP, or ICMPv6\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::SecurityGroup`, `AWS::EC2::SecurityGroupIngress`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.3 rule specification](#ct-ec2-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.3 rule specification](#ct-ec2-pr-3-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.3 example templates](#ct-ec2-pr-3-templates) 

**Explanation**

Security groups provide stateful filtering of ingress and egress network traffic to AWS resources\. Disallowing usage of the strings `0.0.0.0/0` or `::/0` helps protect against this common misconfiguration and encourages users to choose a range aligned with least\-privilege principles\.

AWS recommends a layered approach, to ensure that network access is provided only as necessary for your business requirements\. Security group rules should follow the principle of least privileged access\. Unrestricted access increases the opportunity for malicious activity\. Unless a port is specifically allowed, the port should deny unrestricted access \(any IP address with a `/0` suffix\)\.

**Usage considerations**  
This control applies only to Amazon EC2 security group and EC2 security group ingress resources with ingress rules that allow inbound traffic from `0.0.0.0/0` or `::/0`

### Remediation for rule failure<a name="ct-ec2-pr-3-remediation"></a>

Ensure that security groups with ingress rules that allow TCP or UDP traffic from `0.0.0.0/0` or `::/0` allow traffic from ports `80` or `443` only\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Security Group \- Example One<a name="ct-ec2-pr-3-remediation-1"></a>

Amazon EC2 Security Group allowing inbound TCP traffic from `0.0.0.0/0` on port `80`\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "SecurityGroup": {
        "Type": "AWS::EC2::SecurityGroup",
        "Properties": {
            "GroupDescription": {
                "Fn::Sub": "${AWS::StackName}-example"
            },
            "SecurityGroupIngress": [
                {
                    "IpProtocol": "tcp",
                    "CidrIp": "0.0.0.0/0",
                    "FromPort": 80,
                    "ToPort": 80
                }
            ]
        }
    }
}
```

**YAML example**

```
SecurityGroup:
  Type: AWS::EC2::SecurityGroup
  Properties:
    GroupDescription: !Sub '${AWS::StackName}-example'
    SecurityGroupIngress:
      - IpProtocol: tcp
        CidrIp: '0.0.0.0/0'
        FromPort: 80
        ToPort: 80
```

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Security Group \- Example Two<a name="ct-ec2-pr-3-remediation-2"></a>

Amazon EC2 Security Group allowing inbound TCP traffic from `0.0.0.0/0` on port `443`\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "SecurityGroup": {
        "Type": "AWS::EC2::SecurityGroup",
        "Properties": {
            "GroupDescription": {
                "Fn::Sub": "${AWS::StackName}-example"
            },
            "SecurityGroupIngress": [
                {
                    "IpProtocol": "tcp",
                    "CidrIp": "0.0.0.0/0",
                    "FromPort": 443,
                    "ToPort": 443
                }
            ]
        }
    }
}
```

**YAML example**

```
SecurityGroup:
  Type: AWS::EC2::SecurityGroup
  Properties:
    GroupDescription: !Sub '${AWS::StackName}-example'
    SecurityGroupIngress:
      - IpProtocol: tcp
        CidrIp: '0.0.0.0/0'
        FromPort: 443
        ToPort: 443
```

### CT\.EC2\.PR\.3 rule specification<a name="ct-ec2-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   vpc_sg_open_only_to_authorized_ports_check
# 
# Description:
#    This control checks whether the Amazon EC2 security group contains the string '0.0.0.0/0' or '::/0' as a source IP range. 
#    This control is not triggered if a rule allows connection to port 80 or 443 with TCP, UDP, ICMP, or ICMPv6.
# 
# Reports on:
#   AWS::EC2::SecurityGroup, AWS::EC2::SecurityGroupIngress
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
#       And: The input document does not contain any Amazon EC2 security group or EC2 security group ingress resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon EC2 security group resource or EC2 security group ingress resource
#       And: The EC2 security group or EC2 security group ingress resource does not allow inbound traffic from a source
#            prefix list and has no rules allowing inbound traffic from source '0.0.0.0/0' or '::/0'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon EC2 security group resource or EC2 security group ingress resource
#       And: The EC2 security group or EC2 security group ingress resource has rules allowing inbound traffic
#            from a source prefix list, or source '0.0.0.0/0' or '::/0'
#       And: The EC2 security group or EC2 security group ingress resource has a rule that allows all traffic
#            ('IpProtocol' is set to '-1' or another protocol number)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon EC2 security group resource or EC2 security group ingress resource
#       And: The EC2 security group or EC2 security group ingress resource has rules allowing inbound traffic
#            from a source prefix list, or source '0.0.0.0/0' or '::/0'
#       And: The EC2 security group or EC2 security group ingress resource has no rules that allow all traffic
#            ('IpProtocol' is not set to '-1' or another protocol number)
#       And: Ports allowed are not in the list of allowed ports
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon EC2 security group resource or EC2 security group ingress resource
#       And: The EC2 security group or EC2 security group ingress resource has rules allowing inbound traffic
#            from a source prefix list, or source '0.0.0.0/0' or '::/0'
#       And: The EC2 security group or EC2 security group ingress resource has no rules that allow all traffic
#            ('IpProtocol' is not set to '-1' or another protocol number)
#       And: Ports allowed are in the list of allowed ports
#      Then: PASS

#
# Constants
#
let SECURITY_GROUP_TYPE = "AWS::EC2::SecurityGroup"
let SECURITY_GROUP_INGRESS_TYPE = "AWS::EC2::SecurityGroupIngress"
let ALLOWED_PORTS = [80, 443]
let AUTHORIZED_PROTOCOLS = ["tcp", "udp", "icmp", "icmpv6"]
let UNRESTRICTED_IPV4_RANGES = ["0.0.0.0/0"]
let UNRESTRICTED_IPV6_RANGES = ["::/0"]
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_security_groups = Resources[
    Type == %SECURITY_GROUP_TYPE
]
let ec2_security_group_ingress_rules = Resources[
    Type == %SECURITY_GROUP_INGRESS_TYPE
]

#
# Primary Rules
#
rule vpc_sg_open_only_to_authorized_ports_check when is_cfn_template(%INPUT_DOCUMENT)
                                                     %ec2_security_groups not empty {

    check_security_group(%ec2_security_groups.Properties)
        <<
        [CT.EC2.PR.3]: Require that any Amazon EC2 security group rule does not use the source IP range 0.0.0.0/0 or ::/0 for ports other than 80 and 443
        [FIX]: Ensure that security groups with ingress rules that allow TCP or UDP traffic from '0.0.0.0/0' or '::/0' only allow traffic to ports 80 or 443. The use of managed prefix lists is not supported.
        >>
}

rule vpc_sg_open_only_to_authorized_ports_check when is_cfn_template(%INPUT_DOCUMENT)
                                                     %ec2_security_group_ingress_rules not empty {

    check_ingress_rule(%ec2_security_group_ingress_rules.Properties)
        <<
        [CT.EC2.PR.3]: Require that any Amazon EC2 security group rule does not use the source IP range 0.0.0.0/0 or ::/0 for ports other than 80 and 443
        [FIX]: Ensure that security groups with ingress rules that allow TCP or UDP traffic from '0.0.0.0/0' or '::/0' only allow traffic to ports 80 or 443. The use of managed prefix lists is not supported.
        >>
}

rule vpc_sg_open_only_to_authorized_ports_check when is_cfn_hook(%INPUT_DOCUMENT, %SECURITY_GROUP_TYPE) {

    check_security_group(%INPUT_DOCUMENT.%SECURITY_GROUP_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.3]: Require that any Amazon EC2 security group rule does not use the source IP range 0.0.0.0/0 or ::/0 for ports other than 80 and 443
        [FIX]: Ensure that security groups with ingress rules that allow TCP or UDP traffic from '0.0.0.0/0' or '::/0' only allow traffic to ports 80 or 443. The use of managed prefix lists is not supported.
        >>
}

rule vpc_sg_open_only_to_authorized_ports_check when is_cfn_hook(%INPUT_DOCUMENT, %SECURITY_GROUP_INGRESS_TYPE) {

    check_ingress_rule(%INPUT_DOCUMENT.%SECURITY_GROUP_INGRESS_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.3]: Require that any Amazon EC2 security group rule does not use the source IP range 0.0.0.0/0 or ::/0 for ports other than 80 and 443
        [FIX]: Ensure that security groups with ingress rules that allow TCP or UDP traffic from '0.0.0.0/0' or '::/0' only allow traffic to ports 80 or 443. The use of managed prefix lists is not supported.
        >>
}

#
# Parameterized Rules
#
rule check_security_group(security_group) {
    %security_group [
        SecurityGroupIngress exists
        SecurityGroupIngress is_list
        SecurityGroupIngress not empty
    ] {
        SecurityGroupIngress[*] {
            check_ingress_rule(this)
        }
    }
}

rule check_ingress_rule(ingress_rule) {
    %ingress_rule[ CidrIp in %UNRESTRICTED_IPV4_RANGES or
                   CidrIpv6 in %UNRESTRICTED_IPV6_RANGES or 
                   SourcePrefixListId exists ] {
        # Scenario 3
        IpProtocol exists
        IpProtocol in %AUTHORIZED_PROTOCOLS

        when IpProtocol in ["tcp", "udp"] {
            FromPort exists
            ToPort exists
            # Scenarios 4 and 5
            check_ports(FromPort, ToPort)
        }
    }
}

rule check_ports(from_port, to_port) {
    %from_port in %ALLOWED_PORTS
    %to_port in %ALLOWED_PORTS
    %from_port in %to_port
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

### CT\.EC2\.PR\.3 example templates<a name="ct-ec2-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription:
        Fn::Sub: ${AWS::StackName}-example
      SecurityGroupIngress:
      - IpProtocol: tcp
        CidrIp: 0.0.0.0/0
        FromPort: 80
        ToPort: 80
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  PrefixList:
    Type: AWS::EC2::PrefixList
    Properties:
      PrefixListName:
        Fn::Sub: ${AWS::StackName}-example
      AddressFamily: IPv4
      MaxEntries: 10
      Entries:
        - Cidr: "0.0.0.0/0"
          Description: Public internet
  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription:
        Fn::Sub: ${AWS::StackName}-example
  SecurityGroupIngress:
    Type: AWS::EC2::SecurityGroupIngress
    Properties:
      GroupId:
        Fn::GetAtt: [ SecurityGroup, GroupId ]
      IpProtocol: -1
      SourcePrefixListId:
        Ref: PrefixList
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription:
        Fn::Sub: ${AWS::StackName}-example
  SecurityGroupIngress:
    Type: AWS::EC2::SecurityGroupIngress
    Properties:
      GroupId:
        Fn::GetAtt: [ SecurityGroup, GroupId ]
      IpProtocol: udp
      CidrIp: 0.0.0.0/0
      FromPort: 80
      ToPort: 90
```

## \[CT\.EC2\.PR\.4\] Require that any Amazon EC2 security group rule does not use the source IP range `0.0.0.0/0` or `::/0` for specific high\-risk ports<a name="ct-ec2-pr-4-description"></a>

This control checks whether an Amazon EC2 security group rule that contains the strings `0.0.0.0/0` or `::/0` as a source IP range does not allow incoming TCP, UDP, ICMP, or ICMPv6 traffic to the following ports: `3389`, `20`, `23`, `110`, `143`, `3306`, `8080`, `1433`, `9200`, `9300`, `25`, `445`, `135`, `21`, `1434`, `4333`, `5432`, `5500`, `5601`, `22`, `3000`, `5000`, `8088`, `8888`\. The use of managed prefix lists is not supported\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::SecurityGroup`, `AWS::EC2::SecurityGroupIngress`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.4 rule specification](#ct-ec2-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.4 rule specification](#ct-ec2-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.4 example templates](#ct-ec2-pr-4-templates) 

**Explanation**

Security groups provide stateful filtering of ingress and egress network traffic to AWS resources\. Disallowing usage of the strings `0.0.0.0/0` or `::/0` helps protect against this common misconfiguration and encourages users to choose a range aligned with least\-privilege principles\.

AWS recommends a layered approach, to ensure that network access is provided only as necessary for your business requirements\. No security group should allow unrestricted ingress access to the following ports:

`3389`, `20`, `23`, `110`, `143`, `3306`, `8080`, `1433`, `9200`, `9300`, `25`, `445`, `135`, `21`, `1434`, `4333`, `5432`, `5500`, `5601`, `22`, `3000`, `5000`, `8088`, `8888`\.

Unrestricted access \(0\.0\.0\.0/0\) increases opportunities for malicious activity, such as hacking, denial\-of\-service attacks, and loss of data\.

**Usage considerations**  
This control applies only to Amazon EC2 security group and security group ingress resources with ingress rules that allow inbound traffic from `0.0.0.0/0` or `::/0`\.

### Remediation for rule failure<a name="ct-ec2-pr-4-remediation"></a>

Remove Amazon EC2 security group ingress rules that allow traffic from `0.0.0.0/0` or `::/0` to high\-risk ports: `3389`, `20`, `23`, `110`, `143`, `3306`, `8080`, `1433`, `9200`, `9300`, `25`, `445`, `135`, `21`, `1434`, `4333`, `5432`, `5500`, `5601`, `22`, `3000`, `5000`, `8088`, `8888`\.

The use of managed prefix lists is not supported\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Security Group \- Example<a name="ct-ec2-pr-4-remediation-1"></a>

Amazon EC2 security group configured to allow traffic from the source IP range `0.0.0.0/0` or `::/0` on a port range that does not include a high\-risk port\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "SecurityGroup": {
        "Type": "AWS::EC2::SecurityGroup",
        "Properties": {
            "GroupDescription": "sample-security-group",
            "SecurityGroupIngress": [
                {
                    "IpProtocol": "tcp",
                    "CidrIp": "0.0.0.0/0",
                    "FromPort": 80,
                    "ToPort": 80
                }
            ]
        }
    }
}
```

**YAML example**

```
SecurityGroup:
  Type: AWS::EC2::SecurityGroup
  Properties:
    GroupDescription: sample-security-group
    SecurityGroupIngress:
      - IpProtocol: tcp
        CidrIp: '0.0.0.0/0'
        FromPort: 80
        ToPort: 80
```

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Security Group Ingress Rule \- Example<a name="ct-ec2-pr-4-remediation-2"></a>

Amazon EC2 security group ingress rule configured to allow traffic from the source IP range `0.0.0.0/0` or `::/0` on a port range that does not include a high\-risk port\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "SecurityGroupIngress": {
        "Type": "AWS::EC2::SecurityGroupIngress",
        "Properties": {
            "GroupId": {
                "Fn::GetAtt": [
                    "SecurityGroup",
                    "GroupId"
                ]
            },
            "IpProtocol": "tcp",
            "CidrIp": "0.0.0.0/0",
            "FromPort": 80,
            "ToPort": 90
        }
    }
}
```

**YAML example**

```
SecurityGroupIngress:
  Type: AWS::EC2::SecurityGroupIngress
  Properties:
    GroupId: !GetAtt 'SecurityGroup.GroupId'
    IpProtocol: tcp
    CidrIp: '0.0.0.0/0'
    FromPort: 80
    ToPort: 90
```

### CT\.EC2\.PR\.4 rule specification<a name="ct-ec2-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   vpc_sg_restricted_common_ports_check
# 
# Description:
#   This control checks whether an Amazon EC2 security group rule that contains the strings '0.0.0.0/0' or '::/0' as a source IP range 
#   does not allow incoming TCP, UDP, ICMP, ICMPv6 traffic to the following ports: '3389', '20', '23', '110', '143',
#   '3306', '8080', '1433', '9200', '9300', '25', '445', '135', '21', '1434', '4333', '5432', '5500', '5601', '22', '3000', '5000',
#   '8088', '8888'.
# 
# Reports on:
#   AWS::EC2::SecurityGroup, AWS::EC2::SecurityGroupIngress
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation Hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any EC2 security group or EC2 security group ingress resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 security group resource or EC2 security group ingress resource
#       And: EC2 security group or EC2 security group ingress resource does not allow inbound traffic from a source
#            prefix list and has no rules allowing inbound traffic from source '0.0.0.0/0' or '::/0'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 security group resource or EC2 security group ingress resource
#       And: EC2 security group or EC2 security group ingress resource has rules allowing inbound traffic
#            from a source prefix list, or source '0.0.0.0/0' or '::/0'
#       And: EC2 security group or EC2 security group ingress resource has a rule that allows all traffic
#            ('IpProtocol' is set to '-1' or another protocol number)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 security group resource or EC2 security group ingress resource
#       And: EC2 security group or EC2 security group ingress resource has rules allowing inbound traffic
#            from a source prefix list, or source '0.0.0.0/0' or '::/0'
#       And: EC2 security group or EC2 security group ingress resource has no rules that allow all traffic
#            ('IpProtocol' is not set to '-1' or another protocol number)
#       And: Ports allowed are in the list of blocked ports
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 security group resource or EC2 security group ingress resource
#       And: EC2 security group or EC2 security group ingress resource has rules allowing inbound traffic
#            from a source prefix list, or source '0.0.0.0/0' or '::/0'
#       And: EC2 security group or EC2 security group ingress resource has no rules that allow all traffic
#            ('IpProtocol' is not set to '-1' or another protocol number)
#       And: Ports allowed are not in the list of blocked ports
#      Then: PASS

#
# Constants
#
let SECURITY_GROUP_TYPE = "AWS::EC2::SecurityGroup"
let SECURITY_GROUP_INGRESS_TYPE = "AWS::EC2::SecurityGroupIngress"
let BLOCKED_PORTS = [3389, 20, 23, 110, 143, 3306, 8080, 1433, 9200, 9300, 25, 445, 135, 21, 1434, 4333, 5432, 5500,
                     5601, 22, 3000, 5000, 8088, 8888]
let AUTHORIZED_PROTOCOLS = ["tcp", "udp", "icmp", "icmpv6"]
let UNRESTRICTED_IPV4_RANGES = ["0.0.0.0/0"]
let UNRESTRICTED_IPV6_RANGES = ["::/0"]
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_security_groups = Resources[
    Type == %SECURITY_GROUP_TYPE
]
let ec2_security_group_ingress_rules = Resources[
    Type == %SECURITY_GROUP_INGRESS_TYPE
]

#
# Primary Rules
#
rule vpc_sg_restricted_common_ports_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %ec2_security_groups not empty {

    check_security_group(%ec2_security_groups.Properties)
        <<
        [CT.EC2.PR.4]: Require that any Amazon EC2 security group rule does not use the source IP range 0.0.0.0/0 or ::/0 for specific high-risk ports
            [FIX]: Remove Amazon EC2 security group ingress rules that allow traffic from '0.0.0.0/0' or '::/0' to high-risk ports: '3389', '20', '23', '110', '143', '3306', '8080', '1433', '9200', '9300', '25', '445', '135', '21', '1434', '4333', '5432', '5500', '5601', '22', '3000', '5000', '8088', '8888'. The use of managed prefix lists is not supported.
        >>
}

rule vpc_sg_restricted_common_ports_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %ec2_security_group_ingress_rules not empty {

    check_ingress_rule(%ec2_security_group_ingress_rules.Properties)
        <<
        [CT.EC2.PR.4]: Require that any Amazon EC2 security group rule does not use the source IP range 0.0.0.0/0 or ::/0 for specific high-risk ports
            [FIX]: Remove Amazon EC2 security group ingress rules that allow traffic from '0.0.0.0/0' or '::/0' to high-risk ports: '3389', '20', '23', '110', '143', '3306', '8080', '1433', '9200', '9300', '25', '445', '135', '21', '1434', '4333', '5432', '5500', '5601', '22', '3000', '5000', '8088', '8888'. The use of managed prefix lists is not supported.
        >>
}

rule vpc_sg_restricted_common_ports_check when is_cfn_hook(%INPUT_DOCUMENT, %SECURITY_GROUP_TYPE) {

    check_security_group(%INPUT_DOCUMENT.%SECURITY_GROUP_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.4]: Require that any Amazon EC2 security group rule does not use the source IP range 0.0.0.0/0 or ::/0 for specific high-risk ports
            [FIX]: Remove Amazon EC2 security group ingress rules that allow traffic from '0.0.0.0/0' or '::/0' to high-risk ports: '3389', '20', '23', '110', '143', '3306', '8080', '1433', '9200', '9300', '25', '445', '135', '21', '1434', '4333', '5432', '5500', '5601', '22', '3000', '5000', '8088', '8888'. The use of managed prefix lists is not supported.
        >>
}

rule vpc_sg_restricted_common_ports_check when is_cfn_hook(%INPUT_DOCUMENT, %SECURITY_GROUP_INGRESS_TYPE) {

    check_ingress_rule(%INPUT_DOCUMENT.%SECURITY_GROUP_INGRESS_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.4]: Require that any Amazon EC2 security group ruledoes not use the source IP range 0.0.0.0/0 or ::/0 for specific high-risk ports
            [FIX]: Remove Amazon EC2 security group ingress rules that allow traffic from '0.0.0.0/0' or '::/0' to high-risk ports: '3389', '20', '23', '110', '143', '3306', '8080', '1433', '9200', '9300', '25', '445', '135', '21', '1434', '4333', '5432', '5500', '5601', '22', '3000', '5000', '8088', '8888'. The use of managed prefix lists is not supported.
        >>
}

#
# Parameterized Rules
#
rule check_security_group(security_group) {
    %security_group [
        SecurityGroupIngress exists
        SecurityGroupIngress is_list
        SecurityGroupIngress not empty
    ] {
        SecurityGroupIngress[*] {
            check_ingress_rule(this)
        }
    }
}

rule check_ingress_rule(ingress_rule) {
    %ingress_rule[ CidrIp in %UNRESTRICTED_IPV4_RANGES or
                   CidrIpv6 in %UNRESTRICTED_IPV6_RANGES or 
                   SourcePrefixListId exists ] {
        # Scenario 3
        IpProtocol exists
        IpProtocol in %AUTHORIZED_PROTOCOLS

        when IpProtocol in ["tcp", "udp"] {
            FromPort exists
            ToPort exists

            let ingress_block = this

            %BLOCKED_PORTS.* {
                # Scenarios 4 and 5
                check_ports(this, %ingress_block.FromPort, %ingress_block.ToPort)
            }
        }
    }
}
rule check_ports(port, FromPort, ToPort) {
    %FromPort > %port or
    %ToPort < %port
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

### CT\.EC2\.PR\.4 example templates<a name="ct-ec2-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription:
        Fn::Sub: ${AWS::StackName}-example
      SecurityGroupIngress:
      - IpProtocol: tcp
        CidrIp: 0.0.0.0/0
        FromPort: 80
        ToPort: 80
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  PrefixList:
    Type: AWS::EC2::PrefixList
    Properties:
      PrefixListName:
        Fn::Sub: ${AWS::StackName}-example
      AddressFamily: IPv4
      MaxEntries: 10
      Entries:
        - Cidr: "0.0.0.0/0"
          Description: Public internet
  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription:
        Fn::Sub: ${AWS::StackName}-example
      SecurityGroupIngress:
      - IpProtocol: -1
        SourcePrefixListId: 
          Ref: PrefixList
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription:
        Fn::Sub: ${AWS::StackName}-example
      SecurityGroupIngress:
      - IpProtocol: tcp
        CidrIp: 0.0.0.0/0
        FromPort: 22
        ToPort: 22
```

## \[CT\.EC2\.PR\.5\] Require any Amazon EC2 network ACL to prevent ingress from 0\.0\.0\.0/0 to port 22 or port 3389<a name="ct-ec2-pr-5-description"></a>

This control checks whether the Amazon EC2 network ACL inbound entry allows unrestricted incoming traffic \(`0.0.0.0/0` or `::/0`\) for SSH or RDP\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::NetworkAclEntry`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.5 rule specification](#ct-ec2-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.5 rule specification](#ct-ec2-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.5 example templates](#ct-ec2-pr-5-templates) 

**Explanation**

Access to remote server administration ports, such as port 22 \(SSH\) and port 3389 \(RDP\), should not be publicly accessible, because these ports may allow unintended access to resources within your VPC\.

**Usage considerations**  
This control only applies to Amazon EC2 network ACL entry resources that allow unrestricted inbound traffic\.

### Remediation for rule failure<a name="ct-ec2-pr-5-remediation"></a>

For Amazon EC2 network ACL entries that allow inbound connectivity on port 22 or port 3389, provide a CIDR range in `CidrBlock` or `Ipv6CidrBlock` that does not allow traffic from all sources\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Network ACL Entry \- Example One<a name="ct-ec2-pr-5-remediation-1"></a>

Amazon EC2 network ACL entry configured to allow unrestricted inbound IPv4 TCP traffic in a port range excluding port 22 \(SSH\) and port 3389 \(RDP\)\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "NetworkAclEntry": {
        "Type": "AWS::EC2::NetworkAclEntry",
        "Properties": {
            "CidrBlock": "0.0.0.0/0",
            "Egress": false,
            "NetworkAclId": {
                "Ref": "NACL"
            },
            "Protocol": 6,
            "PortRange": {
                "From": 2000,
                "To": 2005
            },
            "RuleAction": "allow",
            "RuleNumber": 100
        }
    }
}
```

**YAML example**

```
NetworkAclEntry:
  Type: AWS::EC2::NetworkAclEntry
  Properties:
    CidrBlock: '0.0.0.0/0'
    Egress: false
    NetworkAclId: !Ref 'NACL'
    Protocol: 6
    PortRange:
      From: 2000
      To: 2005
    RuleAction: allow
    RuleNumber: 100
```

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Network ACL Entry \- Example Two<a name="ct-ec2-pr-5-remediation-2"></a>

Amazon EC2 network ACL entry configured to allow unrestricted inbound IPv6 UDP traffic in a port range excluding port 3389 \(RDP\)\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "NetworkAclEntry": {
        "Type": "AWS::EC2::NetworkAclEntry",
        "Properties": {
            "Ipv6CidrBlock": "::/0",
            "Egress": false,
            "NetworkAclId": {
                "Ref": "NACL"
            },
            "Protocol": 17,
            "PortRange": {
                "From": 100,
                "To": 200
            },
            "RuleAction": "allow",
            "RuleNumber": 100
        }
    }
}
```

**YAML example**

```
NetworkAclEntry:
  Type: AWS::EC2::NetworkAclEntry
  Properties:
    Ipv6CidrBlock: ::/0
    Egress: false
    NetworkAclId: !Ref 'NACL'
    Protocol: 17
    PortRange:
      From: 100
      To: 200
    RuleAction: allow
    RuleNumber: 100
```

### CT\.EC2\.PR\.5 rule specification<a name="ct-ec2-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   nacl_no_unrestricted_ssh_rdp_check
# 
# Description:
#   This control checks whether the Amazon EC2 network ACL inbound entry allows unrestricted incoming traffic ('0.0.0.0/0' or '::/0') for SSH or RDP.
# 
# Reports on:
#   AWS::EC2::NetworkAclEntry
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
#       And: The input document does not contain any EC2 network ACL entry resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a EC2 network ACL entry resource
#       And: EC2 network ACL entry resource has no CIDR block allowing inbound traffic
#            from source '0.0.0.0/0' or '::/0'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a EC2 network ACL entry resource
#       And: EC2 network ACL entry resource allows inbound traffic
#            from source '0.0.0.0/0' or '::/0'
#       And: EC2 network ACL entry resource allows all traffic
#            ('IpProtocol' is set to '-1')
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a EC2 network ACL entry resource
#       And: EC2 network ACL entry resource allows inbound traffic
#            from source '0.0.0.0/0' or '::/0'
#       And: EC2 network ACL entry resource allows TCP (protocol 6) traffic
#       And: EC2 network ACL entry resource allows traffic from a PortRange that includes 22
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a EC2 network ACL entry resource
#       And: EC2 network ACL entry resource allows inbound traffic
#            from source '0.0.0.0/0' or '::/0'
#       And: EC2 network ACL entry resource allows TCP (protocol 6) or UDP (protocol 17) traffic
#       And: EC2 network ACL entry resource allows traffic from a PortRange that includes 3389
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a EC2 network ACL entry resource
#       And: EC2 network ACL entry resource allows inbound traffic
#            from source '0.0.0.0/0' or '::/0'
#       And: EC2 network ACL entry resource allows TCP (protocol 6) traffic
#       And: EC2 network ACL entry resource allows traffic from a PortRange that excludes 22
#      Then: PASS
# Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a EC2 network ACL entry resource
#       And: EC2 network ACL entry resource allows inbound traffic
#            from source '0.0.0.0/0' or '::/0'
#       And: EC2 network ACL entry resource allows TCP (protocol 6) or UDP (protocol 17) traffic
#       And: EC2 network ACL entry resource allows traffic from a PortRange that excludes 3389
#      Then: PASS

#
# Constants
#
let NETWORK_ACL_TYPE = "AWS::EC2::NetworkAclEntry"
let INPUT_DOCUMENT = this
let ALL_TRAFFIC_PROTOCOL = [-1, "-1"]
let TCP_PROTOCOL = [6, "6"]
let UDP_PROTOCOL = [17, "17"]
let UNRESTRICTED_IPV4_RANGES = ["0.0.0.0/0"]
let UNRESTRICTED_IPV6_RANGES = ["::/0"]
let SSH_PORT = 22
let RDP_PORT = 3389

#
# Assignments
#
let nacl_entries = Resources.*[ Type == %NETWORK_ACL_TYPE ]

#
# Primary Rules
#
rule nacl_no_unrestricted_ssh_rdp_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %nacl_entries not empty {
    check(%nacl_entries.Properties)
        <<
        [CT.EC2.PR.5]: Require any Amazon EC2 network ACL to prevent ingress from 0.0.0.0/0 to port 22 or port 3389
        [FIX]: For Amazon EC2 network ACL entries that allow inbound connectivity on port 22 or port 3389, provide a CIDR range in 'CidrBlock' or 'Ipv6CidrBlock' that does not allow traffic from all sources.
        >>
}

rule nacl_no_unrestricted_ssh_rdp_check when is_cfn_hook(%INPUT_DOCUMENT, %NETWORK_ACL_TYPE) {
    check(%INPUT_DOCUMENT.%NETWORK_ACL_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.5]: Require any Amazon EC2 network ACL to prevent ingress from 0.0.0.0/0 to port 22 or port 3389
        [FIX]: For Amazon EC2 network ACL entries that allow inbound connectivity on port 22 or port 3389, provide a CIDR range in 'CidrBlock' or 'Ipv6CidrBlock' that does not allow traffic from all sources.
        >>
}

#
# Parameterized Rules
#
rule check(nacl_entry) {
    %nacl_entry [
        # Scenario 2
        filter_allow_unrestricted_ingress(this)
    ] {
        # Scenario 3
        Protocol exists
        Protocol not in %ALL_TRAFFIC_PROTOCOL

        # Scenario 4, 6
        check_for_open_ssh(this)

        # Scenario 5, 7
        check_for_open_rdp(this)
    }
}

rule filter_allow_unrestricted_ingress(nacl_entry) {
    Egress not exists or
    Egress != true

    CidrBlock in %UNRESTRICTED_IPV4_RANGES or
    Ipv6CidrBlock in %UNRESTRICTED_IPV6_RANGES

    RuleAction == "allow"
}

rule check_for_open_ssh(nacl_entry) {
    %nacl_entry [
        Protocol in %TCP_PROTOCOL
    ] {
        check_port_range_exists(this)
        check_ports(%SSH_PORT, PortRange.From, PortRange.To)
   }
}

rule check_for_open_rdp(nacl_entry) {
    %nacl_entry [
           Protocol in %TCP_PROTOCOL or
           Protocol in %UDP_PROTOCOL
    ] {
        check_port_range_exists(this)
        check_ports(%RDP_PORT, PortRange.From, PortRange.To)
   }
}

rule check_port_range_exists(nacl_entry) {
    PortRange exists
    PortRange is_struct
    PortRange {
        From exists
        To exists
    }
}

rule check_ports(port, nacl_from_port, nacl_to_port) {
    %nacl_from_port > %port or
    %nacl_to_port < %port
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

rule is_cfn_hook(doc, NETWORK_ACL_TYPE) {
    %doc.%NETWORK_ACL_TYPE.resourceProperties exists
}
```

### CT\.EC2\.PR\.5 example templates<a name="ct-ec2-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 192.168.0.0/16
  NACL:
    Type: AWS::EC2::NetworkAcl
    Properties:
      VpcId:
        Ref: VPC
  NetworkAclEntry:
    Type: AWS::EC2::NetworkAclEntry
    Properties:
      CidrBlock: 0.0.0.0/0
      Egress: false
      NetworkAclId:
        Ref: NACL
      Protocol: 6
      PortRange:
        From: 2000
        To: 2005
      RuleAction: allow
      RuleNumber: 100
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 192.168.0.0/16
  NACL:
    Type: AWS::EC2::NetworkAcl
    Properties:
      VpcId:
        Ref: VPC
  NetworkAclEntry:
    Type: AWS::EC2::NetworkAclEntry
    Properties:
      CidrBlock: 0.0.0.0/0
      Egress: false
      NetworkAclId:
        Ref: NACL
      Protocol: 6
      PortRange:
        From: 3000
        To: 3500
      RuleAction: allow
      RuleNumber: 100
```

## \[CT\.EC2\.PR\.6\] Require that Amazon EC2 transit gateways refuse automatic Amazon VPC attachment requests<a name="ct-ec2-pr-6-description"></a>

This control checks whether Amazon EC2 transit gateways are configured to accept Amazon VPC attachment requests automatically\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::TransitGateway`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.6 rule specification](#ct-ec2-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.6 rule specification](#ct-ec2-pr-6-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.6 example templates](#ct-ec2-pr-6-templates) 

**Explanation**

Turning on the `AutoAcceptSharedAttachments` property configures a transit gateway to accept cross\-account VPC attachment requests automatically, without verifying the request or the account from which the attachment is originating\. In alignment with the best practices of authorization and authentication, we recommended turning off this feature, to ensure that only authorized VPC attachment requests are accepted\.

### Remediation for rule failure<a name="ct-ec2-pr-6-remediation"></a>

Omit the `AutoAcceptSharedAttachments` property or set the property to `disable`\.

The examples that follow show how to implement this remediation\.

#### AWS Transit Gateway \- Example<a name="ct-ec2-pr-6-remediation-1"></a>

AWS Transit Gateway configured to deactivate auto\-acceptance of cross\-account Amazon VPC attachments\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "TransitGateway": {
        "Type": "AWS::EC2::TransitGateway",
        "Properties": {
            "AutoAcceptSharedAttachments": "disable"
        }
    }
}
```

**YAML example**

```
TransitGateway:
  Type: AWS::EC2::TransitGateway
  Properties:
    AutoAcceptSharedAttachments: disable
```

### CT\.EC2\.PR\.6 rule specification<a name="ct-ec2-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ec2_transit_gateway_auto_vpc_attach_disabled_check
# 
# Description:
#   This control checks whether Amazon EC2 transit gateways are configured to accept Amazon VPC attachment requests automatically.
# 
# Reports on:
#   AWS::EC2::TransitGateway
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
#       And: The input document does not contain any EC2 transit gateway resources
#      Then: SKIP
#   Scenario: 2
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 transit gateway resource
#        And: 'AutoAcceptSharedAttachments' configuration has been provided and is set to a value other than 'disable'
#       Then: FAIL
#   Scenario: 3
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 transit gateway resource
#        And: 'AutoAcceptSharedAttachments' configuration has not been provided
#       Then: PASS
#   Scenario: 4
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 transit gateway resource
#        And: 'AutoAcceptSharedAttachments' configuration has been provided and set to 'disable'
#       Then: PASS

#
# Constants
#
let EC2_TRANSIT_GATEWAY_TYPE = "AWS::EC2::TransitGateway"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_transit_gateway = Resources.*[ Type == %EC2_TRANSIT_GATEWAY_TYPE ]

#
# Primary Rules
#
rule ec2_transit_gateway_auto_vpc_attach_disabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                             %ec2_transit_gateway not empty {
    check(%ec2_transit_gateway.Properties)
        <<
        [CT.EC2.PR.6]: Require that Amazon EC2 transit gateways refuse automatic Amazon VPC attachment requests
            [FIX]: Omit the 'AutoAcceptSharedAttachments' property or set the property to 'disable'.
        >>
}

rule ec2_transit_gateway_auto_vpc_attach_disabled_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_TRANSIT_GATEWAY_TYPE) {
    check(%INPUT_DOCUMENT.%EC2_TRANSIT_GATEWAY_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.6]: Require that Amazon EC2 transit gateways refuse automatic Amazon VPC attachment requests
            [FIX]: Omit the 'AutoAcceptSharedAttachments' property or set the property to 'disable'.
        >>
}

#
# Parameterized Rules
#
rule check(ec2_transit_gateway) {
    %ec2_transit_gateway {
        # Scenario 3
        AutoAcceptSharedAttachments not exists or
        # Scenario 2 and 4
        AutoAcceptSharedAttachments == "disable"
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

### CT\.EC2\.PR\.6 example templates<a name="ct-ec2-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  TransitGateway:
    Type: AWS::EC2::TransitGateway
    Properties:
      AutoAcceptSharedAttachments: disable
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  TransitGateway:
    Type: AWS::EC2::TransitGateway
    Properties:
      AutoAcceptSharedAttachments: enable
```

## \[CT\.EC2\.PR\.7\] Require that an Amazon EBS volume attached to an Amazon EC2 instance is encrypted at rest<a name="ct-ec2-pr-7-description"></a>

This control checks whether your standalone Amazon EC2 EBS volumes and new Amazon EBS volumes created through EC2 instance Block Device Mappings are encrypted at rest\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::Instance`, `AWS::EC2::Volume`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.7 rule specification](#ct-ec2-pr-7-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.7 rule specification](#ct-ec2-pr-7-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.7 example templates](#ct-ec2-pr-7-templates) 

**Explanation**

For an added layer of security of your sensitive data in Amazon EC2 EBS volumes, you should enable EBS encryption at rest\. Amazon EBS encryption offers a straightforward encryption solution for your EBS resources that doesn't require you to build, maintain, and secure your own key management infrastructure\. It uses KMS keys when creating encrypted volumes and snapshots\.

**Usage considerations**  
This control applies only to standalone Amazon EBS volumes and EC2 instances with block device mappings\.

### Remediation for rule failure<a name="ct-ec2-pr-7-remediation"></a>

Set `Encryption` to true on Amazon EC2 EBS Volumes\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Instance \- Example<a name="ct-ec2-pr-7-remediation-1"></a>

Amazon EC2 instance with an encrypted EBS volume\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EC2Instance": {
        "Type": "AWS::EC2::Instance",
        "Properties": {
            "ImageId": {
                "Ref": "LatestAmiId"
            },
            "InstanceType": "t3.micro",
            "NetworkInterfaces": [
                {
                    "DeviceIndex": 0,
                    "SubnetId": {
                        "Ref": "Subnet"
                    },
                    "AssociatePublicIpAddress": false
                }
            ],
            "BlockDeviceMappings": [
                {
                    "DeviceName": "/dev/sdm",
                    "Ebs": {
                        "VolumeType": "gp3",
                        "Iops": 200,
                        "Encrypted": true,
                        "DeleteOnTermination": true,
                        "VolumeSize": 20
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
EC2Instance:
  Type: AWS::EC2::Instance
  Properties:
    ImageId: !Ref 'LatestAmiId'
    InstanceType: t3.micro
    NetworkInterfaces:
      - DeviceIndex: 0
        SubnetId: !Ref 'Subnet'
        AssociatePublicIpAddress: false
    BlockDeviceMappings:
      - DeviceName: /dev/sdm
        Ebs:
          VolumeType: gp3
          Iops: 200
          Encrypted: true
          DeleteOnTermination: true
          VolumeSize: 20
```

The examples that follow show how to implement this remediation\.

#### Amazon EBS Volume \- Example<a name="ct-ec2-pr-7-remediation-2"></a>

Amazon EBS Volume with encryption configured\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EBSVolume": {
        "Type": "AWS::EC2::Volume",
        "Properties": {
            "Size": 100,
            "AvailabilityZone": {
                "Fn::Select": [
                    0,
                    {
                        "Fn::GetAZs": ""
                    }
                ]
            },
            "Encrypted": true
        }
    }
}
```

**YAML example**

```
EBSVolume:
  Type: AWS::EC2::Volume
  Properties:
    Size: 100
    AvailabilityZone: !Select
      - 0
      - !GetAZs ''
    Encrypted: true
```

### CT\.EC2\.PR\.7 rule specification<a name="ct-ec2-pr-7-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ec2_encrypted_volumes_check
# 
# Description:
#   Checks whether standalone Amazon EC2 EBS volumes and new EC2 EBS volumes created through EC2 instance
#   Block Device Mappings are encrypted at rest.
# 
# Reports on:
#   AWS::EC2::Instance, AWS::EC2::Volume
# 
# Evaluates:
#   AWS CloudFormation, CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Amazon EC2 volume resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'BlockDeviceMappings' has not been provided or has been provided as an empty list
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'BlockDeviceMappings' has been provided as a non-empty list
#       And: 'Ebs' has been provided in a 'BlockDeviceMappings' configuration
#       And: 'Encrypted' has not been provided in the 'Ebs' configuration
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'BlockDeviceMappings' has been provided as a non-empty list
#       And: 'Ebs' has been provided in a 'BlockDeviceMappings' configuration
#       And: 'Encrypted' has been provided in the 'Ebs' configuration and set to bool(false)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an EC2 volume resource
#       And: 'Encrypted' on the EC2 volume has not been provided
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an EC2 volume resource
#       And: 'Encrypted' on the EC2 volume has been provided and is set to bool(false)
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'BlockDeviceMappings' has been provided as a non-empty list
#       And: 'Ebs' has been provided in a 'BlockDeviceMappings' configuration
#       And: 'Encrypted' has been provided in the 'Ebs' configuration and set to bool(true)
#      Then: PASS
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an EC2 volume resource
#       And: 'Encrypted' on the EC2 volume has been provided and is set to bool(true)
#      Then: PASS

#
# Constants
#
let EC2_VOLUME_TYPE = "AWS::EC2::Volume"
let EC2_INSTANCE_TYPE = "AWS::EC2::Instance"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_volumes = Resources.*[ Type == %EC2_VOLUME_TYPE ]
let ec2_instances = Resources.*[ Type == %EC2_INSTANCE_TYPE ]

#
# Primary Rules
#
rule ec2_encrypted_volumes_check when is_cfn_template(%INPUT_DOCUMENT)
                                      %ec2_volumes not empty {
    check_volume(%ec2_volumes.Properties)
        <<
        [CT.EC2.PR.7]: Require that an Amazon EBS volume attached to an Amazon EC2 instance is encrypted at rest
        [FIX]: Set 'Encryption' to true on EC2 EBS Volumes.
        >>
}

rule ec2_encrypted_volumes_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_VOLUME_TYPE) {
    check_volume(%INPUT_DOCUMENT.%EC2_VOLUME_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.7]: Require that an Amazon EBS volume attached to an Amazon EC2 instance is encrypted at rest
        [FIX]: Set 'Encryption' to true on EC2 EBS Volumes.
        >>
}

rule ec2_encrypted_volumes_check when is_cfn_template(%INPUT_DOCUMENT)
                                      %ec2_instances not empty {
    check_instance(%ec2_instances.Properties)
        <<
        [CT.EC2.PR.7]: Require that an Amazon EBS volume attached to an Amazon EC2 instance is encrypted at rest
        [FIX]: Set 'Encryption' to true on EC2 EBS Volumes.
        >>
}

rule ec2_encrypted_volumes_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_INSTANCE_TYPE) {
    check_instance(%INPUT_DOCUMENT.%EC2_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.7]: Require that an Amazon EBS volume attached to an Amazon EC2 instance is encrypted at rest
        [FIX]: Set 'Encryption' to true on EC2 EBS Volumes.
        >>
}

#
# Parameterized Rules
#

rule check_instance(ec2_instance) {
    %ec2_instance[
        filter_ec2_instance_block_device_mappings(this)
    ] {
        BlockDeviceMappings[
            Ebs exists
            Ebs is_struct
        ] {
            check_volume(Ebs)
        }
    }
}

rule check_volume(ec2_volume) {
    %ec2_volume {
        # Scenario 2
        Encrypted exists
        # Scenarios 3 and 4
        Encrypted == true
    }
}

rule filter_ec2_instance_block_device_mappings(ec2_instance) {
    %ec2_instance {
        BlockDeviceMappings exists
        BlockDeviceMappings is_list
        BlockDeviceMappings not empty
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

### CT\.EC2\.PR\.7 example templates<a name="ct-ec2-pr-7-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

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
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      NetworkInterfaces:
      - DeviceIndex: 0
        SubnetId:
          Ref: Subnet
        AssociatePublicIpAddress: false
      BlockDeviceMappings:
      - DeviceName: "/dev/sdm"
        Ebs:
          VolumeType: gp3
          Iops: 200
          Encrypted: true
          DeleteOnTermination: true
          VolumeSize: 20
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
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      NetworkInterfaces:
      - DeviceIndex: 0
        SubnetId:
          Ref: Subnet
        AssociatePublicIpAddress: false
      BlockDeviceMappings:
      - DeviceName: "/dev/sdm"
        Ebs:
          VolumeType: gp3
          Iops: 200
          Encrypted: false
          DeleteOnTermination: true
          VolumeSize: 20
```

## \[CT\.EC2\.PR\.8\] Require any Amazon EC2 instance to have a non\-public IP address<a name="ct-ec2-pr-8-description"></a>

This control checks whether your Amazon EC2 instance is configured to associate a public IP address\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::Instance`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.8 rule specification](#ct-ec2-pr-8-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.8 rule specification](#ct-ec2-pr-8-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.8 example templates](#ct-ec2-pr-8-templates) 

**Explanation**

A public IPv4 address is an IP address that is reachable from the internet\. If you launch your instance with a public IP address, then your EC2 instance is reachable from the internet\. A private IPv4 address is an IP address that is not reachable from the internet\. You can use private IPv4 addresses for communication between EC2 instances in the same VPC or in your connected private network\.

IPv6 addresses are globally unique, and therefore are reachable from the internet\. However, by default all subnets have the IPv6 addressing attribute set to false\.

**Usage considerations**  
This control applies only to new network interfaces created by means of the `NetworkInterfaceId` property \(`NetworkInterfaces` configurations where a `NetworkInterfaceId` has not been specified\)\.
This control requires setting `AssociatePublicIpAddress` to `false` on new network interfaces created by means of the `NetworkInterfaces` property\.
This control requires subnet information to be specified within a `NetworkInterfaces` configuration instead of the root level `SubnetId` property\.

### Remediation for rule failure<a name="ct-ec2-pr-8-remediation"></a>

Specify network interfaces using the `NetworkInterfaces` property instead of the root level `SubnetId` property\. Set `AssociatePublicIpAddress` to false within each `NetworkInterfaces` configuration\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Instance \- Example<a name="ct-ec2-pr-8-remediation-1"></a>

Amazon EC2 instance configured with a new interface that disables public IP address association on creation\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EC2Instance": {
        "Type": "AWS::EC2::Instance",
        "Properties": {
            "InstanceType": "t3.micro",
            "ImageId": {
                "Ref": "LatestAmiId"
            },
            "NetworkInterfaces": [
                {
                    "DeviceIndex": 0,
                    "SubnetId": {
                        "Ref": "Subnet"
                    },
                    "AssociatePublicIpAddress": false
                }
            ]
        }
    }
}
```

**YAML example**

```
EC2Instance:
  Type: AWS::EC2::Instance
  Properties:
    InstanceType: t3.micro
    ImageId: !Ref 'LatestAmiId'
    NetworkInterfaces:
      - DeviceIndex: 0
        SubnetId: !Ref 'Subnet'
        AssociatePublicIpAddress: false
```

### CT\.EC2\.PR\.8 rule specification<a name="ct-ec2-pr-8-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ec2_instance_no_public_ip_check
# 
# Description:
#   This control checks whether your Amazon EC2 instance is configured to associate a public IP address.
# 
# Reports on:
#   AWS::EC2::Instance
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
#       And: The input document does not contain any EC2 instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'NetworkInterfaces' is not present on the EC2 instance resource or is an empty list
#       And: 'SubnetId' is not provided as a top-level resource property
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'NetworkInterfaces' is present on the EC2 instance resource as a non-empty list
#       And: 'NetworkInterfaceId' is present for a configuration in 'NetworkInterfaces' and is a non-empty string or
#             valid local reference
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'NetworkInterfaces' is not provided
#       And: 'SubnetId' is provided as a top-level resource property
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'NetworkInterfaces' is present on the EC2 instance resource with one or more configurations
#       And: 'NetworkInterfaceId' is not present or is present and and is an empty string or invalid local reference for
#            a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is not present for a configuration in 'NetworkInterfaces'
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'NetworkInterfaces' is present on the EC2 instance resource
#       And: 'NetworkInterfaceId' is not present or is present and and is an empty string or invalid local reference for
#            a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is present for a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is set to bool(true)
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 instance resource
#       And: 'NetworkInterfaces' is present on the EC2 instance resource
#       And: 'NetworkInterfaceId' is not present or is present and and is an empty string or invalid local reference for
#            a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is present for a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is set to bool(false)
#      Then: PASS

#
# Constants
#
let EC2_INSTANCE_TYPE = "AWS::EC2::Instance"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_instances = Resources.*[ Type == %EC2_INSTANCE_TYPE ]

#
# Primary Rules
#
rule ec2_instance_no_public_ip_check when is_cfn_template(%INPUT_DOCUMENT)
                                          %ec2_instances not empty {
    check(%ec2_instances.Properties)
        <<
        [CT.EC2.PR.8]: Require any Amazon EC2 instance to have a non-public IP address
        [FIX]: Specify network interfaces using the 'NetworkInterfaces' property instead of the root level 'SubnetId' property. Set 'AssociatePublicIpAddress' to false within each 'NetworkInterfaces' configuration.
        >>
}

rule ec2_instance_no_public_ip_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%EC2_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.8]: Require any Amazon EC2 instance to have a non-public IP address
        [FIX]: Specify network interfaces using the 'NetworkInterfaces' property instead of the root level 'SubnetId' property. Set 'AssociatePublicIpAddress' to false within each 'NetworkInterfaces' configuration.
        >>
}

#
# Parameterized Rules
#
rule check(ec2_instance) {
    %ec2_instance[ SubnetId exists ] {
        # Scenario 5
        SubnetId not exists
    }

    %ec2_instance[
        # Scenario 2
        NetworkInterfaces exists
        NetworkInterfaces is_list
        NetworkInterfaces not empty
    ] {
        NetworkInterfaces[
            # Scenario 3 and 4
            filter_network_interfaces(this)
        ] {
            # Scenario 6
            AssociatePublicIpAddress exists
            # Scenarios 7 and 8
            AssociatePublicIpAddress == false
        }
    }
}

rule filter_network_interfaces(network_interface) {
    %network_interface {
        NetworkInterfaceId not exists or
        filter_property_is_empty_string(NetworkInterfaceId) or
        filter_exclude_valid_local_reference(%INPUT_DOCUMENT, NetworkInterfaceId, "AWS::EC2::NetworkInterface")
     }
}

rule filter_property_is_empty_string(value) {
    %value {
        this is_string
        this == /\A\s*\z/
    }
}

rule filter_exclude_valid_local_reference(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        this not is_string
        this is_struct

        when this.'Fn::GetAtt' exists {
            'Fn::GetAtt' {
                when filter_query_template_resources(%doc, this[0], %referenced_resource_type) {
                    this not exists
                }
                this exists
            }
        }
        when this.'Fn::GetAtt' not exists {
            this exists
        }
    }
}

rule filter_query_template_resources(doc, resource_key, referenced_resource_type) {
    let referenced_resource = %doc.Resources[ keys == %resource_key ]
    %referenced_resource not empty
    %referenced_resource {
        Type in %referenced_resource_type
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

### CT\.EC2\.PR\.8 example templates<a name="ct-ec2-pr-8-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

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
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t3.micro
      ImageId:
        Ref: LatestAmiId
      NetworkInterfaces:
      - DeviceIndex: 0
        SubnetId:
          Ref: Subnet
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
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t3.micro
      ImageId:
        Ref: LatestAmiId
      SubnetId:
        Ref: Subnet
```

## \[CT\.EC2\.PR\.9\] Require any Amazon EC2 launch template not to auto\-assign public IP addresses to network interfaces<a name="ct-ec2-pr-9-description"></a>

This control checks whether your Amazon EC2 launch templates are configured to assign public IP addresses to network interfaces\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::LaunchTemplate`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.9 rule specification](#ct-ec2-pr-9-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.9 rule specification](#ct-ec2-pr-9-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.9 example templates](#ct-ec2-pr-9-templates) 

**Explanation**

A public IP address is an IP address that is reachable from the internet\. If you configure your network interfaces with a public IP address, then the resources associated to those network interfaces are reachable from the internet\. EC2 resources should not be publicly accessible, because this may allow unintended access to your application servers\.

**Usage considerations**  
This control applies only to new network interfaces created by means of the `NetworkInterfaceId` property in `LaunchTemplateData` \(`NetworkInterfaces` configurations where a `NetworkInterfaceId` has not been specified\)\.
This control requires setting `AssociatePublicIpAddress` to `false` on new network interfaces created by means of the `NetworkInterfaces` property in `LaunchTemplateData`\.

### Remediation for rule failure<a name="ct-ec2-pr-9-remediation"></a>

Set `AssociatePublicIpAddress` to `false` within each `NetworkInterfaces` configuration in `LaunchTemplateData`\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Launch Template \- Example<a name="ct-ec2-pr-9-remediation-1"></a>

Amazon EC2 launch template configured with a network interface that disables public IP address association\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EC2LaunchTemplate": {
        "Type": "AWS::EC2::LaunchTemplate",
        "Properties": {
            "LaunchTemplateData": {
                "NetworkInterfaces": [
                    {
                        "DeviceIndex": 0,
                        "SubnetId": {
                            "Ref": "Subnet"
                        },
                        "AssociatePublicIpAddress": false
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
EC2LaunchTemplate:
  Type: AWS::EC2::LaunchTemplate
  Properties:
    LaunchTemplateData:
      NetworkInterfaces:
        - DeviceIndex: 0
          SubnetId: !Ref 'Subnet'
          AssociatePublicIpAddress: false
```

### CT\.EC2\.PR\.9 rule specification<a name="ct-ec2-pr-9-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ec2_launch_template_public_ip_disabled_check
# 
# Description:
#   This control checks whether your Amazon EC2 launch templates are configured to assign public IP addresses to network interfaces.
# 
# Reports on:
#   AWS::EC2::LaunchTemplate
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
#       And: The input document does not contain any Amazon EC2 launch template resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 launch template resource
#       And: 'NetworkInterfaces' is not provided in 'LaunchTemplateData'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 launch template resource
#       And: 'LaunchTemplateData.NetworkInterfaces' is present on the Amazon EC2 launch template resource as a non empty list
#       And: 'NetworkInterfaceId' is present for a configuration in 'NetworkInterfaces' and is a non-empty string or
#             valid local reference
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 launch template resource
#       And: 'LaunchTemplateData.NetworkInterfaces' is present on the Amazon EC2 launch template resource
#       And: 'NetworkInterfaceId' is not present or is present and is an empty string or invalid local reference for
#            a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is not present for a configuration in 'NetworkInterfaces'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 launch template resource
#       And: 'LaunchTemplateData.NetworkInterfaces' is present on the Amazon EC2 launch template resource
#       And: 'NetworkInterfaceId' is not present or is present and is an empty string or invalid local reference for
#            a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is present for a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is set to bool(true)
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 launch template resource
#       And: 'LaunchTemplateData.NetworkInterfaces' is present on the Amazon EC2 launch template resource
#       And: 'NetworkInterfaceId' is not present or is present and is an empty string or invalid local reference for
#            a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is present for a configuration in 'NetworkInterfaces'
#       And: 'AssociatePublicIpAddress' is set to bool(false)
#      Then: PASS

#
# Constants
#
let EC2_LAUNCH_TEMPLATE_TYPE = "AWS::EC2::LaunchTemplate"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_launch_templates = Resources.*[ Type == %EC2_LAUNCH_TEMPLATE_TYPE ]

#
# Primary Rules
#
rule ec2_launch_template_public_ip_disabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                       %ec2_launch_templates not empty {
    check(%ec2_launch_templates.Properties)
        <<
        [CT.EC2.PR.9]: Require any Amazon EC2 launch template not to auto-assign public IP addresses to network interfaces
        [FIX]: Set 'AssociatePublicIpAddress' to 'false' within each 'NetworkInterfaces' configuration in 'LaunchTemplateData'.
        >>
}

rule ec2_launch_template_public_ip_disabled_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_LAUNCH_TEMPLATE_TYPE) {
    check(%INPUT_DOCUMENT.%EC2_LAUNCH_TEMPLATE_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.9]: Require any Amazon EC2 launch template not to auto-assign public IP addresses to network interfaces
        [FIX]: Set 'AssociatePublicIpAddress' to 'false' within each 'NetworkInterfaces' configuration in 'LaunchTemplateData'.
        >>
}

#
# Parameterized Rules
#
rule check(ec2_launch_templates) {
    %ec2_launch_templates[
        # Scenario 2
        filter_launch_template(this)
    ] {
        LaunchTemplateData {
            NetworkInterfaces[
                # Scenario 3 and 4
                filter_network_interfaces(this)
            ] {
                # Scenario 5 and 6
                AssociatePublicIpAddress exists
                AssociatePublicIpAddress == false
            }
        }
    }
}

rule filter_launch_template(ec2_launch_template) {
    %ec2_launch_template {
        LaunchTemplateData exists
        LaunchTemplateData is_struct
        LaunchTemplateData {
            NetworkInterfaces exists
            NetworkInterfaces is_list
            NetworkInterfaces not empty
        }
    }
}

rule filter_network_interfaces(network_interface) {
    %network_interface {
        NetworkInterfaceId not exists or
        filter_property_is_empty_string(NetworkInterfaceId) or
        filter_exclude_valid_local_reference(%INPUT_DOCUMENT, NetworkInterfaceId, "AWS::EC2::NetworkInterface")
    }
}

rule filter_property_is_empty_string(value) {
    %value {
        this is_string
        this == /\A\s*\z/
    }
}

rule filter_exclude_valid_local_reference(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        this not is_string
        this is_struct

        when this.'Fn::GetAtt' exists {
            'Fn::GetAtt' {
                when query_for_resource(%doc, this[0], %referenced_resource_type) {
                    this not exists
                }
                this exists
            }
        }
        when this.'Fn::GetAtt' not exists {
            this exists
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

rule query_for_resource(doc, resource_key, referenced_resource_type) {
    let referenced_resource = %doc.Resources[ keys == %resource_key ]
    %referenced_resource not empty
    %referenced_resource {
        Type == %referenced_resource_type
    }
}
```

### CT\.EC2\.PR\.9 example templates<a name="ct-ec2-pr-9-templates"></a>

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
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        NetworkInterfaces:
        - DeviceIndex: 0
          SubnetId:
            Ref: Subnet
          AssociatePublicIpAddress: false
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

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
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        NetworkInterfaces:
        - DeviceIndex: 0
          SubnetId:
            Ref: Subnet
          AssociatePublicIpAddress: true
```

## \[CT\.EC2\.PR\.10\] Require Amazon EC2 launch templates to have Amazon CloudWatch detailed monitoring activated<a name="ct-ec2-pr-10-description"></a>

This control checks whether the Amazon EC2 launch template has detailed monitoring enabled\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::LaunchTemplate`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.10 rule specification](#ct-ec2-pr-10-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.10 rule specification](#ct-ec2-pr-10-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.10 example templates](#ct-ec2-pr-10-templates) 

**Explanation**

Monitoring is an important part of maintaining the reliability, availability, and performance of your AWS solutions\. You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multi\-point failure if one occurs\. From a security perspective, logging is also an important feature to enable for future forensics efforts in the case of any security incidents\.

### Remediation for rule failure<a name="ct-ec2-pr-10-remediation"></a>

In `LaunchTemplateData`, provide a `Monitoring` configuration with `Enabled` set to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Launch Template \- Example<a name="ct-ec2-pr-10-remediation-1"></a>

Amazon EC2 launch template configured with detailed monitoring enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EC2LaunchTemplate": {
        "Type": "AWS::EC2::LaunchTemplate",
        "Properties": {
            "LaunchTemplateData": {
                "Monitoring": {
                    "Enabled": true
                }
            }
        }
    }
}
```

**YAML example**

```
EC2LaunchTemplate:
  Type: AWS::EC2::LaunchTemplate
  Properties:
    LaunchTemplateData:
      Monitoring:
        Enabled: true
```

### CT\.EC2\.PR\.10 rule specification<a name="ct-ec2-pr-10-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ec2_launch_template_monitoring_enabled_check
# 
# Description:
#   This control checks whether the Amazon EC2 launch template has detailed monitoring enabled.
# 
# Reports on:
#   AWS::EC2::LaunchTemplate
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
#        And: The input document does not contain any EC2 launch template resources
#       Then: SKIP
#   Scenario: 2
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 launch template resource
#        And: 'LaunchTemplateData.Monitoring.Enabled' has not been provided or has been provided and is empty.
#       Then: FAIL
#   Scenario: 3
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 launch template resource
#        And: 'LaunchTemplateData.Monitoring.Enabled' has been provided
#        And: 'LaunchTemplateData.Monitoring.Enabled' is equal to a value other than bool(true)
#       Then: FAIL
#   Scenario: 4
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an EC2 launch template resource
#        And: 'LaunchTemplateData.Monitoring.Enabled' has been provided
#        And: 'LaunchTemplateData.Monitoring.Enabled' is equal to bool(true)
#       Then: PASS

#
# Constants
#
let EC2_LAUNCH_TEMPLATE_TYPE = "AWS::EC2::LaunchTemplate"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_launch_templates = Resources.*[ Type == %EC2_LAUNCH_TEMPLATE_TYPE ]

#
# Primary Rules
#
rule ec2_launch_template_monitoring_enabled_check when is_cfn_template(this)
                                                       %ec2_launch_templates not empty {
    check(%ec2_launch_templates.Properties)
        <<
        [CT.EC2.PR.10]: Require Amazon EC2 launch templates to have Amazon CloudWatch detailed monitoring activated
        [FIX]: In 'LaunchTemplateData', provide a 'Monitoring' configuration with 'Enabled' set to 'true'.
        >>
}

rule ec2_launch_template_monitoring_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_LAUNCH_TEMPLATE_TYPE) {
    check(%INPUT_DOCUMENT.%EC2_LAUNCH_TEMPLATE_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.10]: Require Amazon EC2 launch templates to have Amazon CloudWatch detailed monitoring activated
        [FIX]: In 'LaunchTemplateData', provide a 'Monitoring' configuration with 'Enabled' set to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(ec2_launch_template) {
    %ec2_launch_template {
        # Scenario 2
        LaunchTemplateData exists
        LaunchTemplateData is_struct

        LaunchTemplateData {
            Monitoring exists
            Monitoring is_struct

            # Scenario 3 and 4
            Monitoring {
                Enabled exists
                Enabled == true
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

### CT\.EC2\.PR\.10 example templates<a name="ct-ec2-pr-10-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        Monitoring:
          Enabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  EC2LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        Monitoring:
          Enabled: false
```

## \[CT\.EC2\.PR\.11\] Require that an Amazon EC2 subnet does not automatically assign public IP addresses<a name="ct-ec2-pr-11-description"></a>

This control checks whether your Amazon VPC subnets assign public IP addresses automatically\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::Subnet`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.11 rule specification](#ct-ec2-pr-11-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.11 rule specification](#ct-ec2-pr-11-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.11 example templates](#ct-ec2-pr-11-templates) 

**Explanation**

All subnets have an attribute that determines whether a network interface created in the subnet automatically receives a public IPv4 address\. When launched into subnets that have this attribute enabled, instances receive a public IP address assigned to their primary network interface\.

**Usage considerations**  
This control deactivates automatic assignment of public IP addresses for new network interfaces in Amazon VPC subnets\.
When this control is in operation, public IP addresses can be assigned to network interfaces by means of resource\-level settings\. \(For example, assignment of a public IP address can be made at EC2 instance launch time\.\)

### Remediation for rule failure<a name="ct-ec2-pr-11-remediation"></a>

Omit the `MapPublicIpOnLaunch` property to use the default configuration, or set the `MapPublicIpOnLaunch` property to `false`\.

The examples that follow show how to implement this remediation\.

#### Amazon VPC Subnet \- Example One<a name="ct-ec2-pr-11-remediation-1"></a>

Amazon VPC subnet configured to deactivate automatic assignment of public IP addresses by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "Subnet": {
        "Type": "AWS::EC2::Subnet",
        "Properties": {
            "VpcId": {
                "Ref": "VPC"
            },
            "CidrBlock": "10.0.0.0/24",
            "AvailabilityZone": {
                "Fn::Select": [
                    0,
                    {
                        "Fn::GetAZs": ""
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
Subnet:
  Type: AWS::EC2::Subnet
  Properties:
    VpcId: !Ref 'VPC'
    CidrBlock: 10.0.0.0/24
    AvailabilityZone: !Select
      - 0
      - !GetAZs ''
```

The examples that follow show how to implement this remediation\.

#### Amazon VPC Subnet \- Example Two<a name="ct-ec2-pr-11-remediation-2"></a>

Amazon VPC subnet configured to deactivate automatic assignment of public IP addresses by means of the `MapPublicIpOnLaunch` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "Subnet": {
        "Type": "AWS::EC2::Subnet",
        "Properties": {
            "VpcId": {
                "Ref": "VPC"
            },
            "CidrBlock": "10.0.0.0/24",
            "AvailabilityZone": {
                "Fn::Select": [
                    0,
                    {
                        "Fn::GetAZs": ""
                    }
                ]
            },
            "MapPublicIpOnLaunch": false
        }
    }
}
```

**YAML example**

```
Subnet:
  Type: AWS::EC2::Subnet
  Properties:
    VpcId: !Ref 'VPC'
    CidrBlock: 10.0.0.0/24
    AvailabilityZone: !Select
      - 0
      - !GetAZs ''
    MapPublicIpOnLaunch: false
```

### CT\.EC2\.PR\.11 rule specification<a name="ct-ec2-pr-11-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# 
# Rule Identifier:
#   subnet_auto_assign_public_ip_disabled_check
# 
# Description:
#   This control checks whether your Amazon VPC subnets automatically assign public IP addresses.
# 
# Reports on:
#   AWS::EC2::Subnet
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
#       And: The input document does not contain any EC2 subnet resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 subnet resource
#       And: 'MapPublicIpOnLaunch' is present and set to bool(true)
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 subnet resource
#       And: 'MapPublicIpOnLaunch' is not present
#      Then: PASS
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an EC2 subnet resource
#       And: 'MapPublicIpOnLaunch' is present and set to bool(false)
#      Then: PASS

#
# Constants
#
let EC2_SUBNET_TYPE  = "AWS::EC2::Subnet"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_subnets = Resources.*[ Type == %EC2_SUBNET_TYPE ]

#
# Primary Rules
#
rule subnet_auto_assign_public_ip_disabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %ec2_subnets not empty {
    check(%ec2_subnets.Properties)
        <<
        [CT.EC2.PR.11]: Require that an Amazon EC2 subnet does not automatically assign public IP addresses
        [FIX]: Omit the 'MapPublicIpOnLaunch' property to use the default configuration, or set the 'MapPublicIpOnLaunch' property to 'false'.
        >>
}

rule subnet_auto_assign_public_ip_disabled_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_SUBNET_TYPE) {
    check(%INPUT_DOCUMENT.%EC2_SUBNET_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.11]: Require that an Amazon EC2 subnet does not automatically assign public IP addresses
        [FIX]: Omit the 'MapPublicIpOnLaunch' property to use the default configuration, or set the 'MapPublicIpOnLaunch' property to 'false'.
        >>
}

#
# Parameterized Rules
#
rule check(ec2_subnet) {
    %ec2_subnet {
        # Scenario 3
        MapPublicIpOnLaunch not exists or
        # Scenarios 2 and 4
        MapPublicIpOnLaunch == false
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

### CT\.EC2\.PR\.11 example templates<a name="ct-ec2-pr-11-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
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
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
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
      MapPublicIpOnLaunch: true
```

## \[CT\.EC2\.PR\.12\] Require an Amazon EC2 instance to configure one ENI only<a name="ct-ec2-pr-12-description"></a>

This control checks whether your Amazon Elastic Compute Cloud \(Amazon EC2\) instance uses multiple ENIs \(Elastic Network Interfaces\)\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EC2::Instance`
+ **AWS CloudFormation guard rule: ** [CT\.EC2\.PR\.12 rule specification](#ct-ec2-pr-12-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.EC2\.PR\.12 rule specification](#ct-ec2-pr-12-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.EC2\.PR\.12 example templates](#ct-ec2-pr-12-templates) 

**Explanation**

Multiple ENIs can cause dual\-homed instances, meaning instances that have multiple subnets\. This duplication can add network security complexity and introduce unintended network paths and access\.

**Usage considerations**  
This rule is incompatible with Amazon EC2 Instances that require the use of multiple ENIs\. For example, this control will fail if an EC2 instance that belongs to an Amazon EKS cluster has more than one ENI\.

### Remediation for rule failure<a name="ct-ec2-pr-12-remediation"></a>

Configure Amazon EC2 instances with only one ENI\.

The examples that follow show how to implement this remediation\.

#### Amazon EC2 Instance \- Example<a name="ct-ec2-pr-12-remediation-1"></a>

EC2 Instance with a single network interface\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EC2Instance": {
        "Type": "AWS::EC2::Instance",
        "Properties": {
            "ImageId": {
                "Ref": "LatestAmiId"
            },
            "NetworkInterfaces": [
                {
                    "SubnetId": {
                        "Ref": "TestSubnet"
                    },
                    "DeviceIndex": 0
                }
            ]
        }
    }
}
```

**YAML example**

```
EC2Instance:
  Type: AWS::EC2::Instance
  Properties:
    ImageId: !Ref 'LatestAmiId'
    NetworkInterfaces:
      - SubnetId: !Ref 'TestSubnet'
        DeviceIndex: 0
```

### CT\.EC2\.PR\.12 rule specification<a name="ct-ec2-pr-12-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ec2_instance_multiple_eni_check
# 
# Description:
#   Checks whether Amazon Elastic Compute Cloud (Amazon EC2) instances use multiple ENIs (Elastic Network Interfaces)
#   or Elastic Fabric Adapters (EFAs).
# 
# Reports on:
#   AWS::EC2::Instance
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
#       And: The input document does not contain any Amazon EC2 instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 instance resource
#       And: 'NetworkInterfaces' is not present or is present and contains 0 configurations
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 instance resource
#       And: 'NetworkInterfaces' is present and contains >1 configurations
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EC2 instance resource
#       And: 'NetworkInterfaces' is present
#       And: 'NetworkInterfaces' is present and contains 1 configuration
#      Then: PASS

#
# Constants
#
let EC2_INSTANCE_TYPE = "AWS::EC2::Instance"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ec2_instances = Resources.*[ Type == %EC2_INSTANCE_TYPE ]

#
# Primary Rules
#
rule ec2_instance_multiple_eni_check when is_cfn_template(%INPUT_DOCUMENT)
                                          %ec2_instances not empty {
    check(%ec2_instances.Properties)
        <<
        [CT.EC2.PR.12]: Require an Amazon EC2 instance to configure one ENI only
        [FIX]: Configure Amazon EC2 instances with only one ENI.
        >>

}

rule ec2_instance_multiple_eni_check when is_cfn_hook(%INPUT_DOCUMENT, %EC2_INSTANCE_TYPE) {

    check(%INPUT_DOCUMENT.%EC2_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.EC2.PR.12]: Require an Amazon EC2 instance to configure one ENI only
        [FIX]: Configure Amazon EC2 instances with only one ENI.
        >>
}

#
# Parameterized Rules
#
rule check(ec2_instance) {
    %ec2_instance [
        # Scenario 2
        NetworkInterfaces exists
        NetworkInterfaces is_list
        NetworkInterfaces not empty
    ] {
        # Scenario 3 and 4
        NetworkInterfaces[1] not exists
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

### CT\.EC2\.PR\.12 example templates<a name="ct-ec2-pr-12-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

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
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      NetworkInterfaces:
      - SubnetId:
          Ref: Subnet
        DeviceIndex: 0
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
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId:
        Ref: LatestAmiId
      InstanceType: t3.micro
      NetworkInterfaces:
      - SubnetId:
          Ref: Subnet
        DeviceIndex: 0
      - SubnetId:
          Ref: Subnet
        DeviceIndex: 1
```