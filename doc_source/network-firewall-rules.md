# AWS Network Firewall controls<a name="network-firewall-rules"></a>

**Topics**
+ [\[CT\.NETWORK\-FIREWALL\.PR\.1\] Require any AWS Network Firewall firewall policy to have an associated rule group](#ct-network-firewall-pr-1-description)
+ [\[CT\.NETWORK\-FIREWALL\.PR\.2\] Require any AWS Network Firewall firewall policy to drop or forward stateless full packets by default when they do not match a rule](#ct-network-firewall-pr-2-description)
+ [\[CT\.NETWORK\-FIREWALL\.PR\.3\] Require any AWS Network Firewall firewall policy to drop or forward fragmented packets by default when they do not match a stateless rule](#ct-network-firewall-pr-3-description)
+ [\[CT\.NETWORK\-FIREWALL\.PR\.4\] Require any AWS Network Firewall rule group to contain at least one rule](#ct-network-firewall-pr-4-description)

## \[CT\.NETWORK\-FIREWALL\.PR\.1\] Require any AWS Network Firewall firewall policy to have an associated rule group<a name="ct-network-firewall-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether there is at least one stateful or stateless rule group associated with an AWS Network Firewall firewall policy\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::NetworkFirewall::FirewallPolicy`
+ **AWS CloudFormation guard rule: ** [CT\.NETWORK\-FIREWALL\.PR\.1 rule specification](#ct-network-firewall-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.NETWORK\-FIREWALL\.PR\.1 rule specification](#ct-network-firewall-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.NETWORK\-FIREWALL\.PR\.1 example templates](#ct-network-firewall-pr-1-templates) 

**Explanation**

A firewall policy defines how your firewall monitors and handles traffic in Amazon Virtual Private Cloud \(Amazon VPC\)\. Configuration of stateless and stateful rule groups helps to filter packets and traffic flows, and to define the default traffic handling settings\.

### Remediation for rule failure<a name="ct-network-firewall-pr-1-remediation"></a>

Within the `FirewallPolicy` definition, refer to one or more rule groups in `StatefulRuleGroupReferences` or `StatelessRuleGroupReferences`\.

The examples that follow show how to implement this remediation\.

#### AWS Network Firewall Firewall Policy \- Example<a name="ct-network-firewall-pr-1-remediation-1"></a>

AWS Network Firewall firewall policy configured with stateful and stateless rule group associations\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "FirewallPolicy": {
        "Type": "AWS::NetworkFirewall::FirewallPolicy",
        "Properties": {
            "FirewallPolicyName": "sample-firewall-policy",
            "FirewallPolicy": {
                "StatelessDefaultActions": [
                    "aws:forward_to_sfe"
                ],
                "StatelessFragmentDefaultActions": [
                    "aws:drop"
                ],
                "StatefulRuleGroupReferences": [
                    {
                        "ResourceArn": {
                            "Ref": "StatefulRuleGroup"
                        }
                    }
                ],
                "StatelessRuleGroupReferences": [
                    {
                        "ResourceArn": {
                            "Ref": "StatelessRuleGroup"
                        },
                        "Priority": 100
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
FirewallPolicy:
  Type: AWS::NetworkFirewall::FirewallPolicy
  Properties:
    FirewallPolicyName: sample-firewall-policy
    FirewallPolicy:
      StatelessDefaultActions:
        - aws:forward_to_sfe
      StatelessFragmentDefaultActions:
        - aws:drop
      StatefulRuleGroupReferences:
        - ResourceArn: !Ref 'StatefulRuleGroup'
      StatelessRuleGroupReferences:
        - ResourceArn: !Ref 'StatelessRuleGroup'
          Priority: 100
```

### CT\.NETWORK\-FIREWALL\.PR\.1 rule specification<a name="ct-network-firewall-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   netfw_policy_rule_group_associated_check
# 
# Description:
#   This control checks whether there is at least one stateful or stateless rule group associated with an AWS Network Firewall firewall policy.
# 
# Reports on:
#   AWS::NetworkFirewall::FirewallPolicy
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
#       And: The input document does not contain any NetworkFirewall firewall policy resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a NetworkFirewall firewall policy resource
#       And: 'StatefulRuleGroupReferences' has not been provided in 'FirewallPolicy'
#       And: 'StatelessRuleGroupReferences' has not been provided in 'FirewallPolicy'
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a NetworkFirewall firewall policy resource
#       And: 'StatefulRuleGroupReferences' has not been provided in 'FirewallPolicy'
#       And: 'StatelessRuleGroupReferences' has been provided as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a NetworkFirewall firewall policy resource
#       And: 'StatelessRuleGroupReferences' has not been provided in 'FirewallPolicy'
#       And: 'StatefulRuleGroupReferences' has been provided as an empty list
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a NetworkFirewall firewall policy resource
#       And: 'StatelessRuleGroupReferences' has been provided as an empty list
#       And: 'StatefulRuleGroupReferences' has been provided as an empty list
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a NetworkFirewall firewall policy resource
#       And: One or both of 'StatelessRuleGroupReferences' and 'StatefulRuleGroupReferences' have been provided as a
#            non-empty list
#      Then: PASS

#
# Constants
#
let NETFW_FIREWALL_POLICY_TYPE = "AWS::NetworkFirewall::FirewallPolicy"
let INPUT_DOCUMENT = this

#
# Assignments
#
let netfw_firewall_policies = Resources.*[ Type == %NETFW_FIREWALL_POLICY_TYPE ]

#
# Primary Rules
#
rule netfw_policy_rule_group_associated_check when is_cfn_template(%INPUT_DOCUMENT)
                                                   %netfw_firewall_policies not empty {
    check(%netfw_firewall_policies.Properties)
        <<
        [CT.NETWORK-FIREWALL.PR.1]: Require any AWS Network Firewall firewall policy to have an associated rule group
            [FIX]: Within the 'FirewallPolicy' definition, refer to one or more rule groups in 'StatefulRuleGroupReferences' or 'StatelessRuleGroupReferences'.
        >>
}

rule netfw_policy_rule_group_associated_check when is_cfn_hook(%INPUT_DOCUMENT, %NETFW_FIREWALL_POLICY_TYPE) {
    check(%INPUT_DOCUMENT.%NETFW_FIREWALL_POLICY_TYPE.resourceProperties)
        <<
        [CT.NETWORK-FIREWALL.PR.1]: Require any AWS Network Firewall firewall policy to have an associated rule group
            [FIX]: Within the 'FirewallPolicy' definition, refer to one or more rule groups in 'StatefulRuleGroupReferences' or 'StatelessRuleGroupReferences'.
        >>
}

#
# Parameterized Rules
#
rule check(netfw_firewall_policy) {
    %netfw_firewall_policy {
        # Scenario 2
        FirewallPolicy exists
        FirewallPolicy is_struct

        FirewallPolicy {
            # Scenario 3, 4, 5 and 6
            StatefulRuleGroupReferences exists or
            StatelessRuleGroupReferences exists

            check_property_is_list_and_not_empty(StatefulRuleGroupReferences) or
            check_property_is_list_and_not_empty(StatelessRuleGroupReferences)
        }
    }
}

rule check_property_is_list_and_not_empty(property) {
    %property {
        this is_list
        this not empty
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

### CT\.NETWORK\-FIREWALL\.PR\.1 example templates<a name="ct-network-firewall-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  StatefulRuleGroup:
    Type: AWS::NetworkFirewall::RuleGroup
    Properties:
      RuleGroupName:
        Fn::Sub: ${AWS::StackName}-stateful-example
      Type: STATEFUL
      RuleGroup:
        RulesSource:
          RulesString: pass tcp 10.20.20.0/24 45400:45500 < 10.10.10.0/24 5203 (msg:"test";sid:1;rev:1;)
      Capacity: 100
  StatelessRuleGroup:
    Type: AWS::NetworkFirewall::RuleGroup
    Properties:
      RuleGroupName:
        Fn::Sub: ${AWS::StackName}-stateless-example
      Type: STATELESS
      RuleGroup:
        RulesSource:
          StatelessRulesAndCustomActions:
            StatelessRules: []
      Capacity: 100
  FirewallPolicy:
    Type: AWS::NetworkFirewall::FirewallPolicy
    Properties:
      FirewallPolicyName:
        Fn::Sub: ${AWS::StackName}-example
      FirewallPolicy:
        StatelessDefaultActions:
        - aws:forward_to_sfe
        StatelessFragmentDefaultActions:
        - aws:drop
        StatefulRuleGroupReferences:
        - ResourceArn:
            Ref: StatefulRuleGroup
        StatelessRuleGroupReferences:
        - ResourceArn:
            Ref: StatelessRuleGroup
          Priority: 100
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  FirewallPolicy:
    Type: AWS::NetworkFirewall::FirewallPolicy
    Properties:
      FirewallPolicyName:
        Fn::Sub: ${AWS::StackName}-example
      FirewallPolicy:
        StatelessDefaultActions:
        - aws:forward_to_sfe
        StatelessFragmentDefaultActions:
        - aws:drop
```

## \[CT\.NETWORK\-FIREWALL\.PR\.2\] Require any AWS Network Firewall firewall policy to drop or forward stateless full packets by default when they do not match a rule<a name="ct-network-firewall-pr-2-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether an AWS Network Firewall firewall policy is configured with a user\-defined stateless default action for full packets\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::NetworkFirewall::FirewallPolicy`
+ **AWS CloudFormation guard rule: ** [CT\.NETWORK\-FIREWALL\.PR\.2 rule specification](#ct-network-firewall-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.NETWORK\-FIREWALL\.PR\.2 rule specification](#ct-network-firewall-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.NETWORK-FIREWALL.PR.2) 

**Explanation**

A firewall policy defines how your firewall monitors and handles traffic in Amazon VPC\. You configure stateless and stateful rule groups to filter packets and traffic flows\. Defaulting to `Pass` can allow unintended traffic\.

### Remediation for rule failure<a name="ct-network-firewall-pr-2-remediation"></a>

Within `FirewallPolicy`, include one of `aws:drop` or `aws:forward_to_sfe` in `StatelessDefaultActions`\.

The examples that follow show how to implement this remediation\.

#### AWS Network Firewall Firewall Policy \- Example One<a name="ct-network-firewall-pr-2-remediation-1"></a>

AWS Network Firewall firewall policy configured with a stateless default action to drop full packets\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "FirewallPolicy": {
        "Type": "AWS::NetworkFirewall::FirewallPolicy",
        "Properties": {
            "FirewallPolicyName": {
                "Fn::Sub": "${AWS::StackName}-sample"
            },
            "FirewallPolicy": {
                "StatelessFragmentDefaultActions": [
                    "aws:forward_to_sfe"
                ],
                "StatelessDefaultActions": [
                    "aws:drop"
                ]
            }
        }
    }
}
```

**YAML example**

```
FirewallPolicy:
  Type: AWS::NetworkFirewall::FirewallPolicy
  Properties:
    FirewallPolicyName: !Sub '${AWS::StackName}-sample'
    FirewallPolicy:
      StatelessFragmentDefaultActions:
        - aws:forward_to_sfe
      StatelessDefaultActions:
        - aws:drop
```

The examples that follow show how to implement this remediation\.

#### AWS Network Firewall Firewall Policy \- Example Two<a name="ct-network-firewall-pr-2-remediation-2"></a>

AWS Network Firewall firewall policy configured with a stateless default action to forward full packets to the stateful rule engine for further inspection\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "FirewallPolicy": {
        "Type": "AWS::NetworkFirewall::FirewallPolicy",
        "Properties": {
            "FirewallPolicyName": {
                "Fn::Sub": "${AWS::StackName}-sample"
            },
            "FirewallPolicy": {
                "StatelessFragmentDefaultActions": [
                    "aws:forward_to_sfe"
                ],
                "StatelessDefaultActions": [
                    "aws:forward_to_sfe"
                ]
            }
        }
    }
}
```

**YAML example**

```
FirewallPolicy:
  Type: AWS::NetworkFirewall::FirewallPolicy
  Properties:
    FirewallPolicyName: !Sub '${AWS::StackName}-sample'
    FirewallPolicy:
      StatelessFragmentDefaultActions:
        - aws:forward_to_sfe
      StatelessDefaultActions:
        - aws:forward_to_sfe
```

### CT\.NETWORK\-FIREWALL\.PR\.2 rule specification<a name="ct-network-firewall-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   netfw_policy_default_action_full_packets_check
# 
# Description:
#   This control checks whether an AWS Network Firewall firewall policy is configured with a user-defined stateless default action for full packets.
# 
# Reports on:
#   AWS::NetworkFirewall::FirewallPolicy
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
#       And: The input document does not contain any Network Firewall firewall policy resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall firewall policy resource
#       And: 'StatelessDefaultActions' has not been provided in 'FirewallPolicy'
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall firewall policy resource
#       And: 'StatelessDefaultActions' has been provided in 'FirewallPolicy' as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall firewall policy resource
#       And: 'StatelessDefaultActions' has been provided in 'FirewallPolicy' as a list that does not contain
#             one of 'aws:drop' or 'aws:forward_to_sfe'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall firewall policy resource
#       And: 'StatelessDefaultActions' has been provided in 'FirewallPolicy' as a list that contains either
#            'aws:drop' or 'aws:forward_to_sfe'
#      Then: PASS


#
# Constants
#
let NETFW_FIREWALL_POLICY_TYPE = "AWS::NetworkFirewall::FirewallPolicy"
let INPUT_DOCUMENT = this
let ALLOWED_STATELESS_ACTIONS_LIST = [ "aws:drop", "aws:forward_to_sfe" ]

#
# Assignments
#
let netfw_firewall_policies = Resources.*[ Type == %NETFW_FIREWALL_POLICY_TYPE ]

#
# Primary Rules
#
rule netfw_policy_default_action_full_packets_check when is_cfn_template(%INPUT_DOCUMENT)
                                                             %netfw_firewall_policies not empty {
    check(%netfw_firewall_policies.Properties)
        <<
        [CT.NETWORK-FIREWALL.PR.2]: Require any AWS Network Firewall firewall policy to drop or forward stateless full packets by default when they do not match a rule
        [FIX]: Within 'FirewallPolicy', include one of 'aws:drop' or 'aws:forward_to_sfe' in 'StatelessDefaultActions'.
        >>
}

rule netfw_policy_default_action_full_packets_check when is_cfn_hook(%INPUT_DOCUMENT, %NETFW_FIREWALL_POLICY_TYPE) {
    check(%INPUT_DOCUMENT.%NETFW_FIREWALL_POLICY_TYPE.resourceProperties)
        <<
        [CT.NETWORK-FIREWALL.PR.2]: Require any AWS Network Firewall firewall policy to drop or forward stateless full packets by default when they do not match a rule
        [FIX]: Within 'FirewallPolicy', include one of 'aws:drop' or 'aws:forward_to_sfe' in 'StatelessDefaultActions'.
        >>
}

#
# Parameterized Rules
#
rule check(netfw_firewall_policy) {
    %netfw_firewall_policy {
        # Scenario 2
        FirewallPolicy exists
        FirewallPolicy is_struct

        FirewallPolicy {
            StatelessDefaultActions exists
            # Scenario 3
            StatelessDefaultActions is_list
            StatelessDefaultActions not empty

            # Scenario 4 and 5
            some StatelessDefaultActions[*] {
                this in %ALLOWED_STATELESS_ACTIONS_LIST
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

## \[CT\.NETWORK\-FIREWALL\.PR\.3\] Require any AWS Network Firewall firewall policy to drop or forward fragmented packets by default when they do not match a stateless rule<a name="ct-network-firewall-pr-3-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether an AWS Network Firewall firewall policy is configured with a default action to drop or forward fragmented packets, when the packets do not match a stateless rule\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::NetworkFirewall::FirewallPolicy`
+ **AWS CloudFormation guard rule: ** [CT\.NETWORK\-FIREWALL\.PR\.3 rule specification](#ct-network-firewall-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.NETWORK\-FIREWALL\.PR\.3 rule specification](#ct-network-firewall-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.NETWORK-FIREWALL.PR.3) 

**Explanation**

A firewall policy defines how your firewall monitors and handles traffic in Amazon VPC\. You configure stateless and stateful rule groups to filter packets and traffic flows\. Defaulting to `Pass` can allow unintended traffic\.

### Remediation for rule failure<a name="ct-network-firewall-pr-3-remediation"></a>

Within `FirewallPolicy`, include one of `aws:drop` or `aws:forward_to_sfe` in `StatelessFragmentDefaultActions`\.

The examples that follow show how to implement this remediation\.

#### AWS Network Firewall Firewall Policy \- Example One<a name="ct-network-firewall-pr-3-remediation-1"></a>

AWS Network Firewall firewall policy configured with a stateless default action to drop fragmented packets\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "FirewallPolicy": {
        "Type": "AWS::NetworkFirewall::FirewallPolicy",
        "Properties": {
            "FirewallPolicyName": {
                "Fn::Sub": "${AWS::StackName}-sample"
            },
            "FirewallPolicy": {
                "StatelessDefaultActions": [
                    "aws:forward_to_sfe"
                ],
                "StatelessFragmentDefaultActions": [
                    "aws:drop"
                ]
            }
        }
    }
}
```

**YAML example**

```
FirewallPolicy:
  Type: AWS::NetworkFirewall::FirewallPolicy
  Properties:
    FirewallPolicyName: !Sub '${AWS::StackName}-sample'
    FirewallPolicy:
      StatelessDefaultActions:
        - aws:forward_to_sfe
      StatelessFragmentDefaultActions:
        - aws:drop
```

The examples that follow show how to implement this remediation\.

#### AWS Network Firewall Firewall Policy \- Example Two<a name="ct-network-firewall-pr-3-remediation-2"></a>

AWS Network Firewall firewall policy configured with a stateless default action to forward fragmented packets to the stateful rule engine for further inspection\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "FirewallPolicy": {
        "Type": "AWS::NetworkFirewall::FirewallPolicy",
        "Properties": {
            "FirewallPolicyName": {
                "Fn::Sub": "${AWS::StackName}-sample"
            },
            "FirewallPolicy": {
                "StatelessDefaultActions": [
                    "aws:forward_to_sfe"
                ],
                "StatelessFragmentDefaultActions": [
                    "aws:forward_to_sfe"
                ]
            }
        }
    }
}
```

**YAML example**

```
FirewallPolicy:
  Type: AWS::NetworkFirewall::FirewallPolicy
  Properties:
    FirewallPolicyName: !Sub '${AWS::StackName}-sample'
    FirewallPolicy:
      StatelessDefaultActions:
        - aws:forward_to_sfe
      StatelessFragmentDefaultActions:
        - aws:forward_to_sfe
```

### CT\.NETWORK\-FIREWALL\.PR\.3 rule specification<a name="ct-network-firewall-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   netfw_policy_default_action_fragment_packets_check
# 
# Description:
#   This control checks whether an AWS Network Firewall firewall policy is configured with a default action to drop or forward fragmented packets, when the packets do not match a stateless rule.
# 
# Reports on:
#   AWS::NetworkFirewall::FirewallPolicy
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
#       And: The input document does not contain any Network Firewall firewall policy resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall firewall policy resource
#       And: 'StatelessFragmentDefaultActions' has not been provided in 'FirewallPolicy'
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall firewall policy resource
#       And: 'StatelessFragmentDefaultActions' has been provided in 'FirewallPolicy' as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall firewall policy resource
#       And: 'StatelessFragmentDefaultActions' has been provided in 'FirewallPolicy' as a list that does not contain
#             one of 'aws:drop' or 'aws:forward_to_sfe'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall firewall policy resource
#       And: 'StatelessFragmentDefaultActions' has been provided in 'FirewallPolicy' as a list that contains either
#            'aws:drop' or 'aws:forward_to_sfe'
#      Then: PASS


#
# Constants
#
let NETFW_FIREWALL_POLICY_TYPE = "AWS::NetworkFirewall::FirewallPolicy"
let ALLOWED_STATELESS_FRAGMENT_ACTIONS_LIST = [ "aws:drop", "aws:forward_to_sfe" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let netfw_firewall_policies = Resources.*[ Type == %NETFW_FIREWALL_POLICY_TYPE ]

#
# Primary Rules
#
rule netfw_policy_default_action_fragment_packets_check when is_cfn_template(%INPUT_DOCUMENT)
                                                             %netfw_firewall_policies not empty {
    check(%netfw_firewall_policies.Properties)
        <<
        [CT.NETWORK-FIREWALL.PR.3]: Require any AWS Network Firewall firewall policy to drop or forward fragmented packets by default when they do not match a stateless rule
        [FIX]: Within 'FirewallPolicy', include one of 'aws:drop' or 'aws:forward_to_sfe' in 'StatelessFragmentDefaultActions'.
        >>
}

rule netfw_policy_default_action_fragment_packets_check when is_cfn_hook(%INPUT_DOCUMENT, %NETFW_FIREWALL_POLICY_TYPE) {
    check(%INPUT_DOCUMENT.%NETFW_FIREWALL_POLICY_TYPE.resourceProperties)
        <<
        [CT.NETWORK-FIREWALL.PR.3]: Require any AWS Network Firewall firewall policy to drop or forward fragmented packets by default when they do not match a stateless rule
        [FIX]: Within 'FirewallPolicy', include one of 'aws:drop' or 'aws:forward_to_sfe' in 'StatelessFragmentDefaultActions'.
        >>
}

#
# Parameterized Rules
#
rule check(netfw_firewall_policy) {
    %netfw_firewall_policy {
        # Scenario 2
        FirewallPolicy exists
        FirewallPolicy is_struct

        FirewallPolicy {
            # Scenario 2 and 3
            StatelessFragmentDefaultActions exists
            StatelessFragmentDefaultActions is_list
            StatelessFragmentDefaultActions not empty

            # Scenario 4 and 5
            some StatelessFragmentDefaultActions[*] {
                this in %ALLOWED_STATELESS_FRAGMENT_ACTIONS_LIST
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

## \[CT\.NETWORK\-FIREWALL\.PR\.4\] Require any AWS Network Firewall rule group to contain at least one rule<a name="ct-network-firewall-pr-4-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether an AWS Network Firewall stateless rule group contains rules\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::NetworkFirewall::RuleGroup`
+ **AWS CloudFormation guard rule: ** [CT\.NETWORK\-FIREWALL\.PR\.4 rule specification](#ct-network-firewall-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.NETWORK\-FIREWALL\.PR\.4 rule specification](#ct-network-firewall-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.NETWORK-FIREWALL.PR.4) 

**Explanation**

A rule group contains rules that define how your firewall processes traffic in your VPC\. An empty, stateless rule group, when present in a firewall policy, might give the impression that the rule group will process traffic\. However, when the stateless rule group is empty, it does not process traffic\.

**Usage considerations**  
This control applies only to AWS Network Firewall stateless rule groups\.

### Remediation for rule failure<a name="ct-network-firewall-pr-4-remediation"></a>

Provide one or more AWS Network Firewall stateless rules within the `RuleGroup.RulesSource.StatelessRulesAndCustomActions.StatelessRules` property\.

The examples that follow show how to implement this remediation\.

#### AWS Network Firewall Rule Group \- Example<a name="ct-network-firewall-pr-4-remediation-1"></a>

AWS Network Firewall rule group configured with a stateless rule\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "NetworkFirewallRuleGroup": {
        "Type": "AWS::NetworkFirewall::RuleGroup",
        "Properties": {
            "RuleGroupName": {
                "Fn::Sub": "${AWS::StackName}-sample"
            },
            "Capacity": 100,
            "Description": "Sample rule group",
            "Type": "STATELESS",
            "RuleGroup": {
                "RulesSource": {
                    "StatelessRulesAndCustomActions": {
                        "StatelessRules": [
                            {
                                "RuleDefinition": {
                                    "MatchAttributes": {
                                        "Sources": [
                                            {
                                                "AddressDefinition": "0.0.0.0/0"
                                            }
                                        ],
                                        "Destinations": [
                                            {
                                                "AddressDefinition": "10.0.0.0/8"
                                            }
                                        ],
                                        "SourcePorts": [
                                            {
                                                "FromPort": 15000,
                                                "ToPort": 30000
                                            }
                                        ],
                                        "DestinationPorts": [
                                            {
                                                "FromPort": 443,
                                                "ToPort": 443
                                            }
                                        ],
                                        "Protocols": [
                                            6
                                        ]
                                    },
                                    "Actions": [
                                        "aws:forward_to_sfe"
                                    ]
                                },
                                "Priority": 1
                            }
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
NetworkFirewallRuleGroup:
  Type: AWS::NetworkFirewall::RuleGroup
  Properties:
    RuleGroupName: !Sub '${AWS::StackName}-sample'
    Capacity: 100
    Description: Sample rule group
    Type: STATELESS
    RuleGroup:
      RulesSource:
        StatelessRulesAndCustomActions:
          StatelessRules:
            - RuleDefinition:
                MatchAttributes:
                  Sources:
                    - AddressDefinition: '0.0.0.0/0'
                  Destinations:
                    - AddressDefinition: 10.0.0.0/8
                  SourcePorts:
                    - FromPort: 15000
                      ToPort: 30000
                  DestinationPorts:
                    - FromPort: 443
                      ToPort: 443
                  Protocols:
                    - 6
                Actions:
                  - aws:forward_to_sfe
              Priority: 1
```

### CT\.NETWORK\-FIREWALL\.PR\.4 rule specification<a name="ct-network-firewall-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   netfw_stateless_rule_group_not_empty_check
# 
# Description:
#   This control checks whether an AWS Network Firewall stateless rule group contains rules.
# 
# Reports on:
#   AWS::NetworkFirewall::RuleGroup
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
#       And: The input document does not contain any Network Firewall rule group resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall rule group resource
#       And: 'Type' is not equal to 'STATELESS'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall rule group resource
#       And: 'Type' is 'STATELESS'
#       And: 'RuleGroup.RulesSource.StatelessRulesAndCustomActions' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall rule group resource
#       And: 'Type' is 'STATELESS'
#       And: 'RuleGroup.RulesSource.StatelessRulesAndCustomActions' has been provided
#       And: 'StatelessRules' has not been provided within 'StatelessRulesAndCustomActions' or has been provided with
#            an empty list value
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Network Firewall rule group resource
#       And: 'Type' is 'STATELESS'
#       And: 'RuleGroup.RulesSource.StatelessRulesAndCustomActions' has been provided
#       And: 'StatelessRules' has been provided within 'StatelessRulesAndCustomActions' as a non-empty list value
#      Then: PASS

#
# Constants
#
let NETFW_RULE_GROUP_TYPE = "AWS::NetworkFirewall::RuleGroup"
let INPUT_DOCUMENT = this

#
# Assignments
#
let netfw_rule_group = Resources.*[ Type == %NETFW_RULE_GROUP_TYPE ]

#
# Primary Rules
#
rule netfw_stateless_rule_group_not_empty_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %netfw_rule_group not empty {
    check(%netfw_rule_group.Properties)
        <<
        [CT.NETWORK-FIREWALL.PR.4]: Require any AWS Network Firewall rule group to contain at least one rule
        [FIX]: Provide one or more AWS Network Firewall stateless rules within the 'RuleGroup.RulesSource.StatelessRulesAndCustomActions.StatelessRules' property.
        >>
}

rule netfw_stateless_rule_group_not_empty_check when is_cfn_hook(%INPUT_DOCUMENT, %NETFW_RULE_GROUP_TYPE) {
    check(%INPUT_DOCUMENT.%NETFW_RULE_GROUP_TYPE.resourceProperties)
        <<
        [CT.NETWORK-FIREWALL.PR.4]: Require any AWS Network Firewall rule group to contain at least one rule
        [FIX]: Provide one or more AWS Network Firewall stateless rules within the 'RuleGroup.RulesSource.StatelessRulesAndCustomActions.StatelessRules' property.
        >>
}

#
# Parameterized Rules
#
rule check(netfw_rule_group) {
    %netfw_rule_group[
        # Scenario 2
        Type exists
        Type == "STATELESS"
    ] {
        # Scenario 3
        RuleGroup exists
        RuleGroup is_struct
        RuleGroup {
            RulesSource exists
            RulesSource is_struct
            RulesSource {
                StatelessRulesAndCustomActions exists
                StatelessRulesAndCustomActions is_struct
                StatelessRulesAndCustomActions {
                    # Scenarios 4 and 5
                    StatelessRules exists
                    StatelessRules is_list
                    StatelessRules not empty
                }
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