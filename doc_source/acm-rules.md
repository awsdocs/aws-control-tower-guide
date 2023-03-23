# AWS Certificate Manager controls<a name="acm-rules"></a>

**Topics**
+ [\[CT\.ACM\.PR\.1\] Require an AWS Private CA certificate to have a single domain name](#ct-acm-pr-1-description)

## \[CT\.ACM\.PR\.1\] Require an AWS Private CA certificate to have a single domain name<a name="ct-acm-pr-1-description"></a>

This control checks whether any AWS Certificate Manager \(ACM\) Private CA certificates have wildcard domain names instead of single domain names\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CertificateManager::Certificate`
+ **AWS CloudFormation guard rule: ** [CT\.ACM\.PR\.1 rule specification](#ct-acm-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ACM\.PR\.1 rule specification](#ct-acm-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.ACM\.PR\.1 example templates](#ct-acm-pr-1-templates) 

**Explanation**

AWS Private CA allows you to use wildcards \(\*\) in the domain name, so you can protect several sites in the same domain\. This type of certificate presents some risk, because if the private key of a certificate is compromised, all domain and subdomains with the compromised certificate are compromised\. We recommend that you use single domain name certificates instead of wildcard certificates to reduce these associated risks\. 

### Remediation for rule failure<a name="ct-acm-pr-1-remediation"></a>

Set `DomainName` and each entry within `SubjectAlternativeNames` to a fully qualified domain name \(FQDN\) that does not contain a wildcard \(\*\)\.

The examples that follow show how to implement this remediation\.

#### AWS Certificate Manager Private CA Certificate \- Example One<a name="ct-acm-pr-1-remediation-1"></a>

AWS Certificate Manager Private CA certificate configured with a single domain and no subject alternative names\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "Resources": {
        "ACMCertificate": {
            "Type": "AWS::CertificateManager::Certificate",
            "Properties": {
                "CertificateAuthorityArn": "arn:aws:acm-pca:us-east-1:123456789012:certificate-authority/12345678-1234-1234-1234-123456789012",
                "DomainName": "example.com"
            }
        }
    }
}
```

**YAML example**

```
Resources:
  ACMCertificate:
    Type: AWS::CertificateManager::Certificate
    Properties:
      CertificateAuthorityArn: arn:aws:acm-pca:us-east-1:123456789012:certificate-authority/12345678-1234-1234-1234-123456789012
      DomainName: example.com
```

The examples that follow show how to implement this remediation\.

#### AWS Certificate Manager Private CA Certificate \- Example Two<a name="ct-acm-pr-1-remediation-2"></a>

AWS Certificate Manager private CA certificate configured with a single domain and one subject alternative name\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "Resources": {
        "ACMCertificate": {
            "Type": "AWS::CertificateManager::Certificate",
            "Properties": {
                "CertificateAuthorityArn": "arn:aws:acm-pca:us-east-1:123456789012:certificate-authority/12345678-1234-1234-1234-123456789012",
                "DomainName": "example.com",
                "SubjectAlternativeNames": [
                    "www.example.com"
                ]
            }
        }
    }
}
```

**YAML example**

```
Resources:
  ACMCertificate:
    Type: AWS::CertificateManager::Certificate
    Properties:
      CertificateAuthorityArn: arn:aws:acm-pca:us-east-1:123456789012:certificate-authority/12345678-1234-1234-1234-123456789012
      DomainName: example.com
      SubjectAlternativeNames:
        - www.example.com
