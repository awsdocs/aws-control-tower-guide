# Amazon Elastic Container Service controls<a name="ecs-rules"></a>

**Topics**
+ [\[CT\.ECS\.PR\.1\] Require Amazon ECS Fargate Services to run on the latest Fargate platform version](#ct-ecs-pr-1-description)
+ [\[CT\.ECS\.PR\.2\] Require any Amazon ECS cluster to have container insights activated](#ct-ecs-pr-2-description)
+ [\[CT\.ECS\.PR\.3\] Require any Amazon ECS task definition to specify a user that is not the root](#ct-ecs-pr-3-description)
+ [\[CT\.ECS\.PR\.4\] Require Amazon ECS tasks to use 'awsvpc' networking mode](#ct-ecs-pr-4-description)
+ [\[CT\.ECS\.PR\.5\] Require an active Amazon ECS task definition to have a logging configuration](#ct-ecs-pr-5-description)
+ [\[CT\.ECS\.PR\.6\] Require Amazon ECS containers to allow read\-only access to the root filesystem](#ct-ecs-pr-6-description)
+ [\[CT\.ECS\.PR\.7\] Require an Amazon ECS task definition to have a specific memory usage limit](#ct-ecs-pr-7-description)
+ [\[CT\.ECS\.PR\.8\] Require Amazon ECS task definitions to have secure networking modes and user definitions](#ct-ecs-pr-8-description)
+ [\[CT\.ECS\.PR\.9\] Require Amazon ECS services not to assign public IP addresses automatically](#ct-ecs-pr-9-description)
+ [\[CT\.ECS\.PR\.10\] Require that Amazon ECS task definitions do not share the host's process namespace](#ct-ecs-pr-10-description)
+ [\[CT\.ECS\.PR\.11\] Require an Amazon ECS container to run as non\-privileged](#ct-ecs-pr-11-description)
+ [\[CT\.ECS\.PR\.12\] Require that Amazon ECS task definitions do not pass secrets as container environment variables](#ct-ecs-pr-12-description)

## \[CT\.ECS\.PR\.1\] Require Amazon ECS Fargate Services to run on the latest Fargate platform version<a name="ct-ecs-pr-1-description"></a>

This control checks whether Amazon Elastic Container Service \(Amazon ECS\) Fargate services are configured to deploy using the `LATEST` platform version rather than a specified version number\.
+ **Control objective: **Manage vulnerabilities
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::Service`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.1 rule specification](#ct-ecs-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.1 rule specification](#ct-ecs-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.1 example templates](#ct-ecs-pr-1-templates) 

**Explanation**

AWS Fargate platform versions refer to a specific runtime environment for Fargate task infrastructure, which is a combination of kernel and container runtime versions\. New platform versions are released as the runtime environment evolves\. For example, a new version may be released for kernel or operating system updates, new features, bug fixes, or security updates\. Security updates and patches are deployed automatically for your Fargate tasks\. If a security issue is found that affects a platform version, AWS patches the platform version\.

**Usage considerations**  
This control only applies to Amazon ECS services with a `LaunchType` of `FARGATE`

### Remediation for rule failure<a name="ct-ecs-pr-1-remediation"></a>

When `LaunchType` is set to `FARGATE`, set the `PlatformVersion` property to `LATEST` or omit the `PlatformVersion` property \(default: `LATEST`\)\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Service \- Example One<a name="ct-ecs-pr-1-remediation-1"></a>

Amazon ECS service configured to deploy using the `LATEST` platform version by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSService": {
        "Type": "AWS::ECS::Service",
        "Properties": {
            "Cluster": {
                "Ref": "ECSCluster"
            },
            "DesiredCount": 1,
            "TaskDefinition": {
                "Ref": "ECSTaskDefinition"
            },
            "NetworkConfiguration": {
                "AwsvpcConfiguration": {
                    "AssignPublicIp": "DISABLED",
                    "Subnets": [
                        {
                            "Ref": "SubnetOne"
                        },
                        {
                            "Ref": "SubnetTwo"
                        }
                    ]
                }
            },
            "LaunchType": "FARGATE"
        }
    }
}
```

**YAML example**

```
ECSService:
  Type: AWS::ECS::Service
  Properties:
    Cluster: !Ref 'ECSCluster'
    DesiredCount: 1
    TaskDefinition: !Ref 'ECSTaskDefinition'
    NetworkConfiguration:
      AwsvpcConfiguration:
        AssignPublicIp: DISABLED
        Subnets:
          - !Ref 'SubnetOne'
          - !Ref 'SubnetTwo'
    LaunchType: FARGATE
```

The examples that follow show how to implement this remediation\.

#### Amazon ECS Service \- Example Two<a name="ct-ecs-pr-1-remediation-2"></a>

Amazon ECS service configured to deploy using the `LATEST` platform version by means of the `PlatformVersion` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSService": {
        "Type": "AWS::ECS::Service",
        "Properties": {
            "Cluster": {
                "Ref": "ECSCluster"
            },
            "DesiredCount": 1,
            "TaskDefinition": {
                "Ref": "ECSTaskDefinition"
            },
            "NetworkConfiguration": {
                "AwsvpcConfiguration": {
                    "AssignPublicIp": "DISABLED",
                    "Subnets": [
                        {
                            "Ref": "SubnetOne"
                        },
                        {
                            "Ref": "SubnetTwo"
                        }
                    ]
                }
            },
            "LaunchType": "FARGATE",
            "PlatformVersion": "LATEST"
        }
    }
}
```

**YAML example**

```
ECSService:
  Type: AWS::ECS::Service
  Properties:
    Cluster: !Ref 'ECSCluster'
    DesiredCount: 1
    TaskDefinition: !Ref 'ECSTaskDefinition'
    NetworkConfiguration:
      AwsvpcConfiguration:
        AssignPublicIp: DISABLED
        Subnets:
          - !Ref 'SubnetOne'
          - !Ref 'SubnetTwo'
    LaunchType: FARGATE
    PlatformVersion: LATEST
```

### CT\.ECS\.PR\.1 rule specification<a name="ct-ecs-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_fargate_latest_platform_version_check
# 
# Description:
#   This control checks whether Amazon Elastic Container Service (Amazon ECS) Fargate services are configured to deploy using the 'LATEST' platform version rather than a specified version number.
# 
# Reports on:
#   AWS::ECS::Service
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document does not contain an Amazon ECS service resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'LaunchType' is not present
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'LaunchType' is present and not set to 'FARGATE'
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'LaunchType' is present and set to 'FARGATE'
#       And: 'PlatformVersion' is present and not set to 'LATEST'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'LaunchType' is present and set to 'FARGATE'
#       And: 'PlatformVersion' is not present
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'LaunchType' is present and set to 'FARGATE'
#       And: 'PlatformVersion' is set to 'LATEST'
#      Then: PASS

#
# Constants
#
let ECS_SERVICE_TYPE = "AWS::ECS::Service"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecs_services = Resources.*[ Type == %ECS_SERVICE_TYPE ]

#
# Primary Rules
#
rule ecs_fargate_latest_platform_version_check when is_cfn_template(%INPUT_DOCUMENT)
                                                    %ecs_services not empty {
    check(%ecs_services.Properties)
        <<
        [CT.ECS.PR.1]: Require Amazon ECS Fargate Services to run on the latest Fargate platform version
        [FIX]: When 'LaunchType' is set to 'FARGATE', set the 'PlatformVersion' property to 'LATEST' or omit the 'PlatformVersion' property (default: 'LATEST').
        >>
}

rule ecs_fargate_latest_platform_version_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_SERVICE_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_SERVICE_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.1]: Require Amazon ECS Fargate Services to run on the latest Fargate platform version
        [FIX]: When 'LaunchType' is set to 'FARGATE', set the 'PlatformVersion' property to 'LATEST' or omit the 'PlatformVersion' property (default: 'LATEST').
        >>
}

#
# Parameterized Rules
#
rule check(ecs_service) {
    %ecs_service [ filter_launch_type_is_fargate(this) ]{
        # Scenario 5
        PlatformVersion not exists  or

        # Scenario 4 and 6
        check_fargate_version_latest(PlatformVersion)
    }
}

rule filter_launch_type_is_fargate(ecs_service) {
    %ecs_service {
        # Scenario 2
        LaunchType exists
        LaunchType is_string

        # Scenario 3
        LaunchType == "FARGATE"
    }
}

rule check_fargate_version_latest(property) {
    %property {
        this is_string
        this == "LATEST"
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

### CT\.ECS\.PR\.1 example templates<a name="ct-ecs-pr-1-templates"></a>

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
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
  ECSCluster:
    Type: AWS::ECS::Cluster
    Properties:
      CapacityProviders:
        - FARGATE
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainer
      Memory: '512'
      RequiresCompatibilities:
        - FARGATE
      NetworkMode: awsvpc
      Cpu: 256
  ECSService:
    Type: AWS::ECS::Service
    Properties:
      Cluster:
        Ref: ECSCluster
      DesiredCount: 0
      TaskDefinition:
        Ref: ECSTaskDefinition
      NetworkConfiguration:
        AwsvpcConfiguration:
          AssignPublicIp: DISABLED
          Subnets:
           - Ref: SubnetOne
           - Ref: SubnetTwo
      LaunchType: FARGATE
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
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
  ECSCluster:
    Type: AWS::ECS::Cluster
    Properties:
      CapacityProviders:
        - FARGATE
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainer
      Memory: '512'
      RequiresCompatibilities:
        - FARGATE
      NetworkMode: awsvpc
      Cpu: 256
  ECSService:
    Type: AWS::ECS::Service
    Properties:
      Cluster:
        Ref: ECSCluster
      DesiredCount: 0
      TaskDefinition:
        Ref: ECSTaskDefinition
      NetworkConfiguration:
        AwsvpcConfiguration:
          AssignPublicIp: DISABLED
          Subnets:
           - Ref: SubnetOne
           - Ref: SubnetTwo
      LaunchType: FARGATE
      PlatformVersion: 1.4.0
```

## \[CT\.ECS\.PR\.2\] Require any Amazon ECS cluster to have container insights activated<a name="ct-ecs-pr-2-description"></a>

This control checks whether your Amazon Elastic Container Service \(Amazon ECS\) clusters have container insights enabled\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::Cluster`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.2 rule specification](#ct-ecs-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.2 rule specification](#ct-ecs-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.2 example templates](#ct-ecs-pr-2-templates) 

**Explanation**

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon ECS clusters\. Use CloudWatch container insights to collect, aggregate, and summarize metrics and logs from your containerized applications and microservices\. CloudWatch automatically collects metrics for many resources, such as CPU, memory, disk, and network\. The container insights capability also provides diagnostic information, such as container restart failures, which helps you isolate issues and resolve them quickly\. You can set CloudWatch alarms on the metrics that container insights collects\.

### Remediation for rule failure<a name="ct-ecs-pr-2-remediation"></a>

Enable container insights on Amazon ECS clusters with an entry in `ClusterSettings` that has `Name` set to `containerInsights` and `Value` set to `enabled`\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Cluster \- Example<a name="ct-ecs-pr-2-remediation-1"></a>

Amazon ECS cluster configured with container insights enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSCluster": {
        "Type": "AWS::ECS::Cluster",
        "Properties": {
            "ClusterSettings": [
                {
                    "Name": "containerInsights",
                    "Value": "enabled"
                }
            ]
        }
    }
}
```

**YAML example**

```
ECSCluster:
  Type: AWS::ECS::Cluster
  Properties:
    ClusterSettings:
      - Name: containerInsights
        Value: enabled
```

### CT\.ECS\.PR\.2 rule specification<a name="ct-ecs-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_container_insights_enabled_check
# 
# Description:
#   This control checks whether your Amazon Elastic Container Service (Amazon ECS) clusters have container insights enabled.
# 
# Reports on:
#   AWS::ECS::Cluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document does not contain an Amazon ECS cluster resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS cluster resource
#       And: 'ClusterSettings' property is not present or is an empty list
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS cluster resource
#       And: 'ClusterSettings' property is present
#       And: An entry with 'Name' set to 'containerInsights' is not present in 'ClusterSettings'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS cluster resource
#       And: 'ClusterSettings' property is present
#       And: An entry with 'Name' set to 'containerInsights' is present in 'ClusterSettings' with a 'Value' not set
#            to 'enabled'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS cluster resource
#       And: 'ClusterSettings' property is present
#       And: An entry with 'Name' set to 'containerInsights' is present in 'ClusterSettings' with a 'Value' set to
#            'enabled'
#      Then: PASS

#
# Constants
#
let ECS_CLUSTER_TYPE = "AWS::ECS::Cluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecs_clusters = Resources.*[ Type == %ECS_CLUSTER_TYPE ]

#
# Primary Rules
#
rule ecs_container_insights_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %ecs_clusters not empty {
    check(%ecs_clusters.Properties)
        <<
        [CT.ECS.PR.2]: Require any Amazon ECS cluster to have container insights activated
        [FIX]: Enable container insights on Amazon ECS clusters with an entry in 'ClusterSettings' that has 'Name' set to 'containerInsights' and 'Value' set to 'enabled'.
        >>
}

rule ecs_container_insights_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.2]: Require any Amazon ECS cluster to have container insights activated
        [FIX]: Enable container insights on Amazon ECS clusters with an entry in 'ClusterSettings' that has 'Name' set to 'containerInsights' and 'Value' set to 'enabled'.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_cluster) {
    %ecs_cluster {
        # Scenario 2
        ClusterSettings exists
        ClusterSettings is_list
        ClusterSettings not empty

        # Scenario 3, 4 and 5
        some ClusterSettings[*] {
            Name exists
            Value exists

            Name is_string
            Value is_string

            Name == "containerInsights"
            Value == "enabled"
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

### CT\.ECS\.PR\.2 example templates<a name="ct-ecs-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECSCluster:
    Type: AWS::ECS::Cluster
    Properties:
      ClusterSettings:
      - Name: containerInsights
        Value: enabled
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECSCluster:
    Type: AWS::ECS::Cluster
    Properties:
      ClusterSettings:
      - Name: containerInsights
        Value: disabled
```

## \[CT\.ECS\.PR\.3\] Require any Amazon ECS task definition to specify a user that is not the root<a name="ct-ecs-pr-3-description"></a>

This control checks whether Amazon Elastic Container Service \(ECS\) task definitions run as a non\-root user user within Amazon ECS containers\.
+ **Control objective: **Enforce least privilege, Manage vulnerabilities
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::TaskDefinition`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.3 rule specification](#ct-ecs-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.3 rule specification](#ct-ecs-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.3 example templates](#ct-ecs-pr-3-templates) 

**Explanation**

It is a best practice to run containers as a non\-root user user\. By default, containers run as the root user user, unless the `User` directive is included in your Dockerfile\. The default Linux capabilities that are assigned by Docker restrict the actions that can be run as the root user user, but only marginally\. For example, a container running as the root user user does not have access to devices\.

**Usage considerations**  
This control applies only to Amazon ECS task definitions that are configured with container definitions\.

### Remediation for rule failure<a name="ct-ecs-pr-3-remediation"></a>

Set the `User` property to a non\-root user user\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example<a name="ct-ecs-pr-3-remediation-1"></a>

Amazon ECS task definition configured with a container definition and a non\-root user user\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSTaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "Memory": "512",
            "ContainerDefinitions": [
                {
                    "Essential": true,
                    "Image": "nginx:latest",
                    "Name": "SampleContainerA",
                    "User": "sampleuser",
                    "Memory": 256
                }
            ]
        }
    }
}
```

**YAML example**

```
ECSTaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    Memory: '512'
    ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainerA
        User: sampleuser
        Memory: 256
```

### CT\.ECS\.PR\.3 rule specification<a name="ct-ecs-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_task_definition_nonroot_user_check
# 
# Description:
#   This control checks whether Amazon Elastic Container Service (ECS) task definitions run as a non-root user within Amazon ECS containers.
# 
# Reports on:
#   AWS::ECS::TaskDefinition
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document does not contain an ECS task definition resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' is not present or is an empty list
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present and is not an empty list
#       And: One or more containers defined in 'ContainerDefinitions' do not provide a 'User' property
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' is present and is not an empty list
#       And: One or more containers defined in 'ContainerDefinitions' has a 'User' property set to a root user
#            value (0, 'root', '0:<group>', 'root:<group>')
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' is present and is not an empty list
#       And: All containers defined in 'ContainerDefinitions' do not have a 'User' property set to a root user
#            value (0, 'root', '0:<group>', 'root:<group>')
#      Then: PASS

#
# Constants
#
let ECS_TASK_DEFINITION_TYPE = "AWS::ECS::TaskDefinition"
let INPUT_DOCUMENT = this
let ROOT_USER_PATTERNS = [ 0 , "0" , "root" , /^0:.*$/ , /^root:.*$/ ]

#
# Assignments
#
let ecs_task_definitions = Resources.*[ Type == %ECS_TASK_DEFINITION_TYPE ]

#
# Primary Rules
#
rule ecs_task_definition_nonroot_user_check when is_cfn_template(%INPUT_DOCUMENT)
                                                 %ecs_task_definitions not empty {
    check(%ecs_task_definitions.Properties)
        <<
        [CT.ECS.PR.3]: Require any Amazon ECS task definition to specify a user that is not the root
            [FIX]: Set the 'User' property to a non-root user.
        >>
}

rule ecs_task_definition_nonroot_user_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_TASK_DEFINITION_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_TASK_DEFINITION_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.3]: Require any Amazon ECS task definition to specify a user that is not the root
            [FIX]: Set the 'User' property to a non-root user.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_task_definition) {
    %ecs_task_definition [
        filter_container_definitions_is_present(this)
    ]{
        ContainerDefinitions[*] {
            # Scenario 3
            User exists

            # Scenario 4 and 5
            User not in %ROOT_USER_PATTERNS
        }
    }
}

rule filter_container_definitions_is_present(ecs_task_definition) {
    %ecs_task_definition {
        # Scenario 2
        ContainerDefinitions exists
        ContainerDefinitions is_list
        ContainerDefinitions not empty
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

### CT\.ECS\.PR\.3 example templates<a name="ct-ecs-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainerA
        User: exampleuser
        Memory: 256
      - Name: ExampleContainerB
        Image: alpine:latest
        User: exampleuser
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainerA
        User: exampleuser
        Memory: 256
      - Name: ExampleContainerB
        Image: alpine:latest
        User: root
```

## \[CT\.ECS\.PR\.4\] Require Amazon ECS tasks to use 'awsvpc' networking mode<a name="ct-ecs-pr-4-description"></a>

This control checks whether the networking mode for Amazon Elastic Container Service \(ECS\) task definitions is set to `awsvpc`\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::TaskDefinition`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.4 rule specification](#ct-ecs-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.4 rule specification](#ct-ecs-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.4 example templates](#ct-ecs-pr-4-templates) 

**Explanation**

Amazon ECS recommends using the `awsvpc` network mode, unless you have a specific reason to use a different network mode\. The `awsvpc` network mode simplifies container networking, and it gives you control over the ways that containerized applications communicate with each other, or with other services, within your VPCs\. The `awsvpc` network mode provides improved security for your containers, because it empowers you to use security groups and network monitoring tools at a detailed level within your tasks\. Each task has its own elastic network interface \(ENI\); therefore, you can include other Amazon EC2 networking features, such as VPC Flow Logs, which help you monitor traffic among your tasks\.

**Usage considerations**  
This control applies only to Amazon ECS task definitions for launch types of `EC2` and `Fargate`\.

### Remediation for rule failure<a name="ct-ecs-pr-4-remediation"></a>

Set `NetworkMode` to `awsvpc` for Amazon ECS tasks that deploy to Amazon EC2 or AWS Fargate\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example<a name="ct-ecs-pr-4-remediation-1"></a>

Amazon ECS task definition configured with `awsvpc` networking mode\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "Resources": {
        "ECSTaskDefinition": {
            "Type": "AWS::ECS::TaskDefinition",
            "Properties": {
                "ContainerDefinitions": [
                    {
                        "Essential": true,
                        "Image": "nginx:latest",
                        "Name": "SampleContainer"
                    }
                ],
                "Memory": 512,
                "NetworkMode": "awsvpc"
            }
        }
    }
}
```

**YAML example**

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
        - Essential: true
          Image: nginx:latest
          Name: SampleContainer
      Memory: 512
      NetworkMode: awsvpc
```

### CT\.ECS\.PR\.4 rule specification<a name="ct-ecs-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_awsvpc_networking_enabled_check
# 
# Description:
#   This control checks whether the networking mode for Amazon Elastic Container Service (ECS) task definitions is set to 'awsvpc'.
# 
# Reports on:
#   AWS::ECS::TaskDefinition
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document does not contain an ECS task definition resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'RequiresCompatibilities' is present and only has one entry in the list set to 'EXTERNAL'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'RequiresCompatibilities' is either not present or set to a list with entries that include 'EC2',
#            'FARGATE' or both.
#       And: 'NetworkMode' is not present
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'RequiresCompatibilities' is either not present or set to a list with entries that include 'EC2',
#            'FARGATE' or both.
#       And: 'NetworkMode' is present
#       And: 'NetworkMode' is not set to 'awsvpc'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'RequiresCompatibilities' is either not present or set to a list with entries that include 'EC2',
#            'FARGATE' or both.
#       And: 'NetworkMode' is present
#       And: 'NetworkMode' is set to 'awsvpc'
#      Then: PASS

#
# Constants
#
let ECS_TASK_DEFINITION_TYPE = "AWS::ECS::TaskDefinition"
let ALLOWED_NETWORK_MODES = [ "awsvpc" ]
let SUPPORTED_LAUNCH_PLATFORMS = [ "EC2" , "FARGATE" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecs_task_definitions = Resources.*[ Type == %ECS_TASK_DEFINITION_TYPE ]

#
# Primary Rules
#
rule ecs_awsvpc_networking_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                              %ecs_task_definitions not empty {
    check(%ecs_task_definitions.Properties)
        <<
        [CT.ECS.PR.4]: Require Amazon ECS tasks to use 'awsvpc' networking mode
            [FIX]: Set 'NetworkMode' to 'awsvpc' for Amazon ECS tasks that deploy to Amazon EC2 or AWS Fargate.
        >>
}

rule ecs_awsvpc_networking_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_TASK_DEFINITION_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_TASK_DEFINITION_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.4]: Require Amazon ECS tasks to use 'awsvpc' networking mode
            [FIX]: Set 'NetworkMode' to 'awsvpc' for Amazon ECS tasks that deploy to Amazon EC2 or AWS Fargate.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_task_definition) {
    %ecs_task_definition [ filter_external_task_definitions(this) ] {
        # Scenario 3
        NetworkMode exists

        # Scenario 4 and 5
        NetworkMode is_string
        NetworkMode in %ALLOWED_NETWORK_MODES
    }
}

rule filter_external_task_definitions(ecs_task_definition) {
    %ecs_task_definition {
        # Scenario 2
        RequiresCompatibilities not exists or
        filter_supported_task_definitions(RequiresCompatibilities)
    }
}

rule filter_supported_task_definitions(requires_compatibilities) {
    %requires_compatibilities {
        this is_list
        this not empty
        some this[*] in %SUPPORTED_LAUNCH_PLATFORMS
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

### CT\.ECS\.PR\.4 example templates<a name="ct-ecs-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainer
      Memory: 512
      NetworkMode: awsvpc
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainer
      Memory: 512
```

## \[CT\.ECS\.PR\.5\] Require an active Amazon ECS task definition to have a logging configuration<a name="ct-ecs-pr-5-description"></a>

This control checks whether Amazon Elastic Container Service \(ECS\) task definitions have a logging configuration specified\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::TaskDefinition`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.5 rule specification](#ct-ecs-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.5 rule specification](#ct-ecs-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.5 example templates](#ct-ecs-pr-5-templates) 

**Explanation**

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon Elastic Container Service \(ECS\) and your AWS environments\. We recommend that you collect monitoring data from all parts of your AWS environment, because this data can help you debug a multi\-point failure, if such a failure occurs\.

**Usage considerations**  
This control applies only to Amazon ECS task definitions that are configured with container definitions\.

### Remediation for rule failure<a name="ct-ecs-pr-5-remediation"></a>

For each container definition, within `LogConfiguration` set the `LogDriver` property to a supported log driver\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example<a name="ct-ecs-pr-5-remediation-1"></a>

Amazon ECS task definition configured to send log information to Amazon CloudWatch Logs for each container definition\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSTaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "Memory": "512",
            "ContainerDefinitions": [
                {
                    "Name": "ContainerA",
                    "Image": "nginx:latest",
                    "Essential": true,
                    "LogConfiguration": {
                        "LogDriver": "awslogs",
                        "Options": {
                            "awslogs-group": {
                                "Ref": "LogGroup"
                            },
                            "awslogs-stream-prefix": "Container-A-LogStream",
                            "awslogs-region": {
                                "Ref": "AWS::Region"
                            }
                        }
                    }
                },
                {
                    "Name": "ContainerB",
                    "Image": "nginx:latest",
                    "LogConfiguration": {
                        "LogDriver": "awslogs",
                        "Options": {
                            "awslogs-group": {
                                "Ref": "LogGroup"
                            },
                            "awslogs-stream-prefix": "Container-B-LogStream",
                            "awslogs-region": {
                                "Ref": "AWS::Region"
                            }
                        }
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
ECSTaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    Memory: '512'
    ContainerDefinitions:
      - Name: ContainerA
        Image: nginx:latest
        Essential: true
        LogConfiguration:
          LogDriver: awslogs
          Options:
            awslogs-group: !Ref 'LogGroup'
            awslogs-stream-prefix: Container-A-LogStream
            awslogs-region: !Ref 'AWS::Region'
      - Name: ContainerB
        Image: nginx:latest
        LogConfiguration:
          LogDriver: awslogs
          Options:
            awslogs-group: !Ref 'LogGroup'
            awslogs-stream-prefix: Container-B-LogStream
            awslogs-region: !Ref 'AWS::Region'
```

### CT\.ECS\.PR\.5 rule specification<a name="ct-ecs-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_task_definition_log_configuration_check
# 
# Description:
#   This control checks whether Amazon Elastic Container Service (ECS) task definitions have a logging configuration specified.
# 
# Reports on:
#   AWS::ECS::TaskDefinition
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document does not contain an ECS task definition resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is not present or is an empty list
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present and is not an empty list
#       And: One or more containers defined  in 'ContainerDefinitions' do not have 'LogConfiguration' set or it is set
#            to an empty struct
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present and is not an empty list
#       And: One or more containers defined  in 'ContainerDefinitions' have 'LogConfiguration' property present
#       And: 'LogConfiguration.LogDriver' is not present or is set to an empty string
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present
#       And: All containers defined  in 'ContainerDefinitions' have 'LogConfiguration' property present
#       And: 'LogConfiguration.LogDriver' is present and set to a non-empty string
#      Then: PASS

#
# Constants
#
let ECS_TASK_DEFINITION_TYPE = "AWS::ECS::TaskDefinition"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecs_task_definitions = Resources.*[ Type == %ECS_TASK_DEFINITION_TYPE ]

#
# Primary Rules
#
rule ecs_task_definition_log_configuration_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %ecs_task_definitions not empty {
    check(%ecs_task_definitions.Properties)
        <<
        [CT.ECS.PR.5]: Require an active Amazon ECS task definition to have a logging configuration
            [FIX]: For each container definition, within 'LogConfiguration' set the 'LogDriver' property to a supported log driver.
        >>
}

rule ecs_task_definition_log_configuration_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_TASK_DEFINITION_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_TASK_DEFINITION_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.5]: Require an active Amazon ECS task definition to have a logging configuration
            [FIX]: For each container definition, within 'LogConfiguration' set the 'LogDriver' property to a supported log driver.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_task_definition) {
    %ecs_task_definition [
        filter_container_definitions_is_present(this)
    ]{
        ContainerDefinitions[*] {
            # Scenario 3
            LogConfiguration exists
            LogConfiguration is_struct
            LogConfiguration not empty

            # Scenario 4 and 5
            LogConfiguration {
                LogDriver exists
                check_is_string_and_not_empty(LogDriver)
            }
        }
    }
}

rule filter_container_definitions_is_present(ecs_task_definition) {
    %ecs_task_definition {
        # Scenario 2
        ContainerDefinitions exists
        ContainerDefinitions is_list
        ContainerDefinitions not empty
    }
}

#
# Utility Rules
#
rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
}

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

### CT\.ECS\.PR\.5 example templates<a name="ct-ecs-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  LogGroup:
    Type: AWS::Logs::LogGroup
    DeletionPolicy: Delete
    UpdateReplacePolicy: Delete
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      ContainerDefinitions:
      - Name: ContainerA
        LogConfiguration:
          LogDriver: awslogs
          Options:
            awslogs-group:
              Ref: LogGroup
            awslogs-stream-prefix: Container-A-LogStream
            awslogs-region:
              Ref: AWS::Region
        Essential: true
        Image: nginx:latest
      - Name: ContainerB
        LogConfiguration:
          LogDriver: awslogs
          Options:
            awslogs-group:
              Ref: LogGroup
            awslogs-stream-prefix: Container-B-LogStream
            awslogs-region:
              Ref: AWS::Region
        Image: nginx:latest
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  LogGroup:
    Type: AWS::Logs::LogGroup
    DeletionPolicy: Delete
    UpdateReplacePolicy: Delete
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      ContainerDefinitions:
      - Name: ContainerA
        Essential: true
        Image: nginx:latest
      - Name: ContainerB
        Image: nginx:latest
        LogConfiguration:
          LogDriver: awslogs
          Options:
            awslogs-group:
              Ref: LogGroup
            awslogs-stream-prefix: Container-B-LogStream
            awslogs-region:
              Ref: AWS::Region
```

## \[CT\.ECS\.PR\.6\] Require Amazon ECS containers to allow read\-only access to the root filesystem<a name="ct-ecs-pr-6-description"></a>

This control checks whether Amazon Elastic Container Service \(Amazon ECS\) task definitions have been configured to require read\-only access to container root filesystems\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::TaskDefinition`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.6 rule specification](#ct-ecs-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.6 rule specification](#ct-ecs-pr-6-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.6 example templates](#ct-ecs-pr-6-templates) 

**Explanation**

Enabling this option reduces security attack vectors\. When it is enabled, the container instance's filesystem cannot be tampered with or written to unless it has explicitly granted read\-write permissions on its filesystem folder and directories\. This control adheres to the principle of least privilege\.

**Usage considerations**  
This control is incompatible with Amazon ECS task definitions that use Windows containers\.

### Remediation for rule failure<a name="ct-ecs-pr-6-remediation"></a>

Set the `ReadonlyRootFilesystem` property to `true` for all `ContainerDefinitions`\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example<a name="ct-ecs-pr-6-remediation-1"></a>

Amazon ECS task definition with read\-only access to container root filesystems\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSTaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "ContainerDefinitions": [
                {
                    "Essential": true,
                    "Image": "nginx:latest",
                    "Name": "SampleContainerA",
                    "ReadonlyRootFilesystem": true
                },
                {
                    "Image": "alpine:latest",
                    "Name": "SampleContainerB",
                    "ReadonlyRootFilesystem": true
                }
            ],
            "Memory": "512"
        }
    }
}
```

**YAML example**

```
ECSTaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainerA
        ReadonlyRootFilesystem: true
      - Image: alpine:latest
        Name: SampleContainerB
        ReadonlyRootFilesystem: true
    Memory: '512'
```

### CT\.ECS\.PR\.6 rule specification<a name="ct-ecs-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# Rule Identifier:
#   ecs_containers_readonly_access_check
# 
# Description:
#   This control checks whether Amazon Elastic Container Service (Amazon ECS) task definitions have been configured to require read-only access to container root filesystems.
# 
# Reports on:
#   AWS::ECS::TaskDefinition
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
# Scenario: 1
#   Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#     And: The input document does not contain an Amazon ECS task definition resource
#    Then: SKIP
# Scenario: 2
#   Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#     And: The input document contains an Amazon ECS task definition resource
#     And: 'ContainerDefinitions' property is not present or is empty
#    Then: SKIP
# Scenario: 3
#   Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#     And: The input document contains an Amazon ECS task definition resource
#     And: 'ContainerDefinitions' property is present
#     And: One or more containers defined  in 'ContainerDefinitions' do not have 'ReadonlyRootFilesystem' present
#    Then: FAIL
# Scenario: 4
#   Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#     And: The input document contains an Amazon ECS task definition resource
#     And: 'ContainerDefinitions' property is present
#     And: One or more containers defined  in 'ContainerDefinitions' have the value of 'ReadonlyRootFilesystem' set to
#          bool(false)
#     Then: FAIL
# Scenario: 5
#   Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#     And: The input document contains an Amazon ECS task definition resource
#     And: 'ContainerDefinitions' property is present
#     And: All containers defined  in 'ContainerDefinitions' have the value of 'ReadonlyRootFilesystem' set to
#          bool(true)
#    Then: PASS

#
# Constants
#
let ECS_TASK_DEFINITION_TYPE = "AWS::ECS::TaskDefinition"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecs_task_definitions = Resources.*[ Type == %ECS_TASK_DEFINITION_TYPE ]

#
# Primary Rules
#
rule ecs_containers_readonly_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %ecs_task_definitions not empty {
    check(%ecs_task_definitions.Properties)
        <<
        [CT.ECS.PR.6]: Require Amazon ECS containers to allow read-only access to the root filesystem
        [FIX]: Set the 'ReadonlyRootFilesystem' property to 'true' for all 'ContainerDefinitions'.
        >>
}

rule ecs_containers_readonly_access_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_TASK_DEFINITION_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_TASK_DEFINITION_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.6]: Require Amazon ECS containers to allow read-only access to the root filesystem
        [FIX]: Set the 'ReadonlyRootFilesystem' property to 'true' for all 'ContainerDefinitions'.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_task_definition) {
    %ecs_task_definition [ filter_container_definitions_is_present(this) ]{
        ContainerDefinitions[*] {
            # Scenario 3
            ReadonlyRootFilesystem exists

            # Scenario 4
            ReadonlyRootFilesystem == true
        }
    }
}

rule filter_container_definitions_is_present(ecs_task_definition) {
    %ecs_task_definition {
        # Scenario 2
        ContainerDefinitions exists
        ContainerDefinitions is_list
        ContainerDefinitions not empty
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

### CT\.ECS\.PR\.6 example templates<a name="ct-ecs-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainerA
        ReadonlyRootFilesystem: true
      - Image: alpine:latest
        Name: SampleContainerB
        ReadonlyRootFilesystem: true
      Memory: '512'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainerA
        ReadonlyRootFilesystem: false
      - Image: alpine:latest
        Name: SampleContainerB
        ReadonlyRootFilesystem: false
      Memory: '512'
```

## \[CT\.ECS\.PR\.7\] Require an Amazon ECS task definition to have a specific memory usage limit<a name="ct-ecs-pr-7-description"></a>

This control checks whether Amazon Elastic Container Service \(ECS\) task definitions have specified a memory limit for container definitions\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::TaskDefinition`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.7 rule specification](#ct-ecs-pr-7-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.7 rule specification](#ct-ecs-pr-7-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.7 example templates](#ct-ecs-pr-7-templates) 

**Explanation**

We recommend that you specify the maximum memory available to your containers, because this limit protects your resources in the event of malicious access to your containers\.

**Usage considerations**  
This control applies only to Amazon ECS task definitions that are configured with container definitions\.

### Remediation for rule failure<a name="ct-ecs-pr-7-remediation"></a>

Set the `Memory` property in `ContainerDefinitions` for Amazon ECS task definitions\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example<a name="ct-ecs-pr-7-remediation-1"></a>

Amazon ECS task definition configured with a specified memory limit for container definitions\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSTaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "ContainerDefinitions": [
                {
                    "Essential": true,
                    "Image": "nginx:latest",
                    "Name": "SampleContainer",
                    "Memory": 256
                }
            ]
        }
    }
}
```

**YAML example**

```
ECSTaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainer
        Memory: 256
```

### CT\.ECS\.PR\.7 rule specification<a name="ct-ecs-pr-7-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_task_definition_memory_hard_limit_check
# 
# Description:
#   This control checks whether Amazon Elastic Container Service (ECS) task definitions have specified a memory limit for container definitions.
# 
# Reports on:
#   AWS::ECS::TaskDefinition
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document does not contain an ECS task definition resource
#      Then: SKIP
#   Scenario: 2
#    Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#      And: The input document contains an ECS task definition resource
#      And: 'ContainerDefinitions' property is not present or is an empty list
#     Then: SKIP
#   Scenario: 3
#    Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#      And: The input document contains an ECS task definition resource
#      And: 'ContainerDefinitions' property is present and is not an empty list
#      And: One or more containers defined in 'ContainerDefinitions' do not have 'Memory' property set
#     Then: FAIL
#   Scenario: 4
#    Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#      And: The input document contains an ECS task definition resource
#      And: 'ContainerDefinitions' property is present and is not an empty list
#      And: One or more containers defined in 'ContainerDefinitions' have 'Memory' property set to an integer
#           value less than four (< 4)
#     Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present
#       And: All containers defined in 'ContainerDefinitions' have 'Memory' property set to an integer value
#            greater than or equal to four (>= 4)
#      Then: PASS

#
# Constants
#
let ECS_TASK_DEFINITION_TYPE = "AWS::ECS::TaskDefinition"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecs_task_definitions = Resources.*[ Type == %ECS_TASK_DEFINITION_TYPE ]

#
# Primary Rules
#
rule ecs_task_definition_memory_hard_limit_check when is_cfn_template(%INPUT_DOCUMENT)
                                                 %ecs_task_definitions not empty {
    check(%ecs_task_definitions.Properties)
        <<
        [CT.ECS.PR.7]: Require an Amazon ECS task definition to have a specific memory usage limit
            [FIX]: Set the 'Memory' property in 'ContainerDefinitions' for Amazon ECS task definitions.
        >>
}

rule ecs_task_definition_memory_hard_limit_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_TASK_DEFINITION_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_TASK_DEFINITION_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.7]: Require an Amazon ECS task definition to have a specific memory usage limit
            [FIX]: Set the 'Memory' property in 'ContainerDefinitions' for Amazon ECS task definitions.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_task_definition) {
    %ecs_task_definition [filter_container_definitions_is_present(this)]{
        ContainerDefinitions[*] {
            # Scenario 3
            Memory exists

            # Scenario 4 and 5
            Memory >= 4
        }
    }
}

