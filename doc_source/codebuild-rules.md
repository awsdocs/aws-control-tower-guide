# AWS CodeBuild controls<a name="codebuild-rules"></a>

**Topics**
+ [\[CT\.CODEBUILD\.PR\.1\] Require OAuth on GitHub or Bitbucket source repository URLs for AWS CodeBuild projects](#ct-codebuild-pr-1-description)
+ [\[CT\.CODEBUILD\.PR\.2\] Require any AWS CodeBuild project environment variable to encrypt credentials in environment variables](#ct-codebuild-pr-2-description)
+ [\[CT\.CODEBUILD\.PR\.3\] Require any AWS CodeBuild project environment to have logging configured](#ct-codebuild-pr-3-description)
+ [\[CT\.CODEBUILD\.PR\.4\] Require any AWS CodeBuild project to deactivate privileged mode when running](#ct-codebuild-pr-4-description)
+ [\[CT\.CODEBUILD\.PR\.5\] Require encryption on all AWS CodeBuild project artifacts](#ct-codebuild-pr-5-description)
+ [\[CT\.CODEBUILD\.PR\.6\] Require encryption on all Amazon S3 logs for AWS CodeBuild projects](#ct-codebuild-pr-6-description)

## \[CT\.CODEBUILD\.PR\.1\] Require OAuth on GitHub or Bitbucket source repository URLs for AWS CodeBuild projects<a name="ct-codebuild-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether the GitHub or Bitbucket source repository URL contains either personal access tokens or a username and password\.
+ **Control objective: **Use strong authentication
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CodeBuild::Project`
+ **AWS CloudFormation guard rule: ** [CT\.CODEBUILD\.PR\.1 rule specification](#ct-codebuild-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CODEBUILD\.PR\.1 rule specification](#ct-codebuild-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CODEBUILD\.PR\.1 example templates](#ct-codebuild-pr-1-templates) 

**Explanation**

Authentication credentials should never be stored or transmitted in clear text or appear in the repository URL\. Instead of personal access tokens or username and password, you should use OAuth to grant authorization for accessing GitHub or Bitbucket repositories\. Using personal access tokens or a username and password could expose your credentials to unintended data exposure and unauthorized access\.

**Usage considerations**  
This control applies only to AWS CodeBuild projects with a primary or secondary source type of `GitHub` or `Bitbucket`\.

### Remediation for rule failure<a name="ct-codebuild-pr-1-remediation"></a>

Remove any embedded credentials from repository URLs in AWS CodeBuild project source configurations\. Instead, connect your CodeBuild projects to `GitHub` or `Bitbucket` repositories by configuring `GitHub Access Token` or `Bitbucket App Password` credentials in the AWS Management Console or AWS CLI\.

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example One<a name="ct-codebuild-pr-1-remediation-1"></a>

AWS CodeBuild project configured with a GitHub primary source location that does not contain a personal access token\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Artifacts": {
                "Type": "NO_ARTIFACTS"
            },
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER"
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\nartifacts:\n  files:\n    - '**/*'\n",
                "Type": "GITHUB",
                "Location": "https://github.com/username/repo.git"
            }
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Artifacts:
      Type: NO_ARTIFACTS
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      BuildSpec: |
        version: 0.2
        phases:
          install:
            commands:
              - npm install
          build:
            commands:
              - npm test
        artifacts:
          files:
            - '**/*'
      Type: GITHUB
      Location: https://github.com/username/repo.git
```

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example Two<a name="ct-codebuild-pr-1-remediation-2"></a>

AWS CodeBuild project configured with primary and secondary source locations that do not contain credentials or personal access tokens\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Artifacts": {
                "Type": "NO_ARTIFACTS"
            },
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER"
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\nartifacts:\n  files:\n    - '**/*'\n",
                "Type": "BITBUCKET",
                "Location": "https://bitbucket.org/user/repo.git"
            },
            "SecondarySources": [
                {
                    "Type": "GITHUB",
                    "Location": "https://github.com/username/repo.git",
                    "SourceIdentifier": "GitHubSource"
                }
            ]
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Artifacts:
      Type: NO_ARTIFACTS
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      BuildSpec: |
        version: 0.2
        phases:
          install:
            commands:
              - npm install
          build:
            commands:
              - npm test
        artifacts:
          files:
            - '**/*'
      Type: BITBUCKET
      Location: https://bitbucket.org/user/repo.git
    SecondarySources:
      - Type: GITHUB
        Location: https://github.com/username/repo.git
        SourceIdentifier: GitHubSource
```

### CT\.CODEBUILD\.PR\.1 rule specification<a name="ct-codebuild-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   codebuild_project_source_repo_url_check
# 
# Description:
#   This control checks whether the GitHub or Bitbucket source repository URL contains either personal access tokens or a username and password.
# 
# Reports on:
#   AWS::CodeBuild::Project
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
#       And: The input document does not contain any CodeBuild project resources
#       Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Source' configuration is not of 'Type' 'GITHUB' or 'BITBUCKET'
#       And: 'SecondarySources' configuration is not provided or is provided and does not have any item of 'Type'
#            'GITHUB' or 'BITBUCKET'
#       Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Source' configuration is of 'Type' 'GITHUB' or 'BITBUCKET'
#       And: 'Source' configuration has a 'Location' that contains credentials (username and password for BitBucket
#            and Access Token for GitHub)
#       Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'SecondarySources' configuration is provided
#       And: 'SecondarySources' configuration has one or more items of 'Type' 'GITHUB' or 'BITBUCKET'
#       And: 'SecondarySources' configuration has one or more items with 'Location' that contains credentials
#            (username and password for BitBucket and Access Token for GitHub)
#       Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Source' configuration is of 'Type' 'GITHUB' or 'BITBUCKET'
#       And: 'Source' configuration has a 'Location' that does not contain credentials (username and password for
#            BitBucket and Access Token for GitHub)
#       And: 'SecondarySources' configuration is not provided or is provided and does not have any item of 'Type'
#            'GITHUB' or 'BITBUCKET'
#       Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Source' configuration is of 'Type' 'GITHUB' or 'BITBUCKET'
#       And: 'Source' configuration has a 'Location' that does not contain credentials (username and password for
#            BitBucket and Access Token for GitHub)
#       And: 'SecondarySources' configuration is provided
#       And: 'SecondarySources' configuration has one or more items of 'Type' 'GITHUB' or 'BITBUCKET'
#       And: 'SecondarySources' configuration has one or more items with 'Location' that does not contain credentials
#            (username and password for BitBucket and Access Token for GitHub)
#       Then: PASS

#
# Constants
#
let CODEBUILD_PROJECT_TYPE = "AWS::CodeBuild::Project"
let INPUT_DOCUMENT = this
let GITHUB_COMPLIANT_URL_PATTERN = /^(http(s)?)(:\/\/github\.com\/)([^\/]+)\/([\w\.-]+)(\.git)?$/
let BITBUCKET_COMPLIANT_URL_PATTERN = /^https?:\/\/bitbucket\.org/

#
# Assignments
#
let codebuild_project = Resources.*[ Type == %CODEBUILD_PROJECT_TYPE ]

#
# Primary Rules
#
rule codebuild_project_source_repo_url_check when is_cfn_template(%INPUT_DOCUMENT)
                                                  %codebuild_project not empty {
    check(%codebuild_project.Properties)
        <<
        [CT.CODEBUILD.PR.1]: Require OAuth on GitHub or Bitbucket source repository URLs for AWS CodeBuild projects
            [FIX]: Remove any embedded credentials from repository URLs in AWS CodeBuild project source configurations. Instead, connect your CodeBuild projects to 'GitHub' or 'Bitbucket' repositories by configuring 'GitHub Access Token' or 'Bitbucket App Password' credentials in the AWS Management Console or AWS CLI.
        >>
}

rule codebuild_project_source_repo_url_check when is_cfn_hook(%INPUT_DOCUMENT, %CODEBUILD_PROJECT_TYPE) {
    check(%INPUT_DOCUMENT.%CODEBUILD_PROJECT_TYPE.resourceProperties)
        <<
        [CT.CODEBUILD.PR.1]: Require OAuth on GitHub or Bitbucket source repository URLs for AWS CodeBuild projects
            [FIX]: Remove any embedded credentials from repository URLs in AWS CodeBuild project source configurations. Instead, connect your CodeBuild projects to 'GitHub' or 'Bitbucket' repositories by configuring 'GitHub Access Token' or 'Bitbucket App Password' credentials in the AWS Management Console or AWS CLI.
        >>
}

#
# Parameterized Rules
#
rule check(codebuild_project) {
    %codebuild_project[
        filter_github_or_bitbucket_source_configuration(this) or
        filter_github_or_bitbucket_secondary_sources_configuration(this)
    ] {
        # Scenario 3, 5 and 6
        check_source(Source)
        # Scenario 4 and 6
        check_secondary_sources(this)
    }
}

rule filter_github_or_bitbucket_source_configuration(codebuild_project) {
    %codebuild_project {
        Source exists
        Source is_struct
        Source {
            Type == "GITHUB" or
            Type == "BITBUCKET"
        }
    }
}

rule filter_github_or_bitbucket_secondary_sources_configuration(codebuild_project) {
    %codebuild_project {
        SecondarySources exists
        SecondarySources is_list
        SecondarySources not empty

        some SecondarySources[*] {
            Type == "GITHUB" or
            Type == "BITBUCKET"
        }
    }
}

rule check_source(codebuild_source) {
    %codebuild_source [
        Type == "GITHUB"
    ] {
        Location exists
        Location == %GITHUB_COMPLIANT_URL_PATTERN
    }
    %codebuild_source [
        Type == "BITBUCKET"
    ] {
        Location exists
        Location == %BITBUCKET_COMPLIANT_URL_PATTERN
    }
}

rule check_secondary_sources(codebuild_project) {
    %codebuild_project [
        # Scenario 2
        SecondarySources exists
        SecondarySources is_list
        SecondarySources not empty
    ] {
        # Scenario 4 and 6
        SecondarySources[*] {
            check_source(this)
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

### CT\.CODEBUILD\.PR\.1 example templates<a name="ct-codebuild-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
        Type: GITHUB
        Location: https://github.com/username/repo.git
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
        Type: BITBUCKET
        Location: https://username:password@bitbucket.org/user/repo.git
```

## \[CT\.CODEBUILD\.PR\.2\] Require any AWS CodeBuild project environment variable to encrypt credentials in environment variables<a name="ct-codebuild-pr-2-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether AWS CodeBuild projects contain environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` stored as `PLAINTEXT`\.
+ **Control objective: **Use strong authentication
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CodeBuild::Project`
+ **AWS CloudFormation guard rule: ** [CT\.CODEBUILD\.PR\.2 rule specification](#ct-codebuild-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CODEBUILD\.PR\.2 rule specification](#ct-codebuild-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CODEBUILD\.PR\.2 example templates](#ct-codebuild-pr-2-templates) 

**Explanation**

Authentication credentials AWS\_ACCESS\_KEY\_ID and AWS\_SECRET\_ACCESS\_KEY should never be stored in clear text, as this could lead to unintended data exposure and unauthorized access\.

**Usage considerations**  
This control only applies to AWS CodeBuild projects configured with `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables

### Remediation for rule failure<a name="ct-codebuild-pr-2-remediation"></a>

Use `PARAMETER_STORE` or `SECRETS_MANAGER` to store values for environment variables named `AWS_ACCESS_KEY_ID` or `AWS_SECRET_ACCESS_KEY`\.

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example<a name="ct-codebuild-pr-2-remediation-1"></a>

AWS CodeBuild project configured to use credentials stored in AWS Secrets Manager\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Artifacts": {
                "Type": "NO_ARTIFACTS"
            },
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER",
                "EnvironmentVariables": [
                    {
                        "Name": "AWS_ACCESS_KEY_ID",
                        "Type": "SECRETS_MANAGER",
                        "Value": "sample_secret:access_key_id"
                    },
                    {
                        "Name": "AWS_SECRET_ACCESS_KEY",
                        "Type": "SECRETS_MANAGER",
                        "Value": "sample_secret:secret_access_key"
                    }
                ]
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "Type": "NO_SOURCE",
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\nartifacts:\n  files:\n    - '**/*'\n"
            }
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Artifacts:
      Type: NO_ARTIFACTS
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
      EnvironmentVariables:
        - Name: AWS_ACCESS_KEY_ID
          Type: SECRETS_MANAGER
          Value: sample_secret:access_key_id
        - Name: AWS_SECRET_ACCESS_KEY
          Type: SECRETS_MANAGER
          Value: sample_secret:secret_access_key
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      Type: NO_SOURCE
      BuildSpec: |
        version: 0.2
        phases:
          install:
            commands:
              - npm install
          build:
            commands:
              - npm test
        artifacts:
          files:
            - '**/*'
```

### CT\.CODEBUILD\.PR\.2 rule specification<a name="ct-codebuild-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   codebuild_project_envvar_awscred_check
# 
# Description:
#   This control checks whether AWS CodeBuild projects contain environment variables 'AWS_ACCESS_KEY_ID' and 'AWS_SECRET_ACCESS_KEY' stored as 'PLAINTEXT'.
# 
# Reports on:
#   AWS::CodeBuild::Project
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
#       And: The input document does not contain any CodeBuild project resources
#     Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Environment' configuration does not contains 'EnvironmentVariables'
#     Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Environment' configuration contains 'EnvironmentVariables'
#       And: 'EnvironmentVariables' contain variables named 'AWS_ACCESS_KEY_ID' or 'AWS_SECRET_ACCESS_KEY'
#       And: 'Type' is not provided for 'AWS_ACCESS_KEY_ID' and 'AWS_SECRET_ACCESS_KEY' environment variables or is
#            provided as an empty string.
#     Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Environment' configuration contains 'EnvironmentVariables'
#       And: 'EnvironmentVariables' contain variables named 'AWS_ACCESS_KEY_ID' or 'AWS_SECRET_ACCESS_KEY'
#       And: 'Type' is set to 'PLAINTEXT' for 'AWS_ACCESS_KEY_ID' or 'AWS_SECRET_ACCESS_KEY' environment variables
#     Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Environment' configuration contains 'EnvironmentVariables'
#       And: 'EnvironmentVariables' does not contain variables named 'AWS_ACCESS_KEY_ID' or 'AWS_SECRET_ACCESS_KEY'
#     Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Environment' configuration contains 'EnvironmentVariables'
#       And: 'EnvironmentVariables' contain variables named 'AWS_ACCESS_KEY_ID' or 'AWS_SECRET_ACCESS_KEY'
#       And: 'Type' is provided as a non-empty string and not set to 'PLAINTEXT' for 'AWS_ACCESS_KEY_ID' or
#            'AWS_SECRET_ACCESS_KEY' environment variables
#     Then: PASS


#
# Constants
#
let CODEBUILD_PROJECT_TYPE = "AWS::CodeBuild::Project"
let AWS_CREDENTIAL_ENV_VAR_NAMES = [ "AWS_ACCESS_KEY_ID", "AWS_SECRET_ACCESS_KEY" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let codebuild_project = Resources.*[ Type == %CODEBUILD_PROJECT_TYPE ]

#
# Primary Rules
#
rule codebuild_project_envvar_awscred_check when is_cfn_template(%INPUT_DOCUMENT)
                                                 %codebuild_project not empty {
    check(%codebuild_project.Properties)
        <<
        [CT.CODEBUILD.PR.2]: Require any AWS CodeBuild project environment variable to encrypt credentials in environment variables
            [FIX]: Use 'PARAMETER_STORE' or 'SECRETS_MANAGER' to store values for environment variables named 'AWS_ACCESS_KEY_ID' or 'AWS_SECRET_ACCESS_KEY'.
        >>
}

rule codebuild_project_envvar_awscred_check when is_cfn_hook(%INPUT_DOCUMENT, %CODEBUILD_PROJECT_TYPE) {
    check(%INPUT_DOCUMENT.%CODEBUILD_PROJECT_TYPE.resourceProperties)
        <<
        [CT.CODEBUILD.PR.2]: Require any AWS CodeBuild project environment variable to encrypt credentials in environment variables
            [FIX]: Use 'PARAMETER_STORE' or 'SECRETS_MANAGER' to store values for environment variables named 'AWS_ACCESS_KEY_ID' or 'AWS_SECRET_ACCESS_KEY'.
        >>
}

#
# Parameterized Rules
#
rule check(codebuild_project) {
    %codebuild_project [
        # Scenario 2
        filter_codebuild_projects_with_environment_variables(this)
    ] {
        Environment exists
        Environment is_struct
        Environment {
            EnvironmentVariables exists
            EnvironmentVariables is_list
            EnvironmentVariables not empty
            EnvironmentVariables [
                # Scenario 3, 4 and 6
                Name in %AWS_CREDENTIAL_ENV_VAR_NAMES
            ] {
                # Scenario 3
                Type exists
                check_is_string_and_not_empty(Type)
                # Scenario 4 and 6
                Type != "PLAINTEXT"
            }
        }
    }
}

rule filter_codebuild_projects_with_environment_variables(codebuild_project) {
    %codebuild_project {
        Environment exists
        Environment is_struct

        Environment {
            # Scenario 2
            EnvironmentVariables exists
            EnvironmentVariables is_list
            EnvironmentVariables not empty
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
```

### CT\.CODEBUILD\.PR\.2 example templates<a name="ct-codebuild-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
        EnvironmentVariables:
        - Name: AWS_ACCESS_KEY_ID
          Type: SECRETS_MANAGER
          Value: example_secret:access_key_id
        - Name: AWS_SECRET_ACCESS_KEY
          Type: SECRETS_MANAGER
          Value: example_secret:secret_access_key
        - Name: some_other_variable
          Type: PLAINTEXT
          Value: example
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
        EnvironmentVariables:
        - Name: AWS_ACCESS_KEY_ID
          Type: PLAINTEXT
          Value: EXAMPLE_ACCESS_KEY_ID
        - Name: AWS_SECRET_ACCESS_KEY
          Type: PLAINTEXT
          Value: EXAMPLE_SECRET_ACCESS_KEY
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
```

## \[CT\.CODEBUILD\.PR\.3\] Require any AWS CodeBuild project environment to have logging configured<a name="ct-codebuild-pr-3-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether AWS CodeBuild projects environment has at least one logging option enabled\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CodeBuild::Project`
+ **AWS CloudFormation guard rule: ** [CT\.CODEBUILD\.PR\.3 rule specification](#ct-codebuild-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CODEBUILD\.PR\.3 rule specification](#ct-codebuild-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CODEBUILD\.PR\.3 example templates](#ct-codebuild-pr-3-templates) 

**Explanation**

From a security perspective, logging is an important feature to enable, to assist future forensics efforts in case of a security incident\. Correlating anomalies in CodeBuild projects with threat detections can increase confidence in the accuracy of those threat detections\.

### Remediation for rule failure<a name="ct-codebuild-pr-3-remediation"></a>

Set `LogsConfig` with a `CloudWatchLogs` or `S3Logs` configuration\.

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example One<a name="ct-codebuild-pr-3-remediation-1"></a>

AWS CodeBuild project configured to enable logging, by means of Amazon CloudWatch Logs\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Artifacts": {
                "Type": "NO_ARTIFACTS"
            },
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER"
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "Type": "NO_SOURCE",
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\n"
            },
            "LogsConfig": {
                "CloudWatchLogs": {
                    "Status": "ENABLED"
                }
            }
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Artifacts:
      Type: NO_ARTIFACTS
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      Type: NO_SOURCE
      BuildSpec: "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n\
        \  build:\n    commands:\n      - npm test\n"
    LogsConfig:
      CloudWatchLogs:
        Status: ENABLED
```

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example Two<a name="ct-codebuild-pr-3-remediation-2"></a>

AWS CodeBuild project configured to enable logging, by means of Amazon S3\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Artifacts": {
                "Type": "NO_ARTIFACTS"
            },
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER"
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "Type": "NO_SOURCE",
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\n"
            },
            "LogsConfig": {
                "S3Logs": {
                    "Status": "ENABLED",
                    "Location": {
                        "Fn::Join": [
                            "/",
                            [
                                {
                                    "Ref": "S3Bucket"
                                },
                                "path/to/directory"
                            ]
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
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Artifacts:
      Type: NO_ARTIFACTS
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      Type: NO_SOURCE
      BuildSpec: "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n\
        \  build:\n    commands:\n      - npm test\n"
    LogsConfig:
      S3Logs:
        Status: ENABLED
        Location: !Join
          - /
          - - !Ref 'S3Bucket'
            - path/to/directory
```

### CT\.CODEBUILD\.PR\.3 rule specification<a name="ct-codebuild-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   codebuild_project_logging_enabled_check
# 
# Description:
#   This control checks whether AWS CodeBuild projects environment has at least one logging option enabled.
# 
# Reports on:
#   AWS::CodeBuild::Project
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
#       And: The input document does not contain any CodeBuild project resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'LogsConfig' is not provided on the CodeBuild project resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'LogsConfig' is provided on the CodeBuild project resource
#       And: Neither 'CloudWatchLogs' or 'S3Logs' are present in 'LogsConfig'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'LogsConfig' is provided on the CodeBuild project resource
#       And: 'CloudWatchLogs' is not present in 'LogsConfig'
#       And: 'S3Logs' is present in 'LogsConfig' with 'Status' set to 'DISABLED'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'LogsConfig' is provided on the CodeBuild project resource
#       And: 'S3Logs' is not present in 'LogsConfig'
#       And: 'CloudWatchLogs' is present in 'LogsConfig' with 'Status' set to 'DISABLED'
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'LogsConfig' is provided on the CodeBuild project resource
#       And: 'CloudWatchLogs' and 'S3Logs' are present in 'LogsConfig' with 'Status' set to 'DISABLED'
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'LogsConfig' is provided on the CodeBuild project resource
#       And: 'CloudWatchLogs' is not present in 'LogsConfig'
#       And: 'S3Logs' is present in 'LogsConfig' with 'Status' set to 'ENABLED'
#       And: 'Location' has not been provided in 'S3Logs', or has been provided as an empty string or
#             invalid local reference
#      Then: FAIL
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'LogsConfig' is provided on the CodeBuild project resource
#       And: 'S3Logs' is not present in 'LogsConfig'
#       And: 'CloudWatchLogs' is present in 'LogsConfig' with 'Status' set to 'ENABLED'
#      Then: PASS
#   Scenario: 9
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'LogsConfig' is provided on the CodeBuild project resource
#       And: 'CloudWatchLogs' is not present in 'LogsConfig'
#       And: 'S3Logs' is present in 'LogsConfig' with 'Status' set to 'ENABLED'
#       And: 'Location' has been provided in 'S3Logs' as a non-empty string or valid local reference
#      Then: PASS
#   Scenario: 10
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'LogsConfig' is provided on the CodeBuild project resource
#       And: 'CloudWatchLogs' is present in 'LogsConfig' with 'Status' set to 'ENABLED'
#       And: 'S3Logs' is present in 'LogsConfig' with 'Status' set to 'ENABLED'
#       And: 'Location' has been provided in 'S3Logs' as a non-empty string or valid local reference
#      Then: PASS

#
# Constants
#
let CODEBUILD_PROJECT_TYPE = "AWS::CodeBuild::Project"
let INPUT_DOCUMENT = this

#
# Assignments
#
let codebuild_project = Resources.*[ Type == %CODEBUILD_PROJECT_TYPE ]

#
# Primary Rules
#
rule codebuild_project_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                  %codebuild_project not empty {
    check(%codebuild_project.Properties)
        <<
        [CT.CODEBUILD.PR.3]: Require any AWS CodeBuild project environment to have logging configured
            [FIX]: Set 'LogsConfig' with a 'CloudWatchLogs' or 'S3Logs' configuration.
        >>
}

rule codebuild_project_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %CODEBUILD_PROJECT_TYPE) {
    check(%INPUT_DOCUMENT.%CODEBUILD_PROJECT_TYPE.resourceProperties)
        <<
        [CT.CODEBUILD.PR.3]: Require any AWS CodeBuild project environment to have logging configured
            [FIX]: Set 'LogsConfig' with a 'CloudWatchLogs' or 'S3Logs' configuration.
        >>
}

