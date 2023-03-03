# Elastic Load Balancing controls<a name="elb-rules"></a>

**Topics**
+ [\[CCT\.ELASTICLOADBALANCING\.PR\.1\] Require any application load balancer listener default actions to redirect all HTTP requests to HTTPS](#ct-elasticloadbalancing-pr-1-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.2\] Require any Amazon ELB application or network load balancer to have an AWS Certificate Manager certificate](#ct-elasticloadbalancing-pr-2-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.3\] Require any application load balancer to have defensive or strictest desync mitigation mode activated](#ct-elasticloadbalancing-pr-3-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.4\] Require that any application load balancer must be configured to drop HTTP headers](#ct-elasticloadbalancing-pr-4-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.5\] Require that application load balancer deletion protection is activated](#ct-elasticloadbalancing-pr-5-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.6\] Require that application and network load balancer access logging is activated](#ct-elasticloadbalancing-pr-6-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.7\] Require any classic load balancer to have multiple Availability Zones configured](#ct-elasticloadbalancing-pr-7-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.8\] Require any classic load balancer SSL/HTTPS listener to have a certificate provided by AWS Certificate Manager](#ct-elasticloadbalancing-pr-8-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.9\] Require that an AWS ELB Application or Classic Load Balancer listener is configured with HTTPS or TLS termination](#ct-elasticloadbalancing-pr-9-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.10\] Require an ELB application or classic load balancer to have logging activated](#ct-elasticloadbalancing-pr-10-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.11\] Require any ELB classic load balancer to have connection draining activated](#ct-elasticloadbalancing-pr-11-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.12\] Require any ELB classic load balancer SSL/HTTPS listener to have a predefined security policy with a strong configuration](#ct-elasticloadbalancing-pr-12-description)
+ [\[CT\.ELASTICLOADBALANCING\.PR\.13\] Require any ELB classic load balancer to have cross\-zone load balancing activated](#ct-elasticloadbalancing-pr-13-description)

## \[CCT\.ELASTICLOADBALANCING\.PR\.1\] Require any application load balancer listener default actions to redirect all HTTP requests to HTTPS<a name="ct-elasticloadbalancing-pr-1-description"></a>

This control checks whether HTTP to HTTPS redirection is configured as a default action on HTTP listeners of Application Load Balancers\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancingV2::Listener`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.1 rule specification](#ct-elasticloadbalancing-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.1 rule specification](#ct-elasticloadbalancing-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.1 example templates](#ct-elasticloadbalancing-pr-1-templates) 

**Explanation**

Before you start to use your Application Load Balancer, you must add one or more listeners\. A listener is a process that uses the configured protocol and port to check for connection requests\. Listeners support HTTP and HTTPS protocols\. You can use an HTTPS listener to offload the work of encryption and decryption to your Application Load Balancer\. You should utilize redirect actions with Application Load Balancer to redirect any client HTTP request to an HTTPS request on port 443, to enforce encryption in transit\.

**Usage considerations**  
This control evaluates only the default actions on Application Load Balancer listeners\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-1-remediation"></a>

Configure a default HTTPS redirect action on Application Load Balancer HTTP listeners\.

The examples that follow show how to implement this remediation\.

#### Application Load Balancer Listener \- Example<a name="ct-elasticloadbalancing-pr-1-remediation-1"></a>

Application load balancer listener configured with a default action that redirects HTTP requests on port 80 to HTTPS requests on port 443, retaining the original host name, path, and query string\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "Listener": {
        "Type": "AWS::ElasticLoadBalancingV2::Listener",
        "Properties": {
            "LoadBalancerArn": {
                "Ref": "ApplicationLoadBalancer"
            },
            "Port": 80,
            "Protocol": "HTTP",
            "DefaultActions": [
                {
                    "Type": "redirect",
                    "RedirectConfig": {
                        "Protocol": "HTTPS",
                        "Port": 443,
                        "Host": "#{host}",
                        "Path": "/#{path}",
                        "Query": "#{query}",
                        "StatusCode": "HTTP_301"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
Listener:
  Type: AWS::ElasticLoadBalancingV2::Listener
  Properties:
    LoadBalancerArn: !Ref 'ApplicationLoadBalancer'
    Port: 80
    Protocol: HTTP
    DefaultActions:
      - Type: redirect
        RedirectConfig:
          Protocol: HTTPS
          Port: 443
          Host: '#{host}'
          Path: /#{path}
          Query: '#{query}'
          StatusCode: HTTP_301
```

### CT\.ELASTICLOADBALANCING\.PR\.1 rule specification<a name="ct-elasticloadbalancing-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   alb_http_to_https_redirection_check
# 
# Description:
#   This control checks whether HTTP to HTTPS redirection is configured as a default action on HTTP listeners of Application Load Balancers.
# 
# Reports on:
#   AWS::ElasticLoadBalancingV2::Listener
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
#       And: The input document does not contain any ElasticLoadBalancingV2 listener resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener
#       And: 'Protocol' is set to a value other than 'HTTP'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener
#       And: 'Protocol' is set to 'HTTP'
#       And: 'DefaultActions' is missing or is provided and an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener
#       And: 'Protocol' is set to 'HTTP'
#       And: 'DefaultActions' contains an action with 'Type' set to a value other than 'redirect'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener
#       And: 'Protocol' is set to 'HTTP'
#       And: 'DefaultActions' contains an action with 'Type' set to a value of 'redirect'
#       And: 'RedirectConfig.Protocol' is missing or set to a value other than 'HTTPS'
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener
#       And: 'Protocol' is set to 'HTTP'
#       And: All 'DefaultActions' have an action with 'Type' set to a value of 'redirect' and
#            'Protocol.RedirectConfig' set to the value 'HTTPS'
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_V2_LISTENER_TYPE = "AWS::ElasticLoadBalancingV2::Listener"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elb_v2_listeners = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_V2_LISTENER_TYPE ]

#
# Primary Rules
#
rule alb_http_to_https_redirection_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %elb_v2_listeners not empty {
    check(%elb_v2_listeners.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.1]: Require any application load balancer listener default actions to redirect all HTTP requests to HTTPS
        [FIX]: Configure a default HTTPS redirect action on application load balancer HTTP listeners.
        >>
}

rule alb_http_to_https_redirection_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_V2_LISTENER_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_V2_LISTENER_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.1]: Require any application load balancer listener default actions to redirect all HTTP requests to HTTPS
        [FIX]: Configure a default HTTPS redirect action on application load balancer HTTP listeners.
        >>
}

#
# Parameterized Rules
#
rule check(elbv2_listener) {
    %elbv2_listener [
        # Scenario 2
        Protocol in [ "HTTP" ]
    ] {
        # Scenarios 3
        DefaultActions exists
        DefaultActions is_list
        DefaultActions not empty

        # Scenario 4 and 5
        DefaultActions[*] {
            Type == "redirect"

            RedirectConfig exists
            RedirectConfig is_struct
            RedirectConfig {
                Protocol exists
                Protocol == "HTTPS"
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

### CT\.ELASTICLOADBALANCING\.PR\.1 example templates<a name="ct-elasticloadbalancing-pr-1-templates"></a>

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
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
  Listener:
    Type: AWS::ElasticLoadBalancingV2::Listener
    Properties:
      LoadBalancerArn:
        Ref: ApplicationLoadBalancer
      Port: 80
      Protocol: HTTP
      DefaultActions:
      - Type: redirect
        RedirectConfig:
          Protocol: HTTPS
          Port: 443
          Host: "#{host}"
          Path: "/#{path}"
          Query: "#{query}"
          StatusCode: "HTTP_301"
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
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
  Listener:
    Type: AWS::ElasticLoadBalancingV2::Listener
    Properties:
      LoadBalancerArn:
        Ref: ApplicationLoadBalancer
      Port: 80
      Protocol: HTTP
      DefaultActions:
      - Type: redirect
        RedirectConfig:
          Protocol: HTTP
          Port: 8080
          Host: "#{host}"
          Path: "/#{path}"
          Query: "#{query}"
          StatusCode: "HTTP_301"
```

## \[CT\.ELASTICLOADBALANCING\.PR\.2\] Require any Amazon ELB application or network load balancer to have an AWS Certificate Manager certificate<a name="ct-elasticloadbalancing-pr-2-description"></a>

This control checks whether your Elastic Load Balancing \(ELB\) application and network load balancers use certificates provided by AWS Certificate Manager \(ACM\)\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancingV2::Listener`, `AWS::ElasticLoadBalancingV2::ListenerCertificate`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.2 rule specification](#ct-elasticloadbalancing-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.2 rule specification](#ct-elasticloadbalancing-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.2 example templates](#ct-elasticloadbalancing-pr-2-templates) 

**Explanation**

To create a certificate, use AWS Certificate Manager \(ACM\) or another tool that supports the SSL and TLS protocols, such as OpenSSL\. AWS Control Tower recommends that you use AWS Certificate Manager to create or import certificates for your load balancer\.

AWS Certificate Manager integrates with Amazon ELB application load balancers and network load balancers, so that you can deploy the certificate on your load balancer\. We also recommend that you automatically renew these certificates\.

**Usage considerations**  
This control applies only to `HTTPS` and `TLS` Amazon ELB listeners and ELB listener certificate resources that have one or more certificates configured\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-2-remediation"></a>

Configure the `Certificates` property to use certificates provided by AWS Certificate Manager\.

The examples that follow show how to implement this remediation\.

#### Amazon ELB Listener \- Example<a name="ct-elasticloadbalancing-pr-2-remediation-1"></a>

Amazon ELB HTTPS listener configured with an AWS Certificate Manager SSL certificate\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ELBListener": {
        "Type": "AWS::ElasticLoadBalancingV2::Listener",
        "Properties": {
            "DefaultActions": [
                {
                    "Type": "forward",
                    "TargetGroupArn": {
                        "Ref": "TargetGroup"
                    }
                }
            ],
            "LoadBalancerArn": {
                "Ref": "ApplicationLoadBalancer"
            },
            "Protocol": "HTTPS",
            "Certificates": [
                {
                    "CertificateArn": {
                        "Ref": "ACMCertificate"
                    }
                }
            ],
            "Port": 443
        }
    }
}
```

**YAML example**

```
ELBListener:
  Type: AWS::ElasticLoadBalancingV2::Listener
  Properties:
    DefaultActions:
      - Type: forward
        TargetGroupArn: !Ref 'TargetGroup'
    LoadBalancerArn: !Ref 'ApplicationLoadBalancer'
    Protocol: HTTPS
    Certificates:
      - CertificateArn: !Ref 'ACMCertificate'
    Port: 443
```

The examples that follow show how to implement this remediation\.

#### Amazon ELB Listener Certificate \- Example<a name="ct-elasticloadbalancing-pr-2-remediation-2"></a>

Amazon ELB listener certificate configured with an AWS Certificate Manager SSL certificate\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ELBListenerCertificate": {
        "Type": "AWS::ElasticLoadBalancingV2::ListenerCertificate",
        "Properties": {
            "ListenerArn": {
                "Ref": "Listener"
            },
            "Certificates": [
                {
                    "CertificateArn": {
                        "Ref": "ACMCertificate"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
ELBListenerCertificate:
  Type: AWS::ElasticLoadBalancingV2::ListenerCertificate
  Properties:
    ListenerArn: !Ref 'Listener'
    Certificates:
      - CertificateArn: !Ref 'ACMCertificate'
```

### CT\.ELASTICLOADBALANCING\.PR\.2 rule specification<a name="ct-elasticloadbalancing-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elbv2_acm_certificate_required_check
# 
# Description:
#   This control checks whether your Elastic Load Balancing (ELB) application and network load balancers use certificates provided by AWS Certificate Manager (ACM).
# 
# Reports on:
#   AWS::ElasticLoadBalancingV2::Listener, AWS::ElasticLoadBalancingV2::ListenerCertificate
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
#       And: The input document does not contain any ElasticLoadBalancingV2 listener or listener certificate resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener resource
#       And: 'Protocol' is set to a value other than 'HTTPS' or 'TLS'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener certificate resource
#       And: 'Certificates' has not been provided or has been provided as an empty list
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener resource
#       And: 'Protocol' is set to 'HTTPS' or 'TLS'
#       And: 'Certificates' has not been provided or has been provided as an empty list
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener resource
#       And: 'Protocol' is set to 'HTTPS' or 'TLS'
#       And: One or more items in 'Certificates' do not match an ACM certificate ARN
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener certificate resource
#       And: One or more items in 'Certificates' do not match an ACM certificate ARN
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener resource
#       And: 'Protocol' is set to 'HTTPS' or 'TLS'
#       And: All items in 'Certificates' match an ACM certificate ARN
#      Then: PASS
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 listener certificate resource
#       And: All items in 'Certificates' match an ACM certificate ARN
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_V2_LISTENER_TYPE = "AWS::ElasticLoadBalancingV2::Listener"
let ELASTIC_LOAD_BALANCER_V2_CERTIFICATE_TYPE = "AWS::ElasticLoadBalancingV2::ListenerCertificate"
let ACM_CERTIFICATE_ARN_PATTERN = /arn:aws[a-z0-9\-]*:acm:[a-z0-9\-]+:\d{12}:certificate\/[\w\-]{1,64}/
let INPUT_DOCUMENT = this

#
# Assignments
#
let elb_v2_listeners = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_V2_LISTENER_TYPE ]
let elb_v2_certificates = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_V2_CERTIFICATE_TYPE ]