rule filter_container_definitions_is_present(ecs_task_definition) {
    %ecs_task_definition {
        # Scenario 2
        ContainerDefinitions exists
        ContainerDefinitions is_list
        ContainerDefinitions not empty
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

### CT\.ECS\.PR\.7 example templates<a name="ct-ecs-pr-7-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainerA
        Memory: 256
      - Image: alpine:latest
        Name: ExampleContainerB
        Memory: 512
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: "512"
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainerA
        Memory: 256
      - Image: alpine:latest
        Name: ExampleContainerB
        Memory: 3
```

## \[CT\.ECS\.PR\.8\] Require Amazon ECS task definitions to have secure networking modes and user definitions<a name="ct-ecs-pr-8-description"></a>

This control checks whether Amazon Elastic Container Service \(ECS\) task definitions that use `host` networking mode have a privileged container definition, and whether they specify a non\-root user definition\.
+ **Control objective: **Manage vulnerabilities
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::TaskDefinition`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.8 rule specification](#ct-ecs-pr-8-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.8 rule specification](#ct-ecs-pr-8-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.8 example templates](#ct-ecs-pr-8-templates) 

**Explanation**

If a task definition has elevated privileges, it implies that a customer has specifically chosen that configuration\. This control checks for unexpected privilege escalation, which occurs when a task definition enables host networking, but a customer has not opted into elevated privileges\.

**Usage considerations**  
This control applies only to Amazon ECS task definitions that include `host` networking mode and one or more container definitions\.
This control is incompatible with Amazon ECS task definitions that use Windows containers\.

### Remediation for rule failure<a name="ct-ecs-pr-8-remediation"></a>

For Amazon ECS task definitions that use `host` networking mode, your container definitions must set the `User` property to a non\-root user\. Also, to opt into elevated privileges, configure containers to run in privileged mode by setting the `Privileged` property to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example One<a name="ct-ecs-pr-8-remediation-1"></a>

Amazon ECS task definition with host networking mode configured for privileged container definitions\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSTaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "Memory": "512",
            "NetworkMode": "host",
            "ContainerDefinitions": [
                {
                    "Name": "SampleContainerA",
                    "User": "root",
                    "Privileged": true,
                    "Image": "nginx:latest",
                    "Essential": true
                },
                {
                    "Name": "SampleContainerB",
                    "User": "root",
                    "Privileged": true,
                    "Image": "alpine:latest"
                }
            ]
        }
    }
}
```

**YAML example**

```
ECSTaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    Memory: '512'
    NetworkMode: host
    ContainerDefinitions:
      - Name: SampleContainerA
        User: root
        Privileged: true
        Image: nginx:latest
        Essential: true
      - Name: SampleContainerB
        User: root
        Privileged: true
        Image: alpine:latest
