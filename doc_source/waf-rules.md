# AWS WAF controls<a name="waf-rules"></a>

**Topics**
+ [\[CT\.WAF\.PR\.1\] Require any AWS WAF global rule to have a condition](#ct-waf-pr-1-description)
+ [\[CT\.WAF\.PR\.2\] Require any AWS WAF global web ACL to have a rule or rule group](#ct-waf-pr-2-description)

## \[CT\.WAF\.PR\.1\] Require any AWS WAF global rule to have a condition<a name="ct-waf-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether an AWS WAF Classic global rule contains any conditions\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::WAF::Rule`
+ **AWS CloudFormation guard rule: ** [CT\.WAF\.PR\.1 rule specification](#ct-waf-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.WAF\.PR\.1 rule specification](#ct-waf-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.WAF\.PR\.1 example templates](#ct-waf-pr-1-templates)

**Explanation**

A WAF global rule can contain multiple conditions\. A rule's conditions allow for traffic inspection and take a defined action \(allow, block, or count\)\. Without any conditions, the traffic passes without inspection\. A WAF global rule with no conditions, but with a name or tag suggesting allow, block, or count, could lead to the wrong assumption that one of those actions is occurring\.

### Remediation for rule failure<a name="ct-waf-pr-1-remediation"></a>

Provide one or more AWS WAF rule conditions within the `Predicates` property\.

The examples that follow show how to implement this remediation\.

#### AWS WAF Classic Global Rule \- Example<a name="ct-waf-pr-1-remediation-1"></a>

AWS WAF Classic global rule configured with an IP match predicate\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "WAFRule": {
        "Type": "AWS::WAF::Rule",
        "Properties": {
            "Name": "SampleWAFRule",
            "MetricName": "SampleWAFRuleMetric",
            "Predicates": [
                {
                    "DataId": {
                        "Ref": "IPSet"
                    },
                    "Negated": false,
                    "Type": "IPMatch"
                }
            ]
        }
    }
}
```

**YAML example**

```
WAFRule:
  Type: AWS::WAF::Rule
  Properties:
    Name: SampleWAFRule
    MetricName: SampleWAFRuleMetric
    Predicates:
      - DataId: !Ref 'IPSet'
        Negated: false
        Type: IPMatch
```

### CT\.WAF\.PR\.1 rule specification<a name="ct-waf-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   waf_global_rule_not_empty_check
# 
# Description:
#   This control checks whether an AWS WAF Classic global rule contains any conditions.
# 
# Reports on:
#   AWS::WAF::Rule
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
#       And: The input document does not contain any WAF Classic global rule resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a WAF Classic global rule resource
#       And: 'Predicates' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a WAF Classic global rule resource
#       And: 'Predicates' has been provided as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a WAF Classic global rule resource
#       And: 'Predicates' has been provided as a non-empty list
#      Then: PASS

#
# Constants
#
let WAF_GLOBAL_RULE_TYPE = "AWS::WAF::Rule"
let INPUT_DOCUMENT = this

#
# Assignments
#
let waf_global_rules = Resources.*[ Type == %WAF_GLOBAL_RULE_TYPE ]

#
# Primary Rules
#
rule waf_global_rule_not_empty_check when is_cfn_template(%INPUT_DOCUMENT)
                                          %waf_global_rules not empty {
    check(%waf_global_rules.Properties)
        <<
        [CT.WAF.PR.1]: Require any AWS WAF global rule to have a condition
        [FIX]: Provide one or more AWS WAF rule conditions within the 'Predicates' property.
        >>
}

rule waf_global_rule_not_empty_check when is_cfn_hook(%INPUT_DOCUMENT, %WAF_GLOBAL_RULE_TYPE) {
    check(%INPUT_DOCUMENT.%WAF_GLOBAL_RULE_TYPE.resourceProperties)
        <<
        [CT.WAF.PR.1]: Require any AWS WAF global rule to have a condition
        [FIX]: Provide one or more AWS WAF rule conditions within the 'Predicates' property.
        >>
}

#
# Parameterized Rules
#
rule check(waf_global_rule) {
    %waf_global_rule {
        # Scenario 2, 3 and 4
        Predicates exists
        Predicates is_list
        Predicates not empty
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

### CT\.WAF\.PR\.1 example templates<a name="ct-waf-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  IPSetDenylist:
    Type: AWS::WAF::IPSet
    Properties:
      Name: IPSet for deny listed IP addresses
      IPSetDescriptors:
      - Type: IPV4
        Value: 192.0.2.44/32
  WafGlobalRule:
    Type: AWS::WAF::Rule
    Properties:
      Name: ExampleWAFRule
      MetricName: ExampleWAFRuleMetric
      Predicates:
      - DataId:
          Ref: IPSetDenylist
        Negated: false
        Type: "IPMatch"
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  WafGlobalRule:
    Type: AWS::WAF::Rule
    Properties:
      Name: ExampleWAFRule
      MetricName: ExampleWAFRuleMetric
```

## \[CT\.WAF\.PR\.2\] Require any AWS WAF global web ACL to have a rule or rule group<a name="ct-waf-pr-2-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether an AWS WAF Classic global web ACL contains any WAF rules or rule groups\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::WAF::WebACL`
+ **AWS CloudFormation guard rule: ** [CT\.WAF\.PR\.2 rule specification](#ct-waf-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.WAF\.PR\.2 rule specification](#ct-waf-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.WAF\.PR\.2 example templates](#ct-waf-pr-2-templates) 

**Explanation**

A WAF global web ACL can contain a collection of rules and rule groups that inspect and control web requests\. If a web ACL is empty, the web traffic can pass without being detected or acted upon by WAF depending on the default action\.

### Remediation for rule failure<a name="ct-waf-pr-2-remediation"></a>

Provide one or more AWS WAF rules within the `Rules` property\.

The examples that follow show how to implement this remediation\.

#### AWS WAF Classic Global Web ACL \- Example<a name="ct-waf-pr-2-remediation-1"></a>

AWS WAF Classic global web ACL configured with a rule to block requests based on an IP set match\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "WafGlobalWebAcl": {
        "Type": "AWS::WAF::WebACL",
        "Properties": {
            "Name": "SampleWebACL",
            "DefaultAction": {
                "Type": "ALLOW"
            },
            "MetricName": "SampleWebACLMetric",
            "Rules": [
                {
                    "Action": {
                        "Type": "BLOCK"
                    },
                    "Priority": 1,
                    "RuleId": {
                        "Ref": "IPSetRule"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
WafGlobalWebAcl:
  Type: AWS::WAF::WebACL
  Properties:
    Name: SampleWebACL
    DefaultAction:
      Type: ALLOW
    MetricName: SampleWebACLMetric
    Rules:
      - Action:
          Type: BLOCK
        Priority: 1
        RuleId: !Ref 'IPSetRule'
```

### CT\.WAF\.PR\.2 rule specification<a name="ct-waf-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   waf_global_webacl_not_empty_check
# 
# Description:
#   This control checks whether an AWS WAF Classic global web ACL contains any WAF rules or rule groups.
# 
# Reports on:
#   AWS::WAF::WebACL
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
#       And: The input document does not contain any AWS WAF Classic global web ACL resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAF Classic global web ACL resource
#       And: 'Rules' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAF Classic global web ACL resource
#       And: 'Rules' has been provided as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAF Classic global web ACL resource
#       And: 'Rules' has been provided as a non-empty list
#      Then: PASS

#
# Constants
#
let WAF_GLOBAL_WEB_ACL_TYPE = "AWS::WAF::WebACL"
let INPUT_DOCUMENT = this

#
# Assignments
#
let waf_global_web_acls = Resources.*[ Type == %WAF_GLOBAL_WEB_ACL_TYPE ]

#
# Primary Rules
#
rule waf_global_webacl_not_empty_check when is_cfn_template(%INPUT_DOCUMENT)
                                            %waf_global_web_acls not empty {
    check(%waf_global_web_acls.Properties)
        <<
        [CT.WAF.PR.2]: Require any AWS WAF global web ACL to have a rule or rule group
        [FIX]: Provide one or more AWS WAF rules within the 'Rules' property.
        >>
}

rule waf_global_webacl_not_empty_check when is_cfn_hook(%INPUT_DOCUMENT, %WAF_GLOBAL_WEB_ACL_TYPE) {
    check(%INPUT_DOCUMENT.%WAF_GLOBAL_WEB_ACL_TYPE.resourceProperties)
        <<
        [CT.WAF.PR.2]: Require any AWS WAF global web ACL to have a rule or rule group
        [FIX]: Provide one or more AWS WAF rules within the 'Rules' property.
        >>
}

#
# Parameterized Rules
#
rule check(waf_global_web_acl) {
    %waf_global_web_acl {
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

### CT\.WAF\.PR\.2 example templates<a name="ct-waf-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  IPSetDenylist:
    Type: "AWS::WAF::IPSet"
    Properties:
      Name: "IPSet for deny listed IP addresses"
      IPSetDescriptors:
      - Type: "IPV4"
        Value: "192.0.2.44/32"
  IPSetRule:
    Type: AWS::WAF::Rule
    Properties:
      Name: ExampleIPSetRule
      MetricName: ExampleIPSetRuleMetric
      Predicates:
      - DataId:
          Ref: IPSetDenylist
        Negated: false
        Type: IPMatch
  WafGlobalWebAcl:
    Type: AWS::WAF::WebACL
    Properties:
      Name: ExampleWebACL
      DefaultAction:
        Type: ALLOW
      MetricName: ExampleWebACLMetric
      Rules:
      - Action:
          Type: BLOCK
        Priority: 1
        RuleId:
          Ref: IPSetRule
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  WafGlobalWebAcl:
    Type: AWS::WAF::WebACL
    Properties:
      Name: ExampleWebACL
      DefaultAction:
        Type: ALLOW
      MetricName: ExampleWebACLMetric
```