#
# Primary Rules
#
rule elbv2_acm_certificate_required_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %elb_v2_listeners not empty {
    check_listener(%elb_v2_listeners.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.2]: Require any Amazon ELB application or network load balancer to have an AWS Certificate Manager certificate
            [FIX]: Configure the 'Certificates' property to use certificates provided by AWS Certificate Manager.
        >>
}

rule elbv2_acm_certificate_required_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_V2_LISTENER_TYPE) {
    check_listener(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_V2_LISTENER_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.2]: Require any Amazon ELB application or network load balancer to have an AWS Certificate Manager certificate
            [FIX]: Configure the 'Certificates' property to use certificates provided by AWS Certificate Manager.
        >>
}

rule elbv2_acm_certificate_required_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %elb_v2_certificates not empty {
    check_elbv2_listener_certificate(%elb_v2_certificates.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.2]: Require any Amazon ELB application or network load balancer to have an AWS Certificate Manager certificate
            [FIX]: Configure the 'Certificates' property to use certificates provided by AWS Certificate Manager.
        >>
}

rule elbv2_acm_certificate_required_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_V2_CERTIFICATE_TYPE) {
    check_elbv2_listener_certificate(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_V2_CERTIFICATE_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.2]: Require any Amazon ELB application or network load balancer to have an AWS Certificate Manager certificate
            [FIX]: Configure the 'Certificates' property to use certificates provided by AWS Certificate Manager.
        >>
}

#
# Parameterized Rules
#
rule check_listener(elbv2_listener) {
    %elbv2_listener[
        # Scenario 2
        Protocol in ["HTTPS", "TLS"]
    ] {
        # Scenarios 3 and 5
        Certificates exists
        Certificates is_list
        Certificates not empty
        Certificates[*] {
            CertificateArn exists
            check_is_acm_certificate(CertificateArn)
        }
    }
}

rule check_elbv2_listener_certificate(listener_certificate) {
    %listener_certificate[
        Certificates exists
        Certificates is_list
        Certificates not empty
    ] {
        # Scenarios 4 and 6
        Certificates[*] {
            CertificateArn exists
            check_is_acm_certificate(CertificateArn)
        }
    }
}

rule check_is_acm_certificate(certificate) {
    %certificate {
        this == %ACM_CERTIFICATE_ARN_PATTERN or
        check_local_references(%INPUT_DOCUMENT, this, "AWS::CertificateManager::Certificate")
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

### CT\.ELASTICLOADBALANCING\.PR\.2 example templates<a name="ct-elasticloadbalancing-pr-2-templates"></a>

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
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  TargetGroup:
    Type: AWS::ElasticLoadBalancingV2::TargetGroup
    Properties:
      Protocol: HTTP
      Port: 80
      VpcId:
        Ref: VPC
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
  Listener:
    Type: AWS::ElasticLoadBalancingV2::Listener
    Properties:
      DefaultActions:
      - Type: forward
        TargetGroupArn:
          Ref: TargetGroup
      LoadBalancerArn:
        Ref: ApplicationLoadBalancer
      Protocol: HTTPS
      Certificates:
      - CertificateArn:
          Ref: ACMCertificate
      Port: 443
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
  TargetGroup:
    Type: AWS::ElasticLoadBalancingV2::TargetGroup
    Properties:
      Protocol: HTTP
      Port: 80
      VpcId:
        Ref: VPC
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
  Listener:
    Type: AWS::ElasticLoadBalancingV2::Listener
    Properties:
      DefaultActions:
      - Type: forward
        TargetGroupArn:
          Ref: TargetGroup
      LoadBalancerArn:
        Ref: ApplicationLoadBalancer
      Protocol: HTTPS
      Certificates:
      - CertificateArn: arn:aws:iam::123456789012:server-certificate/example-certificate
      Port: 443
```

## \[CT\.ELASTICLOADBALANCING\.PR\.3\] Require any application load balancer to have defensive or strictest desync mitigation mode activated<a name="ct-elasticloadbalancing-pr-3-description"></a>

This control checks to ensure that an Application Load Balancer is configured with `defensive` or `strictest` desync mitigation mode\.
+ **Control objective: **Protect data integrity
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancingV2::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.3 rule specification](#ct-elasticloadbalancing-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.3 rule specification](#ct-elasticloadbalancing-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.3 example templates](#ct-elasticloadbalancing-pr-3-templates) 

**Explanation**

HTTP desynchronization \(desync\) issues can lead to request smuggling and make applications vulnerable to request queue or cache poisoning\. In turn, these vulnerabilities can lead to credential stuffing or execution of unauthorized commands\. When configured with defensive or strictest desync mitigation mode, Application Load Balancers can protect your application from security issues that may be caused by HTTP desync\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-3-remediation"></a>

Omit the load balancer attribute `routing.http.desync_mitigation_mode` or set the attribute to one of `defensive` or `strictest`\.

The examples that follow show how to implement this remediation\.

#### Application Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-3-remediation-1"></a>

Application Load Balancer configured with `defensive` desync mitigation mode, by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ApplicationLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancingV2::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "IpAddressType": "ipv4",
            "Type": "application"
        }
    }
}
```

**YAML example**

```
ApplicationLoadBalancer:
  Type: AWS::ElasticLoadBalancingV2::LoadBalancer
  Properties:
    Scheme: internal
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    IpAddressType: ipv4
    Type: application
```

The examples that follow show how to implement this remediation\.

#### Application Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-3-remediation-2"></a>

Application Load Balancer configured with `strictest` desync mitigation mode, by meand of the `routing.http.desync_mitigation_mode` load balancer attribute\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ApplicationLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancingV2::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "IpAddressType": "ipv4",
            "Type": "application",
            "LoadBalancerAttributes": [
                {
                    "Key": "routing.http.desync_mitigation_mode",
                    "Value": "strictest"
                }
            ]
        }
    }
}
```

**YAML example**

```
ApplicationLoadBalancer:
  Type: AWS::ElasticLoadBalancingV2::LoadBalancer
  Properties:
    Scheme: internal
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    IpAddressType: ipv4
    Type: application
    LoadBalancerAttributes:
      - Key: routing.http.desync_mitigation_mode
        Value: strictest
```

### CT\.ELASTICLOADBALANCING\.PR\.3 rule specification<a name="ct-elasticloadbalancing-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   alb_desync_mode_check
# 
# Description:
#   This control checks to ensure that an Application Load Balancer is configured with 'defensive' or 'strictest' desync mitigation mode.
# 
# Reports on:
#   AWS::ElasticLoadBalancingV2::LoadBalancer
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
#       And: The input document does not contain any ELBv2 load balancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to a value other than 'application'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to 'application' for the ELBv2 load balancer resource
#       And: 'LoadBalancerAttributes' have been specified on the ELBv2 load balancer resource
#       And: The 'LoadBalancerAttribute' 'routing.http.desync_mitigation_mode' has been provided
#            and is not one of 'defensive' or 'strictest'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to 'application' for the ELBv2 load balancer resource
#       And: 'LoadBalancerAttributes' have not been specified on the ELBv2 load balancer resource or specified
#            as an empty list
#      Then: PASS
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to 'application' for the ELBv2 load balancer resource
#       And: 'LoadBalancerAttributes' have been specified on the ELBv2 load balancer resource
#       And: 'routing.http.desync_mitigation_mode' has not been provided as a 'LoadBalancerAttribute'
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to 'application' for the ELBv2 load balancer resource
#       And: 'LoadBalancerAttributes' have been specified on the ELBv2 load balancer resource
#       And: The 'LoadBalancerAttribute' 'routing.http.desync_mitigation_mode' has been provided
#            and is one of 'defensive' or 'strictest'
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_V2_TYPE = "AWS::ElasticLoadBalancingV2::LoadBalancer"
let ALLOWED_DESYNC_MODES = [ "defensive", "strictest" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let elastic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_V2_TYPE ]

#
# Primary Rules
#
rule alb_desync_mode_check when is_cfn_template(%INPUT_DOCUMENT)
                                %elastic_load_balancers not empty {

    check(%elastic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.3]: Require any application load balancer to have defensive or strictest desync mitigation mode activated
        [FIX]: Omit the load balancer attribute 'routing.http.desync_mitigation_mode' or set the attribute to one of 'defensive' or 'strictest'.
        >>

}

rule alb_desync_mode_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_V2_TYPE) {

    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_V2_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.3]: Require any application load balancer to have defensive or strictest desync mitigation mode activated
        [FIX]: Omit the load balancer attribute 'routing.http.desync_mitigation_mode' or set the attribute to one of 'defensive' or 'strictest'.
        >>
}

#
# Parameterized Rules
#
rule check(elastic_load_balancer) {
    %elastic_load_balancer[
        # Scenario 2
        Type == "application"
    ] {
        # Scenario 4
        LoadBalancerAttributes not exists or
        check_application_load_balancer_attributes(this)
    }
}

rule check_application_load_balancer_attributes(application_load_balancer) {
    %application_load_balancer {
        LoadBalancerAttributes is_list
        LoadBalancerAttributes[
            # Scenario 5
            Key exists
            Key == "routing.http.desync_mitigation_mode"
        ] {
            # Scenarios 3 and 6
            Value exists
            Value in %ALLOWED_DESYNC_MODES
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

### CT\.ELASTICLOADBALANCING\.PR\.3 example templates<a name="ct-elasticloadbalancing-pr-3-templates"></a>

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
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
      Type: application
      LoadBalancerAttributes:
      - Key: routing.http.desync_mitigation_mode
        Value: strictest
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
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
      Type: application
      LoadBalancerAttributes:
      - Key: routing.http.desync_mitigation_mode
        Value: monitor
```

