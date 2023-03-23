# AWS WAFV2 controls<a name="wafv2-rules"></a>

**Topics**
+ [\[CT\.WAFV2\.PR\.1\] Require an AWS WAFV2 web ACL to be non\-empty](#ct-wafv2-pr-1-description)
+ [\[CT\.WAFV2\.PR\.2\] Require an AWS WAFV2 rule group to be non\-empty](#ct-wafv2-pr-2-description)

## \[CT\.WAFV2\.PR\.1\] Require an AWS WAFV2 web ACL to be non\-empty<a name="ct-wafv2-pr-1-description"></a>

This control checks whether an AWS WAFV2 web ACL contains any WAF rules or WAF rule groups\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::WAFv2::WebACL`
+ **AWS CloudFormation guard rule: ** [CT\.WAFV2\.PR\.1 rule specification](#ct-wafv2-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.WAFV2\.PR\.1 rule specification](#ct-wafv2-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.WAFV2\.PR\.1 example templates](#ct-wafv2-pr-1-templates) 

**Explanation**

A web access control list \(ACL\) attached to WAFv2 can contain a collection of rules and rule groups\. The rules are designed to inspect and control web requests\. If a web ACL is empty, the web traffic passes without being detected or acted upon by the web application firewall \(WAF\)\.

### Remediation for rule failure<a name="ct-wafv2-pr-1-remediation"></a>

Provide one or more AWS WAFV2 rules within the `Rules` property\.

The examples that follow show how to implement this remediation\.

#### AWS WAFV2 web ACL \- Example<a name="ct-wafv2-pr-1-remediation-1"></a>

AWS WAFV2 web ACL configured with a rule to block requests based on a cross\-site scripting \(XSS\) match\. statement\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "WAFv2WebACL": {
        "Type": "AWS::WAFv2::WebACL",
        "Properties": {
            "Scope": "REGIONAL",
            "Description": "Sample WebACL",
            "DefaultAction": {
                "Allow": {}
            },
            "VisibilityConfig": {
                "SampledRequestsEnabled": true,
                "CloudWatchMetricsEnabled": true,
                "MetricName": "SampleWebACLMetric"
            },
            "Rules": [
                {
                    "Name": "SampleXssRule",
                    "Priority": 0,
                    "Action": {
                        "Block": {}
                    },
                    "VisibilityConfig": {
                        "SampledRequestsEnabled": true,
                        "CloudWatchMetricsEnabled": true,
                        "MetricName": "SampleXssMatchMetric"
                    },
                    "Statement": {
                        "XssMatchStatement": {
                            "FieldToMatch": {
                                "AllQueryArguments": {}
                            },
                            "TextTransformations": [
                                {
                                    "Priority": 1,
                                    "Type": "NONE"
                                }
                            ]
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
WAFv2WebACL:
  Type: AWS::WAFv2::WebACL
  Properties:
    Scope: REGIONAL
    Description: Sample WebACL
    DefaultAction:
      Allow: {}
    VisibilityConfig:
      SampledRequestsEnabled: true
      CloudWatchMetricsEnabled: true
      MetricName: SampleWebACLMetric
    Rules:
      - Name: SampleXssRule
        Priority: 0
        Action:
          Block: {}
        VisibilityConfig:
          SampledRequestsEnabled: true
          CloudWatchMetricsEnabled: true
          MetricName: SampleXssMatchMetric
        Statement:
          XssMatchStatement:
            FieldToMatch:
              AllQueryArguments: {}
            TextTransformations:
              - Priority: 1
                Type: NONE
```

### CT\.WAFV2\.PR\.1 rule specification<a name="ct-wafv2-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   wafv2_webacl_not_empty_check
# 
# Description:
#   This control checks whether an AWS WAFV2 web ACL contains any WAF rules or WAF rule groups.
# 
# Reports on:
#   AWS::WAFv2::WebACL
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
#       And: The input document does not contain any WAFv2 web ACL resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an WAFv2 web ACL resource
#       And: 'Rules' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an WAFv2 web ACL resource
#       And: 'Rules' has been provided as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an WAFv2 web ACL resource
#       And: 'Rules' has been provided as a non-empty list
#      Then: PASS

#
# Constants
#
let WAFV2_WEB_ACL_TYPE = "AWS::WAFv2::WebACL"
let INPUT_DOCUMENT = this

#
# Assignments
#
let wafv2_web_acls = Resources.*[ Type == %WAFV2_WEB_ACL_TYPE ]

#
# Primary Rules
#
rule wafv2_webacl_not_empty_check when is_cfn_template(%INPUT_DOCUMENT)
                                       %wafv2_web_acls not empty {
    check(%wafv2_web_acls.Properties)
        <<
        [CT.WAFV2.PR.1]: Require an AWS WAFV2 web ACL to be non-empty
        [FIX]: Provide one or more AWS WAFv2 rules within the 'Rules' property.
        >>
}

rule wafv2_webacl_not_empty_check when is_cfn_hook(%INPUT_DOCUMENT, %WAFV2_WEB_ACL_TYPE) {
    check(%INPUT_DOCUMENT.%WAFV2_WEB_ACL_TYPE.resourceProperties)
        <<
        [CT.WAFV2.PR.1]: Require an AWS WAFV2 web ACL to be non-empty
        [FIX]: Provide one or more AWS WAFv2 rules within the 'Rules' property.
        >>
}

#
# Parameterized Rules
#
rule check(wafv2_web_acl) {
    %wafv2_web_acl {
        # Scenario 2, 3 and 4
        Rules exists
        Rules is_list
        Rules not empty
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

### CT\.WAFV2\.PR\.1 example templates<a name="ct-wafv2-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  WAFv2WebACL:
    Type: AWS::WAFv2::WebACL
    Properties:
      Scope: REGIONAL
      Description: Example WebACL
      DefaultAction:
        Allow: {}
      VisibilityConfig:
        SampledRequestsEnabled: true
        CloudWatchMetricsEnabled: true
        MetricName: ExampleWebACLMetric
      Rules:
      - Name: ExampleXssRule
        Priority: 0
        Action:
          Block: {}
        VisibilityConfig:
          SampledRequestsEnabled: true
          CloudWatchMetricsEnabled: true
          MetricName: ExampleXssMatchMetric
        Statement:
          XssMatchStatement:
            FieldToMatch:
              AllQueryArguments: {}
            TextTransformations:
            - Priority: 1
              Type: NONE
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  WAFv2WebACL:
    Type: AWS::WAFv2::WebACL
    Properties:
      Scope: REGIONAL
      Description: Example WebACL
      DefaultAction:
        Allow: {}
      VisibilityConfig:
        SampledRequestsEnabled: true
        CloudWatchMetricsEnabled: true
        MetricName: ExampleWebACLMetric
```

## \[CT\.WAFV2\.PR\.2\] Require an AWS WAFV2 rule group to be non\-empty<a name="ct-wafv2-pr-2-description"></a>

This control checks whether AWS WAFV2 rule groups contain rules\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::WAFv2::RuleGroup`
+ **AWS CloudFormation guard rule: ** [CT\.WAFV2\.PR\.2 rule specification](#ct-wafv2-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.WAFV2\.PR\.2 rule specification](#ct-wafv2-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.WAFV2\.PR\.2 example templates](#ct-wafv2-pr-2-templates) 

**Explanation**

An AWS WAFV2 rule group can contain multiple rules\. The rules are designed to inspect and control web requests\. When an empty AWS WAFV2 rule group is associated with a web ACL, and the web ACL is not associated with any other rules or rule groups, the web traffic passes without being detected or acted upon by the web application firewall \(WAF\)\.

### Remediation for rule failure<a name="ct-wafv2-pr-2-remediation"></a>

Provide one or more AWS WAFV2 rules within the `Rules` property\.

The examples that follow show how to implement this remediation\.

#### AWS WAFV2 Rule Group \- Example<a name="ct-wafv2-pr-2-remediation-1"></a>

AWS WAFV2 rule group configured with a rule to block requests based on a cross\-site scripting \(XSS\) match\. statement\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "WAFv2RuleGroup": {
        "Type": "AWS::WAFv2::RuleGroup",
        "Properties": {
            "Scope": "REGIONAL",
            "Description": "Sample Rule Group",
            "VisibilityConfig": {
                "SampledRequestsEnabled": true,
                "CloudWatchMetricsEnabled": true,
                "MetricName": "SampleRuleGroupMetric"
            },
            "Capacity": 1000,
            "Rules": [
                {
                    "Name": "XssRule",
                    "Priority": 0,
                    "Action": {
                        "Block": {}
                    },
                    "VisibilityConfig": {
                        "SampledRequestsEnabled": true,
                        "CloudWatchMetricsEnabled": true,
                        "MetricName": "SampleXssMatchMetric"
                    },
                    "Statement": {
                        "XssMatchStatement": {
                            "FieldToMatch": {
                                "AllQueryArguments": {}
                            },
                            "TextTransformations": [
                                {
                                    "Priority": 1,
                                    "Type": "NONE"
                                }
                            ]
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
WAFv2RuleGroup:
  Type: AWS::WAFv2::RuleGroup
  Properties:
    Scope: REGIONAL
    Description: Sample Rule Group
    VisibilityConfig:
      SampledRequestsEnabled: true
      CloudWatchMetricsEnabled: true
      MetricName: SampleRuleGroupMetric
    Capacity: 1000
    Rules:
      - Name: XssRule
        Priority: 0
        Action:
          Block: {}
        VisibilityConfig:
          SampledRequestsEnabled: true
          CloudWatchMetricsEnabled: true
          MetricName: SampleXssMatchMetric
        Statement:
          XssMatchStatement:
            FieldToMatch:
              AllQueryArguments: {}
            TextTransformations:
              - Priority: 1
                Type: NONE
```

### CT\.WAFV2\.PR\.2 rule specification<a name="ct-wafv2-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   wafv2_rulegroup_not_empty_check
# 
# Description:
#   This control checks whether AWS WAFV2 rule groups contain rules.
# 
# Reports on:
#   AWS::WAFv2::RuleGroup
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
#       And: The input document does not contain any AWS WAFV2 rule group resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAFV2 rule group resource
#       And: 'Rules' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAFV2 rule group resource
#       And: 'Rules' has been provided as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAFV2 rule group resource
#       And: 'Rules' has been provided as a non-empty list
#      Then: PASS

#
# Constants
#
let WAFV2_RULE_GROUP_TYPE = "AWS::WAFv2::RuleGroup"
let INPUT_DOCUMENT = this

#
# Assignments
#
let wafv2_rule_groups = Resources.*[ Type == %WAFV2_RULE_GROUP_TYPE ]

#
# Primary Rules
#
rule wafv2_rulegroup_not_empty_check when is_cfn_template(%INPUT_DOCUMENT)
                                          %wafv2_rule_groups not empty {
    check(%wafv2_rule_groups.Properties)
        <<
        [CT.WAFV2.PR.2]: Require an AWS WAFV2 rule group to be non-empty
        [FIX]: Provide one or more AWS WAFV2 rules within the 'Rules' property.
        >>
}

rule wafv2_rulegroup_not_empty_check when is_cfn_hook(%INPUT_DOCUMENT, %WAFV2_RULE_GROUP_TYPE) {
    check(%INPUT_DOCUMENT.%WAFV2_RULE_GROUP_TYPE.resourceProperties)
        <<
        [CT.WAFV2.PR.2]: Require an AWS WAFV2 rule group to be non-empty
        [FIX]: Provide one or more AWS WAFV2 rules within the 'Rules' property.
        >>
}

#
# Parameterized Rules
#
rule check(wafv2_rule_group) {
    %wafv2_rule_group {
        # Scenario 2, 3 and 4
        Rules exists
        Rules is_list
        Rules not empty
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

### CT\.WAFV2\.PR\.2 example templates<a name="ct-wafv2-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  WAFv2RuleGroup:
    Type: AWS::WAFv2::RuleGroup
    Properties:
      Scope: REGIONAL
      Description: Example Rule Group
      VisibilityConfig:
        SampledRequestsEnabled: true
        CloudWatchMetricsEnabled: true
        MetricName: SampleRuleGroupMetrics
      Capacity: 1000
      Rules:
      - Name: TestXssRule
        Priority: 0
        Action:
          Block: {}
        VisibilityConfig:
          SampledRequestsEnabled: true
          CloudWatchMetricsEnabled: true
          MetricName: ExampleXssMatchMetric
        Statement:
          XssMatchStatement:
            FieldToMatch:
              AllQueryArguments: {}
            TextTransformations:
            - Priority: 1
              Type: NONE
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  WAFv2RuleGroup:
    Type: AWS::WAFv2::RuleGroup
    Properties:
      Scope: REGIONAL
      Description: Example Rule Group
      VisibilityConfig:
        SampledRequestsEnabled: true
        CloudWatchMetricsEnabled: true
        MetricName: ExampleRuleGroupMetric
      Capacity: 1000
      Rules: []
```