```

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example Two<a name="ct-ecs-pr-8-remediation-2"></a>

Amazon ECS task definition with host networking mode configured for non\-root user container definitions and privileged mode deactivated, by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSTaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "Memory": "512",
            "NetworkMode": "host",
            "ContainerDefinitions": [
                {
                    "Name": "SampleContainerA",
                    "User": "root",
                    "Privileged": true,
                    "Image": "nginx:latest",
                    "Essential": true
                },
                {
                    "Name": "SampleContainerB",
                    "User": "root",
                    "Privileged": true,
                    "Image": "alpine:latest"
                }
            ]
        }
    }
}
```

**YAML example**

```
ECSTaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    Memory: '512'
    NetworkMode: host
    ContainerDefinitions:
      - Name: SampleContainerA
        User: root
        Privileged: true
        Image: nginx:latest
        Essential: true
      - Name: SampleContainerB
        User: root
        Privileged: true
        Image: alpine:latest
```

### CT\.ECS\.PR\.8 rule specification<a name="ct-ecs-pr-8-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_task_definition_user_for_host_mode_check
# 
# Description:
#   This control checks whether Amazon Elastic Container Service (ECS) task definitions that use 'host' networking mode have a privileged container definition, and whether they specify a non-root user definition.
# 
# Reports on:
#   AWS::ECS::TaskDefinition
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
#       And: The input document does not contain an ECS task definition resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is not present or is an empty list
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present and is not an empty list
#       And: 'NetworkMode' property is either not present or set to a value other than 'host'
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present and is not an empty list
#       And: 'NetworkMode' property is present and set to 'host'
#       And: A container defined in 'ContainerDefinitions' has 'Privileged' property not set or is set as bool(false)
#       And: This same container either does not have the 'User' property set or has it set to a value that translates
#            to root user
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present
#       And: 'NetworkMode' property is present and set to 'host'
#       And: All Containers defined in 'ContainerDefinitions' either have the 'Privileged' property set to bool(true)
#            or have their 'User' property set to a value that does not translate to root user
#      Then: PASS