## \[CT\.ELASTICLOADBALANCING\.PR\.4\] Require that any application load balancer must be configured to drop HTTP headers<a name="ct-elasticloadbalancing-pr-4-description"></a>

This control checks whether Application Load Balancers are configured to drop non\-valid HTTP headers\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancingV2::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.4 rule specification](#ct-elasticloadbalancing-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.4 rule specification](#ct-elasticloadbalancing-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.4 example templates](#ct-elasticloadbalancing-pr-4-templates) 

**Explanation**

By default, Application Load Balancers are not configured to drop non\-valid HTTP header values\. Removing these header values prevents HTTP desync attacks\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-4-remediation"></a>

Set the load balancer attribute `routing.http.drop_invalid_header_fields.enabled` to `true`\.

The examples that follow show how to implement this remediation\.

#### Application Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-4-remediation-1"></a>

Application Load Balancer configured to drop non\-valid HTTP headers\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ApplicationLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancingV2::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Type": "application",
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "IpAddressType": "ipv4",
            "LoadBalancerAttributes": [
                {
                    "Key": "routing.http.drop_invalid_header_fields.enabled",
                    "Value": "true"
                }
            ]
        }
    }
}
```

**YAML example**

```
ApplicationLoadBalancer:
  Type: AWS::ElasticLoadBalancingV2::LoadBalancer
  Properties:
    Scheme: internal
    Type: application
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    IpAddressType: ipv4
    LoadBalancerAttributes:
      - Key: routing.http.drop_invalid_header_fields.enabled
        Value: 'true'
```

### CT\.ELASTICLOADBALANCING\.PR\.4 rule specification<a name="ct-elasticloadbalancing-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   alb_http_drop_invalid_header_enabled_check
# 
# Description:
#   This control checks whether Application Load Balancers are configured to drop non-valid HTTP headers.
# 
# Reports on:
#   AWS::ElasticLoadBalancingV2::LoadBalancer
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
#       And: The input document does not contain any ELBv2 load balancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to a value other than 'application'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to 'application' for the ELBv2 load balancer resource
#       And: 'LoadBalancerAttributes' have not been specified on the ELBv2 load balancer resource
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to 'application' for the ELBv2 load balancer resource
#       And: 'LoadBalancerAttributes' have been specified on the ELBv2 load balancer resource
#       And: 'routing.http.drop_invalid_header_fields.enabled' has not been provided as a 'LoadBalancerAttribute'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to 'application' for the ELBv2 load balancer resource
#       And: 'LoadBalancerAttributes' have been specified on the ELBv2 load balancer resource
#       And: The 'LoadBalancerAttribute' 'routing.http.drop_invalid_header_fields.enabled' has been provided
#            and is set to bool(false) or string(false)
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 load balancer resource
#       And: 'Type' is set to 'application' for the ELBv2 load balancer resource
#       And: 'LoadBalancerAttributes' have been specified on the ELBv2 load balancer resource
#       And: The 'LoadBalancerAttribute' 'routing.http.drop_invalid_header_fields.enabled' has been provided and
#            is set to bool(true) or string(true)
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_V2_TYPE = "AWS::ElasticLoadBalancingV2::LoadBalancer"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elastic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_V2_TYPE ]

#
# Primary Rules
#
rule alb_http_drop_invalid_header_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                     %elastic_load_balancers not empty {

    check(%elastic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.4]: Require that any application load balancer must be configured to drop HTTP headers
        [FIX]: Set the load balancer attribute 'routing.http.drop_invalid_header_fields.enabled' to 'true'.
        >>
}

rule alb_http_drop_invalid_header_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_V2_TYPE) {

    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_V2_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.4]: Require that any application load balancer must be configured to drop HTTP headers
        [FIX]: Set the load balancer attribute 'routing.http.drop_invalid_header_fields.enabled' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(elastic_load_balancer) {
    %elastic_load_balancer[ Type == "application" ] {
        # Scenario 2
        LoadBalancerAttributes exists
        LoadBalancerAttributes is_list
        LoadBalancerAttributes not empty

        # Scenario 3, 4 and 5
        some LoadBalancerAttributes[*] {
            Key exists
            Value exists

            Key == "routing.http.drop_invalid_header_fields.enabled"
            Value in [ true, "true" ]
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

### CT\.ELASTICLOADBALANCING\.PR\.4 example templates<a name="ct-elasticloadbalancing-pr-4-templates"></a>

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
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Type: application
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
      LoadBalancerAttributes:
      - Key: routing.http.drop_invalid_header_fields.enabled
        Value: "true"
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
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Type: application
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
      LoadBalancerAttributes:
      - Key: routing.http.drop_invalid_header_fields.enabled
        Value: "false"
```

## \[CT\.ELASTICLOADBALANCING\.PR\.5\] Require that application load balancer deletion protection is activated<a name="ct-elasticloadbalancing-pr-5-description"></a>

Checks whether Elastic Load Balancing \(ELB\) has deletion protection activated\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancingV2::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.5 rule specification](#ct-elasticloadbalancing-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.5 rule specification](#ct-elasticloadbalancing-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.5 example templates](#ct-elasticloadbalancing-pr-5-templates) 

**Explanation**

Activate deletion protection to protect your Application Load Balancer from deletion\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-5-remediation"></a>

Set the load balancer attribute `deletion_protection.enabled` to `true`\.

The examples that follow show how to implement this remediation\.

#### Application Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-5-remediation-1"></a>

Application Load Balancer configured with deletion protection active\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "Elb": {
        "Type": "AWS::ElasticLoadBalancingV2::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Type": "application",
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "IpAddressType": "ipv4",
            "LoadBalancerAttributes": [
                {
                    "Key": "deletion_protection.enabled",
                    "Value": "true"
                }
            ]
        }
    }
}
```

**YAML example**

```
Elb:
  Type: AWS::ElasticLoadBalancingV2::LoadBalancer
  Properties:
    Scheme: internal
    Type: application
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    IpAddressType: ipv4
    LoadBalancerAttributes:
      - Key: deletion_protection.enabled
        Value: 'true'
```

### CT\.ELASTICLOADBALANCING\.PR\.5 rule specification<a name="ct-elasticloadbalancing-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elbv2_deletion_protection_enabled_check
# 
# Description:
#   Checks whether Elastic Load Balancing (ELB) has deletion protection activated.
# 
# Reports on:
#   AWS::ElasticLoadBalancingV2::LoadBalancer
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
#       And: The input document does not contain any ELBv2 LoadBalancer resource
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 LoadBalancer resource
#       And: 'LoadBalancerAttributes' have not been specified or is an empty list on the ELBv2 resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 LoadBalancer resource
#       And: 'LoadBalancerAttributes' have been specified on the ELBv2 LoadBalancer resource
#       And: 'deletion_protection.enabled' has not been provided as a 'LoadBalancerAttribute'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 LoadBalancer resource
#       And: 'LoadBalancerAttributes' have been specified on the ELBv2 LoadBalancer resource
#       And: The 'LoadBalancerAttribute' 'deletion_protection.enabled' has been provided and is set to bool(false) or
#            string(false)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ELBv2 LoadBalancer Resource
#       And: 'LoadBalancerAttributes' have been specified on the ELBv2 LoadBalancer resource
#       And: The 'LoadBalancerAttribute' 'deletion_protection.enabled' has been provided and is set to bool(true) or
#            string(true)
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_V2_TYPE = "AWS::ElasticLoadBalancingV2::LoadBalancer"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elastic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_V2_TYPE ]

#
# Primary Rules
#
rule elbv2_deletion_protection_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                  %elastic_load_balancers not empty {
    check(%elastic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.5]: Require that application load balancer deletion protection is activated
        [FIX]: Set the load balancer attribute 'deletion_protection.enabled' to 'true'.
        >>
}

rule elbv2_deletion_protection_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_V2_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_V2_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.5]: Require that application load balancer deletion protection is activated
        [FIX]: Set the load balancer attribute 'deletion_protection.enabled' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(elastic_load_balancer) {
    %elastic_load_balancer {
        # Scenario 2
        LoadBalancerAttributes exists
        LoadBalancerAttributes is_list
        LoadBalancerAttributes not empty

        # Scenario 3, 4 and 5
        some LoadBalancerAttributes[*] {
            Key exists
            Value exists

            Key == "deletion_protection.enabled"
            Value in [ true, "true" ]
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

### CT\.ELASTICLOADBALANCING\.PR\.5 example templates<a name="ct-elasticloadbalancing-pr-5-templates"></a>

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
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Type: application
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
      LoadBalancerAttributes:
      - Key: deletion_protection.enabled
        Value: "true"
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
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Type: application
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
      LoadBalancerAttributes:
      - Key: deletion_protection.enabled
        Value: "false"
```

## \[CT\.ELASTICLOADBALANCING\.PR\.6\] Require that application and network load balancer access logging is activated<a name="ct-elasticloadbalancing-pr-6-description"></a>