```

### CT\.ACM\.PR\.1 rule specification<a name="ct-acm-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   acm_certificate_domain_name_check
# 
# Description:
#   This control checks whether any AWS Certificate Manager (ACM) Private CA certificates have wildcard domain names instead of single domain names.
# 
# Reports on:
#   AWS::CertificateManager::Certificate
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
#       And: The input document does not contain any ACM certificate resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ACM certificate resource
#       And: 'CertificateAuthorityArn' has not been provided
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ACM certificate resource
#       And: 'CertificateAuthorityArn' has been provided
#       And: 'DomainName' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ACM certificate resource
#       And: 'CertificateAuthorityArn' has been provided
#       And: 'SubjectAlternativeNames' has not been provided or provided as an empty list
#       And: 'DomainName' has been provided with a string that begins with a wildcard character ('*').
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ACM certificate resource
#       And: 'CertificateAuthorityArn' has been provided
#       And: 'DomainName' has been provided with a string that does not begin with a wildcard character ('*').
#       And: 'SubjectAlternativeNames' has been provided as a non-empty list containing a string that
#            begins with a wildcard character ('*').
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ACM certificate resource
#       And: 'CertificateAuthorityArn' has been provided
#       And: 'DomainName' has been provided with a string that does not begin with a wildcard character ('*').
#       And: 'SubjectAlternativeNames' has not been provided or provided as an empty list
#      Then: PASS
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an ACM certificate resource
#       And: 'CertificateAuthorityArn' has been provided
#       And: 'DomainName' has been provided with a string that does not begin with a wildcard character ('*').
#       And: 'SubjectAlternativeNames' has been provided as a non-empty list where no entries are strings that
#            begin with a wildcard character ('*').
#      Then: PASS

#
# Constants
#
let ACM_CERTIFICATE_TYPE = "AWS::CertificateManager::Certificate"
let WILDCARD_DOMAIN_NAME_REGEX_PATTERN = /^(\*\.).*$/
let INPUT_DOCUMENT = this

#
# Assignments
#
let acm_certificates = Resources.*[ Type == %ACM_CERTIFICATE_TYPE ]

#
# Primary Rules
#
rule acm_certificate_domain_name_check when is_cfn_template(%INPUT_DOCUMENT)
                                            %acm_certificates not empty {
    check(%acm_certificates.Properties)
        <<
        [CT.ACM.PR.1]: Require an AWS Private CA certificate to have a single domain name
            [FIX]: Set 'DomainName' and each entry within 'SubjectAlternativeNames' to a fully qualified domain name (FQDN) that does not contain a wildcard (*).
        >>
}

rule acm_certificate_domain_name_check when is_cfn_hook(%INPUT_DOCUMENT, %ACM_CERTIFICATE_TYPE) {
    check(%INPUT_DOCUMENT.%ACM_CERTIFICATE_TYPE.resourceProperties)
        <<
        [CT.ACM.PR.1]: Require an AWS Private CA certificate to have a single domain name
            [FIX]: Set 'DomainName' and each entry within 'SubjectAlternativeNames' to a fully qualified domain name (FQDN) that does not contain a wildcard (*).
        >>
}

#
# Parameterized Rules
#
rule check(acm_certificate) {
    %acm_certificate[
        CertificateAuthorityArn exists
    ] {
        # Scenario 2
        DomainName exists
        # Scenario 3 and 4
        check_wildcarded_domain(DomainName)
        check_subject_alternative_names(this)
    }
}

rule check_subject_alternative_names(acm_certificate) {
    %acm_certificate [
        SubjectAlternativeNames exists
        SubjectAlternativeNames is_list
        SubjectAlternativeNames not empty
    ] {
        SubjectAlternativeNames[*] {
            check_wildcarded_domain(this)
        }
    }
}

rule check_wildcarded_domain(domain) {
    %domain {
        this is_string
        this != %WILDCARD_DOMAIN_NAME_REGEX_PATTERN
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

### CT\.ACM\.PR\.1 example templates<a name="ct-acm-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ACMCertificate:
    Type: AWS::CertificateManager::Certificate
    Properties:
      CertificateAuthorityArn: arn:aws:acm-pca:us-east-1:123456789012:certificate-authority/12345678-1234-1234-1234-123456789012
      DomainName: example.com
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ACMCertificate:
    Type: AWS::CertificateManager::Certificate
    Properties:
      CertificateAuthorityArn: arn:aws:acm-pca:us-east-1:123456789012:certificate-authority/12345678-1234-1234-1234-123456789012
      DomainName: '*.example.com'
```