# AWS WAF regional controls<a name="waf-regional-rules"></a>

**Topics**
+ [\[CT\.WAF\-REGIONAL\.PR\.1\] Require any AWS WAF regional rule to have a condition](#ct-waf-regional-pr-1-description)
+ [\[CT\.WAF\-REGIONAL\.PR\.2\] Require any AWS WAF regional web access control list \(ACL\) to have a rule or rule group](#ct-waf-regional-pr-2-description)

## \[CT\.WAF\-REGIONAL\.PR\.1\] Require any AWS WAF regional rule to have a condition<a name="ct-waf-regional-pr-1-description"></a>

This control checks whether an AWS WAF Classic Regional rule contains any conditions\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::WAFRegional::Rule`
+ **AWS CloudFormation guard rule: ** [CT\.WAF\-REGIONAL\.PR\.1 rule specification](#ct-waf-regional-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.WAF\-REGIONAL\.PR\.1 rule specification](#ct-waf-regional-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.WAF\-REGIONAL\.PR\.1 example templates](#ct-waf-regional-pr-1-templates)

**Explanation**

An AWS WAF \(web application firewall\) Regional rule can contain multiple conditions\. The rule count's conditions allow for traffic inspection, based on a defined action, such as `allow`, `block`, or `count`\. Without any conditions, the traffic passes without inspection\. An AWS WAF Regional rule with no conditions, but with a name or tag suggesting `allow`, `block`, or `count`, could lead to the inaccurate assumption that one of those actions is occurring\.

### Remediation for rule failure<a name="ct-waf-regional-pr-1-remediation"></a>

Provide one or more AWS WAF rule conditions within the `Predicates` property\.

The examples that follow show how to implement this remediation\.

#### AWS WAF Classic Regional Rule \- Example<a name="ct-waf-regional-pr-1-remediation-1"></a>

AWS WAF Classic Regional rule configured with an IP match predicate\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "WafRegionalRule": {
        "Type": "AWS::WAFRegional::Rule",
        "Properties": {
            "Name": "SampleRule",
            "MetricName": "SampleRuleMetric",
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
WafRegionalRule:
  Type: AWS::WAFRegional::Rule
  Properties:
    Name: SampleRule
    MetricName: SampleRuleMetric
    Predicates:
      - DataId: !Ref 'IPSet'
        Negated: false
        Type: IPMatch
```

### CT\.WAF\-REGIONAL\.PR\.1 rule specification<a name="ct-waf-regional-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   waf_regional_rule_not_empty_check
# 
# Description:
#   This control checks whether a AWS WAF Classic Regional rule contains any conditions.
# 
# Reports on:
#   AWS::WAFRegional::Rule
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
#       And: The input document does not contain any AWS WAF Classic Regional rule resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAF Classic Regional resource
#       And: 'Predicates' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAF Classic Regional rule resource
#       And: 'Predicates' has been provided as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAF Classic Regional rule resource
#       And: 'Predicates' has been provided as a non-empty list
#      Then: PASS

#
# Constants
#
let WAF_REGIONAL_RULE_TYPE = "AWS::WAFRegional::Rule"
let INPUT_DOCUMENT = this

#
# Assignments
#
let waf_regional_rules = Resources.*[ Type == %WAF_REGIONAL_RULE_TYPE ]

#
# Primary Rules
#
rule waf_regional_rule_not_empty_check when is_cfn_template(%INPUT_DOCUMENT)
                                            %waf_regional_rules not empty {
    check(%waf_regional_rules.Properties)
        <<
        [CT.WAF-REGIONAL.PR.1]: Require any AWS WAF regional rule to have a condition
        [FIX]: Provide one or more AWS WAF rule conditions within the 'Predicates' property.
        >>
}

rule waf_regional_rule_not_empty_check when is_cfn_hook(%INPUT_DOCUMENT, %WAF_REGIONAL_RULE_TYPE) {
    check(%INPUT_DOCUMENT.%WAF_REGIONAL_RULE_TYPE.resourceProperties)
        <<
        [CT.WAF-REGIONAL.PR.1]: Require any AWS WAF regional rule to have a condition
        [FIX]: Provide one or more AWS WAF rule conditions within the 'Predicates' property.
        >>
}

#
# Parameterized Rules
#
rule check(waf_regional_rule) {
    %waf_regional_rule {
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

### CT\.WAF\-REGIONAL\.PR\.1 example templates<a name="ct-waf-regional-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  IPSetDenylist:
    Type: AWS::WAFRegional::IPSet
    Properties:
      Name: IPSet for deny listed IP addresses
      IPSetDescriptors:
      - Type: IPV4
        Value: 192.0.2.44/32
  WafRegionalRule:
    Type: AWS::WAFRegional::Rule
    Properties:
      Name: ExampleRule
      MetricName: ExampleRuleMetric
      Predicates:
      - DataId:
          Ref: IPSetDenylist
        Negated: false
        Type: "IPMatch"
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  WafRegionalRule:
    Type: AWS::WAFRegional::Rule
    Properties:
      Name: ExampleRule
      MetricName: ExampleRuleMetric
```

## \[CT\.WAF\-REGIONAL\.PR\.2\] Require any AWS WAF regional web access control list \(ACL\) to have a rule or rule group<a name="ct-waf-regional-pr-2-description"></a>

This control checks whether an AWS WAF Classic Regional web ACL contains any WAF rules or rule groups\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::WAFRegional::WebACL`
+ **AWS CloudFormation guard rule: ** [CT\.WAF\-REGIONAL\.PR\.2 rule specification](#ct-waf-regional-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.WAF\-REGIONAL\.PR\.2 rule specification](#ct-waf-regional-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.WAF\-REGIONAL\.PR\.2 example templates](#ct-waf-regional-pr-2-templates) 

**Explanation**

An AWS WAF Regional web access control list \(ACL\) can contain a collection of rules and rule groups that inspect and control web requests\. If a web ACL is empty, the web traffic can pass without being detected or acted upon by WAF, depending on the default action\.

### Remediation for rule failure<a name="ct-waf-regional-pr-2-remediation"></a>

Provide one or more AWS WAF rules within the `Rules` property\.

The examples that follow show how to implement this remediation\.

#### AWS WAF Classic Regional web ACL \- Example<a name="ct-waf-regional-pr-2-remediation-1"></a>

AWS WAF Classic Regional web ACL configured with a rule to block requests based on an IP set match\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "WafRegionalWebAcl": {
        "Type": "AWS::WAFRegional::WebACL",
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
WafRegionalWebAcl:
  Type: AWS::WAFRegional::WebACL
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

### CT\.WAF\-REGIONAL\.PR\.2 rule specification<a name="ct-waf-regional-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   waf_regional_webacl_not_empty_check
# 
# Description:
#   This control checks whether an AWS WAF Classic Regional web ACL contains any WAF rules or rule groups.
# 
# Reports on:
#   AWS::WAFRegional::WebACL
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
#       And: The input document does not contain any WAF Classic Regional web ACL resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAF Classic Regional web ACL resource
#       And: 'Rules' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAF Classic Regional web ACL resource
#       And: 'Rules' has been provided as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an AWS WAF Classic Regional web ACL resource
#       And: 'Rules' has been provided as a non-empty list
#      Then: PASS

#
# Constants
#
let WAF_REGIONAL_WEB_ACL_TYPE = "AWS::WAFRegional::WebACL"
let INPUT_DOCUMENT = this

#
# Assignments
#
let waf_regional_web_acls = Resources.*[ Type == %WAF_REGIONAL_WEB_ACL_TYPE ]

#
# Primary Rules
#
rule waf_regional_webacl_not_empty_check when is_cfn_template(%INPUT_DOCUMENT)
                                              %waf_regional_web_acls not empty {
    check(%waf_regional_web_acls.Properties)
        <<
        [CT.WAF-REGIONAL.PR.2]: Require any AWS WAF regional web access control list (ACL) to have a rule or rule group
        [FIX]: Provide one or more AWS WAF rules within the 'Rules' property.
        >>
}

rule waf_regional_webacl_not_empty_check when is_cfn_hook(%INPUT_DOCUMENT, %WAF_REGIONAL_WEB_ACL_TYPE) {
    check(%INPUT_DOCUMENT.%WAF_REGIONAL_WEB_ACL_TYPE.resourceProperties)
        <<
        [CT.WAF-REGIONAL.PR.2]: Require any AWS WAF regional web access control list (ACL) to have a rule or rule group
        [FIX]: Provide one or more AWS WAF rules within the 'Rules' property.
        >>
}

#
# Parameterized Rules
#
rule check(waf_regional_web_acl) {
    %waf_regional_web_acl {
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

### CT\.WAF\-REGIONAL\.PR\.2 example templates<a name="ct-waf-regional-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  IPSetDenylist:
    Type: "AWS::WAFRegional::IPSet"
    Properties:
      Name: "IPSet for deny listed IP addresses"
      IPSetDescriptors:
      - Type: "IPV4"
        Value: "192.0.2.44/32"
  IPSetRule:
    Type: AWS::WAFRegional::Rule
    Properties:
      Name: ExampleIPSetRule
      MetricName: ExampleIPSetRuleMetric
      Predicates:
      - DataId:
          Ref: IPSetDenylist
        Negated: false
        Type: IPMatch
  WafRegionalWebAcl:
    Type: AWS::WAFRegional::WebACL
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
  WafRegionalWebAcl:
    Type: AWS::WAFRegional::WebACL
    Properties:
      Name: ExampleWebACL
      DefaultAction:
        Type: ALLOW
      MetricName: ExampleWebACLMetric
```