This control checks whether your Elastic Load Balancing \(ELB\) application and network load balancers have logging activated\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancingV2::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.6 rule specification](#ct-elasticloadbalancing-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.6 rule specification](#ct-elasticloadbalancing-pr-6-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.6 example templates](#ct-elasticloadbalancing-pr-6-templates) 

**Explanation**

Elastic Load Balancing provides access logs that capture detailed information about requests sent to your load balancer\. Each log contains information such as the time the request was received, the client's IP address, latencies, request paths, and server responses\. You can use these access logs to analyze traffic patterns and to troubleshoot issues\.

**Usage considerations**  
This control applies only to ELB load balancer types of `application` and `network`\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-6-remediation"></a>

Set the load balancer attribute `access_logs.s3.enabled` to `true`, and set `access_logs.s3.bucket` to reach an S3 bucket that's configured to receive application load balancer or network load balancer access logs\.

The examples that follow show how to implement this remediation\.

#### Application Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-6-remediation-1"></a>

Application Load Balancer configured with access logging activated\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ApplicationLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancingV2::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "IpAddressType": "ipv4",
            "Type": "application",
            "LoadBalancerAttributes": [
                {
                    "Key": "access_logs.s3.enabled",
                    "Value": true
                },
                {
                    "Key": "access_logs.s3.bucket",
                    "Value": {
                        "Ref": "LoggingBucket"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
ApplicationLoadBalancer:
  Type: AWS::ElasticLoadBalancingV2::LoadBalancer
  Properties:
    Scheme: internal
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    IpAddressType: ipv4
    Type: application
    LoadBalancerAttributes:
      - Key: access_logs.s3.enabled
        Value: true
      - Key: access_logs.s3.bucket
        Value: !Ref 'LoggingBucket'
```

The examples that follow show how to implement this remediation\.

#### Network Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-6-remediation-2"></a>

Network Load Balancer configured with access logging activated\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "NetworkLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancingV2::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "IpAddressType": "ipv4",
            "Type": "network",
            "LoadBalancerAttributes": [
                {
                    "Key": "access_logs.s3.enabled",
                    "Value": true
                },
                {
                    "Key": "access_logs.s3.bucket",
                    "Value": {
                        "Ref": "LoggingBucket"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
NetworkLoadBalancer:
  Type: AWS::ElasticLoadBalancingV2::LoadBalancer
  Properties:
    Scheme: internal
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    IpAddressType: ipv4
    Type: network
    LoadBalancerAttributes:
      - Key: access_logs.s3.enabled
        Value: true
      - Key: access_logs.s3.bucket
        Value: !Ref 'LoggingBucket'
```

### CT\.ELASTICLOADBALANCING\.PR\.6 rule specification<a name="ct-elasticloadbalancing-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elbv2_logging_enabled_check
# 
# Description:
#   This control checks whether your Elastic Load Balancing (ELB) application and network load balancers have logging activated.
# 
# Reports on:
#   AWS::ElasticLoadBalancingV2::LoadBalancer
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
#       And: The input document does not contain any ElasticLoadBalancingV2 LoadBalancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 LoadBalancer resource
#       And: 'Type' is set to a value other than 'application' or 'network'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 LoadBalancer resource
#       And: The LoadBalancer is of type 'application' or 'network'
#       And: 'LoadBalancerAttributes' has not been provided or is an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 LoadBalancer resource
#       And: The LoadBalancer is of type 'application' or 'network'
#       And: A 'LoadBalancerAttributes' with Key 'access_logs.s3.enabled' and 'access_logs.s3.bucket'
#            has not been provided
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 LoadBalancer resource
#       And: The LoadBalancer is of type 'application' or 'network'
#       And: A 'LoadBalancerAttributes' with Key 'access_logs.s3.enabled' and 'access_logs.s3.bucket' has been provided
#       And: 'access_logs.s3.enabled' is set to bool(false) or string(false)
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 LoadBalancer resource
#       And: The LoadBalancer is of type 'application' or 'network'
#       And: A 'LoadBalancerAttributes' with Key 'access_logs.s3.enabled' and 'access_logs.s3.bucket' has been provided
#       And: 'access_logs.s3.enabled' is set to bool(true) or string(true)
#       And: 'access_logs.s3.bucket' is missing or an empty string value
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancingV2 LoadBalancer resource
#       And: The LoadBalancer is of type 'application'
#       And: A 'LoadBalancerAttributes' with Key 'access_logs.s3.enabled' has been provided
#       And: 'access_logs.s3.enabled' is set to bool(true) or string(true)
#       And: 'access_logs.s3.bucket' is provided and a non-empty string value or valid local reference
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_V2_TYPE = "AWS::ElasticLoadBalancingV2::LoadBalancer"
let INPUT_DOCUMENT = this

#
# Assignments
#
let elastic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_V2_TYPE ]

#
# Primary Rules
#
rule elbv2_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                      %elastic_load_balancers not empty {

    check(%elastic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.6]: Require that application and network load balancer access logging is activated
        [FIX]: Set the load balancer attribute 'access_logs.s3.enabled' to 'true', and set 'access_logs.s3.bucket' to reach an Amazon S3 bucket that's configured to receive application load balancer or network load balancer access logs.
        >>
}

rule elbv2_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_V2_TYPE) {

    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_V2_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.6]: Require that application and network load balancer access logging is activated
        [FIX]: Set the load balancer attribute 'access_logs.s3.enabled' to 'true', and set 'access_logs.s3.bucket' to reach an Amazon S3 bucket that's configured to receive application load balancer or network load balancer access logs.
        >>
}

#
# Parameterized Rules
#
rule check(elastic_load_balancer) {
    %elastic_load_balancer[ Type in ["application", "network"] ] {
        # Scenario 3
        LoadBalancerAttributes exists
        LoadBalancerAttributes is_list
        LoadBalancerAttributes not empty

        # Scenario 4, 5, 6 and 7
        some LoadBalancerAttributes[*] {
            Key exists
            Value exists

            Key == "access_logs.s3.enabled"
            Value in [ true, "true" ]
        }

        some LoadBalancerAttributes[*] {
            Key exists
            Value exists

            Key == "access_logs.s3.bucket"
            check_is_string_and_not_empty(Value) or
            check_local_references(%INPUT_DOCUMENT, Value, "AWS::S3::Bucket")
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

### CT\.ELASTICLOADBALANCING\.PR\.6 example templates<a name="ct-elasticloadbalancing-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Mappings:
  RegionToELBAccountId:
    us-east-1:
      AccountId: '127311923021'
    us-west-1:
      AccountId: '027434742980'
    us-west-2:
      AccountId: '797873946194'
    ca-central-1:
      AccountId: '985666609251'
    eu-west-1:
      AccountId: '156460612806'
    ap-northeast-1:
      AccountId: '582318560864'
    ap-northeast-2:
      AccountId: '600734575887'
    ap-southeast-1:
      AccountId: '114774131450'
    ap-southeast-2:
      AccountId: '783225319266'
    ap-south-1:
      AccountId: '718504428378'
    us-east-2:
      AccountId: '033677994240'
    sa-east-1:
      AccountId: '507241528517'
    eu-central-1:
      AccountId: '054676820928'
    af-south-1:
      AccountId: '098369216593'
    ap-east-1:
      AccountId: '754344448648'
    ap-southeast-3:
      AccountId: '589379963580'
    ap-northeast-3:
      AccountId: '383597477331'
    eu-west-2:
      AccountId: '652711504416'
    eu-south-1:
      AccountId: '635631232127'
    eu-west-3:
      AccountId: '009996457667'
    eu-north-1:
      AccountId: '897822967062'
    me-south-1:
      AccountId: '076674570225'
    us-gov-west-1:
      AccountId: '048591011584'
    us-gov-east-1:
      AccountId: '190560391635'
  RegionToARNPrefix:
    us-east-1:
      ARNPrefix: 'arn:aws:'
    us-west-1:
      ARNPrefix: 'arn:aws:'
    us-west-2:
      ARNPrefix: 'arn:aws:'
    ca-central-1:
      ARNPrefix: 'arn:aws:'
    eu-west-1:
      ARNPrefix: 'arn:aws:'
    ap-northeast-1:
      ARNPrefix: 'arn:aws:'
    ap-northeast-2:
      ARNPrefix: 'arn:aws:'
    ap-southeast-1:
      ARNPrefix: 'arn:aws:'
    ap-southeast-2:
      ARNPrefix: 'arn:aws:'
    ap-south-1:
      ARNPrefix: 'arn:aws:'
    us-east-2:
      ARNPrefix: 'arn:aws:'
    sa-east-1:
      ARNPrefix: 'arn:aws:'
    eu-central-1:
      ARNPrefix: 'arn:aws:'
    af-south-1:
      ARNPrefix: 'arn:aws:'
    ap-east-1:
      ARNPrefix: 'arn:aws:'
    ap-southeast-3:
      ARNPrefix: 'arn:aws:'
    ap-northeast-3:
      ARNPrefix: 'arn:aws:'
    eu-west-2:
      ARNPrefix: 'arn:aws:'
    eu-south-1:
      ARNPrefix: 'arn:aws:'
    eu-west-3:
      ARNPrefix: 'arn:aws:'
    eu-north-1:
      ARNPrefix: 'arn:aws:'
    me-south-1:
      ARNPrefix: 'arn:aws:'
    us-gov-west-1:
      ARNPrefix: 'arn:aws-us-gov:'
    us-gov-east-1:
      ARNPrefix: 'arn:aws-us-gov:'
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
  LoggingBucket:
    Type: AWS::S3::Bucket
  LoggingBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: LoggingBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:PutObject'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - Fn::FindInMap: [RegionToARNPrefix, !Ref 'AWS::Region', ARNPrefix]
                  - 's3:::'
                  - Ref: LoggingBucket
                  - /AWSLogs/
                  - Ref: AWS::AccountId
                  - /*
            Principal:
              AWS: 
                Fn::FindInMap: [RegionToELBAccountId, !Ref 'AWS::Region', AccountId]
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
      Type: application
      LoadBalancerAttributes:
      - Key: access_logs.s3.enabled
        Value: true
      - Key: access_logs.s3.bucket
        Value:
          Ref: LoggingBucket
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
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      IpAddressType: ipv4
      Type: application
```

## \[CT\.ELASTICLOADBALANCING\.PR\.7\] Require any classic load balancer to have multiple Availability Zones configured<a name="ct-elasticloadbalancing-pr-7-description"></a>

This control checks whether an Elastic Load Balancing \(ELB\) classic load balancer has been configured with multiple Availability Zones\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancing::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.7 rule specification](#ct-elasticloadbalancing-pr-7-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.7 rule specification](#ct-elasticloadbalancing-pr-7-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.7 example templates](#ct-elasticloadbalancing-pr-7-templates) 

**Explanation**

A Classic Load Balancer can be set up to distribute incoming requests across Amazon EC2 instances in a single Availability Zone or multiple Availability Zones\. A Classic Load Balancer that does not span multiple Availability Zones is unable to redirect traffic to targets in another Availability Zone, in case the sole configured Availability Zone becomes unavailable\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-7-remediation"></a>

Configure Classic Load Balancers with two or more subnets or Availability Zones\.

The examples that follow show how to implement this remediation\.

#### Classic Load Balancer \- Example One<a name="ct-elasticloadbalancing-pr-7-remediation-1"></a>

Classic Load Balancer configured with two Availability Zones\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ClassicLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
        "Properties": {
            "Scheme": "internet-facing",
            "Listeners": [
                {
                    "InstancePort": "80",
                    "InstanceProtocol": "HTTP",
                    "LoadBalancerPort": "443",
                    "Protocol": "HTTPS",
                    "PolicyNames": [
                        "Sample-SSLNegotiation-Policy"
                    ],
                    "SSLCertificateId": {
                        "Ref": "ACMCertificate"
                    }
                }
            ],
            "Policies": [
                {
                    "PolicyName": "Sample-SSLNegotiation-Policy",
                    "PolicyType": "SSLNegotiationPolicyType",
                    "Attributes": [
                        {
                            "Name": "Reference-Security-Policy",
                            "Value": "ELBSecurityPolicy-TLS-1-2-2017-01"
                        }
                    ]
                }
            ],
            "AvailabilityZones": [
                {
                    "Fn::Select": [
                        0,
                        {
                            "Fn::GetAZs": ""
                        }
                    ]
                },
                {
                    "Fn::Select": [
                        1,
                        {
                            "Fn::GetAZs": ""
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
ClassicLoadBalancer:
  Type: AWS::ElasticLoadBalancing::LoadBalancer
  Properties:
    Scheme: internet-facing
    Listeners:
      - InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        Protocol: HTTPS
        PolicyNames:
          - Sample-SSLNegotiation-Policy
        SSLCertificateId: !Ref 'ACMCertificate'
    Policies:
      - PolicyName: Sample-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
          - Name: Reference-Security-Policy
            Value: ELBSecurityPolicy-TLS-1-2-2017-01
    AvailabilityZones:
      - !Select
        - 0
        - !GetAZs ''
      - !Select
        - 1
        - !GetAZs ''
```

The examples that follow show how to implement this remediation\.

#### Classic Load Balancer \- Example Two<a name="ct-elasticloadbalancing-pr-7-remediation-2"></a>

Classic Load Balancer configured with two subnets\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ClassicLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
        "Properties": {
            "Scheme": "internet-facing",
            "Listeners": [
                {
                    "InstancePort": "80",
                    "InstanceProtocol": "HTTP",
                    "LoadBalancerPort": "443",
                    "Protocol": "HTTPS",
                    "PolicyNames": [
                        "Sample-SSLNegotiation-Policy"
                    ],
                    "SSLCertificateId": {
                        "Ref": "ACMCertificate"
                    }
                }
            ],
            "Policies": [
                {
                    "PolicyName": "Sample-SSLNegotiation-Policy",
                    "PolicyType": "SSLNegotiationPolicyType",
                    "Attributes": [
                        {
                            "Name": "Reference-Security-Policy",
                            "Value": "ELBSecurityPolicy-TLS-1-2-2017-01"
                        }
                    ]
                }
            ],
            "AvailabilityZones": [
                {
                    "Fn::Select": [
                        0,
                        {
                            "Fn::GetAZs": ""
                        }
                    ]
                },
                {
                    "Fn::Select": [
                        1,
                        {
                            "Fn::GetAZs": ""
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
ClassicLoadBalancer:
  Type: AWS::ElasticLoadBalancing::LoadBalancer
  Properties:
    Scheme: internet-facing
    Listeners:
      - InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        Protocol: HTTPS
        PolicyNames:
          - Sample-SSLNegotiation-Policy
        SSLCertificateId: !Ref 'ACMCertificate'
    Policies:
      - PolicyName: Sample-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
          - Name: Reference-Security-Policy
            Value: ELBSecurityPolicy-TLS-1-2-2017-01
    AvailabilityZones:
      - !Select
        - 0
        - !GetAZs ''
      - !Select
        - 1
        - !GetAZs ''
```

### CT\.ELASTICLOADBALANCING\.PR\.7 rule specification<a name="ct-elasticloadbalancing-pr-7-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elb_multiple_az_check
# 
# Description:
#   This control checks whether an Elastic Load Balancing (ELB) Classic Load Balancer has been configured with multiple Availability Zones.
# 
# Reports on:
#   AWS::ElasticLoadBalancing::LoadBalancer
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
#       And: The input document does not contain any Elastic Load Balancing load balancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: Neither 'AvailabilityZones' or 'Subnets' have been specified
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'AvailabilityZones' been specified on the Elastic Load Balancing load balancer resource
#       And: The number of entries in 'AvailabilityZones' is < 2 or the number of
#            unique 'AvailabilityZones' provided is less than 2 (< 2)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'Subnets' been specified on the Elastic Load Balancing load balancer resource
#       And: The number of entries in 'Subnets' is < 2
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'AvailabilityZones' been specified on the Elastic Load Balancing load balancer resource
#       And: The number of entries in 'AvailabilityZones' is >= 2
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'Subnets' been specified on the Elastic Load Balancing load balancer resource
#       And: The number of entries in 'Subnets' is >= 2
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_TYPE = "AWS::ElasticLoadBalancing::LoadBalancer"
let INPUT_DOCUMENT = this

#
# Assignments
#
let classic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_TYPE ]

#
# Primary Rules
#
rule elb_multiple_az_check when is_cfn_template(%INPUT_DOCUMENT)
                                %classic_load_balancers not empty {

    check(%classic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.7]: Require any classic load balancer to have multiple Availability Zones configured
        [FIX]: Configure Classic Load Balancers with two or more subnets or Availability Zones.
        >>
}

rule elb_multiple_az_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_TYPE) {

    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.7]: Require any classic load balancer to have multiple Availability Zones configured
        [FIX]: Configure Classic Load Balancers with two or more subnets or Availability Zones.
        >>
}

#
# Parameterized Rules
#
rule check(classic_load_balancer) {
    %classic_load_balancer {
        # Scenario 2
        AvailabilityZones exists or
        Subnets exists

        when AvailabilityZones exists {
            # Scenarios 3 and 5
            two_or_more_entries(AvailabilityZones)
            AvailabilityZones[0] not in AvailabilityZones[1]
        }

        when Subnets exists {
            # Scenarios 4 and 6
            two_or_more_entries(Subnets)
        }
    }
}

rule two_or_more_entries(list_property) {
    %list_property {
        this is_list
        this not empty
        this[0] exists
        this[1] exists
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

### CT\.ELASTICLOADBALANCING\.PR\.7 example templates<a name="ct-elasticloadbalancing-pr-7-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internet-facing
      Listeners:
      - Protocol: HTTPS
        InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        PolicyNames:
        - Example-SSLNegotiation-Policy
        SSLCertificateId:
          Ref: ACMCertificate
      Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
        - Name: Reference-Security-Policy
          Value: ELBSecurityPolicy-TLS-1-2-2017-01
      AvailabilityZones:
      - Fn::Select:
        - 0
        - Fn::GetAZs: ''
      - Fn::Select:
        - 1
        - Fn::GetAZs: ''
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
      CidrBlock: 10.0.0.0/24
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Listeners:
      - Protocol: HTTP
        InstancePort: 80
        LoadBalancerPort: 80
      Subnets:
      - Ref: Subnet
```

## \[CT\.ELASTICLOADBALANCING\.PR\.8\] Require any classic load balancer SSL/HTTPS listener to have a certificate provided by AWS Certificate Manager<a name="ct-elasticloadbalancing-pr-8-description"></a>

This control checks whether classic load balancers use HTTPS/SSL certificates provided by AWS Certificate Manager\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancing::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.8 rule specification](#ct-elasticloadbalancing-pr-8-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.8 rule specification](#ct-elasticloadbalancing-pr-8-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.8 example templates](#ct-elasticloadbalancing-pr-8-templates) 

**Explanation**

To create a certificate, you can use either ACM or a tool that supports the SSL and TLS protocols, such as OpenSSL\. Security Hub recommends that you use ACM to create or import certificates for your load balancer\.

ACM integrates with Classic Load Balancers, so that you can deploy the certificate on your load balancer\. You also should renew these certificates automatically\.

**Usage considerations**  
This control applies only to Classic Load Balancers configured with HTTPS or SSL listeners\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-8-remediation"></a>

Configure Classic Load Balancers to use certificates provided by AWS Certificate Manager \(ACM\)\.

The examples that follow show how to implement this remediation\.

#### Classic Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-8-remediation-1"></a>

Classic Load Balancer configured with an HTTPS listener and AWS Certificate Manager SSL certificate\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ClassicLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "Policies": [
                {
                    "PolicyName": "Example-SSLNegotiation-Policy",
                    "PolicyType": "SSLNegotiationPolicyType",
                    "Attributes": [
                        {
                            "Name": "Reference-Security-Policy",
                            "Value": "ELBSecurityPolicy-TLS-1-2-2017-01"
                        }
                    ]
                }
            ],
            "Listeners": [
                {
                    "InstancePort": "80",
                    "InstanceProtocol": "HTTP",
                    "LoadBalancerPort": "443",
                    "Protocol": "HTTPS",
                    "PolicyNames": [
                        "Example-SSLNegotiation-Policy"
                    ],
                    "SSLCertificateId": {
                        "Ref": "ACMCertificate"
                    }
                }
            ]
        }
    }
}
```

**YAML example**

```
ClassicLoadBalancer:
  Type: AWS::ElasticLoadBalancing::LoadBalancer
  Properties:
    Scheme: internal
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
          - Name: Reference-Security-Policy
            Value: ELBSecurityPolicy-TLS-1-2-2017-01
    Listeners:
      - InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        Protocol: HTTPS
        PolicyNames:
          - Example-SSLNegotiation-Policy
        SSLCertificateId: !Ref 'ACMCertificate'
```

### CT\.ELASTICLOADBALANCING\.PR\.8 rule specification<a name="ct-elasticloadbalancing-pr-8-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elb_acm_certificate_required_check
# 
# Description:
#   This control checks whether Classic Load Balancers use HTTPS/SSL certificates provided by AWS Certificate Manager.
# 
# Reports on:
#   AWS::ElasticLoadBalancing::LoadBalancer
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
#       And: The input document does not contain any Elastic Load Balancing load balancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: There are no HTTPS or SSL 'Listeners' configured on the load balancer resource
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: There are one or more HTTPS or SSL 'Listeners' configured on the load balancer resource
#       And: 'SSLCertificateId' on load balancer HTTPS or SSL 'Listeners' is missing or not a valid ACM certificate ARN
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: There are one or more HTTPS or SSL 'Listeners' configured on the load balancer resource
#       And: 'SSLCertificateId' matches an ACM certificate ARN for all 'HTTPS' and 'SSL' 'Listeners'
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_TYPE = "AWS::ElasticLoadBalancing::LoadBalancer"
let ACM_CERTIFICATE_ARN_PATTERN = /arn:aws[a-z0-9\-]*:acm:[a-z0-9\-]+:\d{12}:certificate\/[\w\-]{1,64}/
let SECURE_LISTENER_PROTOCOLS = ["HTTPS", "SSL"]
let INPUT_DOCUMENT = this

#
# Assignments
#
let classic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_TYPE ]

#
# Primary Rules
#
rule elb_acm_certificate_required_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %classic_load_balancers not empty {

    check(%classic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.8]: Require any classic load balancer SSL/HTTPS listener to have a certificate provided by AWS Certificate Manager
        [FIX]: Configure Classic Load Balancers to use certificates provided by AWS Certificate Manager (ACM).
        >>
}

rule elb_acm_certificate_required_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_TYPE) {

    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.8]: Require any classic load balancer SSL/HTTPS listener to have a certificate provided by AWS Certificate Manager
        [FIX]: Configure Classic Load Balancers to use certificates provided by AWS Certificate Manager (ACM).
        >>
}

#
# Parameterized Rules
#
rule check(classic_load_balancer) {
    %classic_load_balancer [
        filter_load_balancer_with_listeners(this)
    ] {
        Listeners [
            filter_secure_listeners(this)
        ] {
            # Scenarios 3 and 4
            SSLCertificateId exists
            SSLCertificateId == %ACM_CERTIFICATE_ARN_PATTERN or
            check_local_references(%INPUT_DOCUMENT, SSLCertificateId, "AWS::CertificateManager::Certificate")
        }
    }
}

rule filter_load_balancer_with_listeners(classic_load_balancer) {
    %classic_load_balancer {
        Listeners exists
        Listeners is_list
        Listeners not empty
    }
}

rule filter_secure_listeners(listener) {
    %listener {
        Protocol exists
        Protocol in %SECURE_LISTENER_PROTOCOLS
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

### CT\.ELASTICLOADBALANCING\.PR\.8 example templates<a name="ct-elasticloadbalancing-pr-8-templates"></a>

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
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
        - Name: Reference-Security-Policy
          Value: ELBSecurityPolicy-TLS-1-2-2017-01
      Listeners:
      - InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        Protocol: HTTPS
        PolicyNames:
        - Example-SSLNegotiation-Policy
        SSLCertificateId:
          Ref: ACMCertificate
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
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
        - Name: Reference-Security-Policy
          Value: ELBSecurityPolicy-TLS-1-2-2017-01
      Listeners:
      - InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        Protocol: HTTPS
        PolicyNames:
        - Example-SSLNegotiation-Policy
        SSLCertificateId: arn:aws:iam::123456789012:server-certificate/example-certificate
```

## \[CT\.ELASTICLOADBALANCING\.PR\.9\] Require that an AWS ELB Application or Classic Load Balancer listener is configured with HTTPS or TLS termination<a name="ct-elasticloadbalancing-pr-9-description"></a>

This control checks whether your Elastic Load Balancing \(ELB\) Classic Load Balancer front\-end listeners are configured with HTTPS or SSL protocols\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancing::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.9 rule specification](#ct-elasticloadbalancing-pr-9-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.9 rule specification](#ct-elasticloadbalancing-pr-9-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.9 example templates](#ct-elasticloadbalancing-pr-9-templates) 

**Explanation**

Before you start to use a load balancer, you must add one or more listeners\. A listener is a process that uses the configured protocol and port to check for connection requests\. Listeners can support HTTP and HTTPS/TLS protocols\. You should always use an HTTPS or TLS listener, so that the load balancer does the work of encryption and decryption in transit\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-9-remediation"></a>

Configure Classic Load Balancer front\-end listeners with HTTPS or SSL protocols\.

The examples that follow show how to implement this remediation\.

#### Classic Load Balancer \- Example One<a name="ct-elasticloadbalancing-pr-9-remediation-1"></a>

Classic Load Balancer configured with an HTTPS Listener\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "LoadBalancer": {
        "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Subnets": [
                {
                    "Ref": "Subnet"
                }
            ],
            "Listeners": [
                {
                    "Protocol": "HTTPS",
                    "SSLCertificateId": {
                        "Ref": "ACMCertificate"
                    },
                    "InstancePort": 80,
                    "LoadBalancerPort": 443
                }
            ]
        }
    }
}
```

**YAML example**

```
LoadBalancer:
  Type: AWS::ElasticLoadBalancing::LoadBalancer
  Properties:
    Scheme: internal
    Subnets:
      - !Ref 'Subnet'
    Listeners:
      - Protocol: HTTPS
        SSLCertificateId: !Ref 'ACMCertificate'
        InstancePort: 80
        LoadBalancerPort: 443
```

The examples that follow show how to implement this remediation\.

#### Classic Load Balancer \- Example Two<a name="ct-elasticloadbalancing-pr-9-remediation-2"></a>

Classic Load Balancer configured with an SSL Listener\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "LoadBalancer": {
        "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Subnets": [
                {
                    "Ref": "Subnet"
                }
            ],
            "Listeners": [
                {
                    "Protocol": "SSL",
                    "SSLCertificateId": {
                        "Ref": "ACMCertificate"
                    },
                    "InstancePort": 80,
                    "LoadBalancerPort": 443
                }
            ]
        }
    }
}
```

**YAML example**

```
LoadBalancer:
  Type: AWS::ElasticLoadBalancing::LoadBalancer
  Properties:
    Scheme: internal
    Subnets:
      - !Ref 'Subnet'
    Listeners:
      - Protocol: SSL
        SSLCertificateId: !Ref 'ACMCertificate'
        InstancePort: 80
        LoadBalancerPort: 443
```

### CT\.ELASTICLOADBALANCING\.PR\.9 rule specification<a name="ct-elasticloadbalancing-pr-9-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elb_tls_https_listeners_only_check
# 
# Description:
#   Checks whether Classic Load Balancer front-end listeners are configured with HTTPS or SSL protocols.
# 
# Reports on:
#   AWS::ElasticLoadBalancing::LoadBalancer
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
#       And: The input document does not contain any Elastic Load Balancing LoadBalancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancing LoadBalancer resource
#       And: 'Listeners' has not been provided or is provided with a value of an empty list
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancing LoadBalancer resource
#       And: 'Protocol' on LoadBalancer 'Listeners' is not set to 'HTTPS' or 'SSL'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ElasticLoadBalancing LoadBalancer resource
#       And: 'Protocol' is set to 'HTTPS' or 'SSL' for all 'Listeners'
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_TYPE = "AWS::ElasticLoadBalancing::LoadBalancer"
let INPUT_DOCUMENT = this

#
# Assignments
#
let classic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_TYPE ]

#
# Primary Rules
#
rule elb_tls_https_listeners_only_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %classic_load_balancers not empty {

    check(%classic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.9]: Require that an AWS ELB Application or Classic Load Balancer listener is configured with HTTPS or TLS termination
        [FIX]: Configure Classic Load Balancer front-end listeners with HTTPS or SSL protocols.
        >>
}

rule elb_tls_https_listeners_only_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_TYPE) {

    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.9]: Require that an AWS ELB Application or Classic Load Balancer listener is configured with HTTPS or TLS termination
        [FIX]: Configure Classic Load Balancer front-end listeners with HTTPS or SSL protocols.
        >>
}

#
# Parameterized Rules
#
rule check(classic_load_balancer) {
    %classic_load_balancer {
        # Scenario 2
        Listeners exists
        Listeners is_list
        Listeners not empty

        # Scenarios 3 and 4
        Listeners[*] {
           Protocol exists
           Protocol in ["HTTPS", "SSL"]
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

### CT\.ELASTICLOADBALANCING\.PR\.9 example templates<a name="ct-elasticloadbalancing-pr-9-templates"></a>

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
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      Listeners:
      - Protocol: HTTPS
        SSLCertificateId: arn:aws:acm:us-east-1:123456789012:certificate/12345678-12ab-34cd-56ef-12345678
        InstancePort: 80
        LoadBalancerPort: 443
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
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      Listeners:
      - Protocol: HTTP
        InstancePort: 80
        LoadBalancerPort: 80
```

## \[CT\.ELASTICLOADBALANCING\.PR\.10\] Require an ELB application or classic load balancer to have logging activated<a name="ct-elasticloadbalancing-pr-10-description"></a>

This control checks whether Classic Load Balancers have logging enabled\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancing::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.10 rule specification](#ct-elasticloadbalancing-pr-10-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.10 rule specification](#ct-elasticloadbalancing-pr-10-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.10 example templates](#ct-elasticloadbalancing-pr-10-templates) 

**Explanation**

Elastic Load Balancing provides access logs that capture detailed information about requests sent to your load balancer\. Each log contains information such as the time the request was received, the client's IP address, latencies, request paths, and server responses\. You can use these access logs to analyze traffic patterns and to troubleshoot issues\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-10-remediation"></a>

Set an `AccessLoggingPolicy` and provide an `S3BucketName` with an Amazon S3 bucket configured to receive classic load balancer access logs\.

The examples that follow show how to implement this remediation\.

#### Classic Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-10-remediation-1"></a>

Classic Load Balancer configured with an HTTPS listener and access logging\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ClassicLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Listeners": [
                {
                    "Protocol": "HTTPS",
                    "InstancePort": 80,
                    "LoadBalancerPort": 443
                }
            ],
            "Subnets": [
                {
                    "Ref": "Subnet"
                }
            ],
            "AccessLoggingPolicy": {
                "Enabled": true,
                "S3BucketName": {
                    "Ref": "LoggingBucket"
                }
            }
        }
    }
}
```

**YAML example**

```
ClassicLoadBalancer:
  Type: AWS::ElasticLoadBalancing::LoadBalancer
  Properties:
    Scheme: internal
    Listeners:
      - Protocol: HTTPS
        InstancePort: 80
        LoadBalancerPort: 443
    Subnets:
      - !Ref 'Subnet'
    AccessLoggingPolicy:
      Enabled: true
      S3BucketName: !Ref 'LoggingBucket'
```

### CT\.ELASTICLOADBALANCING\.PR\.10 rule specification<a name="ct-elasticloadbalancing-pr-10-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elb_logging_enabled_check
# 
# Description:
#   This control checks whether Classic Load Balancers have logging enabled.
# 
# Reports on:
#   AWS::ElasticLoadBalancing::LoadBalancer
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
#       And: The input document does not contain any Elastic Load Balancing load balancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'AccessLoggingPolicy' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'AccessLoggingPolicy' has been provided
#       And: 'Enabled' in 'AccessLoggingPolicy' is missing or has been set to bool(false) or 'S3BucketName' is missing
#             or empty string value
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'AccessLoggingPolicy' has been provided
#       And: 'Enabled' has been provided in 'AccessLoggingPolicy' and has been set to bool(true)
#       And: 'S3BucketName' has been provided in 'AccessLoggingPolicy' as a non-empty string value or
#            valid local reference
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_TYPE = "AWS::ElasticLoadBalancing::LoadBalancer"
let INPUT_DOCUMENT = this

#
# Assignments
#
let classic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_TYPE ]

#
# Primary Rules
#
rule elb_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                    %classic_load_balancers not empty {
    check(%classic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.10]: Require an ELB application or classic load balancer to have logging activated
        [FIX]: Set an 'AccessLoggingPolicy' and provide an 'S3BucketName' with an Amazon S3 bucket configured to receive classic load balancer access logs.
        >>

}

rule elb_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.10]: Require an ELB application or classic load balancer to have logging activated
        [FIX]: Set an 'AccessLoggingPolicy' and provide an 'S3BucketName' with an Amazon S3 bucket configured to receive classic load balancer access logs.
        >>
}

#
# Parameterized Rules
#
rule check(classic_load_balancer) {
    %classic_load_balancer {
        # Scenario 2
        AccessLoggingPolicy exists
        AccessLoggingPolicy is_struct

        AccessLoggingPolicy {
            # Scenario 3 and 4
            Enabled exists
            Enabled == true

            S3BucketName exists
            check_is_string_and_not_empty(S3BucketName) or
            check_local_references(%INPUT_DOCUMENT, S3BucketName, "AWS::S3::Bucket")
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

### CT\.ELASTICLOADBALANCING\.PR\.10 example templates<a name="ct-elasticloadbalancing-pr-10-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Mappings:
  RegionToELBAccountId:
    us-east-1:
      AccountId: '127311923021'
    us-west-1:
      AccountId: '027434742980'
    us-west-2:
      AccountId: '797873946194'
    ca-central-1:
      AccountId: '985666609251'
    eu-west-1:
      AccountId: '156460612806'
    ap-northeast-1:
      AccountId: '582318560864'
    ap-northeast-2:
      AccountId: '600734575887'
    ap-southeast-1:
      AccountId: '114774131450'
    ap-southeast-2:
      AccountId: '783225319266'
    ap-south-1:
      AccountId: '718504428378'
    us-east-2:
      AccountId: '033677994240'
    sa-east-1:
      AccountId: '507241528517'
    eu-central-1:
      AccountId: '054676820928'
    af-south-1:
      AccountId: '098369216593'
    ap-east-1:
      AccountId: '754344448648'
    ap-southeast-3:
      AccountId: '589379963580'
    ap-northeast-3:
      AccountId: '383597477331'
    eu-west-2:
      AccountId: '652711504416'
    eu-south-1:
      AccountId: '635631232127'
    eu-west-3:
      AccountId: '009996457667'
    eu-north-1:
      AccountId: '897822967062'
    me-south-1:
      AccountId: '076674570225'
    us-gov-west-1:
      AccountId: '048591011584'
    us-gov-east-1:
      AccountId: '190560391635'
  RegionToARNPrefix:
    us-east-1:
      ARNPrefix: 'arn:aws:'
    us-west-1:
      ARNPrefix: 'arn:aws:'
    us-west-2:
      ARNPrefix: 'arn:aws:'
    ca-central-1:
      ARNPrefix: 'arn:aws:'
    eu-west-1:
      ARNPrefix: 'arn:aws:'
    ap-northeast-1:
      ARNPrefix: 'arn:aws:'
    ap-northeast-2:
      ARNPrefix: 'arn:aws:'
    ap-southeast-1:
      ARNPrefix: 'arn:aws:'
    ap-southeast-2:
      ARNPrefix: 'arn:aws:'
    ap-south-1:
      ARNPrefix: 'arn:aws:'
    us-east-2:
      ARNPrefix: 'arn:aws:'
    sa-east-1:
      ARNPrefix: 'arn:aws:'
    eu-central-1:
      ARNPrefix: 'arn:aws:'
    af-south-1:
      ARNPrefix: 'arn:aws:'
    ap-east-1:
      ARNPrefix: 'arn:aws:'
    ap-southeast-3:
      ARNPrefix: 'arn:aws:'
    ap-northeast-3:
      ARNPrefix: 'arn:aws:'
    eu-west-2:
      ARNPrefix: 'arn:aws:'
    eu-south-1:
      ARNPrefix: 'arn:aws:'
    eu-west-3:
      ARNPrefix: 'arn:aws:'
    eu-north-1:
      ARNPrefix: 'arn:aws:'
    me-south-1:
      ARNPrefix: 'arn:aws:'
    us-gov-west-1:
      ARNPrefix: 'arn:aws-us-gov:'
    us-gov-east-1:
      ARNPrefix: 'arn:aws-us-gov:'
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
  LoggingBucket:
    Type: AWS::S3::Bucket
  LoggingBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: LoggingBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:PutObject'
            Effect: Allow
            Resource:
              Fn::Join:
                - ''
                - - Fn::FindInMap: [RegionToARNPrefix, !Ref 'AWS::Region', ARNPrefix]
                  - 's3:::'
                  - Ref: LoggingBucket
                  - /AWSLogs/
                  - Ref: AWS::AccountId
                  - /*
            Principal:
              AWS: 
                Fn::FindInMap: [RegionToELBAccountId, !Ref 'AWS::Region', AccountId]
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Listeners:
      - Protocol: HTTP
        InstancePort: 80
        LoadBalancerPort: 80
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      AccessLoggingPolicy:
        Enabled: true
        S3BucketName:
          Ref: LoggingBucket
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
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Listeners:
      - Protocol: HTTP
        InstancePort: 80
        LoadBalancerPort: 80
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
```

## \[CT\.ELASTICLOADBALANCING\.PR\.11\] Require any ELB classic load balancer to have connection draining activated<a name="ct-elasticloadbalancing-pr-11-description"></a>

This control checks whether Elastic Load Balancing \(ELB\) Classic Load Balancers have connection draining configured\.
+ **Control objective: **Improve resiliency
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancing::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.11 rule specification](#ct-elasticloadbalancing-pr-11-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.11 rule specification](#ct-elasticloadbalancing-pr-11-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.11 example templates](#ct-elasticloadbalancing-pr-11-templates) 

**Explanation**

Activating connection draining on Classic Load Balancers ensures that the load balancer stops sending requests to instances that are de\-registering or unhealthy\. It keeps the existing connections open\. This configuration is particularly useful for instances in Auto Scaling groups, to ensure that connections aren't severed abruptly\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-11-remediation"></a>

Configure a `ConnectionDrainingPolicy` on Elastic Load Balancing Classic Load Balancers\.

The examples that follow show how to implement this remediation\.

#### Classic Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-11-remediation-1"></a>

Classic Load Balancer configured with connection draining active\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ClassicLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Listeners": [
                {
                    "InstancePort": "80",
                    "InstanceProtocol": "HTTP",
                    "LoadBalancerPort": "443",
                    "Protocol": "HTTPS",
                    "PolicyNames": [
                        "Example-SSLNegotiation-Policy"
                    ],
                    "SSLCertificateId": {
                        "Ref": "ACMCertificate"
                    }
                }
            ],
            "Policies": [
                {
                    "PolicyName": "Example-SSLNegotiation-Policy",
                    "PolicyType": "SSLNegotiationPolicyType",
                    "Attributes": [
                        {
                            "Name": "Reference-Security-Policy",
                            "Value": "ELBSecurityPolicy-TLS-1-2-2017-01"
                        }
                    ]
                }
            ],
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "ConnectionDrainingPolicy": {
                "Enabled": true
            }
        }
    }
}
```

**YAML example**

```
ClassicLoadBalancer:
  Type: AWS::ElasticLoadBalancing::LoadBalancer
  Properties:
    Scheme: internal
    Listeners:
      - InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        Protocol: HTTPS
        PolicyNames:
          - Example-SSLNegotiation-Policy
        SSLCertificateId: !Ref 'ACMCertificate'
    Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
          - Name: Reference-Security-Policy
            Value: ELBSecurityPolicy-TLS-1-2-2017-01
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    ConnectionDrainingPolicy:
      Enabled: true
```

### CT\.ELASTICLOADBALANCING\.PR\.11 rule specification<a name="ct-elasticloadbalancing-pr-11-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elb_connection_draining_enabled_check
# 
# Description:
#   This control checks whether Elastic Load Balancing (ELB) Classic Load Balancers have connection draining configured.
# 
# Reports on:
#   AWS::ElasticLoadBalancing::LoadBalancer
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
#       And: The input document does not contain any Elastic Load Balancing load balancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'ConnectionDrainingPolicy' has not been specified
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'ConnectionDrainingPolicy' has been specified
#       And: 'Enabled' in 'ConnectionDrainingPolicy' is missing or has been set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing load balancer resource
#       And: 'ConnectionDrainingPolicy' has been specified
#       And: 'Enabled' in 'ConnectionDrainingPolicy' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_TYPE = "AWS::ElasticLoadBalancing::LoadBalancer"
let INPUT_DOCUMENT = this

#
# Assignments
#
let classic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_TYPE ]

#
# Primary Rules
#
rule elb_connection_draining_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                %classic_load_balancers not empty {

    check(%classic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.11]: Require any ELB classic load balancer to have connection draining activated
        [FIX]: Configure a 'ConnectionDrainingPolicy' on Elastic Load Balancing Classic Load Balancers.
        >>
}

rule elb_connection_draining_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.11]: Require any ELB classic load balancer to have connection draining activated
        [FIX]: Configure a 'ConnectionDrainingPolicy' on Elastic Load Balancing Classic Load Balancers.
        >>
}

#
# Parameterized Rules
#
rule check(classic_load_balancer) {
    %classic_load_balancer {
        # Scenario 2
        ConnectionDrainingPolicy exists
        ConnectionDrainingPolicy is_struct

        ConnectionDrainingPolicy {
            # Scenario 3 and 4
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

### CT\.ELASTICLOADBALANCING\.PR\.11 example templates<a name="ct-elasticloadbalancing-pr-11-templates"></a>

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
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Listeners:
      - InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        Protocol: HTTPS
        PolicyNames:
        - Example-SSLNegotiation-Policy
        SSLCertificateId:
          Ref: ACMCertificate
      Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
        - Name: Reference-Security-Policy
          Value: ELBSecurityPolicy-TLS-1-2-2017-01
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      ConnectionDrainingPolicy:
        Enabled: true
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
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Listeners:
      - Protocol: HTTP
        InstancePort: 80
        LoadBalancerPort: 80
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
```

## \[CT\.ELASTICLOADBALANCING\.PR\.12\] Require any ELB classic load balancer SSL/HTTPS listener to have a predefined security policy with a strong configuration<a name="ct-elasticloadbalancing-pr-12-description"></a>

This control checks whether Elastic Load Balancing \(ELB\) Classic Load Balancer HTTPS/SSL listeners use the predefined security policy `ELBSecurityPolicy-TLS-1-2-2017-01`\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancing::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.12 rule specification](#ct-elasticloadbalancing-pr-12-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.12 rule specification](#ct-elasticloadbalancing-pr-12-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.12 example templates](#ct-elasticloadbalancing-pr-12-templates) 

**Explanation**

A security policy is a combination of SSL protocols, ciphers, and the server order preference option\. Predefined policies control the ciphers, protocols, and preference orders that provide support during SSL negotiations between a client and load balancer\.

Using `ELBSecurityPolicy-TLS-1-2-2017-01` can help you to meet compliance and security standards that require you to turn off specific versions of SSL and TLS\.

**Usage considerations**  
This control applies only to Elastic Load Balancing Classic Load Balancers configured with HTTPS or SSL listeners\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-12-remediation"></a>

Configure Classic Load Balancer HTTPS/SSL listeners to use the predefined security policy called `ELBSecurityPolicy-TLS-1-2-2017-01`\.

The examples that follow show how to implement this remediation\.

#### Classic Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-12-remediation-1"></a>

Classic Load Balancer configured with an HTTPS listener and SSL negotiation policy that references the `ELBSecurityPolicy-TLS-1-2-2017-01` predefined security policy for classic load balancers\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ClassicLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "Policies": [
                {
                    "PolicyName": "Example-SSLNegotiation-Policy",
                    "PolicyType": "SSLNegotiationPolicyType",
                    "Attributes": [
                        {
                            "Name": "Reference-Security-Policy",
                            "Value": "ELBSecurityPolicy-TLS-1-2-2017-01"
                        }
                    ]
                }
            ],
            "Listeners": [
                {
                    "InstancePort": 80,
                    "InstanceProtocol": "HTTP",
                    "LoadBalancerPort": 443,
                    "Protocol": "HTTPS",
                    "SSLCertificateId": {
                        "Ref": "ACMCertificate"
                    },
                    "PolicyNames": [
                        "Example-SSLNegotiation-Policy"
                    ]
                }
            ]
        }
    }
}
```

**YAML example**

```
ClassicLoadBalancer:
  Type: AWS::ElasticLoadBalancing::LoadBalancer
  Properties:
    Scheme: internal
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
          - Name: Reference-Security-Policy
            Value: ELBSecurityPolicy-TLS-1-2-2017-01
    Listeners:
      - InstancePort: 80
        InstanceProtocol: HTTP
        LoadBalancerPort: 443
        Protocol: HTTPS
        SSLCertificateId: !Ref 'ACMCertificate'
        PolicyNames:
          - Example-SSLNegotiation-Policy
```

### CT\.ELASTICLOADBALANCING\.PR\.12 rule specification<a name="ct-elasticloadbalancing-pr-12-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elb_predefined_security_policy_ssl_check
# 
# Description:
#   This control checks whether Elastic Load Balancing (ELB) Classic Load Balancer HTTPS/SSL listeners use the predefined security policy 'ELBSecurityPolicy-TLS-1-2-2017-01'.
# 
# Reports on:
#   AWS::ElasticLoadBalancing::LoadBalancer
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
#       And: The input document does not contain any Elastic Load Balancing LoadBalancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing LoadBalancer resource
#       And: There are no HTTPS or SSL 'Listeners' configured on the Elastic Load Balancing LoadBalancer resource
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing LoadBalancer resource
#       And: 'Policies' does not contain a policy with 'PolicyType' equal to 'SSLNegotiationPolicyType'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing LoadBalancer resource
#       And: 'Policies' contains a policy with 'PolicyType' equal to 'SSLNegotiationPolicyType'
#       And: 'Policies' is missing a 'Reference-Security-Policy' with a value of
#            'ELBSecurityPolicy-TLS-1-2-2017-01'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing LoadBalancer resource
#       And: 'Policies' contains a policy with 'PolicyType' equal to 'SSLNegotiationPolicyType'
#       And: 'Policies' contains a 'Reference-Security-Policy' with a value of
#            'ELBSecurityPolicy-TLS-1-2-2017-01'
#       And: A 'HTTPS' or 'SSL' Listener on the LoadBalancer resource does not reference the secure policy
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing LoadBalancer resource
#       And: 'Policies' contains a policy with 'PolicyType' equal to 'SSLNegotiationPolicyType'
#       And: 'Policies' contains a 'Reference-Security-Policy' with a value of
#            'ELBSecurityPolicy-TLS-1-2-2017-01'
#       And: All 'HTTPS' and 'SSL' Listeners on the LoadBalancer resource reference the secure policy
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_TYPE = "AWS::ElasticLoadBalancing::LoadBalancer"
let VALID_REFERENCE_SECURITY_POLICIES = [ "ELBSecurityPolicy-TLS-1-2-2017-01" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let classic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_TYPE ]

#
# Primary Rules
#
rule elb_predefined_security_policy_ssl_check when is_cfn_template(%INPUT_DOCUMENT)
                                                   %classic_load_balancers not empty {

    check(%classic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.12]: Require any ELB classic load balancer SSL/HTTPS listener to have a predefined security policy with a strong configuration
        [FIX]: Configure classic load balancer HTTPS/SSL listeners to use the predefined security policy called ELBSecurityPolicy-TLS-1-2-2017-01.
        >>
}

rule elb_predefined_security_policy_ssl_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_TYPE) {

    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.12]: Require any ELB classic load balancer SSL/HTTPS listener to have a predefined security policy with a strong configuration
        [FIX]: Configure classic load balancer HTTPS/SSL listeners to use the predefined security policy called ELBSecurityPolicy-TLS-1-2-2017-01.
        >>
}

#
# Parameterized Rules
#
rule check(classic_load_balancer) {
    %classic_load_balancer {
        let elb = this

        # Scenario 2
        Listeners[ Protocol in ["HTTPS", "SSL"] ] {
            %elb.Policies exists
            %elb.Policies is_list
            %elb.Policies not empty

            let secure_policies = %elb.Policies[
                PolicyType == "SSLNegotiationPolicyType"
                some Attributes[*] {
                    Name == "Reference-Security-Policy"
                    Value in %VALID_REFERENCE_SECURITY_POLICIES
                }
            ].PolicyName

            # Scenarios 3 and 4
            %secure_policies not empty

            # Scenarios 5 and 6
            PolicyNames exists
            PolicyNames is_list
            PolicyNames not empty
            some PolicyNames.* in %secure_policies
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

### CT\.ELASTICLOADBALANCING\.PR\.12 example templates<a name="ct-elasticloadbalancing-pr-12-templates"></a>

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
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
        - Name: Reference-Security-Policy
          Value: ELBSecurityPolicy-TLS-1-2-2017-01
      Listeners:
      - InstancePort: 80
        InstanceProtocol: HTTP
        LoadBalancerPort: 443
        Protocol: HTTPS
        SSLCertificateId:
          Ref: ACMCertificate
        PolicyNames:
        - Example-SSLNegotiation-Policy
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
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
        - Name: Reference-Security-Policy
          Value: ELBSecurityPolicy-2016-08
      Listeners:
      - InstancePort: 80
        InstanceProtocol: HTTP
        LoadBalancerPort: 443
        Protocol: HTTPS
        SSLCertificateId: arn:aws:iam::123456789012:server-certificate/example-certificate
        PolicyNames:
        - Example-SSLNegotiation-Policy
```

## \[CT\.ELASTICLOADBALANCING\.PR\.13\] Require any ELB classic load balancer to have cross\-zone load balancing activated<a name="ct-elasticloadbalancing-pr-13-description"></a>

This control checks whether cross\-zone load balancing is configured for your Classic Load Balancer\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ElasticLoadBalancing::LoadBalancer`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICLOADBALANCING\.PR\.13 rule specification](#ct-elasticloadbalancing-pr-13-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICLOADBALANCING\.PR\.13 rule specification](#ct-elasticloadbalancing-pr-13-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ELASTICLOADBALANCING\.PR\.13 example templates](#ct-elasticloadbalancing-pr-13-templates) 

**Explanation**

A load balancer node distributes traffic across the registered targets in its Availability Zone\. When cross\-zone load balancing is turned off, each load balancer node distributes traffic only across the registered targets in its own Availability Zone\. If the number of registered targets is not same across the Availability Zones, traffic is not distributed evenly, so the instances in one zone may become over\-utilized, when compared to the instances in another zone\. With cross\-zone load balancing activated, each load balancer node for your classic load balancer distributes requests evenly across the registered instances in all enabled Availability Zones\.

### Remediation for rule failure<a name="ct-elasticloadbalancing-pr-13-remediation"></a>

Set `CrossZone` to `true` on Classic Load Balancers\.

The examples that follow show how to implement this remediation\.

#### Classic Load Balancer \- Example<a name="ct-elasticloadbalancing-pr-13-remediation-1"></a>

Classic Load Balancer configured with cross\-zone load balancing active\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ClassicLoadBalancer": {
        "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
        "Properties": {
            "Scheme": "internal",
            "Listeners": [
                {
                    "InstancePort": "80",
                    "InstanceProtocol": "HTTP",
                    "LoadBalancerPort": "443",
                    "Protocol": "HTTPS",
                    "PolicyNames": [
                        "Sample-SSLNegotiation-Policy"
                    ],
                    "SSLCertificateId": {
                        "Ref": "ACMCertificate"
                    }
                }
            ],
            "Policies": [
                {
                    "PolicyName": "Sample-SSLNegotiation-Policy",
                    "PolicyType": "SSLNegotiationPolicyType",
                    "Attributes": [
                        {
                            "Name": "Reference-Security-Policy",
                            "Value": "ELBSecurityPolicy-TLS-1-2-2017-01"
                        }
                    ]
                }
            ],
            "Subnets": [
                {
                    "Ref": "SubnetOne"
                },
                {
                    "Ref": "SubnetTwo"
                }
            ],
            "CrossZone": true
        }
    }
}
```

**YAML example**

```
ClassicLoadBalancer:
  Type: AWS::ElasticLoadBalancing::LoadBalancer
  Properties:
    Scheme: internal
    Listeners:
      - InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        Protocol: HTTPS
        PolicyNames:
          - Sample-SSLNegotiation-Policy
        SSLCertificateId: !Ref 'ACMCertificate'
    Policies:
      - PolicyName: Sample-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
          - Name: Reference-Security-Policy
            Value: ELBSecurityPolicy-TLS-1-2-2017-01
    Subnets:
      - !Ref 'SubnetOne'
      - !Ref 'SubnetTwo'
    CrossZone: true
```

### CT\.ELASTICLOADBALANCING\.PR\.13 rule specification<a name="ct-elasticloadbalancing-pr-13-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   elb_cross_zone_load_balancing_enabled_check
# 
# Description:
#   This control checks whether cross-zone load balancing is configured for your Classic Load Balancer.
# 
# Reports on:
#   AWS::ElasticLoadBalancing::LoadBalancer
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
#       And: The input document does not contain any Elastic Load Balancing LoadBalancer resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing LoadBalancer resource
#       And: 'CrossZone' has not been specified
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing LoadBalancer resource
#       And: 'CrossZone' has been specified and set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Elastic Load Balancing LoadBalancer resource
#       And: 'CrossZone' has been specified and set to bool(true)
#      Then: PASS

#
# Constants
#
let ELASTIC_LOAD_BALANCER_TYPE = "AWS::ElasticLoadBalancing::LoadBalancer"
let INPUT_DOCUMENT = this

#
# Assignments
#
let classic_load_balancers = Resources.*[ Type == %ELASTIC_LOAD_BALANCER_TYPE ]

#
# Primary Rules
#
rule elb_cross_zone_load_balancing_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %classic_load_balancers not empty {
    check(%classic_load_balancers.Properties)
        <<
        [CT.ELASTICLOADBALANCING.PR.13]: Require any ELB classic load balancer to have cross-zone load balancing activated
        [FIX]: Set 'CrossZone' to 'true' on Classic Load Balancers.
        >>
}

rule elb_cross_zone_load_balancing_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %ELASTIC_LOAD_BALANCER_TYPE) {
    check(%INPUT_DOCUMENT.%ELASTIC_LOAD_BALANCER_TYPE.resourceProperties)
        <<
        [CT.ELASTICLOADBALANCING.PR.13]: Require any ELB classic load balancer to have cross-zone load balancing activated
        [FIX]: Set 'CrossZone' to 'true' on Classic Load Balancers.
        >>
}

#
# Parameterized Rules
#
rule check(classic_load_balancer) {
    %classic_load_balancer {
        # Scenario 2
        CrossZone exists

        # Scenario 3 and 4
        CrossZone == true
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

### CT\.ELASTICLOADBALANCING\.PR\.13 example templates<a name="ct-elasticloadbalancing-pr-13-templates"></a>

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
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Listeners:
      - InstancePort: '80'
        InstanceProtocol: HTTP
        LoadBalancerPort: '443'
        Protocol: HTTPS
        PolicyNames:
        - Example-SSLNegotiation-Policy
        SSLCertificateId:
          Ref: ACMCertificate
      Policies:
      - PolicyName: Example-SSLNegotiation-Policy
        PolicyType: SSLNegotiationPolicyType
        Attributes:
        - Name: Reference-Security-Policy
          Value: ELBSecurityPolicy-TLS-1-2-2017-01
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      CrossZone: true
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
  ClassicLoadBalancer:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
      Scheme: internal
      Listeners:
      - Protocol: HTTP
        InstancePort: 80
        LoadBalancerPort: 80
      Subnets:
      - Ref: SubnetOne
      - Ref: SubnetTwo
      CrossZone: false
```