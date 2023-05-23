# Amazon OpenSearch controls<a name="opensearch-rules"></a>

**Topics**
+ [\[CT\.OPENSEARCH\.PR\.1\] Require an Elasticsearch domain to encrypt data at rest](#ct-opensearch-pr-1-description)
+ [\[CT\.OPENSEARCH\.PR\.2\] Require an Elasticsearch domain to be created in a user\-specified Amazon VPC](#ct-opensearch-pr-2-description)
+ [\[CT\.OPENSEARCH\.PR\.3\] Require an Elasticsearch domain to encrypt data sent between nodes](#ct-opensearch-pr-3-description)
+ [\[CT\.OPENSEARCH\.PR\.4\] Require an Elasticsearch domain to send error logs to Amazon CloudWatch Logs](#ct-opensearch-pr-4-description)
+ [\[CT\.OPENSEARCH\.PR\.5\] Require an Elasticsearch domain to send audit logs to Amazon CloudWatch Logs](#ct-opensearch-pr-5-description)
+ [\[CT\.OPENSEARCH\.PR\.6\] Require an Elasticsearch domain to have zone awareness and at least three data nodes](#ct-opensearch-pr-6-description)
+ [\[CT\.OPENSEARCH\.PR\.7\] Require an Elasticsearch domain to have at least three dedicated master nodes](#ct-opensearch-pr-7-description)
+ [\[CT\.OPENSEARCH\.PR\.8\] Require an Elasticsearch Service domain to use TLSv1\.2](#ct-opensearch-pr-8-description)
+ [\[CT\.OPENSEARCH\.PR\.9\] Require an Amazon OpenSearch Service domain to encrypt data at rest](#ct-opensearch-pr-9-description)
+ [\[CT\.OPENSEARCH\.PR\.10\] Require an Amazon OpenSearch Service domain to be created in a user\-specified Amazon VPC](#ct-opensearch-pr-10-description)
+ [\[CT\.OPENSEARCH\.PR\.11\] Require an Amazon OpenSearch Service domain to encrypt data sent between nodes](#ct-opensearch-pr-11-description)
+ [\[CT\.OPENSEARCH\.PR\.12\] Require an Amazon OpenSearch Service domain to send error logs to Amazon CloudWatch Logs](#ct-opensearch-pr-12-description)
+ [\[CT\.OPENSEARCH\.PR\.13\] Require an Amazon OpenSearch Service domain to send audit logs to Amazon CloudWatch Logs](#ct-opensearch-pr-13-description)
+ [\[CT\.OPENSEARCH\.PR\.14\] Require an Amazon OpenSearch Service domain to have zone awareness and at least three data nodes](#ct-opensearch-pr-14-description)
+ [\[CT\.OPENSEARCH\.PR\.15\] Require an Amazon OpenSearch Service domain to use fine\-grained access control](#ct-opensearch-pr-15-description)
+ [\[CT\.OPENSEARCH\.PR\.16\] Require an Amazon OpenSearch Service domain to use TLSv1\.2](#ct-opensearch-pr-16-description)

## \[CT\.OPENSEARCH\.PR\.1\] Require an Elasticsearch domain to encrypt data at rest<a name="ct-opensearch-pr-1-description"></a>

This control checks whether Elasticsearch domains have encryption\-at\-rest enabled\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Elasticsearch::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.1 rule specification](#ct-opensearch-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.1 rule specification](#ct-opensearch-pr-1-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.1 example templates](#ct-opensearch-pr-1-templates) 

**Explanation**

For an added layer of security for your sensitive data in OpenSearch, you should configure your OpenSearch to be encrypted at rest\. Elasticsearch domains offer encryption of data at rest\. The feature uses AWS KMS to store and manage your encryption keys\. To perform the encryption, it uses the Advanced Encryption Standard algorithm with 256\-bit keys \(AES\-256\)\.

### Remediation for rule failure<a name="ct-opensearch-pr-1-remediation"></a>

Within `EncryptionAtRestOptions`, set `Enabled` to `true`\.

The examples that follow show how to implement this remediation\.

#### Elasticsearch Domain \- Example<a name="ct-opensearch-pr-1-remediation-1"></a>

An Elasticsearch domain configured with encryption\-at\-rest enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticsearchDomain": {
        "Type": "AWS::Elasticsearch::Domain",
        "Properties": {
            "ElasticsearchVersion": 7.1,
            "ElasticsearchClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.elasticsearch"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "EncryptionAtRestOptions": {
                "Enabled": true
            }
        }
    }
}
```

**YAML example**

```
ElasticsearchDomain:
  Type: AWS::Elasticsearch::Domain
  Properties:
    ElasticsearchVersion: 7.1
    ElasticsearchClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.elasticsearch
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    EncryptionAtRestOptions:
      Enabled: true
```

### CT\.OPENSEARCH\.PR\.1 rule specification<a name="ct-opensearch-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elasticsearch_encrypted_at_rest_check
# 
# Description:
#   This control checks whether Elasticsearch domains have encryption-at-rest enabled.
# 
# Reports on:
#   AWS::Elasticsearch::Domain
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
#       And: The input document does not contain any Elasticsearch domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'EncryptionAtRestOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'EncryptionAtRestOptions' has been provided
#       And: 'Enabled' in 'EncryptionAtRestOptions' has not been provided or provided
#            and set to a value other than bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'EncryptionAtRestOptions' has been provided
#       And: 'Enabled' in 'EncryptionAtRestOptions' has been provided and set to bool(true)
#      Then: PASS

#
# Constants
#
let ELASTICSEARCH_DOMAIN_TYPE = "AWS::Elasticsearch::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elasticsearch_domains = Resources.*[ Type == %ELASTICSEARCH_DOMAIN_TYPE ]

#
# Primary Rules
#
rule elasticsearch_encrypted_at_rest_check when is_cfn_template(%INPUT_DOCUMENT)
                                                %elasticsearch_domains not empty {
    check(%elasticsearch_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.1]: Require an Elasticsearch domain to encrypt data at rest
            [FIX]: Within 'EncryptionAtRestOptions', set 'Enabled' to 'true'.
        >>
}

rule elasticsearch_encrypted_at_rest_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTICSEARCH_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTICSEARCH_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.1]: Require an Elasticsearch domain to encrypt data at rest
            [FIX]: Within 'EncryptionAtRestOptions', set 'Enabled' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(elasticsearch_domain) {
    %elasticsearch_domain {
        # Scenario 2
        EncryptionAtRestOptions exists
        EncryptionAtRestOptions is_struct

        EncryptionAtRestOptions {
            # Scenarios 3 and 4
            Enabled exists
            Enabled == true
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

### CT\.OPENSEARCH\.PR\.1 example templates<a name="ct-opensearch-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      EncryptionAtRestOptions:
        Enabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      EncryptionAtRestOptions:
        Enabled: false
```

## \[CT\.OPENSEARCH\.PR\.2\] Require an Elasticsearch domain to be created in a user\-specified Amazon VPC<a name="ct-opensearch-pr-2-description"></a>

This control checks whether Elasticsearch domains are configured with VPC option settings that specify a target Amazon VPC\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Elasticsearch::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.2 rule specification](#ct-opensearch-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.2 rule specification](#ct-opensearch-pr-2-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.2 example templates](#ct-opensearch-pr-2-templates) 

**Explanation**

Elasticsearch domains deployed within a VPC can communicate with VPC resources over the private AWS network, without the need to traverse the public internet\. This configuration increases the security posture by limiting access to the data in transit\. VPCs provide a number of network controls that help create secure access to Elasticsearch domains, including network ACLs and security groups\. Security Hub recommends that you migrate public Elasticsearch domains to VPCs to take advantage of these controls\.

### Remediation for rule failure<a name="ct-opensearch-pr-2-remediation"></a>

Within `VPCOptions`, set `SubnetIds` to a list with one or more Amazon EC2 subnet IDs\.

The examples that follow show how to implement this remediation\.

#### Elasticsearch Domain \- Example<a name="ct-opensearch-pr-2-remediation-1"></a>

An Elasticsearch domain configured to deploy within an Amazon VPC by means of VPC option settings\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticsearchDomain": {
        "Type": "AWS::Elasticsearch::Domain",
        "Properties": {
            "ElasticsearchVersion": 7.1,
            "ElasticsearchClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.elasticsearch"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "VPCOptions": {
                "SubnetIds": [
                    {
                        "Ref": "Subnet"
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
ElasticsearchDomain:
  Type: AWS::Elasticsearch::Domain
  Properties:
    ElasticsearchVersion: 7.1
    ElasticsearchClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.elasticsearch
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    VPCOptions:
      SubnetIds:
        - !Ref 'Subnet'
```

### CT\.OPENSEARCH\.PR\.2 rule specification<a name="ct-opensearch-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elasticsearch_in_vpc_only_check
# 
# Description:
#   This control checks whether Elasticsearch domains are configured with VPC option settings that specify a target Amazon VPC.
# 
# Reports on:
#   AWS::Elasticsearch::Domain
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
#       And: The input document does not contain any Elasticsearch domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'VPCOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'VPCOptions' has been provided
#       And: 'SubnetIds' in 'VPCOptions' has not been provided or has been provided
#            as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'VPCOptions' has been provided
#       And: 'SubnetIds' in 'VPCOptions' has been provided as a list with one or more values
#      Then: PASS

#
# Constants
#
let ELASTICSEARCH_DOMAIN_TYPE = "AWS::Elasticsearch::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elasticsearch_domains = Resources.*[ Type == %ELASTICSEARCH_DOMAIN_TYPE ]

#
# Primary Rules
#
rule elasticsearch_in_vpc_only_check when is_cfn_template(%INPUT_DOCUMENT)
                                          %elasticsearch_domains not empty {
    check(%elasticsearch_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.2]: Require an Elasticsearch domain to be created in a user-specified Amazon VPC
            [FIX]: Within 'VPCOptions', set 'SubnetIds' to a list with one or more Amazon EC2 subnet IDs.
        >>
}

rule elasticsearch_in_vpc_only_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTICSEARCH_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTICSEARCH_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.2]: Require an Elasticsearch domain to be created in a user-specified Amazon VPC
            [FIX]: Within 'VPCOptions', set 'SubnetIds' to a list with one or more Amazon EC2 subnet IDs.
        >>
}

#
# Parameterized Rules
#
rule check(elasticsearch_domain) {
    %elasticsearch_domain {
        # Scenario 2
        VPCOptions exists
        VPCOptions is_struct

        VPCOptions {
            # Scenarios 3 and 4
            SubnetIds exists
            SubnetIds is_list
            SubnetIds not empty
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

### CT\.OPENSEARCH\.PR\.2 example templates<a name="ct-opensearch-pr-2-templates"></a>

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
      CidrBlock: 10.0.0.0/16
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      VPCOptions:
        SubnetIds:
        - Ref: Subnet
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
```

## \[CT\.OPENSEARCH\.PR\.3\] Require an Elasticsearch domain to encrypt data sent between nodes<a name="ct-opensearch-pr-3-description"></a>

This control checks whether Elasticsearch domains have node\-to\-node encryption enabled\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Elasticsearch::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.3 rule specification](#ct-opensearch-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.3 rule specification](#ct-opensearch-pr-3-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.3 example templates](#ct-opensearch-pr-3-templates) 

**Explanation**

HTTPS \(TLS\) can help prevent potential attackers from eavesdropping on or manipulating network traffic using person\-in\-the\-middle, or similar, attacks\. Only encrypted connections over HTTPS \(TLS\) should be allowed\. Enabling node\-to\-node encryption for Elasticsearch domains ensures that intra\-cluster communications are encrypted in transit\.

**Usage considerations**  
A performance penalty may be associated with this configuration\. You should be aware of and test the performance trade\-offs before enabling this option\.

### Remediation for rule failure<a name="ct-opensearch-pr-3-remediation"></a>

Within `NodeToNodeEncryptionOptions`, set `Enabled` to `true`\.

The examples that follow show how to implement this remediation\.

#### Elasticsearch Domain \- Example<a name="ct-opensearch-pr-3-remediation-1"></a>

An Elasticsearch domain configured with node\-to\-node encryption enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticsearchDomain": {
        "Type": "AWS::Elasticsearch::Domain",
        "Properties": {
            "ElasticsearchVersion": 7.1,
            "ElasticsearchClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.elasticsearch"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "NodeToNodeEncryptionOptions": {
                "Enabled": true
            }
        }
    }
}
```

**YAML example**

```
ElasticsearchDomain:
  Type: AWS::Elasticsearch::Domain
  Properties:
    ElasticsearchVersion: 7.1
    ElasticsearchClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.elasticsearch
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    NodeToNodeEncryptionOptions:
      Enabled: true
```

### CT\.OPENSEARCH\.PR\.3 rule specification<a name="ct-opensearch-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elasticsearch_node_to_node_encryption_check
# 
# Description:
#   This control checks whether Elasticsearch domains have node-to-node encryption enabled.
# 
# Reports on:
#   AWS::Elasticsearch::Domain
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
#       And: The input document does not contain any Elasticsearch domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'NodeToNodeEncryptionOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'NodeToNodeEncryptionOptions' has been provided
#       And: 'Enabled' in 'NodeToNodeEncryptionOptions' has not been provided or has been provided
#            and set to a value other than bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'NodeToNodeEncryptionOptions' has been provided
#       And: 'Enabled' in 'NodeToNodeEncryptionOptions' has been provided and set to a
#            value of bool(true)
#      Then: PASS

#
# Constants
#
let ELASTICSEARCH_DOMAIN_TYPE = "AWS::Elasticsearch::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elasticsearch_domains = Resources.*[ Type == %ELASTICSEARCH_DOMAIN_TYPE ]

#
# Primary Rules
#
rule elasticsearch_node_to_node_encryption_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %elasticsearch_domains not empty {
    check(%elasticsearch_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.3]: Require an Elasticsearch domain to encrypt data sent between nodes
            [FIX]: Within 'NodeToNodeEncryptionOptions', set 'Enabled' to 'true'.
        >>
}

rule elasticsearch_node_to_node_encryption_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTICSEARCH_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTICSEARCH_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.3]: Require an Elasticsearch domain to encrypt data sent between nodes
            [FIX]: Within 'NodeToNodeEncryptionOptions', set 'Enabled' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(elasticsearch_domain) {
    %elasticsearch_domain {
        # Scenario 2
        NodeToNodeEncryptionOptions exists
        NodeToNodeEncryptionOptions is_struct

        NodeToNodeEncryptionOptions {
            # Scenarios 3 and 4
            Enabled exists
            Enabled == true
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

### CT\.OPENSEARCH\.PR\.3 example templates<a name="ct-opensearch-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions: {}
```

## \[CT\.OPENSEARCH\.PR\.4\] Require an Elasticsearch domain to send error logs to Amazon CloudWatch Logs<a name="ct-opensearch-pr-4-description"></a>

This control checks whether Elasticsearch domains are configured to send error logs to an Amazon CloudWatch Logs log group\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Elasticsearch::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.4 rule specification](#ct-opensearch-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.4 rule specification](#ct-opensearch-pr-4-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.4 example templates](#ct-opensearch-pr-4-templates) 

**Explanation**

Enable error logs \(ES\_APPLICATION\_LOGS\) for Elasticsearch domains and send those logs to CloudWatch Logs for retention and response\. Domain error logs can assist with security and access audits, and can help to diagnose availability issues\.

### Remediation for rule failure<a name="ct-opensearch-pr-4-remediation"></a>

Within `LogPublishingOptions`, provide an `ES_APPLICATION_LOGS` configuration, set `Enabled` to `true`, and set `CloudWatchLogsLogGroupArn` to the ARN of a valid Amazon CloudWatch Logs log group\.

The examples that follow show how to implement this remediation\.

#### Elasticsearch Domain \- Example<a name="ct-opensearch-pr-4-remediation-1"></a>

An Elasticsearch domain configured to send error logs to Amazon CloudWatch Logs\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticsearchDomain": {
        "Type": "AWS::Elasticsearch::Domain",
        "Properties": {
            "ElasticsearchVersion": 7.1,
            "ElasticsearchClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.elasticsearch"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "LogPublishingOptions": {
                "ES_APPLICATION_LOGS": {
                    "CloudWatchLogsLogGroupArn": {
                        "Fn::GetAtt": [
                            "LogGroup",
                            "Arn"
                        ]
                    },
                    "Enabled": true
                }
            }
        }
    }
}
```

**YAML example**

```
ElasticsearchDomain:
  Type: AWS::Elasticsearch::Domain
  Properties:
    ElasticsearchVersion: 7.1
    ElasticsearchClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.elasticsearch
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    LogPublishingOptions:
      ES_APPLICATION_LOGS:
        CloudWatchLogsLogGroupArn: !GetAtt 'LogGroup.Arn'
        Enabled: true
```

### CT\.OPENSEARCH\.PR\.4 rule specification<a name="ct-opensearch-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elasticsearch_application_logging_enabled_check
# 
# Description:
#   This control checks whether Elasticsearch domains are configured to send error logs to an Amazon CloudWatch Logs log group.
# 
# Reports on:
#   AWS::Elasticsearch::Domain
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
#       And: The input document does not contain any Elasticsearch domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been provided
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'ES_APPLICATION_LOGS' has not been provided or provided and set to
#            a value other than bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'ES_APPLICATION_LOGS' has not been provided or provided
#            as an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'ES_APPLICATION_LOGS' has been provided and set to bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'ES_APPLICATION_LOGS' has not been provided or provided
#            as an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'ES_APPLICATION_LOGS' has not been provided or provided and set to
#            a value other than bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'ES_APPLICATION_LOGS' has been provided as a non-empty string
#            or valid local reference
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'ES_APPLICATION_LOGS' has been provided and set to bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'ES_APPLICATION_LOGS' has been provided as a non-empty string
#            or valid local reference
#      Then: PASS

#
# Constants
#
let ELASTICSEARCH_DOMAIN_TYPE = "AWS::Elasticsearch::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elasticsearch_domains = Resources.*[ Type == %ELASTICSEARCH_DOMAIN_TYPE ]

#
# Primary Rules
#
rule elasticsearch_application_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                          %elasticsearch_domains not empty {
    check(%elasticsearch_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.4]: Require an Elasticsearch domain to send error logs to Amazon CloudWatch Logs
            [FIX]: Within 'LogPublishingOptions', provide an 'ES_APPLICATION_LOGS' configuration, set 'Enabled' to 'true', and set 'CloudWatchLogsLogGroupArn' to the ARN of a valid Amazon CloudWatch Logs log group.
        >>
}

rule elasticsearch_application_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTICSEARCH_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTICSEARCH_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.4]: Require an Elasticsearch domain to send error logs to Amazon CloudWatch Logs
            [FIX]: Within 'LogPublishingOptions', provide an 'ES_APPLICATION_LOGS' configuration, set 'Enabled' to 'true', and set 'CloudWatchLogsLogGroupArn' to the ARN of a valid Amazon CloudWatch Logs log group.
        >>
}

#
# Parameterized Rules
#
rule check(elasticsearch_domain) {
    %elasticsearch_domain {
        # Scenario 2
        LogPublishingOptions exists
        LogPublishingOptions is_struct

        LogPublishingOptions {
            # Scenario 3
            ES_APPLICATION_LOGS exists
            ES_APPLICATION_LOGS is_struct

            ES_APPLICATION_LOGS {
                # Scenarios 4, 5, 6 and 7
                Enabled exists
                Enabled == true

                CloudWatchLogsLogGroupArn exists
                check_is_string_and_not_empty(CloudWatchLogsLogGroupArn) or
                check_local_references(%INPUT_DOCUMENT, CloudWatchLogsLogGroupArn, "AWS::Logs::LogGroup")
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

### CT\.OPENSEARCH\.PR\.4 example templates<a name="ct-opensearch-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    DependsOn: LogGroupPolicy
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      LogPublishingOptions:
        ES_APPLICATION_LOGS:
          CloudWatchLogsLogGroupArn:
            Fn::GetAtt:
            - LogGroup
            - Arn
          Enabled: true
  LogGroup:
    Type: AWS::Logs::LogGroup
  LogGroupPolicy:
    Type: AWS::Logs::ResourcePolicy
    Properties:
      PolicyName:
        Fn::Sub: ${AWS::StackName}-AllowES
      PolicyDocument:
        Fn::Sub:
        - '{"Version": "2012-10-17","Statement":[{"Effect":"Allow","Principal": {"Service": ["es.amazonaws.com"]},"Action":["logs:PutLogEvents","logs:CreateLogStream"],"Resource":"${LogGroupArn}","Condition":{"StringEquals":{ "aws:SourceAccount": "${AWS::AccountId}"}}}]}'
        - LogGroupArn:
            Fn::GetAtt: [ LogGroup, Arn ]
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      LogPublishingOptions:
        ES_APPLICATION_LOGS:
          Enabled: false
```

## \[CT\.OPENSEARCH\.PR\.5\] Require an Elasticsearch domain to send audit logs to Amazon CloudWatch Logs<a name="ct-opensearch-pr-5-description"></a>

This control checks whether Elasticsearch domains are configured to send audit logs to an Amazon CloudWatch Logs log group\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Elasticsearch::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.5 rule specification](#ct-opensearch-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.5 rule specification](#ct-opensearch-pr-5-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.5 example templates](#ct-opensearch-pr-5-templates) 

**Explanation**

Audit logs are highly customizable\. They allow you to track user activity on your Elasticsearch clusters, including authentication successes and failures, requests to OpenSearch, index changes, and incoming search queries\.

**Usage considerations**  
This control requires that Elasticsearch domains must have advanced security options configured\.
To enable advanced security options on an Elasticsearch domain through the `AdvancedSecurityOptions` property, you must enable encryption of data at rest \(by means of `EncryptionAtRestOptions`\), node\-to\-node encryption \(by means of `NodeToNodeEncryptionOptions`\), and enforce HTTPS connections \(by means of `DomainEndpointOptions`\)\.

### Remediation for rule failure<a name="ct-opensearch-pr-5-remediation"></a>

Within `LogPublishingOptions`, provide an `AUDIT_LOGS` configuration, set `Enabled` to `true`, and set `CloudWatchLogsLogGroupArn` to the ARN of a valid Amazon CloudWatch Logs log group\.

The examples that follow show how to implement this remediation\.

#### Elasticsearch Domain \- Example<a name="ct-opensearch-pr-5-remediation-1"></a>

An Elasticsearch domain configured to send audit logs to Amazon CloudWatch Logs\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticsearchDomain": {
        "Type": "AWS::Elasticsearch::Domain",
        "Properties": {
            "ElasticsearchVersion": 7.1,
            "ElasticsearchClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.elasticsearch"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "NodeToNodeEncryptionOptions": {
                "Enabled": true
            },
            "EncryptionAtRestOptions": {
                "Enabled": true
            },
            "DomainEndpointOptions": {
                "EnforceHTTPS": true
            },
            "AdvancedSecurityOptions": {
                "Enabled": true,
                "InternalUserDatabaseEnabled": false,
                "MasterUserOptions": {
                    "MasterUserARN": {
                        "Fn::GetAtt": [
                            "IAMRole",
                            "Arn"
                        ]
                    }
                }
            },
            "LogPublishingOptions": {
                "AUDIT_LOGS": {
                    "CloudWatchLogsLogGroupArn": {
                        "Fn::GetAtt": [
                            "LogGroup",
                            "Arn"
                        ]
                    },
                    "Enabled": true
                }
            }
        }
    }
}
```

**YAML example**

```
ElasticsearchDomain:
  Type: AWS::Elasticsearch::Domain
  Properties:
    ElasticsearchVersion: 7.1
    ElasticsearchClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.elasticsearch
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    NodeToNodeEncryptionOptions:
      Enabled: true
    EncryptionAtRestOptions:
      Enabled: true
    DomainEndpointOptions:
      EnforceHTTPS: true
    AdvancedSecurityOptions:
      Enabled: true
      InternalUserDatabaseEnabled: false
      MasterUserOptions:
        MasterUserARN: !GetAtt 'IAMRole.Arn'
    LogPublishingOptions:
      AUDIT_LOGS:
        CloudWatchLogsLogGroupArn: !GetAtt 'LogGroup.Arn'
        Enabled: true
```

### CT\.OPENSEARCH\.PR\.5 rule specification<a name="ct-opensearch-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elasticsearch_audit_logging_enabled_check
# 
# Description:
#   This control checks whether Elasticsearch domains are configured to send audit logs to an Amazon CloudWatch Logs log group.
# 
# Reports on:
#   AWS::Elasticsearch::Domain
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
#       And: The input document does not contain any Elasticsearch domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been provided
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'AUDIT_LOGS' has not been provided or provided and set to
#            a value other than bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'AUDIT_LOGS' has not been provided or provided
#            as an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'AUDIT_LOGS' has been provided and set to bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'AUDIT_LOGS' has not been provided or provided
#            as an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'AUDIT_LOGS' has not been provided or provided and set to
#            a value other than bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'AUDIT_LOGS' has been provided as a non-empty string
#            or valid local reference
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'AUDIT_LOGS' has been provided and set to bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'AUDIT_LOGS' has been provided as a non-empty string
#            or valid local reference
#      Then: PASS

#
# Constants
#
let ELASTICSEARCH_DOMAIN_TYPE = "AWS::Elasticsearch::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elasticsearch_domains = Resources.*[ Type == %ELASTICSEARCH_DOMAIN_TYPE ]

#
# Primary Rules
#
rule elasticsearch_audit_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                    %elasticsearch_domains not empty {
    check(%elasticsearch_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.5]: Require an Elasticsearch domain to send audit logs to Amazon CloudWatch Logs
            [FIX]: Within 'LogPublishingOptions', provide an 'AUDIT_LOGS' configuration, set 'Enabled' to 'true', and set 'CloudWatchLogsLogGroupArn' to the ARN of a valid Amazon CloudWatch Logs log group.
        >>
}

rule elasticsearch_audit_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTICSEARCH_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTICSEARCH_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.5]: Require an Elasticsearch domain to send audit logs to Amazon CloudWatch Logs
            [FIX]: Within 'LogPublishingOptions', provide an 'AUDIT_LOGS' configuration, set 'Enabled' to 'true', and set 'CloudWatchLogsLogGroupArn' to the ARN of a valid Amazon CloudWatch Logs log group.
        >>
}

#
# Parameterized Rules
#
rule check(elasticsearch_domain) {
    %elasticsearch_domain {
        # Scenario 2
        LogPublishingOptions exists
        LogPublishingOptions is_struct

        LogPublishingOptions {
            # Scenario 3
            AUDIT_LOGS exists
            AUDIT_LOGS is_struct

            AUDIT_LOGS {
                # Scenarios 4, 5, 6 and 7
                Enabled exists
                Enabled == true

                CloudWatchLogsLogGroupArn exists
                check_is_string_and_not_empty(CloudWatchLogsLogGroupArn) or
                check_local_references(%INPUT_DOCUMENT, CloudWatchLogsLogGroupArn, "AWS::Logs::LogGroup")
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

### CT\.OPENSEARCH\.PR\.5 example templates<a name="ct-opensearch-pr-5-templates"></a>

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
          Action: sts:AssumeRole
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    DependsOn: LogGroupPolicy
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
      EncryptionAtRestOptions:
        Enabled: true
      DomainEndpointOptions:
        EnforceHTTPS: true
      AdvancedSecurityOptions:
        Enabled: true
        InternalUserDatabaseEnabled: false
        MasterUserOptions:
          MasterUserARN:
            Fn::GetAtt:
            - IAMRole
            - Arn
      LogPublishingOptions:
        AUDIT_LOGS:
          CloudWatchLogsLogGroupArn:
            Fn::GetAtt:
            - LogGroup
            - Arn
          Enabled: true
  LogGroup:
    Type: AWS::Logs::LogGroup
  LogGroupPolicy:
    Type: AWS::Logs::ResourcePolicy
    Properties:
      PolicyName: AllowES
      PolicyDocument:
        Fn::Sub:
        - '{"Version": "2012-10-17","Statement":[{"Effect":"Allow","Principal": {"Service": ["es.amazonaws.com"]},"Action":["logs:PutLogEvents","logs:CreateLogStream"],"Resource":"${LogGroupArn}","Condition":{"StringEquals":{ "aws:SourceAccount": "${AWS::AccountId}"}}}]}'
        - LogGroupArn:
            Fn::GetAtt: [ LogGroup, Arn ]
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
          Action: sts:AssumeRole
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
      EncryptionAtRestOptions:
        Enabled: true
      DomainEndpointOptions:
        EnforceHTTPS: true
      AdvancedSecurityOptions:
        Enabled: true
        InternalUserDatabaseEnabled: false
        MasterUserOptions:
          MasterUserARN:
            Fn::GetAtt:
            - IAMRole
            - Arn
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
          Action: sts:AssumeRole
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
      EncryptionAtRestOptions:
        Enabled: true
      DomainEndpointOptions:
        EnforceHTTPS: true
      AdvancedSecurityOptions:
        Enabled: true
        InternalUserDatabaseEnabled: false
        MasterUserOptions:
          MasterUserARN:
            Fn::GetAtt:
            - IAMRole
            - Arn
      LogPublishingOptions:
        AUDIT_LOGS:
          Enabled: false
```

## \[CT\.OPENSEARCH\.PR\.6\] Require an Elasticsearch domain to have zone awareness and at least three data nodes<a name="ct-opensearch-pr-6-description"></a>

This control checks whether ElasticSearch domains are configured with at least three data nodes and zone awareness enabled\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Elasticsearch::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.6 rule specification](#ct-opensearch-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.6 rule specification](#ct-opensearch-pr-6-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.6 example templates](#ct-opensearch-pr-6-templates) 

**Explanation**

An Elasticsearch domain requires at least three data nodes for high availability and fault\-tolerance\. Deploying an Elasticsearch domain with at least three data nodes ensures that cluster operations can continue if a node fails\.

### Remediation for rule failure<a name="ct-opensearch-pr-6-remediation"></a>

Within `ElasticsearchClusterConfig`, set `ZoneAwarenessEnabled` to `true`, and set `InstanceCount` to an integer value greater than or equal to three\.

The examples that follow show how to implement this remediation\.

#### Elasticsearch Domain \- Example<a name="ct-opensearch-pr-6-remediation-1"></a>

An Elasticsearch domain configured with three data nodes and zone awareness enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticsearchDomain": {
        "Type": "AWS::Elasticsearch::Domain",
        "Properties": {
            "ElasticsearchVersion": 7.1,
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "ElasticsearchClusterConfig": {
                "InstanceType": "t3.small.elasticsearch",
                "InstanceCount": 3,
                "ZoneAwarenessEnabled": true,
                "ZoneAwarenessConfig": {
                    "AvailabilityZoneCount": 3
                }
            }
        }
    }
}
```

**YAML example**

```
ElasticsearchDomain:
  Type: AWS::Elasticsearch::Domain
  Properties:
    ElasticsearchVersion: 7.1
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    ElasticsearchClusterConfig:
      InstanceType: t3.small.elasticsearch
      InstanceCount: 3
      ZoneAwarenessEnabled: true
      ZoneAwarenessConfig:
        AvailabilityZoneCount: 3
```

### CT\.OPENSEARCH\.PR\.6 rule specification<a name="ct-opensearch-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elasticsearch_data_node_fault_tolerance_check
# 
# Description:
#   This control checks whether Elasticsearch domains are configured with at least three data nodes and zone awareness enabled.
# 
# Reports on:
#   AWS::Elasticsearch::Domain
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
#       And: The input document does not contain any Elasticsearch domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has been provided
#       And: 'ZoneAwarenessEnabled' in 'ElasticsearchClusterConfig' has not been provided
#             or provided and set to a value other than bool(true)
#       And: 'InstanceCount' in 'ElasticsearchClusterConfig' has not been provided or
#             provided and set to an integer value less than three (< 3)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has been provided
#       And: 'ZoneAwarenessEnabled' in 'ElasticsearchClusterConfig' has been provided
#             and set to bool(true)
#       And: 'InstanceCount' in 'ElasticsearchClusterConfig' has not been provided or
#             provided and set to an integer value less than three (< 3)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has been provided
#       And: 'ZoneAwarenessEnabled' in 'ElasticsearchClusterConfig' has not been provided
#             or provided and set to a value other than bool(true)
#       And: 'InstanceCount' in 'ElasticsearchClusterConfig' has been provided and set to
#             an integer value greater than or equal to three (>= 3)
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has been provided
#       And: 'ZoneAwarenessEnabled' in 'ElasticsearchClusterConfig' has been provided
#             and set to bool(true)
#       And: 'InstanceCount' in 'ElasticsearchClusterConfig' has been provided and set to
#             an integer value greater than or equal to three (>= 3)
#      Then: PASS

#
# Constants
#
let ELASTICSEARCH_DOMAIN_TYPE = "AWS::Elasticsearch::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elasticsearch_domains = Resources.*[ Type == %ELASTICSEARCH_DOMAIN_TYPE ]

#
# Primary Rules
#
rule elasticsearch_data_node_fault_tolerance_check when is_cfn_template(%INPUT_DOCUMENT)
                                                        %elasticsearch_domains not empty {
    check(%elasticsearch_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.6]: Require an Elasticsearch domain to have zone awareness and at least three data nodes
            [FIX]: Within 'ElasticsearchClusterConfig', set 'ZoneAwarenessEnabled' to 'true', and set 'InstanceCount' to an integer value greater than or equal to three.
        >>
}

rule elasticsearch_data_node_fault_tolerance_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTICSEARCH_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTICSEARCH_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.6]: Require an Elasticsearch domain to have zone awareness and at least three data nodes
            [FIX]: Within 'ElasticsearchClusterConfig', set 'ZoneAwarenessEnabled' to 'true', and set 'InstanceCount' to an integer value greater than or equal to three.
        >>
}

#
# Parameterized Rules
#
rule check(elasticsearch_domain) {
    %elasticsearch_domain {
        # Scenario 2
        ElasticsearchClusterConfig exists
        ElasticsearchClusterConfig is_struct

        ElasticsearchClusterConfig {
            # Scenario 3, 4, 5 and 6
            ZoneAwarenessEnabled exists
            ZoneAwarenessEnabled == true

            InstanceCount exists
            InstanceCount >= 3
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

### CT\.OPENSEARCH\.PR\.6 example templates<a name="ct-opensearch-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      ElasticsearchClusterConfig:
        InstanceType: t3.small.elasticsearch
        InstanceCount: 3
        ZoneAwarenessEnabled: true
        ZoneAwarenessConfig:
          AvailabilityZoneCount: 3
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      ElasticsearchClusterConfig:
        InstanceType: t3.small.elasticsearch
        ZoneAwarenessEnabled: false
        InstanceCount: 2
```

## \[CT\.OPENSEARCH\.PR\.7\] Require an Elasticsearch domain to have at least three dedicated master nodes<a name="ct-opensearch-pr-7-description"></a>

This control checks whether Elasticsearch domains are configured with at least three dedicated master nodes\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Elasticsearch::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.7 rule specification](#ct-opensearch-pr-7-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.7 rule specification](#ct-opensearch-pr-7-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.7 example templates](#ct-opensearch-pr-7-templates) 

**Explanation**

An Elasticsearch domain requires at least three dedicated master nodes for high availability and fault\-tolerance\. Dedicated master node resources can be strained during data node blue/green deployments, because additional nodes must be managed\. Deploying an Elasticsearch domain with at least three dedicated master nodes ensures that sufficient master node resource capacity exists, and that cluster operations can continue if a node fails\.

### Remediation for rule failure<a name="ct-opensearch-pr-7-remediation"></a>

Within `ElasticsearchClusterConfig`, set `DedicatedMasterEnabled` to `true`, and set `DedicatedMasterCount` to an integer value greater than or equal to three, or omit the `DedicatedMasterCount` property to adopt the default value of three\.

The examples that follow show how to implement this remediation\.

#### Elasticsearch Domain \- Example One<a name="ct-opensearch-pr-7-remediation-1"></a>

An Elasticsearch domain configured with three dedicated master nodes by means of the `DedicatedMasterCount` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticsearchDomain": {
        "Type": "AWS::Elasticsearch::Domain",
        "Properties": {
            "ElasticsearchVersion": 7.1,
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "ElasticsearchClusterConfig": {
                "InstanceType": "t3.small.elasticsearch",
                "DedicatedMasterEnabled": true,
                "DedicatedMasterCount": 3
            }
        }
    }
}
```

**YAML example**

```
ElasticsearchDomain:
  Type: AWS::Elasticsearch::Domain
  Properties:
    ElasticsearchVersion: 7.1
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    ElasticsearchClusterConfig:
      InstanceType: t3.small.elasticsearch
      DedicatedMasterEnabled: true
      DedicatedMasterCount: 3
```

The examples that follow show how to implement this remediation\.

#### Elasticsearch Domain \- Example Two<a name="ct-opensearch-pr-7-remediation-2"></a>

An Elasticsearch domain configured with three dedicated master nodes by means of the AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticsearchDomain": {
        "Type": "AWS::Elasticsearch::Domain",
        "Properties": {
            "ElasticsearchVersion": 7.1,
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "ElasticsearchClusterConfig": {
                "InstanceType": "t3.small.elasticsearch",
                "DedicatedMasterEnabled": true
            }
        }
    }
}
```

**YAML example**

```
ElasticsearchDomain:
  Type: AWS::Elasticsearch::Domain
  Properties:
    ElasticsearchVersion: 7.1
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    ElasticsearchClusterConfig:
      InstanceType: t3.small.elasticsearch
      DedicatedMasterEnabled: true
```

### CT\.OPENSEARCH\.PR\.7 rule specification<a name="ct-opensearch-pr-7-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elasticsearch_primary_node_fault_tolerance_check
# 
# Description:
#   This control checks whether Elasticsearch domains are configured with at least three dedicated master nodes.
# 
# Reports on:
#   AWS::Elasticsearch::Domain
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
#       And: The input document does not contain any Elasticsearch domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has been provided
#       And: 'DedicatedMasterEnabled' in 'ElasticsearchClusterConfig' has not been provided
#             or provided and set to a value other than bool(true)
#       And: 'DedicatedMasterCount' in 'ElasticsearchClusterConfig' has not been provided or
#             provided and set to an integer value less than three (< 3)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has been provided
#       And: 'DedicatedMasterEnabled' in 'ElasticsearchClusterConfig' has not been provided
#             or provided and set to a value other than bool(true)
#       And: 'DedicatedMasterCount' in 'ElasticsearchClusterConfig' has been provided and set to
#             an integer value greater than or equal to three (>= 3)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has been provided
#       And: 'DedicatedMasterEnabled' in 'ElasticsearchClusterConfig' has been provided
#             and set to bool(true)
#       And: 'DedicatedMasterCount' in 'ElasticsearchClusterConfig' has been provided and set
#            to an integer value less than three (< 3)
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has been provided
#       And: 'DedicatedMasterEnabled' in 'ElasticsearchClusterConfig' has been provided
#             and set to bool(true)
#       And: 'DedicatedMasterCount' in 'ElasticsearchClusterConfig' has not been provided
#      Then: PASS
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'ElasticsearchClusterConfig' has been provided
#       And: 'DedicatedMasterEnabled' in 'ElasticsearchClusterConfig' has been provided
#             and set to bool(true)
#       And: 'DedicatedMasterCount' in 'ElasticsearchClusterConfig' has been provided and set to
#             an integer value greater than or equal to three (>= 3)
#      Then: PASS

#
# Constants
#
let ELASTICSEARCH_DOMAIN_TYPE = "AWS::Elasticsearch::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elasticsearch_domains = Resources.*[ Type == %ELASTICSEARCH_DOMAIN_TYPE ]

#
# Primary Rules
#
rule elasticsearch_primary_node_fault_tolerance_check when is_cfn_template(%INPUT_DOCUMENT)
                                                           %elasticsearch_domains not empty {
    check(%elasticsearch_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.7]: Require an Elasticsearch domain to have at least three dedicated master nodes
            [FIX]: Within 'ElasticsearchClusterConfig', set 'DedicatedMasterEnabled' to 'true', and set 'DedicatedMasterCount' to an integer value greater than or equal to three, or omit the 'DedicatedMasterCount' property to adopt the default value of three.
        >>
}

rule elasticsearch_primary_node_fault_tolerance_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTICSEARCH_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTICSEARCH_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.7]: Require an Elasticsearch domain to have at least three dedicated master nodes
            [FIX]: Within 'ElasticsearchClusterConfig', set 'DedicatedMasterEnabled' to 'true', and set 'DedicatedMasterCount' to an integer value greater than or equal to three, or omit the 'DedicatedMasterCount' property to adopt the default value of three.
        >>
}

#
# Parameterized Rules
#
rule check(elasticsearch_domain) {
    %elasticsearch_domain {
        # Scenario 2
        ElasticsearchClusterConfig exists
        ElasticsearchClusterConfig is_struct

        ElasticsearchClusterConfig {
            # Scenario 3, 4, 5 and 6
            DedicatedMasterEnabled exists
            DedicatedMasterEnabled == true

            DedicatedMasterCount not exists or
            DedicatedMasterCount >= 3
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

### CT\.OPENSEARCH\.PR\.7 example templates<a name="ct-opensearch-pr-7-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceType: t3.small.elasticsearch
        DedicatedMasterEnabled: true
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
```

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceType: t3.small.elasticsearch
        DedicatedMasterEnabled: true
        DedicatedMasterCount: 3
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceType: t3.small.elasticsearch
        DedicatedMasterEnabled: false
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
```

## \[CT\.OPENSEARCH\.PR\.8\] Require an Elasticsearch Service domain to use TLSv1\.2<a name="ct-opensearch-pr-8-description"></a>

This control checks whether Elasticsearch Service domains are configured to require HTTPS with a minimum TLS version of TLSv1\.2\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Elasticsearch::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.8 rule specification](#ct-opensearch-pr-8-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.8 rule specification](#ct-opensearch-pr-8-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.8 example templates](#ct-opensearch-pr-8-templates) 

**Explanation**

HTTPS \(TLS\) can help prevent potential attackers from using person\-in\-the\-middle, or similar attacks, to eavesdrop on or manipulate network traffic\. Only encrypted connections over HTTPS \(TLS\) should be allowed\. Encrypting data in transit can affect performance\. You should test your application with this feature to understand the performance profile and the effects of TLS\. TLS 1\.2 provides several security enhancements over previous versions of TLS\.

### Remediation for rule failure<a name="ct-opensearch-pr-8-remediation"></a>

Within `DomainEndpointOptions`, set `EnforceHTTPS` to `true` and set `TLSSecurityPolicy` to `Policy-Min-TLS-1-2-2019-07`\.

The examples that follow show how to implement this remediation\.

#### Elasticsearch Domain \- Example<a name="ct-opensearch-pr-8-remediation-1"></a>

An Elasticsearch domain configured to require that all traffic to the domain arrives over HTTPS with a minimum TLS version of TLSv1\.2\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticsearchDomain": {
        "Type": "AWS::Elasticsearch::Domain",
        "Properties": {
            "ElasticsearchVersion": 7.1,
            "ElasticsearchClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.elasticsearch"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "DomainEndpointOptions": {
                "EnforceHTTPS": true,
                "TLSSecurityPolicy": "Policy-Min-TLS-1-2-2019-07"
            }
        }
    }
}
```

**YAML example**

```
ElasticsearchDomain:
  Type: AWS::Elasticsearch::Domain
  Properties:
    ElasticsearchVersion: 7.1
    ElasticsearchClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.elasticsearch
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    DomainEndpointOptions:
      EnforceHTTPS: true
      TLSSecurityPolicy: Policy-Min-TLS-1-2-2019-07
```

### CT\.OPENSEARCH\.PR\.8 rule specification<a name="ct-opensearch-pr-8-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elasticsearch_https_required_check
# 
# Description:
#   This control checks whether Elasticsearch domains are configured to require HTTPS with a minimum TLS version of TLSv1.2.
# 
# Reports on:
#   AWS::Elasticsearch::Domain
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
#       And: The input document does not contain any Elasticsearch domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'DomainEndpointOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'DomainEndpointOptions' has been provided
#       And: 'EnforceHTTPS' in 'DomainEndpointOptions' has not been provided or
#            has been provided and set to a value other than bool(true)
#       And: 'TLSSecurityPolicy' in 'DomainEndpointOptions' has not been provided or
#            has been provided and set to a value other than 'Policy-Min-TLS-1-2-2019-07'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'DomainEndpointOptions' has been provided
#       And: 'EnforceHTTPS' in 'DomainEndpointOptions' has been provided and set to bool(true)
#       And: 'TLSSecurityPolicy' in 'DomainEndpointOptions' has not been provided or
#            has been provided and set to a value other than 'Policy-Min-TLS-1-2-2019-07'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'DomainEndpointOptions' has been provided
#       And: 'EnforceHTTPS' in 'DomainEndpointOptions' has not been provided or
#            has been provided and set to a value other than bool(true)
#       And: 'TLSSecurityPolicy' in 'DomainEndpointOptions' has been provided and set
#            to 'Policy-Min-TLS-1-2-2019-07'
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elasticsearch domain resource
#       And: 'DomainEndpointOptions' has been provided
#       And: 'EnforceHTTPS' in 'DomainEndpointOptions' has been provided and set to bool(true)
#       And: 'TLSSecurityPolicy' in 'DomainEndpointOptions' has been provided and set
#            to 'Policy-Min-TLS-1-2-2019-07'
#      Then: PASS

#
# Constants
#
let ELASTICSEARCH_DOMAIN_TYPE = "AWS::Elasticsearch::Domain"
let ALLOWED_TLS_POLICIES = [ "Policy-Min-TLS-1-2-2019-07" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let elasticsearch_domains = Resources.*[ Type == %ELASTICSEARCH_DOMAIN_TYPE ]

#
# Primary Rules
#
rule elasticsearch_https_required_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %elasticsearch_domains not empty {
    check(%elasticsearch_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.8]: Require an Elasticsearch Service domain to use TLSv1.2
            [FIX]: Within 'DomainEndpointOptions', set 'EnforceHTTPS' to 'true' and set 'TLSSecurityPolicy' to 'Policy-Min-TLS-1-2-2019-07'.
        >>
}

rule elasticsearch_https_required_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTICSEARCH_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTICSEARCH_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.8]: Require an Elasticsearch Service domain to use TLSv1.2
            [FIX]: Within 'DomainEndpointOptions', set 'EnforceHTTPS' to 'true' and set 'TLSSecurityPolicy' to 'Policy-Min-TLS-1-2-2019-07'.
        >>
}

#
# Parameterized Rules
#
rule check(elasticsearch_domain) {
    %elasticsearch_domain {
        # Scenario 2
        DomainEndpointOptions exists
        DomainEndpointOptions is_struct

        DomainEndpointOptions {
            # Scenarios 3, 4, 5 and 6
            EnforceHTTPS exists
            EnforceHTTPS == true

            TLSSecurityPolicy exists
            TLSSecurityPolicy in %ALLOWED_TLS_POLICIES
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

### CT\.OPENSEARCH\.PR\.8 example templates<a name="ct-opensearch-pr-8-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      DomainEndpointOptions:
        EnforceHTTPS: true
        TLSSecurityPolicy: Policy-Min-TLS-1-2-2019-07
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ElasticsearchDomain:
    Type: AWS::Elasticsearch::Domain
    Properties:
      ElasticsearchVersion: 7.1
      ElasticsearchClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.elasticsearch
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      DomainEndpointOptions:
        EnforceHTTPS: true
        TLSSecurityPolicy: Policy-Min-TLS-1-0-2019-07
```

## \[CT\.OPENSEARCH\.PR\.9\] Require an Amazon OpenSearch Service domain to encrypt data at rest<a name="ct-opensearch-pr-9-description"></a>

This control checks whether Amazon OpenSearch Service domains have encryption\-at\-rest enabled\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::OpenSearchService::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.9 rule specification](#ct-opensearch-pr-9-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.9 rule specification](#ct-opensearch-pr-9-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.9 example templates](#ct-opensearch-pr-9-templates) 

**Explanation**

For an added layer of security for sensitive data, you should configure your OpenSearch Service domain to be encrypted at rest\. When you configure encryption of data at rest, AWS KMS stores and manages your encryption keys\. To perform the encryption, AWS KMS uses the Advanced Encryption Standard algorithm with 256\-bit keys \(AES\-256\)\.

### Remediation for rule failure<a name="ct-opensearch-pr-9-remediation"></a>

Within `EncryptionAtRestOptions`, set `Enabled` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon OpenSearch Service Domain \- Example<a name="ct-opensearch-pr-9-remediation-1"></a>

An Amazon OpenSearch Service domain configured with encryption\-at\-rest enabled The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "OpenSearchServiceDomain": {
        "Type": "AWS::OpenSearchService::Domain",
        "Properties": {
            "EngineVersion": "OpenSearch_1.3",
            "ClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.search"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "EncryptionAtRestOptions": {
                "Enabled": true
            }
        }
    }
}
```

**YAML example**

```
OpenSearchServiceDomain:
  Type: AWS::OpenSearchService::Domain
  Properties:
    EngineVersion: OpenSearch_1.3
    ClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.search
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    EncryptionAtRestOptions:
      Enabled: true
```

### CT\.OPENSEARCH\.PR\.9 rule specification<a name="ct-opensearch-pr-9-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   opensearch_encrypted_at_rest_check
# 
# Description:
#   This control checks whether Amazon OpenSearch Service domains have encryption-at-rest enabled.
# 
# Reports on:
#   AWS::OpenSearchService::Domain
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
#       And: The input document does not contain any OpenSearch Service domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'EncryptionAtRestOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'EncryptionAtRestOptions' has been provided
#       And: In 'EncryptionAtRestOptions', 'Enabled' has not been provided or provided
#            and set to a value other than bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'EncryptionAtRestOptions' has been provided
#       And: In 'EncryptionAtRestOptions','Enabled' has been provided and set to bool(true)
#      Then: PASS

#
# Constants
#
let OPENSEARCH_SERVICE_DOMAIN_TYPE = "AWS::OpenSearchService::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let opensearch_service_domains = Resources.*[ Type == %OPENSEARCH_SERVICE_DOMAIN_TYPE ]

#
# Primary Rules
#
rule opensearch_encrypted_at_rest_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %opensearch_service_domains not empty {
    check(%opensearch_service_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.9]: Require an Amazon OpenSearch Service domain to encrypt data at rest
            [FIX]: Within 'EncryptionAtRestOptions', set 'Enabled' to 'true'.
        >>
}

rule opensearch_encrypted_at_rest_check when is_cfn_hook(%INPUT_DOCUMENT, %OPENSEARCH_SERVICE_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%OPENSEARCH_SERVICE_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.9]: Require an Amazon OpenSearch Service domain to encrypt data at rest
            [FIX]: Within 'EncryptionAtRestOptions', set 'Enabled' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(opensearch_service_domain) {
    %opensearch_service_domain {
        # Scenario 2
        EncryptionAtRestOptions exists
        EncryptionAtRestOptions is_struct

        EncryptionAtRestOptions {
            # Scenarios 3 and 4
            Enabled exists
            Enabled == true
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

### CT\.OPENSEARCH\.PR\.9 example templates<a name="ct-opensearch-pr-9-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      EncryptionAtRestOptions:
        Enabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      EncryptionAtRestOptions:
        Enabled: false
```

## \[CT\.OPENSEARCH\.PR\.10\] Require an Amazon OpenSearch Service domain to be created in a user\-specified Amazon VPC<a name="ct-opensearch-pr-10-description"></a>

This control checks whether Amazon OpenSearch Service domains are configured with VPC option settings that specify a target Amazon VPC\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::OpenSearchService::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.10 rule specification](#ct-opensearch-pr-10-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.10 rule specification](#ct-opensearch-pr-10-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.10 example templates](#ct-opensearch-pr-10-templates) 

**Explanation**

Ensure that OpenSearch domains are not attached to public subnets\. See `Resource-based policies` in the Amazon OpenSearch Service Developer Guide\. Also ensure that your VPC is configured according to the recommended best practices\. See `Security best practices for your VPC` in the Amazon VPC User Guide\.

OpenSearch domains deployed within a VPC can communicate with VPC resources over the private AWS network, without the need to traverse the public internet\. This configuration increases the security posture by limiting access to the data in transit\. VPCs provide a number of network controls to secure access to OpenSearch domains, including network ACL and security groups\. AWS Control Tower recommends that you migrate public OpenSearch domains to VPCs to take advantage of these controls\.

### Remediation for rule failure<a name="ct-opensearch-pr-10-remediation"></a>

Within `VPCOptions`, set `SubnetIds` to a list with one or more AAmazon EC2 subnet IDs\.

The examples that follow show how to implement this remediation\.

#### Amazon OpenSearch Service Domain \- Example<a name="ct-opensearch-pr-10-remediation-1"></a>

An Amazon OpenSearch Service domain configured to deploy within an Amazon VPC by means of VPC option settings\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "OpenSearchServiceDomain": {
        "Type": "AWS::OpenSearchService::Domain",
        "Properties": {
            "EngineVersion": "OpenSearch_1.3",
            "ClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.search"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "VPCOptions": {
                "SubnetIds": [
                    {
                        "Ref": "Subnet"
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
OpenSearchServiceDomain:
  Type: AWS::OpenSearchService::Domain
  Properties:
    EngineVersion: OpenSearch_1.3
    ClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.search
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    VPCOptions:
      SubnetIds:
        - !Ref 'Subnet'
```

### CT\.OPENSEARCH\.PR\.10 rule specification<a name="ct-opensearch-pr-10-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   opensearch_in_vpc_only_check
# 
# Description:
#   This control checks whether Amazon OpenSearch Service domains are configured with VPC option settings that specify a target Amazon VPC.
# 
# Reports on:
#   AWS::OpenSearchService::Domain
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
#       And: The input document does not contain any OpenSearch Service domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'VPCOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'VPCOptions' has been provided
#       And: 'SubnetIds' in 'VPCOptions' has not been provided or has been provided
#            as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'VPCOptions' has been provided
#       And: 'SubnetIds' in 'VPCOptions' has been provided as a list with one or more values
#      Then: PASS

#
# Constants
#
let OPENSEARCH_SERVICE_DOMAIN_TYPE = "AWS::OpenSearchService::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let opensearch_service_domains = Resources.*[ Type == %OPENSEARCH_SERVICE_DOMAIN_TYPE ]

#
# Primary Rules
#
rule opensearch_in_vpc_only_check when is_cfn_template(%INPUT_DOCUMENT)
                                       %opensearch_service_domains not empty {
    check(%opensearch_service_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.10]: Require an Amazon OpenSearch Service domain to be created in a user-specified Amazon VPC
            [FIX]: Within 'VPCOptions', set 'SubnetIds' to a list with one or more Amazon EC2 subnet IDs.
        >>
}

rule opensearch_in_vpc_only_check when is_cfn_hook(%INPUT_DOCUMENT, %OPENSEARCH_SERVICE_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%OPENSEARCH_SERVICE_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.10]: Require an Amazon OpenSearch Service domain to be created in a user-specified Amazon VPC
            [FIX]: Within 'VPCOptions', set 'SubnetIds' to a list with one or more Amazon EC2 subnet IDs.
        >>
}

#
# Parameterized Rules
#
rule check(opensearch_service_domain) {
    %opensearch_service_domain {
        # Scenario 2
        VPCOptions exists
        VPCOptions is_struct

        VPCOptions {
            # Scenarios 3 and 4
            SubnetIds exists
            SubnetIds is_list
            SubnetIds not empty
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

### CT\.OPENSEARCH\.PR\.10 example templates<a name="ct-opensearch-pr-10-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: 'AWS::EC2::VPC'
    Properties:
      CidrBlock: 10.0.0.0/16
  Subnet:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/16
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      VPCOptions:
        SubnetIds:
        - Ref: Subnet
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
```

## \[CT\.OPENSEARCH\.PR\.11\] Require an Amazon OpenSearch Service domain to encrypt data sent between nodes<a name="ct-opensearch-pr-11-description"></a>

This control checks whether Amazon OpenSearch Service domains have node\-to\-node encryption enabled\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::OpenSearchService::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.11 rule specification](#ct-opensearch-pr-11-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.11 rule specification](#ct-opensearch-pr-11-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.11 example templates](#ct-opensearch-pr-11-templates) 

**Explanation**

HTTPS \(TLS\) can help prevent potential attackers from eavesdropping on or manipulating network traffic using person\-in\-the\-middle, or similar, attacks\. Only encrypted connections over HTTPS \(TLS\) should be allowed\. Enabling node\-to\-node encryption for OpenSearch domains ensures that intra\-cluster communications are encrypted in transit\.

**Usage considerations**  
A performance penalty may be associated with this configuration\. You should be aware of the performance trade\-offs and test them before enabling this option\.

### Remediation for rule failure<a name="ct-opensearch-pr-11-remediation"></a>

Within `NodeToNodeEncryptionOptions`, set `Enabled` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon OpenSearch Service Domain \- Example<a name="ct-opensearch-pr-11-remediation-1"></a>

An Amazon OpenSearch Service domain configured with node\-to\-node encryption enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "OpenSearchServiceDomain": {
        "Type": "AWS::OpenSearchService::Domain",
        "Properties": {
            "EngineVersion": "OpenSearch_1.3",
            "ClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.search"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "NodeToNodeEncryptionOptions": {
                "Enabled": true
            }
        }
    }
}
```

**YAML example**

```
OpenSearchServiceDomain:
  Type: AWS::OpenSearchService::Domain
  Properties:
    EngineVersion: OpenSearch_1.3
    ClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.search
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    NodeToNodeEncryptionOptions:
      Enabled: true
```

### CT\.OPENSEARCH\.PR\.11 rule specification<a name="ct-opensearch-pr-11-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   opensearch_node_to_node_encryption_check
# 
# Description:
#   This control checks whether Amazon OpenSearch Service domains have node-to-node encryption enabled.
# 
# Reports on:
#   AWS::OpenSearchService::Domain
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
#       And: The input document does not contain any OpenSearch Service domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'NodeToNodeEncryptionOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'NodeToNodeEncryptionOptions' has been provided
#       And: In 'NodeToNodeEncryptionOptions', 'Enabled' has not been provided or has been provided and set to a
#            value other than bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'NodeToNodeEncryptionOptions' has been provided
#       And: In 'NodeToNodeEncryptionOptions', 'Enabled' has been provided and set to a
#            value of bool(true)
#      Then: PASS

#
# Constants
#
let OPENSEARCH_SERVICE_DOMAIN_TYPE = "AWS::OpenSearchService::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let opensearch_service_domains = Resources.*[ Type == %OPENSEARCH_SERVICE_DOMAIN_TYPE ]

#
# Primary Rules
#
rule opensearch_node_to_node_encryption_check when is_cfn_template(%INPUT_DOCUMENT)
                                                   %opensearch_service_domains not empty {
    check(%opensearch_service_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.11]: Require an Amazon OpenSearch Service domain to encrypt data sent between nodes
            [FIX]: Within 'NodeToNodeEncryptionOptions', set 'Enabled' to 'true'.
        >>
}

rule opensearch_node_to_node_encryption_check when is_cfn_hook(%INPUT_DOCUMENT, %OPENSEARCH_SERVICE_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%OPENSEARCH_SERVICE_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.11]: Require an Amazon OpenSearch Service domain to encrypt data sent between nodes
            [FIX]: Within 'NodeToNodeEncryptionOptions', set 'Enabled' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(opensearch_service_domain) {
    %opensearch_service_domain {
        # Scenario 2
        NodeToNodeEncryptionOptions exists
        NodeToNodeEncryptionOptions is_struct

        NodeToNodeEncryptionOptions {
            # Scenarios 3 and 4
            Enabled exists
            Enabled == true
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

### CT\.OPENSEARCH\.PR\.11 example templates<a name="ct-opensearch-pr-11-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions: {}
```

## \[CT\.OPENSEARCH\.PR\.12\] Require an Amazon OpenSearch Service domain to send error logs to Amazon CloudWatch Logs<a name="ct-opensearch-pr-12-description"></a>

This control checks whether Amazon OpenSearch Service domains are configured to send error logs to an Amazon CloudWatch Logs log group\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::OpenSearchService::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.12 rule specification](#ct-opensearch-pr-12-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.12 rule specification](#ct-opensearch-pr-12-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.12 example templates](#ct-opensearch-pr-12-templates) 

**Explanation**

Enable error logs \(ES\_APPLICATION\_LOGS\) for OpenSearch Service domains and send those logs to Amazon CloudWatch Logs for retention and response\. Domain error logs can assist with security and access audits, and can help to diagnose availability issues\.

### Remediation for rule failure<a name="ct-opensearch-pr-12-remediation"></a>

Within `LogPublishingOptions`, provide an `ES_APPLICATION_LOGS` configuration, set `Enabled` to `true`, and set `CloudWatchLogsLogGroupArn` to the ARN of a valid Amazon CloudWatch Logs log group\.

The examples that follow show how to implement this remediation\.

#### Amazon OpenSearch Service Domain \- Example<a name="ct-opensearch-pr-12-remediation-1"></a>

An Amazon OpenSearch Service domain configured to send error logs to Amazon CloudWatch Logs\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "OpenSearchServiceDomain": {
        "Type": "AWS::OpenSearchService::Domain",
        "Properties": {
            "EngineVersion": "OpenSearch_1.3",
            "ClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.search"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "LogPublishingOptions": {
                "ES_APPLICATION_LOGS": {
                    "CloudWatchLogsLogGroupArn": {
                        "Fn::GetAtt": [
                            "LogGroup",
                            "Arn"
                        ]
                    },
                    "Enabled": true
                }
            }
        }
    }
}
```

**YAML example**

```
OpenSearchServiceDomain:
  Type: AWS::OpenSearchService::Domain
  Properties:
    EngineVersion: OpenSearch_1.3
    ClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.search
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    LogPublishingOptions:
      ES_APPLICATION_LOGS:
        CloudWatchLogsLogGroupArn: !GetAtt 'LogGroup.Arn'
        Enabled: true
```

### CT\.OPENSEARCH\.PR\.12 rule specification<a name="ct-opensearch-pr-12-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   opensearch_application_logging_enabled_check
# 
# Description:
#   This control checks whether Amazon OpenSearch Service domains are configured to send error logs to an Amazon CloudWatch Logs log group.
# 
# Reports on:
#   AWS::OpenSearchService::Domain
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
#       And: The input document does not contain any OpenSearch Service domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'LogPublishingOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'LogPublishingOptions' has been provided
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'ES_APPLICATION_LOGS' has not been provided or provided and set to
#            a value other than bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'ES_APPLICATION_LOGS' has not been provided or provided
#            as an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service Domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'ES_APPLICATION_LOGS' has been provided and set to bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'ES_APPLICATION_LOGS' has not been provided or provided
#            as an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service Domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'ES_APPLICATION_LOGS' has not been provided or provided and set to
#            a value other than bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'ES_APPLICATION_LOGS' has been provided as a non-empty string
#            or valid local reference
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service Domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'ES_APPLICATION_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'ES_APPLICATION_LOGS' has been provided and set to bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'ES_APPLICATION_LOGS' has been provided as a non-empty string
#            or valid local reference
#      Then: PASS

#
# Constants
#
let OPENSEARCH_SERVICE_DOMAIN_TYPE = "AWS::OpenSearchService::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let opensearch_service_domains = Resources.*[ Type == %OPENSEARCH_SERVICE_DOMAIN_TYPE ]

#
# Primary Rules
#
rule opensearch_application_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                       %opensearch_service_domains not empty {
    check(%opensearch_service_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.12]: Require an Amazon OpenSearch Service domain to send error logs to Amazon CloudWatch Logs
            [FIX]: Within 'LogPublishingOptions', provide an 'ES_APPLICATION_LOGS' configuration, set 'Enabled' to 'true', and set 'CloudWatchLogsLogGroupArn' to the ARN of a valid Amazon CloudWatch Logs log group.
        >>
}

rule opensearch_application_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %OPENSEARCH_SERVICE_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%OPENSEARCH_SERVICE_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.12]: Require an Amazon OpenSearch Service domain to send error logs to Amazon CloudWatch Logs
            [FIX]: Within 'LogPublishingOptions', provide an 'ES_APPLICATION_LOGS' configuration, set 'Enabled' to 'true', and set 'CloudWatchLogsLogGroupArn' to the ARN of a valid Amazon CloudWatch Logs log group.
        >>
}

#
# Parameterized Rules
#
rule check(opensearch_service_domain) {
    %opensearch_service_domain {
        # Scenario 2
        LogPublishingOptions exists
        LogPublishingOptions is_struct

        LogPublishingOptions {
            # Scenario 3
            ES_APPLICATION_LOGS exists
            ES_APPLICATION_LOGS is_struct

            ES_APPLICATION_LOGS {
                # Scenarios 4, 5, 6 and 7
                Enabled exists
                Enabled == true

                CloudWatchLogsLogGroupArn exists
                check_is_string_and_not_empty(CloudWatchLogsLogGroupArn) or
                check_local_references(%INPUT_DOCUMENT, CloudWatchLogsLogGroupArn, "AWS::Logs::LogGroup")
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

### CT\.OPENSEARCH\.PR\.12 example templates<a name="ct-opensearch-pr-12-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    DependsOn: LogGroupPolicy
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      LogPublishingOptions:
        ES_APPLICATION_LOGS:
          CloudWatchLogsLogGroupArn:
            Fn::GetAtt:
            - LogGroup
            - Arn
          Enabled: true
  LogGroup:
    Type: AWS::Logs::LogGroup
  LogGroupPolicy:
    Type: AWS::Logs::ResourcePolicy
    Properties:
      PolicyName:
        Fn::Sub: ${AWS::StackName}-AllowOS
      PolicyDocument:
        Fn::Sub:
        - '{"Version": "2012-10-17","Statement":[{"Effect":"Allow","Principal": {"Service": ["es.amazonaws.com"]},"Action":["logs:PutLogEvents","logs:CreateLogStream"],"Resource":"${LogGroupArn}","Condition":{"StringEquals":{"aws:SourceAccount": "${AWS::AccountId}"}}}]}'
        - LogGroupArn:
            Fn::GetAtt: [LogGroup, Arn]
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      LogPublishingOptions:
        ES_APPLICATION_LOGS:
          Enabled: false
```

## \[CT\.OPENSEARCH\.PR\.13\] Require an Amazon OpenSearch Service domain to send audit logs to Amazon CloudWatch Logs<a name="ct-opensearch-pr-13-description"></a>

This control checks whether Amazon OpenSearch Service domains are configured to send audit logs to an Amazon CloudWatch Logs log group\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::OpenSearchService::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.13 rule specification](#ct-opensearch-pr-13-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.13 rule specification](#ct-opensearch-pr-13-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.13 example templates](#ct-opensearch-pr-13-templates) 

**Explanation**

Audit logs are highly customizable\. They allow you to track user activity on your OpenSearch clusters, including authentication successes and failures, requests to OpenSearch, index changes, and incoming search queries\.

**Usage considerations**  
Audit log publishing requires advanced security options to be enabled on Amazon OpenSearch Service domains\.
To enable advanced security options on an Amazon OpenSearch Service domain, you must enable encryption of data at rest by means of the `EncryptionAtRestOptions` property, node\-to\-node encryption by means of the `NodeToNodeEncryptionOptions` property, and enforce HTTPS connections by means of the `EnforceHTTPS` property within `DomainEndpointOptions`\.

### Remediation for rule failure<a name="ct-opensearch-pr-13-remediation"></a>

Within `LogPublishingOptions`, provide an `AUDIT_LOGS` configuration, set `Enabled` to `true` and `CloudWatchLogsLogGroupArn` to the ARN of a valid Amazon CloudWatch Logs log group\.

The examples that follow show how to implement this remediation\.

#### Amazon OpenSearch Service Domain \- Example<a name="ct-opensearch-pr-13-remediation-1"></a>

An Amazon OpenSearch Service domain configured to send audit logs to Amazon CloudWatch Logs\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "OpenSearchServiceDomain": {
        "Type": "AWS::OpenSearchService::Domain",
        "DependsOn": "LogGroupPolicy",
        "Properties": {
            "EngineVersion": "OpenSearch_1.3",
            "ClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.search"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "NodeToNodeEncryptionOptions": {
                "Enabled": true
            },
            "EncryptionAtRestOptions": {
                "Enabled": true
            },
            "DomainEndpointOptions": {
                "EnforceHTTPS": true
            },
            "AdvancedSecurityOptions": {
                "Enabled": true,
                "InternalUserDatabaseEnabled": false,
                "MasterUserOptions": {
                    "MasterUserARN": {
                        "Fn::GetAtt": [
                            "IAMRole",
                            "Arn"
                        ]
                    }
                }
            },
            "LogPublishingOptions": {
                "AUDIT_LOGS": {
                    "CloudWatchLogsLogGroupArn": {
                        "Fn::GetAtt": [
                            "LogGroup",
                            "Arn"
                        ]
                    },
                    "Enabled": true
                }
            }
        }
    }
}
```

**YAML example**

```
OpenSearchServiceDomain:
  Type: AWS::OpenSearchService::Domain
  DependsOn: LogGroupPolicy
  Properties:
    EngineVersion: OpenSearch_1.3
    ClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.search
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    NodeToNodeEncryptionOptions:
      Enabled: true
    EncryptionAtRestOptions:
      Enabled: true
    DomainEndpointOptions:
      EnforceHTTPS: true
    AdvancedSecurityOptions:
      Enabled: true
      InternalUserDatabaseEnabled: false
      MasterUserOptions:
        MasterUserARN: !GetAtt 'IAMRole.Arn'
    LogPublishingOptions:
      AUDIT_LOGS:
        CloudWatchLogsLogGroupArn: !GetAtt 'LogGroup.Arn'
        Enabled: true
```

### CT\.OPENSEARCH\.PR\.13 rule specification<a name="ct-opensearch-pr-13-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   opensearch_audit_logging_enabled_check
# 
# Description:
#   This control checks whether Amazon OpenSearch Service domains are configured to send audit logs to an Amazon CloudWatch Logs log group.
# 
# Reports on:
#   AWS::OpenSearchService::Domain
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
#       And: The input document does not contain any OpenSearch Service domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'LogPublishingOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'LogPublishingOptions' has been provided
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'AUDIT_LOGS' has not been provided or provided and set to
#            a value other than bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'AUDIT_LOGS' has not been provided or provided
#            as an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'AUDIT_LOGS' has been provided and set to bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'AUDIT_LOGS' has not been provided or provided
#            as an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'AUDIT_LOGS' has not been provided or provided and set to
#            a value other than bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'AUDIT_LOGS' has been provided as a non-empty string
#            or valid local reference
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'LogPublishingOptions' has been specified
#       And: 'AUDIT_LOGS' in 'LogPublishingOptions' has been provided
#       And: 'Enabled' in 'AUDIT_LOGS' has been provided and set to bool(true)
#       And: 'CloudWatchLogsLogGroupArn' in 'AUDIT_LOGS' has been provided as a non-empty string
#            or valid local reference
#      Then: PASS

#
# Constants
#
let OPENSEARCH_SERVICE_DOMAIN_TYPE = "AWS::OpenSearchService::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let opensearch_service_domains = Resources.*[ Type == %OPENSEARCH_SERVICE_DOMAIN_TYPE ]

#
# Primary Rules
#
rule opensearch_audit_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                 %opensearch_service_domains not empty {
    check(%opensearch_service_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.13]: Require an Amazon OpenSearch Service domain to send audit logs to Amazon CloudWatch Logs
            [FIX]: Within 'LogPublishingOptions', provide an 'AUDIT_LOGS' configuration, set 'Enabled' to 'true' and 'CloudWatchLogsLogGroupArn' to the ARN of a valid Amazon CloudWatch Logs log group.
        >>
}

rule opensearch_audit_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %OPENSEARCH_SERVICE_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%OPENSEARCH_SERVICE_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.13]: Require an Amazon OpenSearch Service domain to send audit logs to Amazon CloudWatch Logs
            [FIX]: Within 'LogPublishingOptions', provide an 'AUDIT_LOGS' configuration, set 'Enabled' to 'true' and 'CloudWatchLogsLogGroupArn' to the ARN of a valid Amazon CloudWatch Logs log group.
        >>
}

#
# Parameterized Rules
#
rule check(opensearch_service_domain) {
    %opensearch_service_domain {
        # Scenario 2
        LogPublishingOptions exists
        LogPublishingOptions is_struct

        LogPublishingOptions {
            # Scenario 3
            AUDIT_LOGS exists
            AUDIT_LOGS is_struct

            AUDIT_LOGS {
                # Scenarios 4, 5, 6 and 7
                Enabled exists
                Enabled == true

                CloudWatchLogsLogGroupArn exists
                check_is_string_and_not_empty(CloudWatchLogsLogGroupArn) or
                check_local_references(%INPUT_DOCUMENT, CloudWatchLogsLogGroupArn, "AWS::Logs::LogGroup")
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

### CT\.OPENSEARCH\.PR\.13 example templates<a name="ct-opensearch-pr-13-templates"></a>

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
          Action: sts:AssumeRole
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    DependsOn: LogGroupPolicy
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
      EncryptionAtRestOptions:
        Enabled: true
      DomainEndpointOptions:
        EnforceHTTPS: true
      AdvancedSecurityOptions:
        Enabled: true
        InternalUserDatabaseEnabled: false
        MasterUserOptions:
          MasterUserARN:
            Fn::GetAtt:
            - IAMRole
            - Arn
      LogPublishingOptions:
        AUDIT_LOGS:
          CloudWatchLogsLogGroupArn:
            Fn::GetAtt:
            - LogGroup
            - Arn
          Enabled: true
  LogGroup:
    Type: AWS::Logs::LogGroup
  LogGroupPolicy:
    Type: AWS::Logs::ResourcePolicy
    Properties:
      PolicyName: AllowES
      PolicyDocument:
        Fn::Sub:
        - '{"Version": "2012-10-17","Statement":[{"Effect":"Allow","Principal": {"Service": ["es.amazonaws.com"]},"Action":["logs:PutLogEvents","logs:CreateLogStream"],"Resource":"${LogGroupArn}","Condition":{"StringEquals":{ "aws:SourceAccount": "${AWS::AccountId}"}}}]}'
        - LogGroupArn:
            Fn::GetAtt: [ LogGroup, Arn ]
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
          Action: sts:AssumeRole
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
      EncryptionAtRestOptions:
        Enabled: true
      DomainEndpointOptions:
        EnforceHTTPS: true
      AdvancedSecurityOptions:
        Enabled: true
        InternalUserDatabaseEnabled: false
        MasterUserOptions:
          MasterUserARN:
            Fn::GetAtt:
            - IAMRole
            - Arn
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
          Action: sts:AssumeRole
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
      EncryptionAtRestOptions:
        Enabled: true
      DomainEndpointOptions:
        EnforceHTTPS: true
      AdvancedSecurityOptions:
        Enabled: true
        InternalUserDatabaseEnabled: false
        MasterUserOptions:
          MasterUserARN:
            Fn::GetAtt:
            - IAMRole
            - Arn
      LogPublishingOptions:
        AUDIT_LOGS:
          Enabled: false
```

## \[CT\.OPENSEARCH\.PR\.14\] Require an Amazon OpenSearch Service domain to have zone awareness and at least three data nodes<a name="ct-opensearch-pr-14-description"></a>

This control checks whether Amazon OpenSearch Service domains are configured with at least three data nodes and zone awareness enabled\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::OpenSearchService::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.14 rule specification](#ct-opensearch-pr-14-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.14 rule specification](#ct-opensearch-pr-14-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.14 example templates](#ct-opensearch-pr-14-templates) 

**Explanation**

An OpenSearch domain requires at least three data nodes for high availability and fault\-tolerance\. Deploying an OpenSearch domain with at least three data nodes ensures that the cluster can remain operative if a node fails\.

### Remediation for rule failure<a name="ct-opensearch-pr-14-remediation"></a>

Within `ClusterConfig`, set `ZoneAwarenessEnabled` to `true` and `InstanceCount` to an integer value greater than or equal to three\.

The examples that follow show how to implement this remediation\.

#### Amazon OpenSearch Service Domain \- Example<a name="ct-opensearch-pr-14-remediation-1"></a>

An Amazon OpenSearch Service domain configured with three data nodes and zone awareness enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "OpenSearchServiceDomain": {
        "Type": "AWS::OpenSearchService::Domain",
        "Properties": {
            "EngineVersion": "OpenSearch_1.3",
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "ClusterConfig": {
                "InstanceType": "t3.small.search",
                "InstanceCount": 3,
                "ZoneAwarenessEnabled": true,
                "ZoneAwarenessConfig": {
                    "AvailabilityZoneCount": 3
                }
            }
        }
    }
}
```

**YAML example**

```
OpenSearchServiceDomain:
  Type: AWS::OpenSearchService::Domain
  Properties:
    EngineVersion: OpenSearch_1.3
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    ClusterConfig:
      InstanceType: t3.small.search
      InstanceCount: 3
      ZoneAwarenessEnabled: true
      ZoneAwarenessConfig:
        AvailabilityZoneCount: 3
```

### CT\.OPENSEARCH\.PR\.14 rule specification<a name="ct-opensearch-pr-14-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   opensearch_data_node_fault_tolerance_check
# 
# Description:
#   This control checks whether Amazon OpenSearch Service domains are configured with at least three data nodes and zone awareness enabled. 
# 
# Reports on:
#   AWS::OpenSearchService::Domain
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
#       And: The input document does not contain any OpenSearch Service domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'ClusterConfig' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'ClusterConfig' has been provided
#       And: 'ZoneAwarenessEnabled' in 'ClusterConfig' has not been provided
#             or provided and set to a value other than bool(true)
#       And: 'InstanceCount' in 'ClusterConfig' has not been provided or
#             provided and set to an integer value less than three (< 3)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'ClusterConfig' has been provided
#       And: 'ZoneAwarenessEnabled' in 'ClusterConfig' has been provided
#             and set to bool(true)
#       And: 'InstanceCount' in 'ClusterConfig' has not been provided or
#             provided and set to an integer value less than three (< 3)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'ClusterConfig' has been provided
#       And: 'ZoneAwarenessEnabled' in 'ClusterConfig' has not been provided
#             or provided and set to a value other than bool(true)
#       And: 'InstanceCount' in 'ClusterConfig' has been provided and set to
#             an integer value greater than or equal to three (>= 3)
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'ClusterConfig' has been provided
#       And: 'ZoneAwarenessEnabled' in 'ClusterConfig' has been provided
#             and set to bool(true)
#       And: 'InstanceCount' in 'ClusterConfig' has been provided and set to
#             an integer value greater than or equal to three (>= 3)
#      Then: PASS

#
# Constants
#
let OPENSEARCH_SERVICE_DOMAIN_TYPE = "AWS::OpenSearchService::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let opensearch_service_domains = Resources.*[ Type == %OPENSEARCH_SERVICE_DOMAIN_TYPE ]

#
# Primary Rules
#
rule opensearch_data_node_fault_tolerance_check when is_cfn_template(%INPUT_DOCUMENT)
                                                     %opensearch_service_domains not empty {
    check(%opensearch_service_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.14]: Require an Amazon OpenSearch Service domain to have zone awareness and at least three data nodes
            [FIX]: Within 'ClusterConfig', set 'ZoneAwarenessEnabled' to 'true' and 'InstanceCount' to an integer value greater than or equal to three.
        >>
}

rule opensearch_data_node_fault_tolerance_check when is_cfn_hook(%INPUT_DOCUMENT, %OPENSEARCH_SERVICE_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%OPENSEARCH_SERVICE_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.14]: Require an Amazon OpenSearch Service domain to have zone awareness and at least three data nodes
            [FIX]: Within 'ClusterConfig', set 'ZoneAwarenessEnabled' to 'true' and 'InstanceCount' to an integer value greater than or equal to three.
        >>
}

#
# Parameterized Rules
#
rule check(opensearch_service_domain) {
    %opensearch_service_domain {
        # Scenario 2
        ClusterConfig exists
        ClusterConfig is_struct

        ClusterConfig {
            # Scenario 3, 4, 5 and 6
            ZoneAwarenessEnabled exists
            ZoneAwarenessEnabled == true

            InstanceCount exists
            InstanceCount >= 3
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

### CT\.OPENSEARCH\.PR\.14 example templates<a name="ct-opensearch-pr-14-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      ClusterConfig:
        InstanceType: t3.small.search
        InstanceCount: 3
        ZoneAwarenessEnabled: true
        ZoneAwarenessConfig:
          AvailabilityZoneCount: 3
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceType: t3.small.search
        ZoneAwarenessEnabled: false
        InstanceCount: 2
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
```

## \[CT\.OPENSEARCH\.PR\.15\] Require an Amazon OpenSearch Service domain to use fine\-grained access control<a name="ct-opensearch-pr-15-description"></a>

This control checks whether Amazon OpenSearch Service domains have fine\-grained access control enabled\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::OpenSearchService::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.15 rule specification](#ct-opensearch-pr-15-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.15 rule specification](#ct-opensearch-pr-15-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.15 example templates](#ct-opensearch-pr-15-templates) 

**Explanation**

Fine\-grained access control offers additional ways of controlling access to your data on Amazon OpenSearch Service\.

**Usage considerations**  
Fine\-grained access control requires that advanced security options must be enabled on Amazon OpenSearch Service domains\.
To enable advanced security options on an Amazon OpenSearch Service domain, you must enable encryption of data at rest by means of the `EncryptionAtRestOptions` property, node\-to\-node encryption by means of the `NodeToNodeEncryptionOptions` property, and enforce HTTPS connections by means of the `EnforceHTTPS` property within `DomainEndpointOptions`\.

### Remediation for rule failure<a name="ct-opensearch-pr-15-remediation"></a>

Within `AdvancedSecurityOptions`, set `Enabled` to `true`, set `InternalUserDatabaseEnabled` to `true` or `false`, and set `MasterUserOptions` with an options configuration for your master user\.

The examples that follow show how to implement this remediation\.

#### Amazon OpenSearch Service Domain \- Example<a name="ct-opensearch-pr-15-remediation-1"></a>

An Amazon OpenSearch Service domain configured with fine\-grained access control\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "OpenSearchServiceDomain": {
        "Type": "AWS::OpenSearchService::Domain",
        "Properties": {
            "EngineVersion": "OpenSearch_1.3",
            "ClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.search"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "NodeToNodeEncryptionOptions": {
                "Enabled": true
            },
            "EncryptionAtRestOptions": {
                "Enabled": true
            },
            "DomainEndpointOptions": {
                "EnforceHTTPS": true
            },
            "AdvancedSecurityOptions": {
                "Enabled": true,
                "InternalUserDatabaseEnabled": false,
                "MasterUserOptions": {
                    "MasterUserARN": {
                        "Fn::GetAtt": [
                            "IAMRole",
                            "Arn"
                        ]
                    }
                }
            }
        }
    }
}
```

**YAML example**

```
OpenSearchServiceDomain:
  Type: AWS::OpenSearchService::Domain
  Properties:
    EngineVersion: OpenSearch_1.3
    ClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.search
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    NodeToNodeEncryptionOptions:
      Enabled: true
    EncryptionAtRestOptions:
      Enabled: true
    DomainEndpointOptions:
      EnforceHTTPS: true
    AdvancedSecurityOptions:
      Enabled: true
      InternalUserDatabaseEnabled: false
      MasterUserOptions:
        MasterUserARN: !GetAtt 'IAMRole.Arn'
```

### CT\.OPENSEARCH\.PR\.15 rule specification<a name="ct-opensearch-pr-15-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   opensearch_fine_grained_access_control_enabled_check
# 
# Description:
#   This control checks whether Amazon OpenSearch Service domains have fine-grained access control enabled.
# 
# Reports on:
#   AWS::OpenSearchService::Domain
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
#       And: The input document does not contain any OpenSearch Service domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'AdvancedSecurityOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'AdvancedSecurityOptions' has been provided
#       And: 'Enabled' in 'AdvancedSecurityOptions' has not been provided or
#            has been provided and set to a value other than bool(true)
#       And: 'InternalUserDatabaseEnabled' in 'AdvancedSecurityOptions' has not been provided or provided and set to a
#             non boolean value
#       And: 'MasterUserOptions' in 'AdvancedSecurityOptions' has not been provided or provided and set to a value
#            other than a struct
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'AdvancedSecurityOptions' has been provided
#       And: 'Enabled' in 'AdvancedSecurityOptions' has been provided and set to bool(true)
#       And: 'InternalUserDatabaseEnabled' in 'AdvancedSecurityOptions' has not been provided or provided and set to a
#             non boolean value
#       And: 'MasterUserOptions' in 'AdvancedSecurityOptions' has not been provided or provided and set to a value
#            other than a struct
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'AdvancedSecurityOptions' has been provided
#       And: 'Enabled' in 'AdvancedSecurityOptions' has been provided and set to bool(true)
#       And: 'InternalUserDatabaseEnabled' in 'AdvancedSecurityOptions' has been provided and set to a
#            boolean value
#       And: 'MasterUserOptions' in 'AdvancedSecurityOptions' has not been provided or provided and set to a value
#            other than a struct
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'AdvancedSecurityOptions' has been provided
#       And: 'Enabled' in 'AdvancedSecurityOptions' has been provided and set to bool(true)
#       And: 'InternalUserDatabaseEnabled' in 'AdvancedSecurityOptions' has not been provided or provided and set to a
#             non boolean value
#       And: 'MasterUserOptions' in 'AdvancedSecurityOptions' has been provided and set to a struct
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'AdvancedSecurityOptions' has been provided
#       And: 'Enabled' in 'AdvancedSecurityOptions' has been provided and set to bool(true)
#       And: 'InternalUserDatabaseEnabled' in 'AdvancedSecurityOptions' has been provided and set to a
#            boolean value
#       And: 'MasterUserOptions' in 'AdvancedSecurityOptions' has been provided and set to a struct
#      Then: PASS

#
# Constants
#
let OPENSEARCH_SERVICE_DOMAIN_TYPE = "AWS::OpenSearchService::Domain"
let INPUT_DOCUMENT = this

#
# Assignments
#
let opensearch_service_domains = Resources.*[ Type == %OPENSEARCH_SERVICE_DOMAIN_TYPE ]

#
# Primary Rules
#
rule opensearch_fine_grained_access_control_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                               %opensearch_service_domains not empty {
    check(%opensearch_service_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.15]: Require an Amazon OpenSearch Service domain to use fine-grained access control
            [FIX]: Within 'AdvancedSecurityOptions', set 'Enabled' to 'true', set 'InternalUserDatabaseEnabled' to 'true' or 'false', and set 'MasterUserOptions' with an options configuration for your master user.
        >>
}

rule opensearch_fine_grained_access_control_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %OPENSEARCH_SERVICE_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%OPENSEARCH_SERVICE_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.15]: Require an Amazon OpenSearch Service domain to use fine-grained access control
            [FIX]: Within 'AdvancedSecurityOptions', set 'Enabled' to 'true', set 'InternalUserDatabaseEnabled' to 'true' or 'false', and set 'MasterUserOptions' with an options configuration for your master user.
        >>
}

#
# Parameterized Rules
#
rule check(opensearch_service_domain) {
    %opensearch_service_domain {
        # Scenario 2
        AdvancedSecurityOptions exists
        AdvancedSecurityOptions is_struct

        AdvancedSecurityOptions {
            # Scenarios 3, 4, 5, 6 and 7
            Enabled exists
            Enabled == true

            InternalUserDatabaseEnabled exists
            InternalUserDatabaseEnabled in [ true, false ]

            MasterUserOptions exists
            MasterUserOptions is_struct
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

### CT\.OPENSEARCH\.PR\.15 example templates<a name="ct-opensearch-pr-15-templates"></a>

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
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
      EncryptionAtRestOptions:
        Enabled: true
      DomainEndpointOptions:
        EnforceHTTPS: true
      AdvancedSecurityOptions:
        Enabled: true
        InternalUserDatabaseEnabled: false
        MasterUserOptions:
          MasterUserARN:
            Fn::GetAtt: [IAMRole, Arn]
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      NodeToNodeEncryptionOptions:
        Enabled: true
      EncryptionAtRestOptions:
        Enabled: true
      DomainEndpointOptions:
        EnforceHTTPS: true
      AdvancedSecurityOptions:
        Enabled: false
```

## \[CT\.OPENSEARCH\.PR\.16\] Require an Amazon OpenSearch Service domain to use TLSv1\.2<a name="ct-opensearch-pr-16-description"></a>

This control checks whether Amazon OpenSearch Service domains are configured to require HTTPS with a minimum TLS version of TLSv1\.2\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::OpenSearchService::Domain`
+ **AWS CloudFormation guard rule: ** [CT\.OPENSEARCH\.PR\.16 rule specification](#ct-opensearch-pr-16-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.OPENSEARCH\.PR\.16 rule specification](#ct-opensearch-pr-16-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.OPENSEARCH\.PR\.16 example templates](#ct-opensearch-pr-16-templates) 

**Explanation**

HTTPS \(TLS\) can help prevent potential attackers from using person\-in\-the\-middle, or similar attacks, to eavesdrop on or manipulate network traffic\. Only encrypted connections over HTTPS \(TLS\) should be allowed\. Encrypting data in transit can affect performance\. You should test your application with this feature to understand the performance profile and the effects of TLS\. TLS 1\.2 provides several security enhancements over previous versions of TLS\.

### Remediation for rule failure<a name="ct-opensearch-pr-16-remediation"></a>

Within `DomainEndpointOptions`, set `EnforceHTTPS` to `true` and set `TLSSecurityPolicy` to `Policy-Min-TLS-1-2-2019-07`\.

The examples that follow show how to implement this remediation\.

#### Amazon OpenSearch Service Domain \- Example<a name="ct-opensearch-pr-16-remediation-1"></a>

An Amazon OpenSearch Service domain configured to require all traffic to the domain arrive over HTTPS with a minimum TLS version of TLSv1\.2\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "OpenSearchServiceDomain": {
        "Type": "AWS::OpenSearchService::Domain",
        "Properties": {
            "EngineVersion": "OpenSearch_1.3",
            "ClusterConfig": {
                "InstanceCount": "1",
                "InstanceType": "t3.small.search"
            },
            "EBSOptions": {
                "EBSEnabled": true,
                "Iops": "3000",
                "VolumeSize": "10",
                "VolumeType": "gp3"
            },
            "AccessPolicies": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Deny",
                        "Principal": {
                            "AWS": "*"
                        },
                        "Action": "es:*",
                        "Resource": "*"
                    }
                ]
            },
            "DomainEndpointOptions": {
                "EnforceHTTPS": true,
                "TLSSecurityPolicy": "Policy-Min-TLS-1-2-2019-07"
            }
        }
    }
}
```

**YAML example**

```
OpenSearchServiceDomain:
  Type: AWS::OpenSearchService::Domain
  Properties:
    EngineVersion: OpenSearch_1.3
    ClusterConfig:
      InstanceCount: '1'
      InstanceType: t3.small.search
    EBSOptions:
      EBSEnabled: true
      Iops: '3000'
      VolumeSize: '10'
      VolumeType: gp3
    AccessPolicies:
      Version: '2012-10-17'
      Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
    DomainEndpointOptions:
      EnforceHTTPS: true
      TLSSecurityPolicy: Policy-Min-TLS-1-2-2019-07
```

### CT\.OPENSEARCH\.PR\.16 rule specification<a name="ct-opensearch-pr-16-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   opensearch_https_required_check
# 
# Description:
#   This control checks whether Amazon OpenSearch Service domains are configured to require HTTPS with a minimum TLS version of TLSv1.2.
# 
# Reports on:
#   AWS::OpenSearchService::Domain
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
#       And: The input document does not contain any OpenSearch Service domain resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'DomainEndpointOptions' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'DomainEndpointOptions' has been provided
#       And: 'EnforceHTTPS' in 'DomainEndpointOptions' has not been provided or
#            has been provided and set to a value other than bool(true)
#       And: 'TLSSecurityPolicy' in 'DomainEndpointOptions' has not been provided or
#            has been provided and set ot a value other than 'Policy-Min-TLS-1-2-2019-07'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'DomainEndpointOptions' has been provided
#       And: 'EnforceHTTPS' in 'DomainEndpointOptions' has been provided and set to bool(true)
#       And: 'TLSSecurityPolicy' in 'DomainEndpointOptions' has not been provided or
#            has been provided and set ot a value other than 'Policy-Min-TLS-1-2-2019-07'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'DomainEndpointOptions' has been provided
#       And: 'EnforceHTTPS' in 'DomainEndpointOptions' has not been provided or
#            has been provided and set to a value other than bool(true)
#       And: 'TLSSecurityPolicy' in 'DomainEndpointOptions' has been provided and set
#            to 'Policy-Min-TLS-1-2-2019-07'
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an OpenSearch Service domain resource
#       And: 'DomainEndpointOptions' has been provided
#       And: 'EnforceHTTPS' in 'DomainEndpointOptions' has been provided and set to bool(true)
#       And: 'TLSSecurityPolicy' in 'DomainEndpointOptions' has been provided and set
#            to 'Policy-Min-TLS-1-2-2019-07'
#      Then: PASS

#
# Constants
#
let OPENSEARCH_SERVICE_DOMAIN_TYPE = "AWS::OpenSearchService::Domain"
let ALLOWED_TLS_POLICIES = [ "Policy-Min-TLS-1-2-2019-07" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let opensearch_service_domains = Resources.*[ Type == %OPENSEARCH_SERVICE_DOMAIN_TYPE ]

#
# Primary Rules
#
rule opensearch_https_required_check when is_cfn_template(%INPUT_DOCUMENT)
                                          %opensearch_service_domains not empty {
    check(%opensearch_service_domains.Properties)
        <<
        [CT.OPENSEARCH.PR.16]: Require an Amazon OpenSearch Service domain to use TLSv1.2
            [FIX]: Within 'DomainEndpointOptions', set 'EnforceHTTPS' to 'true' and set 'TLSSecurityPolicy' to 'Policy-Min-TLS-1-2-2019-07'.
        >>
}

rule opensearch_https_required_check when is_cfn_hook(%INPUT_DOCUMENT, %OPENSEARCH_SERVICE_DOMAIN_TYPE) {
    check(%INPUT_DOCUMENT.%OPENSEARCH_SERVICE_DOMAIN_TYPE.resourceProperties)
        <<
        [CT.OPENSEARCH.PR.16]: Require an Amazon OpenSearch Service domain to use TLSv1.2
            [FIX]: Within 'DomainEndpointOptions', set 'EnforceHTTPS' to 'true' and set 'TLSSecurityPolicy' to 'Policy-Min-TLS-1-2-2019-07'.
        >>
}

#
# Parameterized Rules
#
rule check(opensearch_service_domain) {
    %opensearch_service_domain {
        # Scenario 2
        DomainEndpointOptions exists
        DomainEndpointOptions is_struct

        DomainEndpointOptions {
            # Scenarios 3, 4, 5 and 6
            EnforceHTTPS exists
            EnforceHTTPS == true

            TLSSecurityPolicy exists
            TLSSecurityPolicy in %ALLOWED_TLS_POLICIES
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

### CT\.OPENSEARCH\.PR\.16 example templates<a name="ct-opensearch-pr-16-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      DomainEndpointOptions:
        EnforceHTTPS: true
        TLSSecurityPolicy: Policy-Min-TLS-1-2-2019-07
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  OpenSearchServiceDomain:
    Type: AWS::OpenSearchService::Domain
    Properties:
      EngineVersion: OpenSearch_1.3
      ClusterConfig:
        InstanceCount: '1'
        InstanceType: t3.small.search
      EBSOptions:
        EBSEnabled: true
        Iops: '3000'
        VolumeSize: '10'
        VolumeType: gp3
      AccessPolicies:
        Version: '2012-10-17'
        Statement:
        - Effect: Deny
          Principal:
            AWS: '*'
          Action: es:*
          Resource: '*'
      DomainEndpointOptions:
        EnforceHTTPS: true
        TLSSecurityPolicy: Policy-Min-TLS-1-0-2019-07
```