#
# Parameterized Rules
#
rule check(codebuild_project) {
    %codebuild_project {
        # Scenario 2
        LogsConfig exists
        LogsConfig is_struct

        LogsConfig {
            # Scenario 3
            check_cloudwatch_logs(this) or
            check_s3_logs(this)
        }
    }
}

rule check_cloudwatch_logs(codebuild_project) {
    %codebuild_project {
        # Scenario 4
        CloudWatchLogs exists
        CloudWatchLogs is_struct

        CloudWatchLogs {
            # Scenario 5, 6, 8 and 10
            Status exists
            Status == "ENABLED"
        }
    }
}

rule check_s3_logs(codebuild_project) {
    %codebuild_project {
        # Scenario 4
        S3Logs exists
        S3Logs is_struct

        S3Logs {
            # Scenario 4, 6, 9 and 10
            Status exists
            Status == "ENABLED"

            # Scenario 7, 9 and 10
            Location exists
            check_is_string_and_not_empty(Location) or
            check_local_references(%INPUT_DOCUMENT, Location, "AWS::S3::Bucket") or
            check_join_references(%INPUT_DOCUMENT, Location, "AWS::S3::Bucket")
        }
    }
}

rule check_join_references(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        'Fn::Join' {
            this is_list
            this not empty
            some this[1][*] {
                check_local_references(%doc, this, %referenced_resource_type)
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

### CT\.CODEBUILD\.PR\.3 example templates<a name="ct-codebuild-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
      Path: /
      Policies:
      - PolicyName: CodeBuildProjectPolicy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - logs:CreateLogGroup
            - logs:CreateLogStream
            - logs:PutLogEvents
            Resource: '*'
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
      LogsConfig:
        CloudWatchLogs:
          Status: ENABLED
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
      LogsConfig:
        S3Logs:
          Status: DISABLED
        CloudWatchLogs:
          Status: DISABLED
```

## \[CT\.CODEBUILD\.PR\.4\] Require any AWS CodeBuild project to deactivate privileged mode when running<a name="ct-codebuild-pr-4-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether AWS CodeBuild projects have privileged mode turned off\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CodeBuild::Project`
+ **AWS CloudFormation guard rule: ** [CT\.CODEBUILD\.PR\.4 rule specification](#ct-codebuild-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CODEBUILD\.PR\.4 rule specification](#ct-codebuild-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CODEBUILD\.PR\.4 example templates](#ct-codebuild-pr-4-templates) 

**Explanation**

By default, Docker containers do not allow access to any devices\. Privileged mode grants a build project Docker container access to all devices\. Setting `privilegedMode` with value `true` enables the Docker daemon to run inside a Docker container\. The Docker daemon listens for Docker API requests, and it manages Docker objects such as images, containers, networks, and volumes\. This parameter should be set to `true` if the build project is intended to build Docker images\. Otherwise, this setting should be deactivated, to prevent unintended access to Docker APIs, or to the container's underlying hardware\. Unintended access to `privilegedMode` may expose your system to risk of malicious tampering, or deletion of critical resources\.

### Remediation for rule failure<a name="ct-codebuild-pr-4-remediation"></a>

Within `Environment`, set `PrivilegedMode` to `false` or omit the `PrivilegedMode` property\.

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example One<a name="ct-codebuild-pr-4-remediation-1"></a>

AWS CodeBuild project configured to deactivate privileged mode, by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER"
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "Type": "NO_SOURCE",
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\nartifacts:\n  files:\n    - '**/*'\n"
            }
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      Type: NO_SOURCE
      BuildSpec: |
        version: 0.2
        phases:
          install:
            commands:
              - npm install
          build:
            commands:
              - npm test
        artifacts:
          files:
            - '**/*'
```

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example Two<a name="ct-codebuild-pr-4-remediation-2"></a>

AWS CodeBuild project configured to deactivate privileged mode, by means of the `PrivilegedMode` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER",
                "PrivilegedMode": false
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "Type": "NO_SOURCE",
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\nartifacts:\n  files:\n    - '**/*'\n"
            }
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
      PrivilegedMode: false
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      Type: NO_SOURCE
      BuildSpec: |
        version: 0.2
        phases:
          install:
            commands:
              - npm install
          build:
            commands:
              - npm test
        artifacts:
          files:
            - '**/*'
```

### CT\.CODEBUILD\.PR\.4 rule specification<a name="ct-codebuild-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   codebuild_project_environment_privileged_check
# 
# Description:
#   This control checks whether AWS CodeBuild projects have privileged mode turned off.
# 
# Reports on:
#   AWS::CodeBuild::Project
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
#       And: The input document does not contain any CodeBuild project resources
#       Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Environment' configuration is not provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Environment' configuration is provided
#       And: 'PrivilegedMode' within the 'Environment' configuration is provided and set to bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Environment' configuration is provided
#       And: 'PrivilegedMode' within 'Environment' configuration is not provided
#      Then: PASS
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Environment' configuration is provided
#       And: 'PrivilegedMode' within 'Environment' configuration is set to bool(false)
#      Then: PASS

#
# Constants
#
let CODEBUILD_PROJECT_TYPE = "AWS::CodeBuild::Project"
let INPUT_DOCUMENT = this

#
# Assignments
#
let codebuild_projects = Resources.*[ Type == %CODEBUILD_PROJECT_TYPE ]

#
# Primary Rules
#
rule codebuild_project_environment_privileged_check when is_cfn_template(%INPUT_DOCUMENT)
                                                         %codebuild_projects not empty {
    check(%codebuild_projects.Properties)
        <<
        [CT.CODEBUILD.PR.4]: Require any AWS CodeBuild project to deactivate privileged mode when running
            [FIX]: Within 'Environment', set 'PrivilegedMode' to 'false' or omit the 'PrivilegedMode' property.
        >>
}

rule codebuild_project_environment_privileged_check when is_cfn_hook(%INPUT_DOCUMENT, %CODEBUILD_PROJECT_TYPE) {
    check(%INPUT_DOCUMENT.%CODEBUILD_PROJECT_TYPE.resourceProperties)
        <<
        [CT.CODEBUILD.PR.4]: Require any AWS CodeBuild project to deactivate privileged mode when running
            [FIX]: Within 'Environment', set 'PrivilegedMode' to 'false' or omit the 'PrivilegedMode' property.
        >>
}

#
# Parameterized Rules
#
rule check(codebuild_project) {
    %codebuild_project {
        # Scenario 2
        Environment exists
        Environment is_struct
        Environment {
            # Scenario 4
            PrivilegedMode not exists or
            # Scenario 3 and 5
            PrivilegedMode == false
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

### CT\.CODEBUILD\.PR\.4 example templates<a name="ct-codebuild-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
        PrivilegedMode: true
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
```

## \[CT\.CODEBUILD\.PR\.5\] Require encryption on all AWS CodeBuild project artifacts<a name="ct-codebuild-pr-5-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether AWS CodeBuild projects are configured to encrypt artifacts\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CodeBuild::Project`
+ **AWS CloudFormation guard rule: ** [CT\.CODEBUILD\.PR\.5 rule specification](#ct-codebuild-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CODEBUILD\.PR\.5 rule specification](#ct-codebuild-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CODEBUILD\.PR\.5 example templates](#ct-codebuild-pr-5-templates) 

**Explanation**

Encryption of data at rest is a recommended best practice\. It adds a layer of access management around your data\. In case of a compromise to your CodeBuild artifacts, encryption at rest ensures that your data is protected from unintended access\.

**Usage considerations**  
This control applies only to AWS CodeBuild projects configured to return primary or secondary artifacts as output\.

### Remediation for rule failure<a name="ct-codebuild-pr-5-remediation"></a>

Set the `EncryptionDisabled` property in `Artifacts` and any `SecondaryArtifacts` to `false`, or omit the `EncryptionDisabled` property\.

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example One<a name="ct-codebuild-pr-5-remediation-1"></a>

AWS CodeBuild project configured to return primary artifacts as output with artifact encryption enabled, by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER"
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "Type": "NO_SOURCE",
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\nartifacts:\n  files:\n    - '**/*'\n"
            },
            "Artifacts": {
                "Type": "S3",
                "Location": {
                    "Ref": "S3Bucket"
                }
            }
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      Type: NO_SOURCE
      BuildSpec: |
        version: 0.2
        phases:
          install:
            commands:
              - npm install
          build:
            commands:
              - npm test
        artifacts:
          files:
            - '**/*'
    Artifacts:
      Type: S3
      Location: !Ref 'S3Bucket'
```

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example Two<a name="ct-codebuild-pr-5-remediation-2"></a>

AWS CodeBuild project configured to return primary and secondary artifacts as output with artifact encryption enabled, by means of the `EncryptionDisabled` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER"
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "Type": "NO_SOURCE",
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\nartifacts:\n  files:\n    - '**/*'\n  secondary-artifacts:\n    secondaryArtifact:\n      files:\n        - 'directory/file1'\n"
            },
            "Artifacts": {
                "Type": "S3",
                "EncryptionDisabled": false,
                "Location": {
                    "Ref": "S3Bucket"
                }
            },
            "SecondaryArtifacts": [
                {
                    "Type": "S3",
                    "EncryptionDisabled": false,
                    "ArtifactIdentifier": "secondaryArtifact",
                    "Location": {
                        "Ref": "S3Bucket"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      Type: NO_SOURCE
      BuildSpec: |
        version: 0.2
        phases:
          install:
            commands:
              - npm install
          build:
            commands:
              - npm test
        artifacts:
          files:
            - '**/*'
          secondary-artifacts:
            secondaryArtifact:
              files:
                - 'directory/file1'
    Artifacts:
      Type: S3
      EncryptionDisabled: false
      Location: !Ref 'S3Bucket'
    SecondaryArtifacts:
      - Type: S3
        EncryptionDisabled: false
        ArtifactIdentifier: secondaryArtifact
        Location: !Ref 'S3Bucket'
```

### CT\.CODEBUILD\.PR\.5 rule specification<a name="ct-codebuild-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   codebuild_project_artifact_encryption_check
# 
# Description:
#   This control checks whether AWS CodeBuild projects are configured to encrypt artifacts.
# 
# Reports on:
#   AWS::CodeBuild::Project
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario 1:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any CodeBuild project resources
#      Then: SKIP
#   Scenario 2:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Artifacts' configuration is provided and is of 'Type' 'NO_ARTIFACTS'
#       And: 'SecondaryArtifacts' configuration is not provided or provided with an empty list
#      Then: SKIP
#   Scenario 3:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Artifacts' configuration is provided and is of 'Type' 'NO_ARTIFACTS'
#       And: 'SecondaryArtifacts' configuration is provided as a non-empty list
#       And: All 'SecondaryArtifacts' entries have 'Type' set to 'NO_ARTIFACTS'
#      Then: SKIP
#   Scenario 4:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Artifacts' configuration is provided and is not of 'Type' 'NO_ARTIFACTS'
#       And: 'EncryptionDisabled' within 'Artifacts' configuration is provided and set to bool(true)
#      Then: FAIL
#   Scenario 5:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'SecondaryArtifacts' configuration is provided
#       And: There exists one or more items in 'SecondaryArtifacts' which have 'EncryptionDisabled' set to bool(true)
#      Then: FAIL
#   Scenario 6:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'Artifacts.EncryptionDisabled' is not provided, or is set to bool(false)
#       And: There exists no item in 'SecondaryArtifacts' which has 'EncryptionDisabled' set to bool(true)
#      Then: PASS

#
# Constants
#
let CODEBUILD_PROJECT_TYPE = "AWS::CodeBuild::Project"
let INPUT_DOCUMENT = this

#
# Assignments
#
let codebuild_project = Resources.*[ Type == %CODEBUILD_PROJECT_TYPE ]

#
# Primary Rules
#
rule codebuild_project_artifact_encryption_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %codebuild_project not empty {
    check(%codebuild_project.Properties)
        <<
        [CT.CODEBUILD.PR.5]: Require encryption on all AWS CodeBuild project artifacts
            [FIX]: Set the 'EncryptionDisabled' property in 'Artifacts' and any 'SecondaryArtifacts' to 'false', or omit the 'EncryptionDisabled' property.
        >>
}

rule codebuild_project_artifact_encryption_check when is_cfn_hook(%INPUT_DOCUMENT, %CODEBUILD_PROJECT_TYPE) {
    check(%INPUT_DOCUMENT.%CODEBUILD_PROJECT_TYPE.resourceProperties)
        <<
        [CT.CODEBUILD.PR.5]: Require encryption on all AWS CodeBuild project artifacts
            [FIX]: Set the 'EncryptionDisabled' property in 'Artifacts' and any 'SecondaryArtifacts' to 'false', or omit the 'EncryptionDisabled' property.
        >>
}

#
# Parameterized Rules
#
rule check(codebuild_project) {
    %codebuild_project [
        filter_codebuild_projects(this)
    ] {
        Artifacts {
            # Scenario 4 and 6
            check_artifact(this)
        }
        # Scenario 5
        SecondaryArtifacts not exists or
        check_secondary_artifacts(this)
    }
}

rule check_secondary_artifacts(codebuild_project) {
    %codebuild_project {
        SecondaryArtifacts is_list
        SecondaryArtifacts[*] {
            # Scenario 5 and 6
            check_artifact(this)
        }
    }
}

rule check_artifact(artifact) {
    %artifact {
        EncryptionDisabled not exists or
        EncryptionDisabled == false
    }
}

rule filter_codebuild_projects(codebuild_project) {
    %codebuild_project {
        # Scenario 2 and 3
        Artifacts exists
        Artifacts is_struct
        Artifacts {
            filter_artifact(this)
        } or
        filter_secondary_artifacts(this)
    }
}

rule filter_secondary_artifacts(codebuild_project) {
    %codebuild_project {
        # Scenario 2
        SecondaryArtifacts exists
        SecondaryArtifacts is_list
        SecondaryArtifacts not empty
        SecondaryArtifacts[*] {
            # Scenario 3
            filter_artifact(this)
        }
    }
}

rule filter_artifact(artifact) {
    %artifact {
       Type exists
       Type != "NO_ARTIFACTS"
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

### CT\.CODEBUILD\.PR\.5 example templates<a name="ct-codebuild-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
      Path: /
      Policies:
      - PolicyName: CodeBuildProjectPolicy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - logs:CreateLogGroup
            - logs:CreateLogStream
            - logs:PutLogEvents
            Resource: '*'
          - Effect: Allow
            Action:
            - s3:PutObject
            - s3:GetBucketAcl
            - s3:GetBucketLocation
            Resource:
            - Fn::GetAtt:
              - S3Bucket
              - Arn
            - Fn::Join:
              - ""
              - - Fn::GetAtt:
                  - S3Bucket
                  - Arn
                - "/*"
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties: {}
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
      Artifacts:
        Type: S3
        Location:
          Ref: S3Bucket
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
      Path: /
      Policies:
      - PolicyName: CodeBuildProjectPolicy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - logs:CreateLogGroup
            - logs:CreateLogStream
            - logs:PutLogEvents
            Resource: '*'
          - Effect: Allow
            Action:
            - s3:PutObject
            - s3:GetBucketAcl
            - s3:GetBucketLocation
            Resource:
            - Fn::GetAtt:
              - S3Bucket
              - Arn
            - Fn::Join:
              - ""
              - - Fn::GetAtt:
                  - S3Bucket
                  - Arn
                - "/*"
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties: {}
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
      Artifacts:
        Type: S3
        EncryptionDisabled: true
        Location:
          Ref: S3Bucket
```

## \[CT\.CODEBUILD\.PR\.6\] Require encryption on all Amazon S3 logs for AWS CodeBuild projects<a name="ct-codebuild-pr-6-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether AWS CodeBuild projects configured with Amazon S3 logs have encryption enabled\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CodeBuild::Project`
+ **AWS CloudFormation guard rule: ** [CT\.CODEBUILD\.PR\.6 rule specification](#ct-codebuild-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CODEBUILD\.PR\.6 rule specification](#ct-codebuild-pr-6-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CODEBUILD\.PR\.6 example templates](#ct-codebuild-pr-6-templates) 

**Explanation**

Encryption of data at rest is a recommended best practice\. It adds a layer of access management around your data\. In case of a compromise to your CodeBuild artifacts, encryption at rest ensures that your data is protected from unintended access\.

**Usage considerations**  
This control applies only to AWS CodeBuild projects with log delivery to Amazon S3 enabled\.

### Remediation for rule failure<a name="ct-codebuild-pr-6-remediation"></a>

Set `EncryptionDisabled` in `S3Logs` to `false`, or do not specify the `EncryptionDisabled` property\.

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example One<a name="ct-codebuild-pr-6-remediation-1"></a>

AWS CodeBuild project configured to encrypt logs delivered to an Amazon S3 logging destination, by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Artifacts": {
                "Type": "NO_ARTIFACTS"
            },
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER"
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "Type": "NO_SOURCE",
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\nartifacts:\n  files:\n    - '**/*'\n"
            },
            "LogsConfig": {
                "S3Logs": {
                    "Status": "ENABLED",
                    "Location": {
                        "Ref": "S3Bucket"
                    }
                }
            }
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Artifacts:
      Type: NO_ARTIFACTS
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      Type: NO_SOURCE
      BuildSpec: |
        version: 0.2
        phases:
          install:
            commands:
              - npm install
          build:
            commands:
              - npm test
        artifacts:
          files:
            - '**/*'
    LogsConfig:
      S3Logs:
        Status: ENABLED
        Location: !Ref 'S3Bucket'
```

The examples that follow show how to implement this remediation\.

#### AWS CodeBuild Project \- Example Two<a name="ct-codebuild-pr-6-remediation-2"></a>

AWS CodeBuild project configured to encrypt logs delivered to an Amazon S3 logging destination, by means of the `EncryptionDisabled` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CodeBuildProject": {
        "Type": "AWS::CodeBuild::Project",
        "Properties": {
            "Artifacts": {
                "Type": "NO_ARTIFACTS"
            },
            "Environment": {
                "ComputeType": "BUILD_GENERAL1_SMALL",
                "Image": "aws/codebuild/standard:4.0",
                "Type": "LINUX_CONTAINER"
            },
            "ServiceRole": {
                "Fn::GetAtt": [
                    "CodeBuildServiceRole",
                    "Arn"
                ]
            },
            "Source": {
                "Type": "NO_SOURCE",
                "BuildSpec": "version: 0.2\nphases:\n  install:\n    commands:\n      - npm install\n  build:\n    commands:\n      - npm test\nartifacts:\n  files:\n    - '**/*'\n"
            },
            "LogsConfig": {
                "S3Logs": {
                    "Status": "ENABLED",
                    "Location": {
                        "Ref": "S3Bucket"
                    },
                    "EncryptionDisabled": false
                }
            }
        }
    }
}
```

**YAML example**

```
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Artifacts:
      Type: NO_ARTIFACTS
    Environment:
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/standard:4.0
      Type: LINUX_CONTAINER
    ServiceRole: !GetAtt 'CodeBuildServiceRole.Arn'
    Source:
      Type: NO_SOURCE
      BuildSpec: |
        version: 0.2
        phases:
          install:
            commands:
              - npm install
          build:
            commands:
              - npm test
        artifacts:
          files:
            - '**/*'
    LogsConfig:
      S3Logs:
        Status: ENABLED
        Location: !Ref 'S3Bucket'
        EncryptionDisabled: false
```

### CT\.CODEBUILD\.PR\.6 rule specification<a name="ct-codebuild-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   codebuild_project_s3_logs_encrypted_check
# 
# Description:
#   This control checks whether AWS CodeBuild projects configured with Amazon S3 logs have encryption enabled.
# 
# Reports on:
#   AWS::CodeBuild::Project
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario 1:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any CodeBuild project resources
#      Then: SKIP
#   Scenario 2:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'S3Logs' in 'LogsConfig' configuration is not provided
#      Then: SKIP
#   Scenario 3:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'S3Logs' in 'LogsConfig' configuration is provided and its 'Status' is set to 'DISABLED'
#      Then: SKIP
#   Scenario 4:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'S3Logs' in 'LogsConfig' configuration is provided and its 'Status' is set to 'ENABLED'
#       And: 'EncryptionDisabled' within 'S3Logs' is provided and set to bool(true)
#      Then: FAIL
#   Scenario 5:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'S3Logs' in 'LogsConfig' configuration is provided and its 'Status' is set to 'ENABLED'
#       And: 'EncryptionDisabled' within 'S3Logs' is not provided
#      Then: PASS
#   Scenario 6:
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CodeBuild project resource
#       And: 'S3Logs' in 'LogsConfig' configuration is provided and its 'Status' is set to 'ENABLED'
#       And: 'EncryptionDisabled' within 'S3Logs' is provided and set to bool(false)
#      Then: PASS

#
# Constants
#
let CODEBUILD_PROJECT_TYPE = "AWS::CodeBuild::Project"
let INPUT_DOCUMENT = this

#
# Assignments
#
let codebuild_project = Resources.*[ Type == %CODEBUILD_PROJECT_TYPE ]

#
# Primary Rules
#
rule codebuild_project_s3_logs_encrypted_check when is_cfn_template(%INPUT_DOCUMENT)
                                                    %codebuild_project not empty {
    check(%codebuild_project.Properties)
        <<
        [CT.CODEBUILD.PR.6]: Require encryption on all Amazon S3 logs for AWS CodeBuild projects
            [FIX]: Set 'EncryptionDisabled' in 'S3Logs' to 'false', or do not specify the 'EncryptionDisabled' property.
        >>
}

rule codebuild_project_s3_logs_encrypted_check when is_cfn_hook(%INPUT_DOCUMENT, %CODEBUILD_PROJECT_TYPE) {
    check(%INPUT_DOCUMENT.%CODEBUILD_PROJECT_TYPE.resourceProperties)
        <<
        [CT.CODEBUILD.PR.6]: Require encryption on all Amazon S3 logs for AWS CodeBuild projects
            [FIX]: Set 'EncryptionDisabled' in 'S3Logs' to 'false', or do not specify the 'EncryptionDisabled' property.
        >>
}

#
# Parameterized Rules
#
rule check(codebuild_project) {
    %codebuild_project [
        # Scenario 2 and 3
        filter_codebuild_projects(this)
    ] {
        LogsConfig {
            S3Logs {
                # Scenario 5
                EncryptionDisabled not exists or
                # Scenario 4 and 6
                EncryptionDisabled == false
            }
        }
    }
}

rule filter_codebuild_projects(codebuild_project) {
    %codebuild_project {
        LogsConfig exists
        LogsConfig is_struct
        LogsConfig {
            # Scenario 2 and 3
            S3Logs exists
            S3Logs is_struct
            S3Logs {
                Status exists
                # Scenario 3 and 4
                Status == "ENABLED"
            }
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

### CT\.CODEBUILD\.PR\.6 example templates<a name="ct-codebuild-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
      Path: /
      Policies:
      - PolicyName: CodeBuildProjectPolicy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - logs:CreateLogGroup
            - logs:CreateLogStream
            - logs:PutLogEvents
            Resource: '*'
          - Effect: Allow
            Action:
            - s3:PutObject
            - s3:GetBucketAcl
            - s3:GetBucketLocation
            Resource:
              - Fn::GetAtt:
                - S3Bucket
                - Arn
              - Fn::Join:
                - ""
                - - Fn::GetAtt:
                    - S3Bucket
                    - Arn
                  - "/*"
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties: {}
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
      LogsConfig:
        S3Logs:
          Status: ENABLED
          Location:
            Ref: S3Bucket
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service: codebuild.amazonaws.com
          Action: sts:AssumeRole
      Path: /
      Policies:
      - PolicyName: CodeBuildProjectPolicy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - logs:CreateLogGroup
            - logs:CreateLogStream
            - logs:PutLogEvents
            Resource: '*'
          - Effect: Allow
            Action:
            - s3:PutObject
            - s3:GetBucketAcl
            - s3:GetBucketLocation
            Resource:
              - Fn::GetAtt:
                - S3Bucket
                - Arn
              - Fn::Join:
                - ""
                - - Fn::GetAtt:
                    - S3Bucket
                    - Arn
                  - "/*"
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties: {}
  CodeBuildProject:
    Type: AWS::CodeBuild::Project
    Properties:
      Artifacts:
        Type: NO_ARTIFACTS
      Environment:
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/standard:4.0
        Type: LINUX_CONTAINER
      ServiceRole:
        Fn::GetAtt:
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: NO_SOURCE
        BuildSpec: |
          version: 0.2
          phases:
            install:
              commands:
                - npm install
            build:
              commands:
                - npm test
          artifacts:
            files:
              - '**/*'
      LogsConfig:
        S3Logs:
          Status: ENABLED
          Location:
            Ref: S3Bucket
          EncryptionDisabled: true
```