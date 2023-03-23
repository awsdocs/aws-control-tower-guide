# Amazon Elastic Container Registry controls<a name="ecr-rules"></a>

**Topics**
+ [\[CT\.ECR\.PR\.1\] Require Amazon ECR repositories to have a lifecycle policy configured](#ct-ecr-pr-1-description)
+ [\[CT\.ECR\.PR\.2\] Require Amazon ECR private repositories to have image scanning enabled](#ct-ecr-pr-2-description)
+ [\[CT\.ECR\.PR\.3\] Require Amazon ECR private repositories to have tag immutability enabled](#ct-ecr-pr-3-description)

## \[CT\.ECR\.PR\.1\] Require Amazon ECR repositories to have a lifecycle policy configured<a name="ct-ecr-pr-1-description"></a>

This control checks whether a private Amazon Elastic Container Registry \(Amazon ECR\) repository has at least one lifecycle policy configured\.
+ **Control objective: **Manage vulnerabilities, Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECR::Repository`
+ **AWS CloudFormation guard rule: ** [CT\.ECR\.PR\.1 rule specification](#ct-ecr-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECR\.PR\.1 rule specification](#ct-ecr-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECR\.PR\.1 example templates](#ct-ecr-pr-1-templates) 

**Explanation**

Amazon ECR lifecycle policies specify the lifecycle management of images in a repository\. By configuring lifecycle policies, you can automate the cleanup of unused images and the expiration of images, based on age or count\. Automating these tasks can help you to avoid unintentionally using outdated images in your repository\.

### Remediation for rule failure<a name="ct-ecr-pr-1-remediation"></a>

Provide a `LifecyclePolicy` configuration and set `LifecyclePolicyText` to an Amazon ECR repository lifecycle policy\.

The examples that follow show how to implement this remediation\.

#### Amazon ECR Repository \- Example<a name="ct-ecr-pr-1-remediation-1"></a>

Amazon ECR repository configured with a lifecycle policy\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECRRepository": {
        "Type": "AWS::ECR::Repository",
        "Properties": {
            "LifecyclePolicy": {
                "LifecyclePolicyText": "{\n  \"rules\": [\n    {\n      \"rulePriority\": 1,\n      \"description\": \"Expire images older than 14 days\",\n      \"selection\": {\n        \"tagStatus\": \"untagged\",\n        \"countType\": \"sinceImagePushed\",\n        \"countUnit\": \"days\",\n        \"countNumber\": 14\n      },\n      \"action\": {\n        \"type\": \"expire\"\n      }\n    }\n  ]\n}\n"
            }
        }
    }
}
```

**YAML example**

```
ECRRepository:
  Type: AWS::ECR::Repository
  Properties:
    LifecyclePolicy:
      LifecyclePolicyText: |
        {
          "rules": [
            {
              "rulePriority": 1,
              "description": "Expire images older than 14 days",
              "selection": {
                "tagStatus": "untagged",
                "countType": "sinceImagePushed",
                "countUnit": "days",
                "countNumber": 14
              },
              "action": {
                "type": "expire"
              }
            }
          ]
        }
```

### CT\.ECR\.PR\.1 rule specification<a name="ct-ecr-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#    ecr_private_lifecycle_policy_configured_check
# 
# Description:
#   This control checks whether a private Amazon Elastic Container Registry (ECR) repository has at least one lifecycle policy configured.
# 
# Reports on:
#    AWS::ECR::Repository
# 
# Evaluates:
#    AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#    None
# 
# Scenarios:
#  Scenario: 1
#    Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#      And: The input document does not contain any ECR repository resources
#     Then: SKIP
#  Scenario: 2
#    Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#      And: The input document contains an ECR repository resource
#      And: 'LifecyclePolicy' is not present
#     Then: FAIL
#  Scenario: 3
#    Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#      And: The input document contains an ECR repository resource
#      And: 'LifecyclePolicy' is present
#      And: 'LifecyclePolicyText' has not been provided in the 'LifecyclePolicy' configuration or has been provided as
#            an empty string
#     Then: FAIL
#  Scenario: 4
#    Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#      And: The input document contains an ECR repository resource
#      And: 'LifecyclePolicy' is present
#      And: 'LifecyclePolicyText' has been provided in the 'LifecyclePolicy' configuration with a non-empty string
#     Then: PASS

#
# Constants
#
let ECR_REPOSITORY_TYPE = "AWS::ECR::Repository"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecr_repositories = Resources.*[ Type == %ECR_REPOSITORY_TYPE ]

#
# Primary Rules
#
rule ecr_private_lifecycle_policy_configured_check when is_cfn_template(%INPUT_DOCUMENT)
                                                        %ecr_repositories not empty {
    check(%ecr_repositories.Properties)
        <<
        [CT.ECR.PR.1]: Require Amazon ECR repositories to have a lifecycle policy configured
            [FIX]: Provide a 'LifecyclePolicy' configuration and set 'LifecyclePolicyText' to an Amazon ECR repository lifecycle policy.
        >>
}

rule ecr_private_lifecycle_policy_configured_check when is_cfn_hook(%INPUT_DOCUMENT, %ECR_REPOSITORY_TYPE) {
    check(%INPUT_DOCUMENT.%ECR_REPOSITORY_TYPE.resourceProperties)
        <<
        [CT.ECR.PR.1]: Require Amazon ECR repositories to have a lifecycle policy configured
            [FIX]: Provide a 'LifecyclePolicy' configuration and set 'LifecyclePolicyText' to an Amazon ECR repository lifecycle policy.
        >>
}

#
# Parameterized Rules
#
rule check(ecr_repository) {
    %ecr_repository {
        #Scenario 3
        LifecyclePolicy exists
        LifecyclePolicy is_struct
        LifecyclePolicy {
            #Scenario 4
            LifecyclePolicyText exists
            check_is_string_and_not_empty(LifecyclePolicyText)
        }
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
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.ECR\.PR\.1 example templates<a name="ct-ecr-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECRRepository:
    Type: AWS::ECR::Repository
    Properties:
      LifecyclePolicy:
        LifecyclePolicyText: |
          {
            "rules": [
              {
                "rulePriority": 1,
                "description": "Expire images older than 14 days",
                "selection": {
                  "tagStatus": "untagged",
                  "countType": "sinceImagePushed",
                  "countUnit": "days",
                  "countNumber": 14
                },
                "action": {
                  "type": "expire"
                }
              }
            ]
          }
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECRRepository:
    Type: AWS::ECR::Repository
    Properties: {}
```

## \[CT\.ECR\.PR\.2\] Require Amazon ECR private repositories to have image scanning enabled<a name="ct-ecr-pr-2-description"></a>

This control checks whether a private Amazon Elastic Container Registry \(Amazon ECR\) repository has image scanning enabled\.
+ **Control objective: **Manage vulnerabilities
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECR::Repository`
+ **AWS CloudFormation guard rule: ** [CT\.ECR\.PR\.2 rule specification](#ct-ecr-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECR\.PR\.2 rule specification](#ct-ecr-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECR\.PR\.2 example templates](#ct-ecr-pr-2-templates) 

**Explanation**

Amazon ECR image scanning helps with identifying software vulnerabilities in your container images\. Amazon ECR uses the Common Vulnerabilities and Exposures \(CVEs\) database from the open\-source Clair project, and it provides a list of scan findings\. Enabling image scanning on Amazon ECR repositories adds a layer of verification regarding the integrity and safety of the images being stored\.

### Remediation for rule failure<a name="ct-ecr-pr-2-remediation"></a>

Set `ScanOnPush` in `ImageScanningConfiguration` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon ECR Repository \- Example<a name="ct-ecr-pr-2-remediation-1"></a>

Amazon ECR repository with image scanning enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECRRepository": {
        "Type": "AWS::ECR::Repository",
        "Properties": {
            "ImageScanningConfiguration": {
                "ScanOnPush": true
            }
        }
    }
}
```

**YAML example**

```
ECRRepository:
  Type: AWS::ECR::Repository
  Properties:
    ImageScanningConfiguration:
      ScanOnPush: true
```

### CT\.ECR\.PR\.2 rule specification<a name="ct-ecr-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecr_private_image_scanning_enabled_check
# 
# Description:
#   This control checks whether a private Amazon Elastic Container Registry (Amazon ECR) repository has image scanning enabled.
# 
# Reports on:
#   AWS::ECR::Repository
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
#       And: The input document does not contain any Amazon ECR repository resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECR repository resource
#       And: 'ImageScanningConfiguration.ScanOnPush' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECR repository resource
#       And: 'ImageScanningConfiguration.ScanOnPush' has been provided and set to a value other than bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon ECR repository resource
#       And: 'ImageScanningConfiguration.ScanOnPush' has been provided and set to bool(true)
#      Then: PASS

#
# Constants
#
let ECR_REPOSITORY_TYPE = "AWS::ECR::Repository"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecr_repositories = Resources.*[ Type == %ECR_REPOSITORY_TYPE ]

#
# Primary Rules
#
rule ecr_private_image_scanning_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                   %ecr_repositories not empty {
    check(%ecr_repositories.Properties)
        <<
        [CT.ECR.PR.2]: Require Amazon ECR private repositories to have image scanning enabled
        [FIX]: Set 'ScanOnPush' in 'ImageScanningConfiguration' to 'true'.
        >>
}

rule ecr_private_image_scanning_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ECR_REPOSITORY_TYPE) {
    check(%INPUT_DOCUMENT.%ECR_REPOSITORY_TYPE.resourceProperties)
        <<
        [CT.ECR.PR.2]: Require Amazon ECR private repositories to have image scanning enabled
        [FIX]: Set 'ScanOnPush' in 'ImageScanningConfiguration' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(ecr_repository) {
    %ecr_repository {
        # Scenario 2
        ImageScanningConfiguration exists
        ImageScanningConfiguration is_struct

        ImageScanningConfiguration {
            # Scenario 3 and 4
            ScanOnPush exists
            ScanOnPush == true
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

### CT\.ECR\.PR\.2 example templates<a name="ct-ecr-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECRRepository:
    Type: AWS::ECR::Repository
    Properties:
      ImageScanningConfiguration:
        ScanOnPush: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECRRepository:
    Type: AWS::ECR::Repository
    Properties:
      ImageScanningConfiguration:
        ScanOnPush: false
```

## \[CT\.ECR\.PR\.3\] Require Amazon ECR private repositories to have tag immutability enabled<a name="ct-ecr-pr-3-description"></a>

This control checks whether a private Amazon Elastic Container Registry \(Amazon ECR\) repository has tag immutability enabled\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ECR::Repository`
+ **AWS CloudFormation guard rule: ** [CT\.ECR\.PR\.3 rule specification](#ct-ecr-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ECR\.PR\.3 rule specification](#ct-ecr-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ECR\.PR\.3 example templates](#ct-ecr-pr-3-templates) 

**Explanation**

Amazon ECR tag immutability enables customers to rely on the descriptive tags of an image as a reliable mechanism that tracks and uniquely identifies images\. An immutable tag is static, which means that each tag refers to a unique image\. This tagging improves reliability and scalability, because the use of a static tag always results in the same image being deployed\. When configured, tag immutability prevents the tags from being overridden, which reduces the attack surface\.

### Remediation for rule failure<a name="ct-ecr-pr-3-remediation"></a>

Set `ImageTagMutability` to `IMMUTABLE`\.

The examples that follow show how to implement this remediation\.

#### Amazon ECR Repository \- Example<a name="ct-ecr-pr-3-remediation-1"></a>

Amazon ECR repository configured with immutable tags\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ECRRepository": {
        "Type": "AWS::ECR::Repository",
        "Properties": {
            "ImageTagMutability": "IMMUTABLE"
        }
    }
}
```

**YAML example**

```
ECRRepository:
  Type: AWS::ECR::Repository
  Properties:
    ImageTagMutability: IMMUTABLE
```

### CT\.ECR\.PR\.3 rule specification<a name="ct-ecr-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   ecr_private_tag_immutability_enabled_check
# 
# Description:
#   This control checks whether a private Amazon Elastic Container Registry (Amazon ECR) repository has tag immutability enabled.
# 
# Reports on:
#   AWS::ECR::Repository
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
#       And: The input document does not contain any ECR repository resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECR repository resource
#       And: 'ImageTagMutability' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECR repository resource
#       And: 'ImageTagMutability' has been provided with a value of 'MUTABLE'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ECR repository resource
#       And: 'ImageTagMutability' has been provided with a value of 'IMMUTABLE'
#      Then: PASS

#
# Constants
#
let ECR_REPOSITORY_TYPE = "AWS::ECR::Repository"
let INPUT_DOCUMENT = this

#
# Assignments
#
let ecr_repositories = Resources.*[ Type == %ECR_REPOSITORY_TYPE ]

#
# Primary Rules
#
rule ecr_private_tag_immutability_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                     %ecr_repositories not empty {
    check(%ecr_repositories.Properties)
        <<
        [CT.ECR.PR.3]: Require Amazon ECR private repositories to have tag immutability enabled
        [FIX]: Set 'ImageTagMutability' to 'IMMUTABLE'.
        >>
}

rule ecr_private_tag_immutability_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ECR_REPOSITORY_TYPE) {
    check(%INPUT_DOCUMENT.%ECR_REPOSITORY_TYPE.resourceProperties)
        <<
        [CT.ECR.PR.3]: Require Amazon ECR private repositories to have tag immutability enabled
        [FIX]: Set 'ImageTagMutability' to 'IMMUTABLE'.
        >>
}

#
# Parameterized Rules
#
rule check(ecr_repository) {
    %ecr_repository {
        # Scenario 2, 3 and 4
        ImageTagMutability exists
        ImageTagMutability == "IMMUTABLE"
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

### CT\.ECR\.PR\.3 example templates<a name="ct-ecr-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ECRRepository:
    Type: AWS::ECR::Repository
    Properties:
      ImageTagMutability: IMMUTABLE
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ECRRepository:
    Type: AWS::ECR::Repository
    Properties:
      ImageTagMutability: MUTABLE
```