#
# Constants
#
let ECS_TASK_DEFINITION_TYPE = "AWS::ECS::TaskDefinition"
let INPUT_DOCUMENT = this
let ROOT_USER_PATTERNS = [ 0 , "0" , "root" , /^0:.*$/ , /^root:.*$/ ]
let VALID_NETWORK_MODES = [ "host" ]

#
# Assignments
#
let ecs_task_definitions = Resources.*[ Type == %ECS_TASK_DEFINITION_TYPE ]

#
# Primary Rules
#
rule ecs_task_definition_user_for_host_mode_check when is_cfn_template(%INPUT_DOCUMENT)
                                                       %ecs_task_definitions not empty {
    check(%ecs_task_definitions.Properties)
        <<
        [CT.ECS.PR.8]: Require Amazon ECS task definitions to have secure networking modes and user definitions
            [FIX]: For Amazon ECS task definitions that use 'host' networking mode, your container definitions must set the 'User' property to a non-root user. Also, to opt into elevated privileges, configure containers to run in privileged mode by setting the  'Privileged' property to 'true'.
        >>
}

rule ecs_task_definition_user_for_host_mode_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_TASK_DEFINITION_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_TASK_DEFINITION_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.8]: Require Amazon ECS task definitions to have secure networking modes and user definitions
            [FIX]: For Amazon ECS task definitions that use 'host' networking mode, your container definitions must set the 'User' property to a non-root user. Also, to opt into elevated privileges, configure containers to run in privileged mode by setting the  'Privileged' property to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_task_definition) {
    %ecs_task_definition [
        filter_nw_mode_container_definitions(this)
    ]{
        ContainerDefinitions[*] {
            # Scenario 4 and 5
            check_elevated_privilege_containers(this) or
            check_nonroot_user_containers(this)
        }
    }
}

