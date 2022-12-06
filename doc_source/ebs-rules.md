# AWS Elastic Beanstalk controls<a name="ebs-rules"></a>

**Topics**
+ [\[CT\.ELASTICBEANSTALK\.PR\.1\] Require AWS Elastic Beanstalk environments to have enhanced health reporting enabled](#ct-elasticbeanstalk-pr-1-description)
+ [\[CT\.ELASTICBEANSTALK\.PR\.2\] Require an AWS Elastic Beanstalk environment to have managed platform updates configured](#ct-elasticbeanstalk-pr-2-description)

## \[CT\.ELASTICBEANSTALK\.PR\.1\] Require AWS Elastic Beanstalk environments to have enhanced health reporting enabled<a name="ct-elasticbeanstalk-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether AWS Elastic Beanstalk environments and configuration templates are configured for `enhanced` health reporting\.
+ **Control objective: **Improve resiliency
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticBeanstalk::Environment`, `AWS::ElasticBeanstalk::ConfigurationTemplate`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICBEANSTALK\.PR\.1 rule specification](#ct-elasticbeanstalk-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICBEANSTALK\.PR\.1 rule specification](#ct-elasticbeanstalk-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.ELASTICBEANSTALK.PR.1) 

**Explanation**

Elastic Beanstalk enhanced health reporting enables a more rapid response to changes in the health of the underlying infrastructure\. These changes could result in a lack of availability of the application\.

Elastic Beanstalk enhanced health reporting provides a status descriptor to gauge the severity of the identified issues and identify possible causes to investigate\. The Elastic Beanstalk health agent, included in supported Amazon Machine Images \(AMIs\), evaluates logs and metrics of environment EC2 instances\.

### Remediation for rule failure<a name="ct-elasticbeanstalk-pr-1-remediation"></a>

For AWS Elastic Beanstalk environments, configure an `OptionSetting` with `Namespace` set to `aws:elasticbeanstalk:healthreporting:system`, `OptionName` set to `SystemType`, and `Value` set to `enhanced`\. For AWS Elastic Beanstalk configuration templates, configure an `OptionSetting` with `Namespace` set to `aws:elasticbeanstalk:healthreporting:system`, `OptionName` set to `SystemType`, and `Value` set to `enhanced`\. Omit this setting to adopt the default value of `enhanced`\.

The examples that follow show how to implement this remediation\.

#### AWS Elastic Beanstalk Environment \- Example<a name="ct-elasticbeanstalk-pr-1-remediation-1"></a>

AWS Elastic Beanstalk environment configured with enhanced health reporting enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticBeanstalkEnvironment": {
        "Type": "AWS::ElasticBeanstalk::Environment",
        "Properties": {
            "ApplicationName": {
                "Ref": "App"
            },
            "SolutionStackName": "64bit Amazon Linux 2 v3.4.0 running Python 3.8",
            "OptionSettings": [
                {
                    "Namespace": "aws:elasticbeanstalk:healthreporting:system",
                    "OptionName": "SystemType",
                    "Value": "enhanced"
                },
                {
                    "Namespace": "aws:autoscaling:launchconfiguration",
                    "OptionName": "IamInstanceProfile",
                    "Value": {
                        "Ref": "InstanceProfile"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
ElasticBeanstalkEnvironment:
  Type: AWS::ElasticBeanstalk::Environment
  Properties:
    ApplicationName: !Ref 'App'
    SolutionStackName: 64bit Amazon Linux 2 v3.4.0 running Python 3.8
    OptionSettings:
      - Namespace: aws:elasticbeanstalk:healthreporting:system
        OptionName: SystemType
        Value: enhanced
      - Namespace: aws:autoscaling:launchconfiguration
        OptionName: IamInstanceProfile
        Value: !Ref 'InstanceProfile'
```

The examples that follow show how to implement this remediation\.

#### AWS Elastic Beanstalk Configuration Template \- Example One<a name="ct-elasticbeanstalk-pr-1-remediation-2"></a>

AWS Elastic Beanstalk configuration template configured with enhanced health reporting, enabled by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticBeanstalkConfigurationTemplate": {
        "Type": "AWS::ElasticBeanstalk::ConfigurationTemplate",
        "Properties": {
            "ApplicationName": {
                "Ref": "App"
            },
            "SolutionStackName": "64bit Amazon Linux 2 v3.4.0 running Python 3.8",
            "OptionSettings": [
                {
                    "Namespace": "aws:autoscaling:launchconfiguration",
                    "OptionName": "IamInstanceProfile",
                    "Value": {
                        "Ref": "InstanceProfile"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
ElasticBeanstalkConfigurationTemplate:
  Type: AWS::ElasticBeanstalk::ConfigurationTemplate
  Properties:
    ApplicationName: !Ref 'App'
    SolutionStackName: 64bit Amazon Linux 2 v3.4.0 running Python 3.8
    OptionSettings:
      - Namespace: aws:autoscaling:launchconfiguration
        OptionName: IamInstanceProfile
        Value: !Ref 'InstanceProfile'
```

The examples that follow show how to implement this remediation\.

#### AWS Elastic Beanstalk Configuration Template \- Example Two<a name="ct-elasticbeanstalk-pr-1-remediation-3"></a>

AWS Elastic Beanstalk configuration template configured with enhanced health reporting, enabled by means of an entry in the `OptionSettings` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticBeanstalkConfigurationTemplate": {
        "Type": "AWS::ElasticBeanstalk::ConfigurationTemplate",
        "Properties": {
            "ApplicationName": {
                "Ref": "App"
            },
            "SolutionStackName": "64bit Amazon Linux 2 v3.4.0 running Python 3.8",
            "OptionSettings": [
                {
                    "Namespace": "aws:elasticbeanstalk:healthreporting:system",
                    "OptionName": "SystemType",
                    "Value": "enhanced"
                }
            ]
        }
    }
}
```

**YAML example**

```
ElasticBeanstalkConfigurationTemplate:
  Type: AWS::ElasticBeanstalk::ConfigurationTemplate
  Properties:
    ApplicationName: !Ref 'App'
    SolutionStackName: 64bit Amazon Linux 2 v3.4.0 running Python 3.8
    OptionSettings:
      - Namespace: aws:elasticbeanstalk:healthreporting:system
        OptionName: SystemType
        Value: enhanced
```

### CT\.ELASTICBEANSTALK\.PR\.1 rule specification<a name="ct-elasticbeanstalk-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elastic_beanstalk_enhanced_health_reporting_enabled_check
# 
# Description:
#   This control checks whether AWS Elastic Beanstalk environments and configuration templates are configured for 'enhanced' health reporting.
# 
# Reports on:
#   AWS::ElasticBeanstalk::Environment, AWS::ElasticBeanstalk::ConfigurationTemplate
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
#       And: The input document does not contain any Elastic Beanstalk environment resources or
#            Elastic Beanstalk configuration template resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Beanstalk environment resource
#       And: 'OptionSettings' is not present in the resource properties or is an empty list
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Beanstalk environment resource
#       And: 'OptionSettings' is present in the resource properties as a non-empty list
#       And: No entry in the 'OptionSettings' list has both a 'Namespace' property with a value of
#            'aws:elasticbeanstalk:healthreporting:system'
#            and an 'OptionName' property with value of 'SystemType'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Beanstalk environment resource or an Elastic Beanstalk
#            configuration template resource
#       And: 'OptionSettings' is present in the resource properties as a non-empty list
#       And: An entry in the 'OptionSettings' list has a 'Namespace' property with a value of
#            'aws:elasticbeanstalk:healthreporting:system'
#       And: That same entry has an 'OptionName' property with a value of 'SystemType'
#       And: That same entry has a 'Value' property with a value of anything other than 'enhanced', or the 'Value'
#            property is not provided.
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Beanstalk configuration template resource
#       And: 'OptionSettings' is not present in the resource properties or is an empty list
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Beanstalk configuration template resource
#       And: 'OptionSettings' is present in the resource properties as a non-empty list
#       And: No entry in the 'OptionSettings' list has both a 'Namespace' property with a value of
#            'aws:elasticbeanstalk:healthreporting:system'
#            and an 'OptionName' property with value of 'SystemType'
#      Then: PASS
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Beanstalk environment resource or an Elastic Beanstalk
#            configuration template resource
#       And: 'OptionSettings' is present in the resource properties as a non-empty list
#       And: Every entry in the 'OptionSettings' list that has both a 'Namespace' property with a value of
#            'aws:elasticbeanstalk:healthreporting:system'
#            and an 'OptionName' property with a value of 'SystemType' also has a 'Value' property with a value of
#            'enhanced'
#      Then: PASS

#
# Constants
#
let ELASTIC_BEANSTALK_ENVIRONMENT_TYPE = "AWS::ElasticBeanstalk::Environment"
let ELASTIC_BEANSTALK_CONFIGURATION_TEMPLATE_TYPE = "AWS::ElasticBeanstalk::ConfigurationTemplate"
let ELASTIC_BEANSTALK_ENHANCED_HEALTH_REPORTING_NAMESPACE = "aws:elasticbeanstalk:healthreporting:system"
let ELASTIC_BEANSTALK_SYSTEM_TYPE_OPTION_NAME = "SystemType"
let ELASTIC_BEANSTALK_ENHANCED_VALUE = "enhanced"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elastic_beanstalk_environments = Resources.*[ Type == %ELASTIC_BEANSTALK_ENVIRONMENT_TYPE ]
let elastic_beanstalk_configuration_templates = Resources.*[ Type == %ELASTIC_BEANSTALK_CONFIGURATION_TEMPLATE_TYPE ]

#
# Primary Rules
#
rule elastic_beanstalk_enhanced_health_reporting_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                                    %elastic_beanstalk_environments not empty {
    check_elastic_beanstalk_environments(%elastic_beanstalk_environments.Properties)
        <<
        [CT.ELASTICBEANSTALK.PR.1]: Require AWS Elastic Beanstalk environments to have enhanced health reporting enabled
        [FIX]: For AWS Elastic Beanstalk environments, configure an 'OptionSetting' with 'Namespace' set to 'aws:elasticbeanstalk:healthreporting:system', 'OptionName' set to 'SystemType', and 'Value' set to 'enhanced'. For AWS Elastic Beanstalk configuration templates, configure an 'OptionSetting' with 'Namespace' set to 'aws:elasticbeanstalk:healthreporting:system', 'OptionName' set to 'SystemType', and 'Value' set to 'enhanced'. Omit this setting to adopt the default value of 'enhanced'.
        >>
}

rule elastic_beanstalk_enhanced_health_reporting_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                                    %elastic_beanstalk_configuration_templates not empty {
    check_elastic_beanstalk_configuration_templates(%elastic_beanstalk_configuration_templates.Properties)
        <<
        [CT.ELASTICBEANSTALK.PR.1]: Require AWS Elastic Beanstalk environments to have enhanced health reporting enabled
        [FIX]: For AWS Elastic Beanstalk environments, configure an 'OptionSetting' with 'Namespace' set to 'aws:elasticbeanstalk:healthreporting:system', 'OptionName' set to 'SystemType', and 'Value' set to 'enhanced'. For AWS Elastic Beanstalk configuration templates, configure an 'OptionSetting' with 'Namespace' set to 'aws:elasticbeanstalk:healthreporting:system', 'OptionName' set to 'SystemType', and 'Value' set to 'enhanced'. Omit this setting to adopt the default value of 'enhanced'.
        >>
}

rule elastic_beanstalk_enhanced_health_reporting_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_BEANSTALK_ENVIRONMENT_TYPE) {
    check_elastic_beanstalk_environments(%INPUT_DOCUMENT.%ELASTIC_BEANSTALK_ENVIRONMENT_TYPE.resourceProperties)
        <<
        [CT.ELASTICBEANSTALK.PR.1]: Require AWS Elastic Beanstalk environments to have enhanced health reporting enabled
        [FIX]: For AWS Elastic Beanstalk environments, configure an 'OptionSetting' with 'Namespace' set to 'aws:elasticbeanstalk:healthreporting:system', 'OptionName' set to 'SystemType', and 'Value' set to 'enhanced'. For AWS Elastic Beanstalk configuration templates, configure an 'OptionSetting' with 'Namespace' set to 'aws:elasticbeanstalk:healthreporting:system', 'OptionName' set to 'SystemType', and 'Value' set to 'enhanced'. Omit this setting to adopt the default value of 'enhanced'.
        >>
}

rule elastic_beanstalk_enhanced_health_reporting_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_BEANSTALK_CONFIGURATION_TEMPLATE_TYPE) {
    check_elastic_beanstalk_configuration_templates(%INPUT_DOCUMENT.%ELASTIC_BEANSTALK_CONFIGURATION_TEMPLATE_TYPE.resourceProperties)
        <<
        [CT.ELASTICBEANSTALK.PR.1]: Require AWS Elastic Beanstalk environments to have enhanced health reporting enabled
        [FIX]: For AWS Elastic Beanstalk environments, configure an 'OptionSetting' with 'Namespace' set to 'aws:elasticbeanstalk:healthreporting:system', 'OptionName' set to 'SystemType', and 'Value' set to 'enhanced'. For AWS Elastic Beanstalk configuration templates, configure an 'OptionSetting' with 'Namespace' set to 'aws:elasticbeanstalk:healthreporting:system', 'OptionName' set to 'SystemType', and 'Value' set to 'enhanced'. Omit this setting to adopt the default value of 'enhanced'.
        >>
}

#
# Parameterized Rules
#
rule check_elastic_beanstalk_environments(elastic_beanstalk_environments) {
    %elastic_beanstalk_environments {
        # Scenario 2
        check_option_settings_exists_or_is_non_empty_list(this)

        # Scenario 3, 4, 7
        check_option_settings_enhanced(OptionSettings[*])
    }
}

rule check_elastic_beanstalk_configuration_templates(elastic_beanstalk_configuration_templates) {
    %elastic_beanstalk_configuration_templates {
        # Scenario 7
        check_option_settings_with_enhanced_health_reporting(this) or
        # Scenario 6
        check_option_settings_without_health_reporting(this) or
        # Scenario 5
        check_option_settings_not_exists_or_is_empty_list(this)
    }
}

rule check_option_settings_with_enhanced_health_reporting(elastic_beanstalk_configuration_templates) {
    %elastic_beanstalk_configuration_templates [
        filter_option_settings_with_health_reporting(this)
    ] {
        check_option_settings_enhanced(OptionSettings[*])
    }
}

rule filter_option_settings_with_health_reporting(elastic_beanstalk_configuration_templates) {
    some %elastic_beanstalk_configuration_templates {
        check_option_settings_exists_or_is_non_empty_list(this)

        some OptionSettings[*] {
            Namespace exists
            OptionName exists

            Namespace == %ELASTIC_BEANSTALK_ENHANCED_HEALTH_REPORTING_NAMESPACE
            OptionName == %ELASTIC_BEANSTALK_SYSTEM_TYPE_OPTION_NAME
        }
    }
}

rule check_option_settings_enhanced(option_settings) {
    # Scenario 3, 4
    some %option_settings[*] {
        Namespace exists
        OptionName exists
        Value exists

        Namespace == %ELASTIC_BEANSTALK_ENHANCED_HEALTH_REPORTING_NAMESPACE
        OptionName == %ELASTIC_BEANSTALK_SYSTEM_TYPE_OPTION_NAME
        Value == %ELASTIC_BEANSTALK_ENHANCED_VALUE
    }

    # Scenario 7
    let option_setting_duplicates = OptionSettings [
        Namespace exists
        OptionName exists
        Value exists

        Namespace == %ELASTIC_BEANSTALK_ENHANCED_HEALTH_REPORTING_NAMESPACE
        OptionName == %ELASTIC_BEANSTALK_SYSTEM_TYPE_OPTION_NAME
        Value != %ELASTIC_BEANSTALK_ENHANCED_VALUE
    ]
    %option_setting_duplicates empty
}


rule check_option_settings_without_health_reporting(elastic_beanstalk_configuration_templates) {
    some %elastic_beanstalk_configuration_templates {
        check_option_settings_exists_or_is_non_empty_list(this)

        let option_settings_with_health_reporting = OptionSettings [
            Namespace exists
            OptionName exists

            Namespace == %ELASTIC_BEANSTALK_ENHANCED_HEALTH_REPORTING_NAMESPACE
            OptionName == %ELASTIC_BEANSTALK_SYSTEM_TYPE_OPTION_NAME
        ]
        %option_settings_with_health_reporting empty
    }
}

rule check_option_settings_exists_or_is_non_empty_list(elastic_beanstalk_resource) {
    %elastic_beanstalk_resource {
        OptionSettings exists
        OptionSettings is_list
        OptionSettings not empty
    }
}

rule check_option_settings_not_exists_or_is_empty_list(configuration_template) {
    %configuration_template {
        OptionSettings not exists or
        check_is_empty_list(OptionSettings)
    }
}

rule check_is_empty_list(option_settings) {
    %option_settings {
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

## \[CT\.ELASTICBEANSTALK\.PR\.2\] Require an AWS Elastic Beanstalk environment to have managed platform updates configured<a name="ct-elasticbeanstalk-pr-2-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered ](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether managed platform updates in AWS Elastic Beanstalk environments and configuration templates are activated\.
+ **Control objective: **Manage vulnerabilities
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticBeanstalk::Environment`, `AWS::ElasticBeanstalk::ConfigurationTemplate`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICBEANSTALK\.PR\.2 rule specification](#ct-elasticbeanstalk-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICBEANSTALK\.PR\.2 rule specification](#ct-elasticbeanstalk-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICBEANSTALK\.PR\.2 example templates](#ct-elasticbeanstalk-pr-2-templates) 

**Explanation**

Managed platform updates ensure that the most recent platform fixes, updates, and features for the environment are installed\. Keeping patch installations up to date is an important step in securing systems\.

**Usage considerations**  
When you set up managed actions on AWS Elastic Beanstalk environments and configuration templates, you must provide `PreferredStartTime` and `UpdateLevel` option settings also\.
This control allows you to set up managed actions on AWS Elastic Beanstalk environments only, because environment\-level settings take precedence over settings that are defined in configuration templates\.
This control does not allow you to deactivate managed actions on AWS Elastic Beanstalk configuration templates\.

### Remediation for rule failure<a name="ct-elasticbeanstalk-pr-2-remediation"></a>

For AWS Elastic Beanstalk environments, create an `OptionSetting` with a `Namespace` value set to `aws:elasticbeanstalk:managedactions`, `OptionName` set to `ManagedActionsEnabled`, and `Value` set to `true`\. For Elastic Beanstalk configuration templates, create an `OptionSetting` with a `Namespace` value set to `aws:elasticbeanstalk:managedactions`, `OptionName` set to `ManagedActionsEnabled`, and `Value` set to `true`, or omit this setting to adopt the default value of `true`\.

The examples that follow show how to implement this remediation\.

#### AWS Elastic Beanstalk Environment \- Example<a name="ct-elasticbeanstalk-pr-2-remediation-1"></a>

AWS Elastic Beanstalk environment configured with managed platform updates activated\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticBeanstalkEnvironment": {
        "Type": "AWS::ElasticBeanstalk::Environment",
        "Properties": {
            "SolutionStackName": "64bit Amazon Linux 2 v3.4.0 running Python 3.8",
            "ApplicationName": {
                "Ref": "App"
            },
            "OptionSettings": [
                {
                    "Namespace": "aws:autoscaling:launchconfiguration",
                    "OptionName": "IamInstanceProfile",
                    "Value": {
                        "Ref": "InstanceProfile"
                    }
                },
                {
                    "Namespace": "aws:elasticbeanstalk:managedactions",
                    "OptionName": "ManagedActionsEnabled",
                    "Value": true
                },
                {
                    "Namespace": "aws:elasticbeanstalk:managedactions",
                    "OptionName": "PreferredStartTime",
                    "Value": "Tue:09:00"
                },
                {
                    "Namespace": "aws:elasticbeanstalk:managedactions",
                    "OptionName": "ServiceRoleForManagedUpdates",
                    "Value": "AWSServiceRoleForElasticBeanstalkManagedUpdates"
                },
                {
                    "Namespace": "aws:elasticbeanstalk:managedactions:platformupdate",
                    "OptionName": "UpdateLevel",
                    "Value": "patch"
                }
            ]
        }
    }
}
```

**YAML example**

```
ElasticBeanstalkEnvironment:
  Type: AWS::ElasticBeanstalk::Environment
  Properties:
    SolutionStackName: 64bit Amazon Linux 2 v3.4.0 running Python 3.8
    ApplicationName: !Ref 'App'
    OptionSettings:
      - Namespace: aws:autoscaling:launchconfiguration
        OptionName: IamInstanceProfile
        Value: !Ref 'InstanceProfile'
      - Namespace: aws:elasticbeanstalk:managedactions
        OptionName: ManagedActionsEnabled
        Value: true
      - Namespace: aws:elasticbeanstalk:managedactions
        OptionName: PreferredStartTime
        Value: Tue:09:00
      - Namespace: aws:elasticbeanstalk:managedactions
        OptionName: ServiceRoleForManagedUpdates
        Value: AWSServiceRoleForElasticBeanstalkManagedUpdates
      - Namespace: aws:elasticbeanstalk:managedactions:platformupdate
        OptionName: UpdateLevel
        Value: patch
```

The examples that follow show how to implement this remediation\.

#### AWS Elastic Beanstalk Configuration Template \- Example One<a name="ct-elasticbeanstalk-pr-2-remediation-2"></a>

AWS Elastic Beanstalk configuration template configured with managed platform updates enabled, by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticBeanstalkConfigurationTemplate": {
        "Type": "AWS::ElasticBeanstalk::ConfigurationTemplate",
        "Properties": {
            "ApplicationName": {
                "Ref": "App"
            },
            "SolutionStackName": "64bit Amazon Linux 2 v3.4.0 running Python 3.8",
            "OptionSettings": [
                {
                    "Namespace": "aws:autoscaling:launchconfiguration",
                    "OptionName": "IamInstanceProfile",
                    "Value": {
                        "Ref": "InstanceProfile"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
ElasticBeanstalkConfigurationTemplate:
  Type: AWS::ElasticBeanstalk::ConfigurationTemplate
  Properties:
    ApplicationName: !Ref 'App'
    SolutionStackName: 64bit Amazon Linux 2 v3.4.0 running Python 3.8
    OptionSettings:
      - Namespace: aws:autoscaling:launchconfiguration
        OptionName: IamInstanceProfile
        Value: !Ref 'InstanceProfile'
```

The examples that follow show how to implement this remediation\.

#### AWS Elastic Beanstalk Configuration Template \- Example Two<a name="ct-elasticbeanstalk-pr-2-remediation-3"></a>

AWS Elastic Beanstalk configuration template configured with managed platform updates enabled, by means of an entry in the `OptionSettings` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ElasticBeanstalkConfigurationTemplate": {
        "Type": "AWS::ElasticBeanstalk::ConfigurationTemplate",
        "Properties": {
            "ApplicationName": {
                "Ref": "App"
            },
            "SolutionStackName": "64bit Amazon Linux 2 v3.4.0 running Python 3.8",
            "OptionSettings": [
                {
                    "Namespace": "aws:elasticbeanstalk:managedactions",
                    "OptionName": "ManagedActionsEnabled",
                    "Value": true
                },
                {
                    "Namespace": "aws:elasticbeanstalk:managedactions",
                    "OptionName": "PreferredStartTime",
                    "Value": "Tue:09:00"
                },
                {
                    "Namespace": "aws:elasticbeanstalk:managedactions:platformupdate",
                    "OptionName": "UpdateLevel",
                    "Value": "minor"
                }
            ]
        }
    }
}
```

**YAML example**

```
ElasticBeanstalkConfigurationTemplate:
  Type: AWS::ElasticBeanstalk::ConfigurationTemplate
  Properties:
    ApplicationName: !Ref 'App'
    SolutionStackName: 64bit Amazon Linux 2 v3.4.0 running Python 3.8
    OptionSettings:
      - Namespace: aws:elasticbeanstalk:managedactions
        OptionName: ManagedActionsEnabled
        Value: true
      - Namespace: aws:elasticbeanstalk:managedactions
        OptionName: PreferredStartTime
        Value: Tue:09:00
      - Namespace: aws:elasticbeanstalk:managedactions:platformupdate
        OptionName: UpdateLevel
        Value: minor
```

### CT\.ELASTICBEANSTALK\.PR\.2 rule specification<a name="ct-elasticbeanstalk-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elastic_beanstalk_managed_updates_enabled_check
# 
# Description:
#   This control checks whether managed platform updates in AWS Elastic Beanstalk environments and configuration templates are activated.
# 
# Reports on:
#   AWS::ElasticBeanstalk::Environment, AWS::ElasticBeanstalk::ConfigurationTemplate
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
#       And: The input document does not contain any ElasticBeanstalk environment resources or ElasticBeanstalk
#            configuration template resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticBeanstalk environment resource
#       And: 'OptionSettings' is not present in the resource properties or is an empty list
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticBeanstalk environment resource
#       And: 'OptionSettings' is present in the resource properties as a non-empty list
#       And: No entry in the 'OptionSettings' list has both a 'Namespace' property with a value of
#            'aws:elasticbeanstalk:managedactions' and an 'OptionName' property with a value of 'ManagedActionsEnabled'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticBeanstalk environment resource or an ElasticBeanstalk
#            configuration template resource
#       And: 'OptionSettings' is present in the resource properties as a non-empty list
#       And: An entry in the 'OptionSettings' list has a 'Namespace' property with a value of
#            'aws:elasticbeanstalk:managedactions'
#       And: That same entry has an 'OptionName' property with a value of 'ManagedActionsEnabled'
#       And: That same entry has a 'Value' property with a value of anything other than bool(true), or the 'Value'
#            property is not provided.
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticBeanstalk configuration template resource
#       And: 'OptionSettings' is not present in the resource properties or is an empty list
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticBeanstalk configuration template resource
#       And: 'OptionSettings' is present in the resource properties as a non-empty list
#       And: No entry in the 'OptionSettings' list has both a 'Namespace' property with a value of
#            'aws:elasticbeanstalk:managedactions' and an 'OptionName' property with a value of 'ManagedActionsEnabled'
#      Then: PASS
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticBeanstalk environment resource or an ElasticBeanstalk
#            configuration template resource
#       And: 'OptionSettings' is present in the resource properties as a non-empty list
#       And: Every entry in the 'OptionSettings' list that has both a 'Namespace' property with a value of
#            'aws:elasticbeanstalk:managedactions' and an 'OptionName' property with a value of 'ManagedActionsEnabled'
#            also has a 'Value' property with a value of bool(true)
#      Then: PASS

#
# Constants
#
let ELASTIC_BEANSTALK_ENVIRONMENT_TYPE = "AWS::ElasticBeanstalk::Environment"
let ELASTIC_BEANSTALK_CONFIGURATION_TEMPLATE_TYPE = "AWS::ElasticBeanstalk::ConfigurationTemplate"
let ELASTIC_BEANSTALK_MANAGED_ACTIONS_NAMESPACE = "aws:elasticbeanstalk:managedactions"
let ELASTIC_BEANSTALK_MANAGED_ACTIONS_OPTION_NAME = "ManagedActionsEnabled"
let ELASTIC_BEANSTALK_MANAGED_ACTIONS_ENABLED_VALUE = ["true", true]
let INPUT_DOCUMENT = this

#
# Assignments
#
let elastic_beanstalk_environments = Resources.*[ Type == %ELASTIC_BEANSTALK_ENVIRONMENT_TYPE ]
let elastic_beanstalk_configuration_templates = Resources.*[ Type == %ELASTIC_BEANSTALK_CONFIGURATION_TEMPLATE_TYPE ]

#
# Primary Rules
#
rule elastic_beanstalk_managed_updates_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                          %elastic_beanstalk_environments not empty {
    check_elastic_beanstalk_environments(%elastic_beanstalk_environments.Properties)
        <<
        [CT.ELASTICBEANSTALK.PR.2]: Require an AWS Elastic Beanstalk environment to have managed platform updates configured
            [FIX]: For AWS Elastic Beanstalk environments, create an 'OptionSetting' with a 'Namespace' value set to  'aws:elasticbeanstalk:managedactions', 'OptionName' set to 'ManagedActionsEnabled', and 'Value' set to 'true'. For Elastic Beanstalk configuration templates, create an 'OptionSetting' with a 'Namespace' value set to  'aws:elasticbeanstalk:managedactions', 'OptionName' set to 'ManagedActionsEnabled', and 'Value' set to 'true', or omit this setting to adopt the default value of 'true'.
        >>
}

rule elastic_beanstalk_managed_updates_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                          %elastic_beanstalk_configuration_templates not empty {
    check_elastic_beanstalk_configuration_templates(%elastic_beanstalk_configuration_templates.Properties)
        <<
        [CT.ELASTICBEANSTALK.PR.2]: Require an AWS Elastic Beanstalk environment to have managed platform updates configured
            [FIX]: For AWS Elastic Beanstalk environments, create an 'OptionSetting' with a 'Namespace' value set to  'aws:elasticbeanstalk:managedactions', 'OptionName' set to 'ManagedActionsEnabled', and 'Value' set to 'true'. For Elastic Beanstalk configuration templates, create an 'OptionSetting' with a 'Namespace' value set to  'aws:elasticbeanstalk:managedactions', 'OptionName' set to 'ManagedActionsEnabled', and 'Value' set to 'true', or omit this setting to adopt the default value of 'true'.
        >>
}

rule elastic_beanstalk_managed_updates_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_BEANSTALK_ENVIRONMENT_TYPE) {
    check_elastic_beanstalk_environments(%INPUT_DOCUMENT.%ELASTIC_BEANSTALK_ENVIRONMENT_TYPE.resourceProperties)
        <<
        [CT.ELASTICBEANSTALK.PR.2]: Require an AWS Elastic Beanstalk environment to have managed platform updates configured
            [FIX]: For AWS Elastic Beanstalk environments, create an 'OptionSetting' with a 'Namespace' value set to  'aws:elasticbeanstalk:managedactions', 'OptionName' set to 'ManagedActionsEnabled', and 'Value' set to 'true'. For Elastic Beanstalk configuration templates, create an 'OptionSetting' with a 'Namespace' value set to  'aws:elasticbeanstalk:managedactions', 'OptionName' set to 'ManagedActionsEnabled', and 'Value' set to 'true', or omit this setting to adopt the default value of 'true'.
        >>
}

rule elastic_beanstalk_managed_updates_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_BEANSTALK_CONFIGURATION_TEMPLATE_TYPE) {
    check_elastic_beanstalk_configuration_templates(%INPUT_DOCUMENT.%ELASTIC_BEANSTALK_CONFIGURATION_TEMPLATE_TYPE.resourceProperties)
        <<
        [CT.ELASTICBEANSTALK.PR.2]: Require an AWS Elastic Beanstalk environment to have managed platform updates configured
            [FIX]: For AWS Elastic Beanstalk environments, create an 'OptionSetting' with a 'Namespace' value set to  'aws:elasticbeanstalk:managedactions', 'OptionName' set to 'ManagedActionsEnabled', and 'Value' set to 'true'. For Elastic Beanstalk configuration templates, create an 'OptionSetting' with a 'Namespace' value set to  'aws:elasticbeanstalk:managedactions', 'OptionName' set to 'ManagedActionsEnabled', and 'Value' set to 'true', or omit this setting to adopt the default value of 'true'.
        >>
}

#
# Parameterized Rules
#
rule check_elastic_beanstalk_environments(elastic_beanstalk_environments) {
    %elastic_beanstalk_environments {
        # Scenario 2
        check_option_settings_exists_and_is_non_empty_list(this)

        # Scenario 3, 4, 7
        check_option_settings_managed_actions_enabled(OptionSettings[*])
    }
}

rule check_elastic_beanstalk_configuration_templates(elastic_beanstalk_configuration_templates) {
    %elastic_beanstalk_configuration_templates {
        # Scenario 7
        check_option_settings_with_managed_actions_enabled(this) or
        # Scenario 6
        check_option_settings_without_managed_actions(this) or
        # Scenario 5
        check_option_settings_not_exists_or_is_empty_list(this)
    }
}

rule check_option_settings_with_managed_actions_enabled(elastic_beanstalk_configuration_templates) {
    %elastic_beanstalk_configuration_templates [
        filter_option_settings_with_managed_actions(this)
    ] {
        check_option_settings_managed_actions_enabled(OptionSettings[*])
    }
}

rule filter_option_settings_with_managed_actions(elastic_beanstalk_configuration_templates) {
    some %elastic_beanstalk_configuration_templates {
        check_option_settings_exists_and_is_non_empty_list(this)

        some OptionSettings[*] {
            Namespace exists
            OptionName exists

            Namespace == %ELASTIC_BEANSTALK_MANAGED_ACTIONS_NAMESPACE
            OptionName == %ELASTIC_BEANSTALK_MANAGED_ACTIONS_OPTION_NAME
        }
    }
}

rule check_option_settings_managed_actions_enabled(option_settings) {
    # Scenario 3, 4
    some %option_settings[*] {
        Namespace exists
        OptionName exists
        Value exists

        Namespace == %ELASTIC_BEANSTALK_MANAGED_ACTIONS_NAMESPACE
        OptionName == %ELASTIC_BEANSTALK_MANAGED_ACTIONS_OPTION_NAME
        Value in %ELASTIC_BEANSTALK_MANAGED_ACTIONS_ENABLED_VALUE
    }

    # Scenario 7
    let option_setting_duplicates = OptionSettings [
        Namespace exists
        OptionName exists
        Value exists

        Namespace == %ELASTIC_BEANSTALK_MANAGED_ACTIONS_NAMESPACE
        OptionName == %ELASTIC_BEANSTALK_MANAGED_ACTIONS_OPTION_NAME
        Value not in %ELASTIC_BEANSTALK_MANAGED_ACTIONS_ENABLED_VALUE
    ]
    %option_setting_duplicates empty
}


rule check_option_settings_without_managed_actions(elastic_beanstalk_configuration_templates) {
    some %elastic_beanstalk_configuration_templates {
        check_option_settings_exists_and_is_non_empty_list(this)

        let option_settings_with_managed_actions = OptionSettings [
            Namespace exists
            OptionName exists

            Namespace == %ELASTIC_BEANSTALK_MANAGED_ACTIONS_NAMESPACE
            OptionName == %ELASTIC_BEANSTALK_MANAGED_ACTIONS_OPTION_NAME
        ]
        %option_settings_with_managed_actions empty
    }
}

rule check_option_settings_exists_and_is_non_empty_list(elastic_beanstalk_resource) {
    %elastic_beanstalk_resource {
        OptionSettings exists
        OptionSettings is_list
        OptionSettings not empty
    }
}

rule check_option_settings_not_exists_or_is_empty_list(configuration_template) {
    %configuration_template {
        OptionSettings not exists or
        check_is_empty_list(OptionSettings)
    }
}

rule check_is_empty_list(option_settings) {
    %option_settings {
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

### CT\.ELASTICBEANSTALK\.PR\.2 example templates<a name="ct-elasticbeanstalk-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  InstanceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - elasticbeanstalk.amazonaws.com
          Action:
          - 'sts:AssumeRole'
  InstanceProfile:
    Type: AWS::IAM::InstanceProfile
    Properties:
      Roles:
      - Ref: InstanceRole
  App:
    Type: AWS::ElasticBeanstalk::Application
  ElasticBeanstalkConfigurationTemplate:
    Type: AWS::ElasticBeanstalk::ConfigurationTemplate
    Properties:
      ApplicationName:
        Ref: App
      SolutionStackName: "64bit Amazon Linux 2 v3.4.0 running Python 3.8"
      OptionSettings:
      - Namespace: aws:autoscaling:launchconfiguration
        OptionName: IamInstanceProfile
        Value:
          Ref: InstanceProfile
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  InstanceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - ec2.amazonaws.com
          Action:
          - 'sts:AssumeRole'
  InstanceProfile:
    Type: AWS::IAM::InstanceProfile
    Properties:
      Roles:
      - Ref: InstanceRole
  App:
    Type: AWS::ElasticBeanstalk::Application
  ElasticBeanstalkEnvironment:
    Type: AWS::ElasticBeanstalk::Environment
    Properties:
      SolutionStackName: "64bit Amazon Linux 2 v3.4.0 running Python 3.8"
      ApplicationName:
        Ref: App
      OptionSettings:
      - Namespace: aws:autoscaling:launchconfiguration
        OptionName: IamInstanceProfile
        Value:
          Ref: InstanceProfile
      - Namespace: aws:elasticbeanstalk:managedactions
        OptionName: ManagedActionsEnabled
        Value: false
```