rule check_elevated_privilege_containers(container_definition) {
    %container_definition {
        Privileged exists
        Privileged == true
    }
}

rule check_nonroot_user_containers(container_definition) {
    %container_definition {
        User exists
        User not in %ROOT_USER_PATTERNS
    }
}

rule filter_nw_mode_container_definitions(ecs_task_definition) {
    %ecs_task_definition {
        # Scenario 2
        ContainerDefinitions exists
        ContainerDefinitions is_list
        ContainerDefinitions not empty

        # Scenario 3
        NetworkMode exists
        NetworkMode is_string
        NetworkMode in %VALID_NETWORK_MODES
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

### CT\.ECS\.PR\.8 example templates<a name="ct-ecs-pr-8-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      NetworkMode: host
      ContainerDefinitions:
      - Name: ExampleContainerA
        User: root
        Privileged: true
        Image: nginx:latest
        Essential: true
      - Name: ExampleContainerB
        User: root
        Privileged: true
        Image: alpine:latest
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      NetworkMode: host
      ContainerDefinitions:
      - Name: ExampleContainerA
        User: root
        Privileged: true
        Image: nginx:latest
        Essential: true
      - Name: ExampleContainerB
        Image: alpine:latest
        User: root
```

## \[CT\.ECS\.PR\.9\] Require Amazon ECS services not to assign public IP addresses automatically<a name="ct-ecs-pr-9-description"></a>

This control checks whether your Amazon Elastic Container Service \(Amazon ECS\) service resources are configured to assign public IP addresses automatically\.
+ **Control objective: **Limit network access, Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::Service`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.9 rule specification](#ct-ecs-pr-9-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.9 rule specification](#ct-ecs-pr-9-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.9 example templates](#ct-ecs-pr-9-templates) 

**Explanation**

A public IP address is an IP address that is reachable from the internet\. If you launch your Amazon ECS instances with a public IP address, then your Amazon ECS instances are reachable from the internet\. Amazon ECS services should not be publicly accessible, because it may allow unintended access to your container application servers\.

### Remediation for rule failure<a name="ct-ecs-pr-9-remediation"></a>

Set `AssignPublicIp` in `NetworkConfiguration.AwsvpcConfiguration` to `DISABLED`\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Service \- Example One<a name="ct-ecs-pr-9-remediation-1"></a>

Amazon ECS service configured to disallow automatic public IP address assignment, by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSService": {
        "Type": "AWS::ECS::Service",
        "Properties": {
            "Cluster": {
                "Ref": "ECSCluster"
            },
            "DesiredCount": 0,
            "TaskDefinition": {
                "Ref": "ECSTaskDefinition"
            },
            "LaunchType": "FARGATE",
            "NetworkConfiguration": {
                "AwsvpcConfiguration": {
                    "Subnets": [
                        {
                            "Ref": "SubnetOne"
                        },
                        {
                            "Ref": "SubnetTwo"
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
ECSService:
  Type: AWS::ECS::Service
  Properties:
    Cluster: !Ref 'ECSCluster'
    DesiredCount: 0
    TaskDefinition: !Ref 'ECSTaskDefinition'
    LaunchType: FARGATE
    NetworkConfiguration:
      AwsvpcConfiguration:
        Subnets:
          - !Ref 'SubnetOne'
          - !Ref 'SubnetTwo'
```

The examples that follow show how to implement this remediation\.

#### Amazon ECS Service \- Example Two<a name="ct-ecs-pr-9-remediation-2"></a>

Amazon ECS service configured to disallow automatic public IP address assignment, by means of the `AssignPublicIp` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSService": {
        "Type": "AWS::ECS::Service",
        "Properties": {
            "Cluster": {
                "Ref": "ECSCluster"
            },
            "DesiredCount": 0,
            "TaskDefinition": {
                "Ref": "ECSTaskDefinition"
            },
            "LaunchType": "FARGATE",
            "NetworkConfiguration": {
                "AwsvpcConfiguration": {
                    "AssignPublicIp": "DISABLED",
                    "Subnets": [
                        {
                            "Ref": "SubnetOne"
                        },
                        {
                            "Ref": "SubnetTwo"
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
ECSService:
  Type: AWS::ECS::Service
  Properties:
    Cluster: !Ref 'ECSCluster'
    DesiredCount: 0
    TaskDefinition: !Ref 'ECSTaskDefinition'
    LaunchType: FARGATE
    NetworkConfiguration:
      AwsvpcConfiguration:
        AssignPublicIp: DISABLED
        Subnets:
          - !Ref 'SubnetOne'
          - !Ref 'SubnetTwo'
```

### CT\.ECS\.PR\.9 rule specification<a name="ct-ecs-pr-9-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_service_assign_public_ip_disabled_check
# 
# Description:
#   This control checks whether your Amazon Elastic Container Service (Amazon ECS) service resources are configured to assign public IP addresses automatically.
# 
# Reports on:
#   AWS::ECS::Service
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
#       And: The input document does not contain an Amazon ECS service resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'NetworkConfiguration' property is not present
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'NetworkConfiguration.AwsvpcConfiguration' property is not present
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'NetworkConfiguration.AwsvpcConfiguration' property is present
#       And: 'AssignPublicIp' property is present and set to 'ENABLED'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'NetworkConfiguration.AwsvpcConfiguration' property is present
#       And: 'AssignPublicIp' property is not present
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECS service resource
#       And: 'NetworkConfiguration.AwsvpcConfiguration' property is present
#       And: 'AssignPublicIp' property is present and set to 'DISABLED'
#      Then: PASS

#
# Constants
#
let ECS_SERVICE_TYPE = "AWS::ECS::Service"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecs_services = Resources.*[ Type == %ECS_SERVICE_TYPE ]

#
# Primary Rules
#
rule ecs_service_assign_public_ip_disabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %ecs_services not empty {
    check(%ecs_services.Properties)
        <<
        [CT.ECS.PR.9]: Require Amazon ECS services not to assign public IP addresses automatically
        [FIX]: Set 'AssignPublicIp' in 'NetworkConfiguration.AwsvpcConfiguration' to 'DISABLED'.
        >>
}

rule ecs_service_assign_public_ip_disabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_SERVICE_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_SERVICE_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.9]: Require Amazon ECS services not to assign public IP addresses automatically
        [FIX]: Set 'AssignPublicIp' in 'NetworkConfiguration.AwsvpcConfiguration' to 'DISABLED'.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_service) {
    %ecs_service [filter_ecs_service_with_vpc_configuration(this)] {
        NetworkConfiguration {
            AwsvpcConfiguration {
                # Scenario 5
                AssignPublicIp not exists or

                # Scenario 6
                check_assign_public_ip_property(AssignPublicIp)
            }
        }
    }
}

rule check_assign_public_ip_property(public_ip) {
    %public_ip {
        this is_string
        this == "DISABLED"
    }
}

rule filter_ecs_service_with_vpc_configuration(ecs_service) {
    %ecs_service {
        # Scenario 2
        NetworkConfiguration exists
        NetworkConfiguration is_struct

        # Scenarion 3 and 4
        NetworkConfiguration {
            AwsvpcConfiguration exists
            AwsvpcConfiguration is_struct
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

### CT\.ECS\.PR\.9 example templates<a name="ct-ecs-pr-9-templates"></a>

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
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
  ECSCluster:
    Type: AWS::ECS::Cluster
    Properties:
      CapacityProviders:
        - FARGATE
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainer
      Memory: '512'
      RequiresCompatibilities:
        - FARGATE
      NetworkMode: awsvpc
      Cpu: 256
  ECSService:
    Type: AWS::ECS::Service
    Properties:
      Cluster:
        Ref: ECSCluster
      DesiredCount: 0
      TaskDefinition:
        Ref: ECSTaskDefinition
      NetworkConfiguration:
        AwsvpcConfiguration:
          Subnets:
           - Ref: SubnetOne
           - Ref: SubnetTwo
      LaunchType: FARGATE
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
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
  ECSCluster:
    Type: AWS::ECS::Cluster
    Properties:
      CapacityProviders:
        - FARGATE
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainer
      Memory: '512'
      RequiresCompatibilities:
        - FARGATE
      NetworkMode: awsvpc
      Cpu: 256
  ECSService:
    Type: AWS::ECS::Service
    Properties:
      Cluster:
        Ref: ECSCluster
      DesiredCount: 0
      TaskDefinition:
        Ref: ECSTaskDefinition
      NetworkConfiguration:
        AwsvpcConfiguration:
          AssignPublicIp: ENABLED
          Subnets:
           - Ref: SubnetOne
           - Ref: SubnetTwo
      LaunchType: FARGATE
```

## \[CT\.ECS\.PR\.10\] Require that Amazon ECS task definitions do not share the host's process namespace<a name="ct-ecs-pr-10-description"></a>

This control checks whether Amazon Elastic Container Service \(ECS\) task definitions are configured to share a host's process namespace with its containers\.
+ **Control objective: **Protect configurations, Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::TaskDefinition`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.10 rule specification](#ct-ecs-pr-10-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.10 rule specification](#ct-ecs-pr-10-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.10 example templates](#ct-ecs-pr-10-templates) 

**Explanation**

A process ID \(PID\) namespace provides separation between processes\. It prevents system processes from being visible to other processes, and it allows PIDs to be reused, including PID 1\. If the host`s PID namespace is shared with containers, those containers can see all of the processes on the host system. Process visibility reduces the benefit of process-level isolation between the host and the containers. Reduced isolation can allow unauthorized access to processes on the host itself, including the ability to manipulate and terminate the host`s processes\. As a best practice, do not share the host's process namespace with containers running on the host\.

**Usage considerations**  
This control applies only to Amazon ECS task definitions that are configured with container definitions\.
This control is not compatible with Amazon ECS task definitions that are configured to run on AWS Fargate, or definitions that use Windows containers\.

### Remediation for rule failure<a name="ct-ecs-pr-10-remediation"></a>

Omit the `PidMode` property, or set `PidMode` to `task`\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example One<a name="ct-ecs-pr-10-remediation-1"></a>

Amazon ECS task definition configured with a task\-level process namespace, by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "TaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "Memory": "512",
            "ContainerDefinitions": [
                {
                    "Essential": true,
                    "Image": "nginx:latest",
                    "Name": "SampleContainer"
                }
            ]
        }
    }
}
```

**YAML example**

```
TaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    Memory: '512'
    ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainer
```

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example Two<a name="ct-ecs-pr-10-remediation-2"></a>

Amazon ECS task definition configured with a task\-level process namespace, by means of the `PidMode` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "TaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "Memory": "512",
            "ContainerDefinitions": [
                {
                    "Essential": true,
                    "Image": "nginx:latest",
                    "Name": "SampleContainer"
                }
            ],
            "PidMode": "task"
        }
    }
}
```

**YAML example**

```
TaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    Memory: '512'
    ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainer
    PidMode: task
```

### CT\.ECS\.PR\.10 rule specification<a name="ct-ecs-pr-10-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_task_definition_pid_mode_check
# 
# Description:
#   This control checks whether Amazon Elastic Container Service (ECS) task definitions are configured to share a host's process namespace with its containers.
# 
# Reports on:
#   AWS::ECS::TaskDefinition
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
#       And: The input document does not contain an ECS task definition resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'PidMode' is provided as an empty string
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'PidMode' is set to 'host'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'PidMode' is not present
#      Then: PASS
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'PidMode' is provided as a non-empty string that is not 'host'
#      Then: PASS

#
# Constants
#
let ECS_TASK_DEFINITION_TYPE = "AWS::ECS::TaskDefinition"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecs_task_definitions = Resources.*[ Type == %ECS_TASK_DEFINITION_TYPE ]

#
# Primary Rules
#
rule ecs_task_definition_pid_mode_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %ecs_task_definitions not empty {
    check(%ecs_task_definitions.Properties)
        <<
        [CT.ECS.PR.10]: Require that Amazon ECS task definitions do not share the host's process namespace
            [FIX]: Omit the 'PidMode' property, or set 'PidMode' to 'task'.
        >>
}

rule ecs_task_definition_pid_mode_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_TASK_DEFINITION_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_TASK_DEFINITION_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.10]: Require that Amazon ECS task definitions do not share the host's process namespace
            [FIX]: Omit the 'PidMode' property, or set 'PidMode' to 'task'.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_task_definition) {
    %ecs_task_definition {
        # Scenario 2
        PidMode not exists or

        # Scenario 3 and 4
        check_pidmode_value(PidMode)
    }
}

rule check_pidmode_value(pid_mode) {
    %pid_mode {
        check_is_string_and_not_empty(this)
        this != "host"
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

rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
}
```

### CT\.ECS\.PR\.10 example templates<a name="ct-ecs-pr-10-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  TaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainer
      PidMode: task
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  TaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainer
      PidMode: host
```

## \[CT\.ECS\.PR\.11\] Require an Amazon ECS container to run as non\-privileged<a name="ct-ecs-pr-11-description"></a>

This control checks whether container definitions in Amazon Elastic Container Service \(ECS\) task definitions are configured with elevated privileges\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::TaskDefinition`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.11 rule specification](#ct-ecs-pr-11-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.11 rule specification](#ct-ecs-pr-11-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.11 example templates](#ct-ecs-pr-11-templates) 

**Explanation**

We recommend that you remove elevated privileges from your Amazon ECS task definitions\. When the privilege parameter is `true`, the container can operate with elevated privileges on the host container instance\. These privileges are similar to the root user privileges\.

**Usage considerations**  
This control applies only to Amazon ECS task definitions that are configured with container definitions\.
This control is incompatible with Amazon ECS task definitions that use Windows containers\.

### Remediation for rule failure<a name="ct-ecs-pr-11-remediation"></a>

Be sure that all containers defined in `ContainerDefinitions` either omit the `Privileged` property, or that they set `Privileged` to `false`\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example<a name="ct-ecs-pr-11-remediation-1"></a>

Amazon ECS task definition configured with privileged mode deactivated for container definitions\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSTaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "ContainerDefinitions": [
                {
                    "Essential": true,
                    "Image": "alpine:latest",
                    "Name": "SampleContainerA"
                },
                {
                    "Image": "nginx:latest",
                    "Name": "SampleContainerB",
                    "Privileged": false
                }
            ],
            "Memory": "512"
        }
    }
}
```

**YAML example**

```
ECSTaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    ContainerDefinitions:
      - Essential: true
        Image: alpine:latest
        Name: SampleContainerA
      - Image: nginx:latest
        Name: SampleContainerB
        Privileged: false
    Memory: '512'
```

### CT\.ECS\.PR\.11 rule specification<a name="ct-ecs-pr-11-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_containers_nonprivileged_check
# 
# Description:
#   This control checks whether container definitions in Amazon Elastic Container Service (ECS) task definitions are configured with elevated privileges.
# 
# Reports on:
#   AWS::ECS::TaskDefinition
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
#       And: The input document does not contain an ECS task definition resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is not present or is an empty list
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present
#       And: One or more containers defined  in 'ContainerDefinitions' have 'Privileged' set to bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECS task definition resource
#       And: 'ContainerDefinitions' property is present
#       And: All containers defined in 'ContainerDefinitions' either do not have the 'Privileged' property present or
#            'Privileged' is present and set to bool(false)
#      Then: PASS

#
# Constants
#
let ECS_TASK_DEFINITION_TYPE = "AWS::ECS::TaskDefinition"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecs_task_definitions = Resources.*[ Type == %ECS_TASK_DEFINITION_TYPE ]

#
# Primary Rules
#
rule ecs_containers_nonprivileged_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %ecs_task_definitions not empty {
    check(%ecs_task_definitions.Properties)
        <<
        [CT.ECS.PR.11]: Require an Amazon ECS container to run as non-privileged
            [FIX]: Be sure that all containers defined in 'ContainerDefinitions' either omit the 'Privileged' property, or that they set 'Privileged' to 'false'.
        >>
}

rule ecs_containers_nonprivileged_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_TASK_DEFINITION_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_TASK_DEFINITION_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.11]: Require an Amazon ECS container to run as non-privileged
            [FIX]: Be sure that all containers defined in 'ContainerDefinitions' either omit the 'Privileged' property, or that they set 'Privileged' to 'false'.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_task_definition) {
    %ecs_task_definition [
        filter_container_definitions_is_present(this)
    ]{
        ContainerDefinitions[*] {
            # Scenario 3
            Privileged not exists or

            # Scenario 4
            Privileged == false
        }
    }
}

rule filter_container_definitions_is_present(ecs_task_definition) {
    %ecs_task_definition {
        # Scenario 2
        ContainerDefinitions exists
        ContainerDefinitions is_list
        ContainerDefinitions not empty
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

### CT\.ECS\.PR\.11 example templates<a name="ct-ecs-pr-11-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: alpine:latest
        Name: ExampleContainerA
      - Image: nginx:latest
        Name: ExampleContainerB
        Privileged: false
      Memory: '512'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainerA
        Privileged: false
      - Essential: true
        Image: alpine:latest
        Name: ExampleContainerB
      - Image: nginx:latest
        Name: ExampleContainerC
        Privileged: true
      Memory: '512'
```

## \[CT\.ECS\.PR\.12\] Require that Amazon ECS task definitions do not pass secrets as container environment variables<a name="ct-ecs-pr-12-description"></a>

This control checks whether Amazon Elastic Container Service \(ECS\) task definition container definitions include environment variables named `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, or `ECS_ENGINE_AUTH_DATA`\.
+ **Control objective: **Use strong authentication
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECS::TaskDefinition`
+ **AWS CloudFormation guard rule: ** [CT\.ECS\.PR\.12 rule specification](#ct-ecs-pr-12-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECS\.PR\.12 rule specification](#ct-ecs-pr-12-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECS\.PR\.12 example templates](#ct-ecs-pr-12-templates) 

**Explanation**

AWS Systems Manager Parameter Store can help you improve the security posture of your organization\. We recommend the Parameter Store as a way to store secrets and credentials, instead of passing them into your container instances or entering them into your source code\.

**Usage considerations**  
This control applies only to Amazon ECS task definitions that are configured with container definitions\.
This control evaluates plaintext environment variables configured directly on container definitions\.

### Remediation for rule failure<a name="ct-ecs-pr-12-remediation"></a>

Omit environment variables with `Name` set to `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` or `ECS_ENGINE_AUTH_DATA` from container definitions\.

The examples that follow show how to implement this remediation\.

#### Amazon ECS Task Definition \- Example<a name="ct-ecs-pr-12-remediation-1"></a>

Amazon ECS task definition configured to inject sensitive data into a container as an environment variable\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECSTaskDefinition": {
        "Type": "AWS::ECS::TaskDefinition",
        "Properties": {
            "Memory": "512",
            "ExecutionRoleArn": {
                "Fn::GetAtt": [
                    "ECSTaskExecutionRole",
                    "Arn"
                ]
            },
            "ContainerDefinitions": [
                {
                    "Essential": true,
                    "Image": "nginx:latest",
                    "Name": "SampleContainer",
                    "Environment": [
                        {
                            "Name": "SAMPLE_ENV_VAR",
                            "Value": "sampleValue"
                        }
                    ],
                    "Secrets": [
                        {
                            "Name": "SAMPLE_SENSITIVE_ENV_VAR",
                            "ValueFrom": "arn:aws:ssm:us-east-1:123456789012:parameter/sample_parameter"
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
ECSTaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    Memory: '512'
    ExecutionRoleArn: !GetAtt 'ECSTaskExecutionRole.Arn'
    ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: SampleContainer
        Environment:
          - Name: SAMPLE_ENV_VAR
            Value: sampleValue
        Secrets:
          - Name: SAMPLE_SENSITIVE_ENV_VAR
            ValueFrom: arn:aws:ssm:us-east-1:123456789012:parameter/sample_parameter
```

### CT\.ECS\.PR\.12 rule specification<a name="ct-ecs-pr-12-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecs_no_environment_secrets_check
# 
# Description:
#   This control checks whether Amazon Elastic Container Service (ECS) task definition container definitions include environment variables named 'AWS_ACCESS_KEY_ID', 'AWS_SECRET_ACCESS_KEY', or 'ECS_ENGINE_AUTH_DATA'.
# 
# Reports on:
#   AWS::ECS::TaskDefinition
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
# Scenario: 1
#   Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#     And: The input document does not contain an ECS task definition resource
#    Then: SKIP
# Scenario: 2
#   Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#     And: The input document contains an ECS task definition resource
#     And: 'ContainerDefinitions' property is not present or is empty
#    Then: SKIP
# Scenario: 3
#   Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#     And: The input document contains an ECS task definition resource
#     And: 'ContainerDefinitions' property is present
#     And: Containers defined in 'ContainerDefinitions' do not have 'Environment' property present
#    Then: SKIP
# Scenario: 4
#   Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#     And: The input document contains an ECS task definition resource
#     And: 'ContainerDefinitions' property is present
#     And: One or more containers defined  in 'ContainerDefinitions' have 'Environment' present
#     And: 'Environment' property has an entry with 'Name' set to 'AWS_ACCESS_KEY_ID', 'AWS_SECRET_ACCESS_KEY', or
#           'ECS_ENGINE_AUTH_DATA'
#     Then: FAIL
# Scenario: 5
#   Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#     And: The input document contains an ECS task definition resource
#     And: 'ContainerDefinitions' property is present
#     And: One or more containers defined  in 'ContainerDefinitions' have 'Environment' present
#     And: 'Environment' property does not have an entry with 'Name' set to 'AWS_ACCESS_KEY_ID', 'AWS_SECRET_ACCESS_KEY',
#           or 'ECS_ENGINE_AUTH_DATA'
#    Then: PASS

#
# Constants
#
let ECS_TASK_DEFINITION_TYPE = "AWS::ECS::TaskDefinition"
let INPUT_DOCUMENT = this
let RESTRICTED_ENVIRONMENT_VARIABLES = ["AWS_ACCESS_KEY_ID", "AWS_SECRET_ACCESS_KEY", "ECS_ENGINE_AUTH_DATA"]

#
# Assignments
#
let ecs_task_definitions = Resources.*[ Type == %ECS_TASK_DEFINITION_TYPE ]

#
# Primary Rules
#
rule ecs_no_environment_secrets_check when is_cfn_template(%INPUT_DOCUMENT)
                                           %ecs_task_definitions not empty {
    check(%ecs_task_definitions.Properties)
        <<
        [CT.ECS.PR.12]: Require that Amazon ECS task definitions do not pass secrets as container environment variables
            [FIX]: Omit environment variables with 'Name' set to 'AWS_ACCESS_KEY_ID', 'AWS_SECRET_ACCESS_KEY' or 'ECS_ENGINE_AUTH_DATA' from container definitions.
        >>
}

rule ecs_no_environment_secrets_check when is_cfn_hook(%INPUT_DOCUMENT, %ECS_TASK_DEFINITION_TYPE) {
    check(%INPUT_DOCUMENT.%ECS_TASK_DEFINITION_TYPE.resourceProperties)
        <<
        [CT.ECS.PR.12]: Require that Amazon ECS task definitions do not pass secrets as container environment variables
            [FIX]: Omit environment variables with 'Name' set to 'AWS_ACCESS_KEY_ID', 'AWS_SECRET_ACCESS_KEY' or 'ECS_ENGINE_AUTH_DATA' from container definitions.
        >>
}

#
# Parameterized Rules
#
rule check(ecs_task_definition) {
    %ecs_task_definition [
        filter_container_definitions_is_present(this)
    ]{
        ContainerDefinitions[
            filter_environment_is_present(this)
        ] {
            # Scenario 4 and 5
            Environment[*] {
                Name not in %RESTRICTED_ENVIRONMENT_VARIABLES
            }
        }
    }
}

rule filter_container_definitions_is_present(ecs_task_definition) {
    %ecs_task_definition {
        # Scenario 2
        ContainerDefinitions exists
        ContainerDefinitions is_list
        ContainerDefinitions not empty
    }
}

rule filter_environment_is_present(container_definition) {
    %container_definition {
        # Scenario 3
        Environment exists
        Environment is_list
        Environment not empty
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

### CT\.ECS\.PR\.12 example templates<a name="ct-ecs-pr-12-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECSTaskExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - ecs-tasks.amazonaws.com
          Action:
          - sts:AssumeRole
      Path: /
      ManagedPolicyArns:
      - arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy
      Policies:
      - PolicyName: ECSTaskPolicy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - ssm:GetParameters
            Resource: arn:aws:ssm:us-east-1:123456789012:parameter/example_parameter
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      ExecutionRoleArn:
        Fn::GetAtt: [ ECSTaskExecutionRole, Arn ]
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainer
        Environment:
        - Name: EXAMPLE_ENV_VAR
          Value: exampleValue
        Secrets:
        - Name: EXAMPLE_SENSITIVE_ENV_VAR
          ValueFrom: arn:aws:ssm:us-east-1:123456789012:parameter/example_parameter
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECSTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Memory: '512'
      ContainerDefinitions:
      - Essential: true
        Image: nginx:latest
        Name: ExampleContainerA
        Environment:
        - Name: AWS_ACCESS_KEY_ID
          Value: exampleKey
        - Name: AWS_SECRET_ACCESS_KEY
          Value: exampleSecretKey
      - Image: alpine:latest
        Name: ExampleContainerB
```