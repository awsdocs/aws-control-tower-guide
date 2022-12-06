# Amazon CloudFront controls<a name="cloudfront-rules"></a>

**Topics**
+ [\[CT\.CLOUDFRONT\.PR\.1\] Require an Amazon CloudFront distribution to have a default root object configured](#ct-cloudfront-pr-1-description)
+ [\[CT\.CLOUDFRONT\.PR\.2\] Require any Amazon CloudFront distributions with Amazon S3 backed origins to have an origin access identity configured](#ct-cloudfront-pr-2-description)
+ [\[CT\.CLOUDFRONT\.PR\.3\] Require an Amazon CloudFront distribution to have encryption in transit configured](#ct-cloudfront-pr-3-description)
+ [\[CT\.CLOUDFRONT\.PR\.4\] Require an Amazon CloudFront distribution to have origin failover configured](#ct-cloudfront-pr-4-description)
+ [\[CT\.CLOUDFRONT\.PR\.5\] Require any Amazon CloudFront distribution to have logging enabled](#ct-cloudfront-pr-5-description)
+ [\[CT\.CLOUDFRONT\.PR\.6\] Require an Amazon CloudFront distribution to use custom SSL/TLS certificates](#ct-cloudfront-pr-6-description)
+ [\[CT\.CLOUDFRONT\.PR\.7\] Require an Amazon CloudFront distribution to use SNI to serve HTTPS requests](#ct-cloudfront-pr-7-description)
+ [\[CT\.CLOUDFRONT\.PR\.8\] Require an Amazon CloudFront distribution to encrypt traffic to custom origins](#ct-cloudfront-pr-8-description)
+ [\[CT\.CLOUDFRONT\.PR\.9\] Require an Amazon CloudFront distribution to have a security policy of TLSv1\.2 as a minimum](#ct-cloudfront-pr-9-description)
+ [\[CT\.CLOUDFRONT\.PR\.10\] Require any Amazon CloudFrontdistributions with Amazon S3 backed origins to have origin access control configured](#ct-cloudfront-pr-10-description)
+ [\[CT\.CLOUDFRONT\.PR\.11\] Require an Amazon CloudFront distribution to use updated SSL protocols between edge locations and custom origins](#ct-cloudfront-pr-11-description)

## \[CT\.CLOUDFRONT\.PR\.1\] Require an Amazon CloudFront distribution to have a default root object configured<a name="ct-cloudfront-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether an Amazon CloudFront distribution is configured to return a specific object that is the default root object\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.1 rule specification](#ct-cloudfront-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.1 rule specification](#ct-cloudfront-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.1 example templates](#ct-cloudfront-pr-1-templates) 

**Explanation**

A user could possibly request a distribution's root URL instead of an object in the distribution\. In this situation, specifying a default root object can help you to avoid exposing the contents of your web distribution\.

### Remediation for rule failure<a name="ct-cloudfront-pr-1-remediation"></a>

Specify a default root object in the `DefaultRootObject` property\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example<a name="ct-cloudfront-pr-1-remediation-1"></a>

Amazon CloudFront distribution configured with a default root object\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "DefaultRootObject": "index.html"
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      DefaultRootObject: index.html
```

### CT\.CLOUDFRONT\.PR\.1 rule specification<a name="ct-cloudfront-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_default_root_object_configured_check
# 
# Description:
#   This control checks whether an Amazon CloudFront distribution is configured to return a specific object that is the default root object.
# 
# Reports on:
#    AWS::CloudFront::Distribution
# 
# Evaluates:
#    AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DefaultRootObject' is not present on the CloudFront distribution resource or is present and
#            is an empty string
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DefaultRootObject' is present on the CloudFront distribution resource and is a non-empty string
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_default_root_object_configured_check when is_cfn_template(%INPUT_DOCUMENT)
                                                          %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.1]: Require an Amazon CloudFront distribution to have a default root object configured
            [FIX]: Specify a default root object in the 'DefaultRootObject' property.
        >>
}

rule cloudfront_default_root_object_configured_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.1]: Require an Amazon CloudFront distribution to have a default root object configured
            [FIX]: Specify a default root object in the 'DefaultRootObject' property.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            # Scenario 2
            DefaultRootObject exists
            # Scenario 3
            check_is_string_and_not_empty(DefaultRootObject)
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

### CT\.CLOUDFRONT\.PR\.1 example templates<a name="ct-cloudfront-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
         ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        DefaultRootObject: index.html
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
```

## \[CT\.CLOUDFRONT\.PR\.2\] Require any Amazon CloudFront distributions with Amazon S3 backed origins to have an origin access identity configured<a name="ct-cloudfront-pr-2-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether Amazon CloudFront distributions backed by Amazon S3 are configured with an origin access identity\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: **[CT\.CLOUDFRONT\.PR\.2 rule specification](#ct-cloudfront-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see: [CT\.CLOUDFRONT\.PR\.2 rule specification](#ct-cloudfront-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.2 example templates](#ct-cloudfront-pr-2-templates) 

**Explanation**

CloudFront OAI prevents users from gaining direct access to Amazon S3 bucket content\. With direct access to an Amazon S3 bucket, a user bypasses the CloudFront distribution and any permissions that are applied to the underlying S3 bucket content\.

**Usage considerations**  
This control applies only to Amazon CloudFront distributions that are configured with one or more origins that are backed by Amazon S3\.

### Remediation for rule failure<a name="ct-cloudfront-pr-2-remediation"></a>

Configure Amazon S3 backed origins by means of the `Origins` property\. For each origin backed by Amazon S3, configure an origin access identity by means of the `OriginAccessIdentity` property within an `S3OriginConfig` configuration\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example<a name="ct-cloudfront-pr-2-remediation-1"></a>

Amazon CloudFront distribution with an Amazon S3 bucket origin and origin access identity\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleS3Origin",
                        "DomainName": {
                            "Fn::GetAtt": [
                                "OriginBucket",
                                "RegionalDomainName"
                            ]
                        },
                        "S3OriginConfig": {
                            "OriginAccessIdentity": {
                                "Fn::Join": [
                                    "",
                                    [
                                        "origin-access-identity/cloudfront/",
                                        {
                                            "Ref": "OriginBucketOai"
                                        }
                                    ]
                                ]
                            }
                        }
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleS3Origin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                }
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleS3Origin
          DomainName: !GetAtt 'OriginBucket.RegionalDomainName'
          S3OriginConfig:
            OriginAccessIdentity: !Join
              - ''
              - - origin-access-identity/cloudfront/
                - !Ref 'OriginBucketOai'
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleS3Origin
        CachePolicyId: !Ref 'CachePolicy'
```

### CT\.CLOUDFRONT\.PR\.2 rule specification<a name="ct-cloudfront-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_origin_access_identity_enabled_check
# 
# Description:
#   This control checks whether Amazon CloudFront distributions backed by Amazon S3 are configured with an origin access identity.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: No S3 backed 'Origins' are provided on the CloudFront distribution resource or 'Origins' is not present on
#            the CloudFront distribution resource or is present and an empty list
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'S3Origin' is present on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more S3 backed 'Origins' are configured on the CloudFront distribution resource
#       And: 'OriginAccessIdentity' is not present or is an empty string in the 'S3OriginConfig' property or invalid
#            local reference
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more S3 backed 'Origins' are provided on the CloudFront distribution resource
#       And: 'S3OriginConfig' is present with an 'OriginAccessIdentity for each S3 backed 'Origin' on the
#            CloudFront distribution resource that is a non-empty string or valid local reference
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let S3_BUCKET_DNS_NAME_PATTERN = /(.*)\.s3(-external-\d|[-\.][a-z]*-[a-z]*-[0-9])?\.amazonaws\.com(\.cn)?$/
let INPUT_DOCUMENT = this
#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_origin_access_identity_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                          %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.2]: Require any Amazon CloudFront distributions with Amazon S3 backed origins to have an origin access identity configured
            [FIX]: Configure Amazon S3 backed origins by means of the 'Origins' property. For each origin backed by Amazon S3, configure an origin access identity by means of the 'OriginAccessIdentity' property within an 'S3OriginConfig' configuration.
        >>
}

rule cloudfront_origin_access_identity_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.2]: Require any Amazon CloudFront distributions with Amazon S3 backed origins to have an origin access identity configured
            [FIX]: Configure Amazon S3 backed origins by means of the 'Origins' property. For each origin backed by Amazon S3, configure an origin access identity by means of the 'OriginAccessIdentity' property within an 'S3OriginConfig' configuration.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution[
        filter_cloudfront_distribution_with_legacy_s3_origins(this)
    ] {
        DistributionConfig {
            # Scenario 3
            S3Origin not exists
        }
    }

    %cloudfront_distribution[
        # Scenario 2
        filter_cloudfront_distribution_with_origins(this)
    ] {
        DistributionConfig {
            # Scenario 4
            Origins [
                DomainName == %S3_BUCKET_DNS_NAME_PATTERN or
                check_origin_domain_name_get_att(DomainName)
            ] {
                S3OriginConfig exists
                S3OriginConfig is_struct
                S3OriginConfig {
                    # Scenario 3 and 5
                    OriginAccessIdentity exists
                    check_is_string_and_not_empty(OriginAccessIdentity) or
                    check_local_oai(OriginAccessIdentity)
                }
            }
        }
    }
}

rule check_origin_domain_name_get_att(domain) {
  %domain {
    'Fn::GetAtt' {
        this is_list
        this not empty
        this[1] == "DomainName" or
        this[1] == "RegionalDomainName"
    }
    check_local_references(%INPUT_DOCUMENT, this, "AWS::S3::Bucket")
  }
}

rule check_local_oai(oai) {
    %oai {
        'Fn::Join' {
            this[1] exists
            this[1] is_list
            this[1] not empty
            some this[1].* {
                check_local_references(%INPUT_DOCUMENT, this, "AWS::CloudFront::CloudFrontOriginAccessIdentity")
            }
        } or
        'Fn::Sub' {
            when this is_list {
                this[1] exists
                this[1] is_struct
                some this[1].* {
                   check_local_references(%INPUT_DOCUMENT, this, "AWS::CloudFront::CloudFrontOriginAccessIdentity")
                }
            }
            when this is_string {
                check_is_string_and_not_empty(this)
            }
        }
    }
}

rule filter_cloudfront_distribution_with_origins(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            Origins exists
            Origins is_list
            Origins not empty
        }
    }
}

rule filter_cloudfront_distribution_with_legacy_s3_origins(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            S3Origin exists
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

### CT\.CLOUDFRONT\.PR\.2 example templates<a name="ct-cloudfront-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  OriginBucketOai:
    Type: AWS::CloudFront::CloudFrontOriginAccessIdentity
    Properties:
      CloudFrontOriginAccessIdentityConfig:
        Comment:
          Fn::Sub: ${AWS::StackName}-example-oai
  OriginBucket:
    Type: AWS::S3::Bucket
  OriginBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: OriginBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetObject'
            Effect: Allow
            Resource:
              Fn::Join:
              - ''
              - - 'arn:aws:s3:::'
                - Ref: OriginBucket
                - /*
            Principal:
              AWS:
                Fn::Join:
                - ''
                - - 'arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity '
                  - Ref: OriginBucketOai
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleS3Origin
          DomainName:
            Fn::GetAtt:
            - OriginBucket
            - RegionalDomainName
          S3OriginConfig:
            OriginAccessIdentity:
              Fn::Join:
              - ""
              - - "origin-access-identity/cloudfront/"
                - Ref: OriginBucketOai
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleS3Origin
          CachePolicyId:
            Ref: CachePolicy
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleS3Origin
          DomainName: examplebucket.s3.amazonaws.com
          S3OriginConfig: {}
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleS3Origin
          CachePolicyId:
            Ref: CachePolicy
```

## \[CT\.CLOUDFRONT\.PR\.3\] Require an Amazon CloudFront distribution to have encryption in transit configured<a name="ct-cloudfront-pr-3-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon CloudFront distributions use HTTPS, either directly or through a redirection\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.3 rule specification](#ct-cloudfront-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.3 rule specification](#ct-cloudfront-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.3 example templates](#ct-cloudfront-pr-3-templates) 

**Explanation**

HTTPS \(TLS\) can help prevent potential attackers from attempting person\-in\-the\-middle or similar attacks, which can eavesdrop on or manipulate network traffic\. Only encrypted connections over HTTPS \(TLS\) should be allowed\. Encrypting data in transit can affect performance\. We recommend that you test your application with this feature to understand the performance profile and the impact of TLS\.

### Remediation for rule failure<a name="ct-cloudfront-pr-3-remediation"></a>

Set `ViewerProtocolPolicy` in `DefaultCacheBehavior` and `CacheBehavior` to `https-only` or `redirect-to-https`\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example One<a name="ct-cloudfront-pr-3-remediation-1"></a>

Amazon CloudFront distribution configured with a default cache behavior that requires viewer connections to use HTTPS\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                }
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
```

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example Two<a name="ct-cloudfront-pr-3-remediation-2"></a>

Amazon CloudFront distribution configured with a cache behavior that redirects viewer HTTP connections to HTTPS\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "CacheBehaviors": [
                    {
                        "ViewerProtocolPolicy": "redirect-to-https",
                        "TargetOriginId": "sampleOrigin",
                        "PathPattern": "*"
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      CacheBehaviors:
        - ViewerProtocolPolicy: redirect-to-https
          TargetOriginId: sampleOrigin
          PathPattern: '*'
```

### CT\.CLOUDFRONT\.PR\.3 rule specification<a name="ct-cloudfront-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_viewer_policy_https_check
# 
# Description:
#   This control checks whether your Amazon CloudFront distributions use HTTPS, either directly or through a redirection.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.DefaultCacheBehavior' is missing on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.DefaultCacheBehavior' is present on the CloudFront distribution resource
#       And: 'ViewerProtocolPolicy' in 'DefaultCacheBehavior' is missing or set to a value other than 'https-only' or
#            'redirect-to-https' (e.g. 'allow-all')
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.CacheBehavior' is provided on the CloudFront distribution resource
#       And: 'ViewerProtocolPolicy' in the 'CacheBehavior' is  is missing or set to a value other than 'https-only' or
#            'redirect-to-https' (e.g. 'allow-all')
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.DefaultCacheBehavior' is present on the CloudFront distribution resource
#       And: 'ViewerProtocolPolicy' in 'DefaultCacheBehavior' is set to 'https-only' or 'redirect-to-https'
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.CacheBehavior' are provided on the CloudFront distribution resource as a non-empty list
#       And: 'ViewerProtocolPolicy' in the 'CacheBehavior' is set to 'https-only' or 'redirect-to-https'
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let ALLOWED_VIEWER_PROTOCOL_POLICIES = [ "https-only", "redirect-to-https" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_viewer_policy_https_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.3]: Require an Amazon CloudFront distribution to have encryption in transit configured
            [FIX]: Set 'ViewerProtocolPolicy' in 'DefaultCacheBehavior' and 'CacheBehavior' to 'https-only' or 'redirect-to-https'.
        >>
}

rule cloudfront_viewer_policy_https_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.3]: Require an Amazon CloudFront distribution to have encryption in transit configured
            [FIX]: Set 'ViewerProtocolPolicy' in 'DefaultCacheBehavior' and 'CacheBehavior' to 'https-only' or 'redirect-to-https'.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            DefaultCacheBehavior exists
            DefaultCacheBehavior is_struct

            DefaultCacheBehavior {
                # Scenarios 2 and 4
                check_viewer_protocol_policy(this)
            }

            when CacheBehaviors exists
                 CacheBehaviors is_list
                 CacheBehaviors not empty {

                    CacheBehaviors[*] {
                        # Scenarios 3 and 5
                        check_viewer_protocol_policy(this)
                    }
            }
        }
    }
}

rule check_viewer_protocol_policy(cache_behaviour) {
    %cache_behaviour {
        ViewerProtocolPolicy exists
        ViewerProtocolPolicy in %ALLOWED_VIEWER_PROTOCOL_POLICIES
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

### CT\.CLOUDFRONT\.PR\.3 example templates<a name="ct-cloudfront-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: allow-all
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
```

## \[CT\.CLOUDFRONT\.PR\.4\] Require an Amazon CloudFront distribution to have origin failover configured<a name="ct-cloudfront-pr-4-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon CloudFront distribution is configured with an origin group that contains two origin group members\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.4 rule specification](#ct-cloudfront-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.4 rule specification](#ct-cloudfront-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.4 example templates](#ct-cloudfront-pr-4-templates) 

**Explanation**

CloudFront origin failover can increase availability\. Origin failover automatically redirects traffic to a secondary origin if the primary origin is unavailable or if it returns specific HTTP response status codes\.

### Remediation for rule failure<a name="ct-cloudfront-pr-4-remediation"></a>

Configure an origin group on the Amazon CloudFront Distribution with two origin group members\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example One<a name="ct-cloudfront-pr-4-remediation-1"></a>

Amazon CloudFront distribution configured with an origin group that contains two origin group members\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "one.example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    },
                    {
                        "Id": "sampleOrigin2",
                        "DomainName": "two.example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "OriginGroups": {
                    "Quantity": 1,
                    "Items": [
                        {
                            "Id": "ExampleOriginGroup",
                            "FailoverCriteria": {
                                "StatusCodes": {
                                    "Items": [
                                        400
                                    ],
                                    "Quantity": 1
                                }
                            },
                            "Members": {
                                "Quantity": 2,
                                "Items": [
                                    {
                                        "OriginId": "sampleOrigin"
                                    },
                                    {
                                        "OriginId": "sampleOrigin2"
                                    }
                                ]
                            }
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
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleOrigin
          DomainName: one.example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        - Id: sampleOrigin2
          DomainName: two.example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      OriginGroups:
        Quantity: 1
        Items:
          - Id: ExampleOriginGroup
            FailoverCriteria:
              StatusCodes:
                Items:
                  - 400
                Quantity: 1
            Members:
              Quantity: 2
              Items:
                - OriginId: sampleOrigin
                - OriginId: sampleOrigin2
```

### CT\.CLOUDFRONT\.PR\.4 rule specification<a name="ct-cloudfront-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_origin_failover_enabled_check
# 
# Description:
#   This control checks whether your Amazon CloudFront distribution is configured with an origin group that contains two origin group members.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'OriginGroups' is not present on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'OriginGroups' is present on the CloudFront distribution resource
#       And: 'Quantity' within 'OriginGroups' is 0
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'OriginGroups' is present on the CloudFront distribution resource
#       And: 'Quantity' within 'OriginGroups' is >= 1
#       And: 'Quantity' within 'Members' is < 2
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'OriginGroups' is present on the CloudFront distribution resource
#       And: 'Quantity' within 'OriginGroups' is >= 1
#       And: 'Quantity' within 'Members' is == 2
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_origin_failover_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                   %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.4]: Require an Amazon CloudFront distribution to have origin failover configured
            [FIX]: Configure an origin group on the Amazon CloudFront Distribution with two origin group members.
        >>
}

rule cloudfront_origin_failover_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.4]: Require an Amazon CloudFront distribution to have origin failover configured
            [FIX]: Configure an origin group on the Amazon CloudFront Distribution with two origin group members.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            # Scenario 2
            OriginGroups exists
            OriginGroups is_struct

            OriginGroups {
                # Scenario 3
                Quantity exists
                Quantity >= 1

                Items exists
                Items is_list
                Items not empty

                Items[*] {
                    Members exists
                    Members is_struct
                    Members {
                        # Scenarios 4 and 5
                        Quantity == 2
                    }
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
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.CLOUDFRONT\.PR\.4 example templates<a name="ct-cloudfront-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: one.example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        - Id: exampleOrigin2
          DomainName: two.example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        OriginGroups:
          Quantity: 1
          Items:
          - Id: ExampleOriginGroup
            FailoverCriteria:
              StatusCodes:
                Items:
                - 400
                Quantity: 1
            Members:
              Quantity: 2
              Items:
              - OriginId: exampleOrigin
              - OriginId: exampleOrigin2
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
```

## \[CT\.CLOUDFRONT\.PR\.5\] Require any Amazon CloudFront distribution to have logging enabled<a name="ct-cloudfront-pr-5-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether Amazon CloudFront distributions are configured with access logging\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.5 rule specification](#ct-cloudfront-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.5 rule specification](#ct-cloudfront-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.5 example templates](#ct-cloudfront-pr-5-templates) 

**Explanation**

CloudFront access logs provide detailed information about every user request that CloudFront receives\. Each log contains information such as the date and time the request was received, the IP address of the viewer that made the request, the source of the request, and the port number of the request from the viewer\.

These access logs are useful for applications such as security and access audits, and in forensic investigation\.

### Remediation for rule failure<a name="ct-cloudfront-pr-5-remediation"></a>

Set `Bucket` in `DistributionConfig.Logging` to an Amazon S3 bucket that has been configured to receive Amazon CloudFront distribution access logs\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example<a name="ct-cloudfront-pr-5-remediation-1"></a>

Amazon CloudFront distribution configured with access logging enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "Logging": {
                    "Bucket": {
                        "Fn::GetAtt": [
                            "LoggingBucket",
                            "RegionalDomainName"
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
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      Logging:
        Bucket: !GetAtt 'LoggingBucket.RegionalDomainName'
```

### CT\.CLOUDFRONT\.PR\.5 rule specification<a name="ct-cloudfront-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_access_logs_enabled_check
# 
# Description:
#   This control checks whether Amazon CloudFront distributions are configured with access logging.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.Logging.Bucket' configuration is not present on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.Logging' configuration is present on the CloudFront distribution resource
#       And: 'Bucket' has been provided in the 'DistributionConfig.Logging' configuration with with an empty string or
#            invalid local reference
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.Logging' configuration is present on the CloudFront distribution resource
#       And: A 'Bucket' property has been provided within the 'DistributionConfig.Logging' configuration with a
#            non-empty string or valid local stack reference
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let S3_BUCKET_TYPE = "AWS::S3::Bucket"
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_access_logs_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.5]: Require any Amazon CloudFront distribution to have logging enabled
            [FIX]: Set 'Bucket' in 'DistributionConfig.Logging' to an Amazon S3 bucket that has been configured to receive Amazon CloudFront distribution access logs.
        >>
}

rule cloudfront_access_logs_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.5]: Require any Amazon CloudFront distribution to have logging enabled
            [FIX]: Set 'Bucket' in 'DistributionConfig.Logging' to an Amazon S3 bucket that has been configured to receive Amazon CloudFront distribution access logs.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            Logging exists
            Logging is_struct
            Logging {
                # Scenario 2
                Bucket exists

                # Scenarios 3 and 4
                check_is_string_and_not_empty(Bucket) or
                check_local_references(%INPUT_DOCUMENT, Bucket, %S3_BUCKET_TYPE)
            }
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

### CT\.CLOUDFRONT\.PR\.5 example templates<a name="ct-cloudfront-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  LoggingBucket:
    Type: AWS::S3::Bucket
    Properties: {}
  LoggingBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: LoggingBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetBucketAcl'
              - 's3:PutBucketAcl'
            Effect: Allow
            Resource:
              Fn::Join:
              - ''
              - - 'arn:aws:s3:::'
                - Ref: LoggingBucket
            Principal:
              AWS:
                Ref: AWS::AccountId
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        Logging:
          Bucket:
            Fn::GetAtt:
            - LoggingBucket
            - RegionalDomainName
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
```

## \[CT\.CLOUDFRONT\.PR\.6\] Require an Amazon CloudFront distribution to use custom SSL/TLS certificates<a name="ct-cloudfront-pr-6-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether the certificate associated with an Amazon CloudFront distribution is a custom SSL/TLS certificate\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.6 rule specification](#ct-cloudfront-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.6 rule specification](#ct-cloudfront-pr-6-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.6 example templates](#ct-cloudfront-pr-6-templates) 

**Explanation**

Custom SSL/TLS certificates give your users access to content by using alternate domain names\. You can store custom certificates in AWS Certificate Manager \(recommended\), or in IAM\.

**Usage considerations**  
This control requires a viewer certificate configuration compatible only with Amazon CloudFront distributions that use `Aliases`, also known as alternate domain names or CNAMEs\.

### Remediation for rule failure<a name="ct-cloudfront-pr-6-remediation"></a>

Provide a `ViewerCertificate` configuration with values for `AcmCertificateArn`, `MinimumProtocolVersion`, and `SslSupportMethod`\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example<a name="ct-cloudfront-pr-6-remediation-1"></a>

Amazon CloudFront distribution configured with an AWS Certificate Manager SSL certificate\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "ViewerCertificate": {
                    "AcmCertificateArn": {
                        "Ref": "ACMCertificate"
                    },
                    "MinimumProtocolVersion": "TLSv1.2_2021",
                    "SslSupportMethod": "sni-only"
                }
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      ViewerCertificate:
        AcmCertificateArn: !Ref 'ACMCertificate'
        MinimumProtocolVersion: TLSv1.2_2021
        SslSupportMethod: sni-only
```

### CT\.CLOUDFRONT\.PR\.6 rule specification<a name="ct-cloudfront-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_custom_ssl_certificate_check
# 
# Description:
#   This control checks whether the certificate associated with an Amazon CloudFront distribution is a custom SSL/TLS certificate.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'ViewerCertificate' is not present on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'CloudFrontDefaultCertificate' is set to bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'ViewerCertificate' is present on the CloudFront distribution resource
#       And: One of 'AcmCertificateArn' or 'IamCertificateId' are not provided or provided as empty strings or invalid
#            local references
#       And: One of 'MinimumProtocolVersion' and 'SslSupportMethod' is not provided or provided as an empty string
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'AcmCertificateArn' or 'IamCertificateId' are provided in the 'ViewerCertificate' configuration as
#             non-empty strings or 'AcmCertificateArn' is a valid local reference
#       And: 'MinimumProtocolVersion' and 'SslSupportMethod' are provided as non-empty strings
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_custom_ssl_certificate_check when is_cfn_template(%INPUT_DOCUMENT)
                                                  %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.6]: Require an Amazon CloudFront distribution to use custom SSL/TLS certificates
            [FIX]: Provide a 'ViewerCertificate' configuration with values for 'AcmCertificateArn', 'MinimumProtocolVersion', and 'SslSupportMethod'.
        >>
}

rule cloudfront_custom_ssl_certificate_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.6]: Require an Amazon CloudFront distribution to use custom SSL/TLS certificates
            [FIX]: Provide a 'ViewerCertificate' configuration with values for 'AcmCertificateArn', 'MinimumProtocolVersion', and 'SslSupportMethod'.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            ViewerCertificate exists
            ViewerCertificate is_struct

            ViewerCertificate {
                CloudFrontDefaultCertificate not exists or
                CloudFrontDefaultCertificate == false

                check_custom_acm_certificate_provided(AcmCertificateArn, "AWS::CertificateManager::Certificate") or
                check_custom_iam_certificate_provided(IamCertificateId)

                MinimumProtocolVersion exists
                check_is_string_and_not_empty(MinimumProtocolVersion)

                SslSupportMethod exists
                check_is_string_and_not_empty(SslSupportMethod)
            }
        }
    }
}

rule check_custom_acm_certificate_provided(certificate, cfn_type) {
    %certificate {
        this exists
        check_is_string_and_not_empty(this) or
        check_local_references(%INPUT_DOCUMENT, this, %cfn_type)
    }
}

rule check_custom_iam_certificate_provided(certificate) {
    %certificate {
        this exists
        check_is_string_and_not_empty(this)
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

### CT\.CLOUDFRONT\.PR\.6 example templates<a name="ct-cloudfront-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        ViewerCertificate:
          AcmCertificateArn:
            Ref: ACMCertificate
          MinimumProtocolVersion: TLSv1.2_2021
          SslSupportMethod: sni-only
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        ViewerCertificate:
          CloudFrontDefaultCertificate: true
```

## \[CT\.CLOUDFRONT\.PR\.7\] Require an Amazon CloudFront distribution to use SNI to serve HTTPS requests<a name="ct-cloudfront-pr-7-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon CloudFront distributions are configured to use SNI to serve HTTPS requests\.
+ **Control objective: **Encrypt data in transit, Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.7 rule specification](#ct-cloudfront-pr-7-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.7 rule specification](#ct-cloudfront-pr-7-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.7 example templates](#ct-cloudfront-pr-7-templates) 

**Explanation**

Server Name Indication \(SNI\) is an extension to the TLS protocol\. It is supported by browsers and clients released after 2010\. If you configure CloudFront to serve HTTPS requests using SNI, CloudFront associates your alternate domain name with an IP address for each edge location\. When a viewer submits an HTTPS request for your content, DNS routes the request to the IP address for the correct edge location\. The IP address for your domain name is determined during the SSL/TLS handshake negotiation; the IP address isn't dedicated to your distribution\.

**Usage considerations**  
This control requires a viewer certificate configuration which is only compatible with Amazon CloudFront distributions that use `Aliases` \(also known as alternate domain names or CNAMEs\)

### Remediation for rule failure<a name="ct-cloudfront-pr-7-remediation"></a>

Within `ViewerCertificate`, set `SslSupportMethod` to `sni-only`, `MinimumProtocolVersion` to a protocol that supports SNI \(`TLSv1` or greater\), and `AcmCertificateArn` to the ARN of an AWS ACM certificate\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example<a name="ct-cloudfront-pr-7-remediation-1"></a>

Amazon CloudFront distribution configured to use SNI to serve HTTPS requests\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "ViewerCertificate": {
                    "AcmCertificateArn": {
                        "Ref": "ACMCertificate"
                    },
                    "MinimumProtocolVersion": "TLSv1.2_2021",
                    "SslSupportMethod": "sni-only"
                }
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      ViewerCertificate:
        AcmCertificateArn: !Ref 'ACMCertificate'
        MinimumProtocolVersion: TLSv1.2_2021
        SslSupportMethod: sni-only
```

### CT\.CLOUDFRONT\.PR\.7 rule specification<a name="ct-cloudfront-pr-7-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_sni_enabled_check
# 
# Description:
#   This control checks whether your Amazon CloudFront distributions are configured to use SNI to serve HTTPS requests.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'ViewerCertificate' is not present on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'CloudFrontDefaultCertificate' is set to bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'AcmCertificateArn' or 'IamCertificateId' are provided in the 'ViewerCertificate' configuration
#       And: 'MinimumProtocolVersion' is provided in the 'ViewerCertificate' configuration with a protocol that does not
#            support SNI (SSLv3)
#       Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'AcmCertificateArn' or 'IamCertificateId' are provided in the 'ViewerCertificate' configuration
#       And: 'MinimumProtocolVersion' is provided in the 'ViewerCertificate' configuration with a protocol that supports
#            SNI (TLSv1 or greater)
#       And: 'SslSupportMethod' is set to 'vip'
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'AcmCertificateArn' or 'IamCertificateId' are provided in the 'ViewerCertificate' configuration
#       And: 'MinimumProtocolVersion' is provided in the 'ViewerCertificate' configuration with a protocol that supports
#            SNI (TLSv1 or greater)
#       And: 'SslSupportMethod' is set to 'sni-only'
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let UNSUPPORTED_PROTOCOLS_FOR_SNI = [ "SSLv3" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_sni_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                       %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.7]: Require an Amazon CloudFront distribution to use SNI to serve HTTPS requests
            [FIX]: Within 'ViewerCertificate', set 'SslSupportMethod' to 'sni-only', 'MinimumProtocolVersion' to a protocol that supports SNI ('TLSv1' or greater), and 'AcmCertificateArn' to the ARN of an AWS ACM certificate.
        >>
}

rule cloudfront_sni_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.7]: Require an Amazon CloudFront distribution to use SNI to serve HTTPS requests
            [FIX]: Within 'ViewerCertificate', set 'SslSupportMethod' to 'sni-only', 'MinimumProtocolVersion' to a protocol that supports SNI ('TLSv1' or greater), and 'AcmCertificateArn' to the ARN of an AWS ACM certificate.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            ViewerCertificate exists
            ViewerCertificate is_struct

            ViewerCertificate {
                CloudFrontDefaultCertificate not exists or
                CloudFrontDefaultCertificate == false

                check_custom_acm_certificate_provided(AcmCertificateArn, "AWS::CertificateManager::Certificate") or
                check_custom_iam_certificate_provided(IamCertificateId)

                MinimumProtocolVersion exists
                MinimumProtocolVersion not in %UNSUPPORTED_PROTOCOLS_FOR_SNI

                SslSupportMethod exists
                SslSupportMethod == "sni-only"
            }
        }
    }
}

rule check_custom_acm_certificate_provided(certificate, cfn_type) {
    %certificate {
        this exists
        check_is_string_and_not_empty(this) or
        check_local_references(%INPUT_DOCUMENT, this, %cfn_type)
    }
}

rule check_custom_iam_certificate_provided(certificate) {
    %certificate {
        this exists
        check_is_string_and_not_empty(this)
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

### CT\.CLOUDFRONT\.PR\.7 example templates<a name="ct-cloudfront-pr-7-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        ViewerCertificate:
          AcmCertificateArn:
            Ref: ACMCertificate
          MinimumProtocolVersion: TLSv1.2_2021
          SslSupportMethod: sni-only
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        ViewerCertificate:
          AcmCertificateArn:
            Ref: ACMCertificate
          MinimumProtocolVersion: TLSv1
          SslSupportMethod: vip
```

## \[CT\.CLOUDFRONT\.PR\.8\] Require an Amazon CloudFront distribution to encrypt traffic to custom origins<a name="ct-cloudfront-pr-8-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon CloudFront distributions are encrypting traffic to custom origins\.
+ **Control objective: **Encrypt data in transit
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.8 rule specification](#ct-cloudfront-pr-8-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.8 rule specification](#ct-cloudfront-pr-8-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.8 example templates](#ct-cloudfront-pr-8-templates) 

**Explanation**

HTTPS \(TLS\) can help prevent eavesdropping or manipulation of network traffic\. Only encrypted connections over HTTPS \(TLS\) should be allowed\.

**Usage considerations**  
This control applies only to Amazon CloudFront distributions that have one or more origins configured\.

### Remediation for rule failure<a name="ct-cloudfront-pr-8-remediation"></a>

For Amazon CloudFront custom origins, set `OriginProtocolPolicy` to `https-only` or match\-viewer`. When setting `OriginProtocolPolicy` to `match\-viewer`, do not set `ViewerProtocolPolicy` to `allow\-all' for any cache behaviors\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example One<a name="ct-cloudfront-pr-8-remediation-1"></a>

Amazon CloudFront distribution configured to require HTTPS connections to custom origins, by means of an origin protocol policy of `https-only`\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "CacheBehaviors": [
                    {
                        "ViewerProtocolPolicy": "https-only",
                        "TargetOriginId": "sampleOrigin",
                        "PathPattern": "*",
                        "CachePolicyId": {
                            "Ref": "CachePolicy"
                        }
                    }
                ],
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      CacheBehaviors:
        - ViewerProtocolPolicy: https-only
          TargetOriginId: sampleOrigin
          PathPattern: '*'
          CachePolicyId: !Ref 'CachePolicy'
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
```

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example Two<a name="ct-cloudfront-pr-8-remediation-2"></a>

Amazon CloudFront distribution configured to require HTTPS connections to custom origins, by means of an origin protocol policy of `match-viewer`\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "CacheBehaviors": [
                    {
                        "ViewerProtocolPolicy": "https-only",
                        "TargetOriginId": "sampleOrigin",
                        "PathPattern": "*",
                        "CachePolicyId": {
                            "Ref": "CachePolicy"
                        }
                    }
                ],
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "match-viewer"
                        }
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      CacheBehaviors:
        - ViewerProtocolPolicy: https-only
          TargetOriginId: sampleOrigin
          PathPattern: '*'
          CachePolicyId: !Ref 'CachePolicy'
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: match-viewer
```

### CT\.CLOUDFRONT\.PR\.8 rule specification<a name="ct-cloudfront-pr-8-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_traffic_to_origin_encrypted_check
# 
# Description:
#   This control checks whether your Amazon CloudFront distributions are encrypting traffic to custom origins.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIPs
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'Origins' is not present or is an empty list
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more 'Origins' has been configured
#       And: There are no 'Origins' with a 'CustomOriginConfig'
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'CustomOrigin' is present on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more 'Origins' has been configured
#       And: There one or more 'Origins' with a 'CustomOriginConfig'
#       And: At least one 'Origins' with a 'CustomOriginConfig' has an 'OriginProtocolPolicy' of 'http-only'
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more 'Origins' has been configured
#       And: There one or more 'Origins' with a 'CustomOriginConfig'
#       And: At least one 'Origins' with a 'CustomOriginConfig' has an 'OriginProtocolPolicy' of 'match-viewer'
#       And: Any 'ViewerProtocolPolicy' is set to 'allow-all' for 'DefaultCacheBehavior' or any configured
#            'CacheBehaviors'
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more 'Origins' has been configured
#       And: There one or more 'Origins' with a 'CustomOriginConfig'
#       And: All 'Origins' with a 'CustomOriginConfig' have an 'OriginProtocolPolicy' of 'https-only'
#      Then: PASS
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront Distribution resource
#       And: One or more 'Origins' has been configured
#       And: There one or more 'Origins' with a 'CustomOriginConfig'
#       And: At least one 'Origins' with a 'CustomOriginConfig' has an 'OriginProtocolPolicy' of 'match-viewer'
#       And: 'ViewerProtocolPolicy' is not set to 'allow-all' for both 'DefaultCacheBehavior' and any configured
#            'CacheBehaviors'
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_traffic_to_origin_encrypted_check when is_cfn_template(%INPUT_DOCUMENT)
                                                       %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.8]: Require an Amazon CloudFront distribution to encrypt traffic to custom origins
            [FIX]: For Amazon CloudFront custom origins, set 'OriginProtocolPolicy' to 'https-only' or match-viewer'. When setting 'OriginProtocolPolicy' to 'match-viewer', do not set 'ViewerProtocolPolicy' to 'allow-all' for any cache behaviors.
        >>
}

rule cloudfront_traffic_to_origin_encrypted_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.8]: Require an Amazon CloudFront distribution to encrypt traffic to custom origins
            [FIX]: For Amazon CloudFront custom origins, set 'OriginProtocolPolicy' to 'https-only' or match-viewer'. When setting 'OriginProtocolPolicy' to 'match-viewer', do not set 'ViewerProtocolPolicy' to 'allow-all' for any cache behaviors.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution[
        filter_cloudfront_distribution_with_legacy_origins(this)
    ] {
        DistributionConfig {
            # Scenario 4
            CustomOrigin not exists
        }
    }

    %cloudfront_distribution [
        # Scenario 2
        filter_cloudfront_distribution_with_origins(this)
    ] {
        let cloudfront_distro = this

        DistributionConfig {
            Origins [
                # Scenario 3
                CustomOriginConfig exists
                CustomOriginConfig is_struct
            ] {
                CustomOriginConfig {
                    # Scenario 5
                    OriginProtocolPolicy != "http-only"
                    # Scenario 6
                    OriginProtocolPolicy == "https-only" or
                    # Scenario 6 and 8
                    match_viewer_policy_with_no_allow_all_viewer_protocol_policy(OriginProtocolPolicy, %cloudfront_distro)
                }
            }
        }
    }
}

rule match_viewer_policy_with_no_allow_all_viewer_protocol_policy(origin_protocol_policy, cloudfront_distribution) {
    %origin_protocol_policy {
        this == "match-viewer"

        %cloudfront_distribution {
            DistributionConfig {
                DefaultCacheBehavior exists
                DefaultCacheBehavior is_struct

                DefaultCacheBehavior {
                    check_viewer_protocol_policy(this)
                }

                when CacheBehaviors exists
                     CacheBehaviors is_list
                     CacheBehaviors not empty {

                        CacheBehaviors[*] {
                            check_viewer_protocol_policy(this)
                        }
                }
            }
        }
    }
}

rule check_viewer_protocol_policy(cache_behaviour) {
    %cache_behaviour {
        ViewerProtocolPolicy exists
        ViewerProtocolPolicy != "allow-all"
    }
}

rule filter_cloudfront_distribution_with_origins(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            Origins exists
            Origins is_list
            Origins not empty
        }
    }
}

rule filter_cloudfront_distribution_with_legacy_origins(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            CustomOrigin exists
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

### CT\.CLOUDFRONT\.PR\.8 example templates<a name="ct-cloudfront-pr-8-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        CacheBehaviors:
        - ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          PathPattern: '*'
          CachePolicyId:
            Ref: CachePolicy
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        DefaultCacheBehavior:
          ViewerProtocolPolicy: allow-all
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: match-viewer
```

## \[CT\.CLOUDFRONT\.PR\.9\] Require an Amazon CloudFront distribution to have a security policy of TLSv1\.2 as a minimum<a name="ct-cloudfront-pr-9-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon CloudFront distributions are using a minimum security policy and cipher suite of TLSv1\.2 or greater for viewer connections\.
+ **Control objective: **Manage vulnerabilities
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.9 rule specification](#ct-cloudfront-pr-9-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.9 rule specification](#ct-cloudfront-pr-9-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.9 example templates](#ct-cloudfront-pr-9-templates) 

**Explanation**

AWS Control Tower recommends that you use SSL/TLS to communicate with AWS resources\. We recommend TLS version 1\.2 or later\.

You can specify the security policy CloudFront will use for HTTPS connections with viewers\. The security policy determines two settings: 1\) the minimum SSL/TLS protocol that CloudFront can use to communicate with viewers, and 2\) the ciphers that CloudFront can use to encrypt the content that it returns to viewers\.

**Usage considerations**  
This control requires a viewer certificate configuration compatible only with Amazon CloudFront distributions that use `Aliases`, also known as alternate domain names or CNAMEs\.

### Remediation for rule failure<a name="ct-cloudfront-pr-9-remediation"></a>

Provide a `ViewerCertificate` configuration with `MinimumProtocolVersion` set to TLSv1\.2 or higher\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example<a name="ct-cloudfront-pr-9-remediation-1"></a>

Amazon CloudFront distribution configured to use TLS version 1\.2 for viewer HTTPS connections\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only"
                        }
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "ViewerCertificate": {
                    "MinimumProtocolVersion": "TLSv1.2_2018",
                    "AcmCertificateArn": {
                        "Ref": "ACMCertificate"
                    },
                    "SslSupportMethod": "vip"
                }
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      ViewerCertificate:
        MinimumProtocolVersion: TLSv1.2_2018
        AcmCertificateArn: !Ref 'ACMCertificate'
        SslSupportMethod: vip
```

### CT\.CLOUDFRONT\.PR\.9 rule specification<a name="ct-cloudfront-pr-9-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_security_policy_check
# 
# Description:
#   This control checks whether your Amazon CloudFront distributions are using a minimum security policy and cipher suite of TLSv1.2 or greater for viewer connections.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.ViewerCertificate' is not present on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'CloudFrontDefaultCertificate' in 'ViewerCertificate' is set to bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'CloudFrontDefaultCertificate' is not provided in 'ViewerCertificate' or provided and set to bool(false)
#       And: 'MinimumProtocolVersion' is not provided in 'ViewerCertificate' or provided as an empty string
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'CloudFrontDefaultCertificate' is not provided in 'ViewerCertificate' or provided and set to bool(false)
#       And: 'MinimumProtocolVersion' is provided in 'ViewerCertificate' and is to one of SSLv3, TLSv1,
#             TLSv1_2016, or TLSv1.1_2016
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'DistributionConfig.ViewerCertificate' is present on the CloudFront distribution resource
#       And: 'CloudFrontDefaultCertificate' is not provided in 'ViewerCertificate' or provided and set to bool(false)
#       And: 'MinimumProtocolVersion' is provided in 'ViewerCertificate' and is not set to SSLv3, TLSv1,
#            TLSv1_2016, or TLSv1.1_2016
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let INPUT_DOCUMENT = this
let NON_COMPLIANT_TLS_POLICIES_LIST = ["SSLv3", "TLSv1", "TLSv1_2016", "TLSv1.1_2016"]

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_security_policy_check when is_cfn_template(%INPUT_DOCUMENT)
                                           %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.9]: Require an Amazon CloudFront distribution to have a security policy of TLSv1.2 as a minimum
            [FIX]: Provide a 'ViewerCertificate' configuration with 'MinimumProtocolVersion' set to TLSv1.2 or higher.
        >>
}

rule cloudfront_security_policy_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.9]: Require an Amazon CloudFront distribution to have a security policy of TLSv1.2 as a minimum
            [FIX]: Provide a 'ViewerCertificate' configuration with 'MinimumProtocolVersion' set to TLSv1.2 or higher.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            # Scenario 2
            ViewerCertificate exists
            ViewerCertificate is_struct

            ViewerCertificate {
                # Scenario 3
                CloudFrontDefaultCertificate not exists or
                CloudFrontDefaultCertificate == false

                # Scenario 4, 5 and 6
                MinimumProtocolVersion exists

                check_is_string_and_not_empty(MinimumProtocolVersion)
                MinimumProtocolVersion not in %NON_COMPLIANT_TLS_POLICIES_LIST
            }
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

### CT\.CLOUDFRONT\.PR\.9 example templates<a name="ct-cloudfront-pr-9-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        ViewerCertificate:
          MinimumProtocolVersion: TLSv1.2_2018
          AcmCertificateArn:
            Ref: ACMCertificate
          SslSupportMethod: sni-only
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  ACMCertificate:
    Type: "AWS::CertificateManager::Certificate"
    Properties:
      DomainName: example.com
      ValidationMethod: DNS
      DomainValidationOptions:
        - DomainName: www.example.com
          HostedZoneId: ZZZHHHHWWWWAAA
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        ViewerCertificate:
          MinimumProtocolVersion: TLSv1
          AcmCertificateArn:
            Ref: ACMCertificate
          SslSupportMethod: vip
```

## \[CT\.CLOUDFRONT\.PR\.10\] Require any Amazon CloudFrontdistributions with Amazon S3 backed origins to have origin access control configured<a name="ct-cloudfront-pr-10-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon CloudFront distributions backed by Amazon S3 are configured to use an origin access control\.
+ **Control objective: **Enforce least privilege, Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.10 rule specification](#ct-cloudfront-pr-10-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.10 rule specification](#ct-cloudfront-pr-10-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.10 example templates](#ct-cloudfront-pr-10-templates) 

**Explanation**

CloudFront OAC prevents users from gaining direct access to an Amazon S3 bucket's content\. Direct access an S3 bucket bypasses the CloudFront distribution and any permissions that are applied to the underlying S3 bucket content\.

**Usage considerations**  
This control applies only to Amazon CloudFront distributions that have one or more origins backed by Amazon S3 configured\.

### Remediation for rule failure<a name="ct-cloudfront-pr-10-remediation"></a>

The `Origins` property configures origins backed by Amazon S3\. For each origin backed by Amazon S3, configure an origin access control identifier using the `OriginAccessControlId` property\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example<a name="ct-cloudfront-pr-10-remediation-1"></a>

Amazon CloudFront distribution with an Amazon S3 bucket origin, configured with an origin access control\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": {
                            "Fn::GetAtt": [
                                "OriginBucket",
                                "RegionalDomainName"
                            ]
                        },
                        "OriginAccessControlId": {
                            "Ref": "OriginAccessControl"
                        },
                        "S3OriginConfig": {}
                    }
                ],
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                }
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      Origins:
        - Id: sampleOrigin
          DomainName: !GetAtt 'OriginBucket.RegionalDomainName'
          OriginAccessControlId: !Ref 'OriginAccessControl'
          S3OriginConfig: {}
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
```

### CT\.CLOUDFRONT\.PR\.10 rule specification<a name="ct-cloudfront-pr-10-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_origin_access_control_enabled_check
# 
# Description:
#   This control checks whether your Amazon CloudFront distributions backed by Amazon S3 are configured to use an origin access control.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: No S3 backed 'Origins' are provided on the CloudFront distribution resource or 'Origins' is not present on
#            the CloudFront distribution resource or is present and an empty list
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'S3Origin' is present on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more S3 backed 'Origins' are configured on the CloudFront distribution resource
#       And: 'OriginAccessControlId' is not present for the 'Origin' or is an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more S3 backed 'Origins' are provided on the CloudFront distribution resource
#       And: 'OriginAccessControlId' is present for each S3 backed 'Origin' and is a non-empty string or valid local
#            reference
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let CLOUDFRONT_ORIGIN_ACCESS_CONTROL_TYPE = "AWS::CloudFront::OriginAccessControl"
let S3_BUCKET_DNS_NAME_PATTERN = /(.*)\.s3(-external-\d|[-\.][a-z]*-[a-z]*-[0-9])?\.amazonaws\.com(\.cn)?$/
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_origin_access_control_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                          %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.10]: Require any Amazon CloudFront distributions with Amazon S3 backed origins to have origin access control configured
            [FIX]: The 'Origins' property configures origins backed by Amazon S3. For each origin backed by Amazon S3, configure an origin access control identifier using the 'OriginAccessControlId' property.
        >>
}

rule cloudfront_origin_access_control_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.10]: Require any Amazon CloudFront distributions with Amazon S3 backed origins to have origin access control configured
            [FIX]: The 'Origins' property configures origins backed by Amazon S3. For each origin backed by Amazon S3, configure an origin access control identifier using the 'OriginAccessControlId' property.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution[
        filter_cloudfront_distribution_with_legacy_s3_origins(this)
    ] {
        DistributionConfig {
            # Scenario 3
            S3Origin not exists
        }
    }

    %cloudfront_distribution[
        # Scenario 2
        filter_cloudfront_distribution_with_origins(this)
    ] {
        DistributionConfig {
            Origins [
                # Scenario 4
                DomainName == %S3_BUCKET_DNS_NAME_PATTERN or
                check_origin_domain_name_get_att(DomainName)
            ] {
                # Scenario 3 and 5
                OriginAccessControlId exists
                check_is_string_and_not_empty(OriginAccessControlId) or
                check_local_references(%INPUT_DOCUMENT, OriginAccessControlId, %CLOUDFRONT_ORIGIN_ACCESS_CONTROL_TYPE)
            }
        }
    }
}

rule filter_cloudfront_distribution_with_legacy_s3_origins(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            S3Origin exists
        }
    }
}

rule filter_cloudfront_distribution_with_origins(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            Origins exists
            Origins is_list
            Origins not empty
        }
    }
}

rule check_origin_domain_name_get_att(domain) {
  %domain {
    'Fn::GetAtt' {
        this is_list
        this not empty
        this[1] == "DomainName" or
        this[1] == "RegionalDomainName"
    }
    check_local_references(%INPUT_DOCUMENT, this, "AWS::S3::Bucket")
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

### CT\.CLOUDFRONT\.PR\.10 example templates<a name="ct-cloudfront-pr-10-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  OriginAccessControl:
    Type: AWS::CloudFront::OriginAccessControl
    Properties:
      OriginAccessControlConfig:
        Name:
          Fn::Sub: ${AWS::StackName}-example-oac
        OriginAccessControlOriginType: s3
        SigningBehavior: always
        SigningProtocol: sigv4
  OriginBucket:
    Type: AWS::S3::Bucket
  OriginBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: OriginBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetObject'
            Effect: Allow
            Resource:
              Fn::Join:
              - ''
              - - 'arn:aws:s3:::'
                - Ref: OriginBucket
                - /*
            Principal:
              Service: cloudfront.amazonaws.com
            Condition:
              StringEquals:
                "AWS:SourceArn":
                  Fn::Join:
                  - ''
                  - - 'arn:aws:cloudfront::'
                    - Ref: AWS::AccountId
                    - ':distribution/'
                    - Ref: CloudFrontDistribution
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleOrigin
          DomainName:
            Fn::GetAtt:
            - OriginBucket
            - RegionalDomainName
          OriginAccessControlId:
            Ref: OriginAccessControl
          S3OriginConfig: {}
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  OriginBucketOai:
    Type: AWS::CloudFront::CloudFrontOriginAccessIdentity
    Properties:
      CloudFrontOriginAccessIdentityConfig:
        Comment:
          Fn::Sub: ${AWS::StackName}-example-oai
  OriginBucket:
    Type: AWS::S3::Bucket
  OriginBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: OriginBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - 's3:GetObject'
            Effect: Allow
            Resource:
              Fn::Join:
              - ''
              - - 'arn:aws:s3:::'
                - Ref: OriginBucket
                - /*
            Principal:
              AWS:
                Fn::Join:
                - ''
                - - 'arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity '
                  - Ref: OriginBucketOai
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        Origins:
        - Id: exampleS3Origin
          DomainName:
            Fn::GetAtt:
            - OriginBucket
            - RegionalDomainName
          S3OriginConfig:
            OriginAccessIdentity:
              Fn::Sub: "origin-access-identity/cloudfront/${OriginBucketOai}"
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleS3Origin
          CachePolicyId:
            Ref: CachePolicy
```

## \[CT\.CLOUDFRONT\.PR\.11\] Require an Amazon CloudFront distribution to use updated SSL protocols between edge locations and custom origins<a name="ct-cloudfront-pr-11-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon CloudFront distributions are using deprecated SSL protocols for HTTPS communication between CloudFront edge locations and custom origins\.
+ **Control objective: **Manage vulnerabilities
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::CloudFront::Distribution`
+ **AWS CloudFormation guard rule: ** [CT\.CLOUDFRONT\.PR\.11 rule specification](#ct-cloudfront-pr-11-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.CLOUDFRONT\.PR\.11 rule specification](#ct-cloudfront-pr-11-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.CLOUDFRONT\.PR\.11 example templates](#ct-cloudfront-pr-11-templates) 

**Explanation**

In 2015, the Internet Engineering Task Force \(IETF\) officially announced that SSL 3\.0 should be deprecated, because the protocol is insufficiently secure\. We recommend that you use TLSv1\.2 or later for HTTPS communication to your custom origins\.

**Usage considerations**  
This control applies only to Amazon CloudFront distributions that have one or more custom origins configured\.

### Remediation for rule failure<a name="ct-cloudfront-pr-11-remediation"></a>

Remove deprecated SSL protocols from `OriginSSLProtocols` in `Origins` that have `CustomOriginConfig` configurations\.

The examples that follow show how to implement this remediation\.

#### Amazon CloudFront Distribution \- Example<a name="ct-cloudfront-pr-11-remediation-1"></a>

Amazon CloudFront distribution configured to use TLS v1\.2 as an origin SSL protocol\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "CloudFrontDistribution": {
        "Type": "AWS::CloudFront::Distribution",
        "Properties": {
            "DistributionConfig": {
                "Enabled": false,
                "DefaultCacheBehavior": {
                    "ViewerProtocolPolicy": "https-only",
                    "TargetOriginId": "sampleOrigin",
                    "CachePolicyId": {
                        "Ref": "CachePolicy"
                    }
                },
                "Origins": [
                    {
                        "Id": "sampleOrigin",
                        "DomainName": "example.com",
                        "CustomOriginConfig": {
                            "OriginProtocolPolicy": "https-only",
                            "OriginSSLProtocols": [
                                "TLSv1.2"
                            ]
                        }
                    }
                ]
            }
        }
    }
}
```

**YAML example**

```
CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Enabled: false
      DefaultCacheBehavior:
        ViewerProtocolPolicy: https-only
        TargetOriginId: sampleOrigin
        CachePolicyId: !Ref 'CachePolicy'
      Origins:
        - Id: sampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
            OriginSSLProtocols:
              - TLSv1.2
```

### CT\.CLOUDFRONT\.PR\.11 rule specification<a name="ct-cloudfront-pr-11-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   cloudfront_no_deprecated_ssl_protocols_check
# 
# Description:
#   This control checks whether your Amazon CloudFront distributions are using deprecated SSL protocols for HTTPS communication between CloudFront edge locations and custom origins.
# 
# Reports on:
#   AWS::CloudFront::Distribution
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
#       And: The input document does not contain any CloudFront distribution resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'Origins' is not present or is an empty list
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more 'Origins' has been configured
#       And: There are no 'Origins' with a 'CustomOriginConfig'
#      Then: SKIP
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more 'Origins' has been configured
#       And: There one or more 'Origins' with a 'CustomOriginConfig'
#       And: All 'Origins' with a 'CustomOriginConfig' have an 'OriginProtocolPolicy' of 'http-only'
#      Then: SKIP
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: 'CustomOrigin' is present on the CloudFront distribution resource
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more 'Origins' has been configured
#       And: There one or more 'Origins' with a 'CustomOriginConfig'
#       And: One or more 'Origins' with a 'CustomOriginConfig' have an 'OriginProtocolPolicy' not equal to 'http-only'
#       And: 'OriginSSLProtocols' has not been specified or specified as an empty list
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more 'Origins' has been configured
#       And: There one or more 'Origins' with a 'CustomOriginConfig'
#       And: One or more 'Origins' with a 'CustomOriginConfig' have an 'OriginProtocolPolicy' not equal to 'http-only'
#       And: 'OriginSSLProtocols' has been specified as a non-empty list and contains 'SSLv3'
#      Then: FAIL
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a CloudFront distribution resource
#       And: One or more 'Origins' has been configured
#       And: There one or more 'Origins' with a 'CustomOriginConfig'
#       And: One or more 'Origins' with a 'CustomOriginConfig' have an 'OriginProtocolPolicy' not equal to 'http-only'
#       And: 'OriginSSLProtocols' has been specified as a non-empty list and does not contain 'SSLv3'
#      Then: PASS

#
# Constants
#
let CLOUDFRONT_DISTRIBUTION_TYPE = "AWS::CloudFront::Distribution"
let UNSUPPORTED_ORIGIN_SSL_PROTOCOLS = [ "SSLv3" ]
let OUT_OF_SCOPE_PROTOCOL_POLICIES = [ "http-only" ]
let INPUT_DOCUMENT = this

#
# Assignments
#
let cloudfront_distributions = Resources.*[ Type == %CLOUDFRONT_DISTRIBUTION_TYPE ]

#
# Primary Rules
#
rule cloudfront_no_deprecated_ssl_protocols_check when is_cfn_template(%INPUT_DOCUMENT)
                                                       %cloudfront_distributions not empty {
    check(%cloudfront_distributions.Properties)
        <<
        [CT.CLOUDFRONT.PR.11]: Require an Amazon CloudFront distribution to use updated SSL protocols between edge locations and custom origins
            [FIX]: Remove deprecated SSL protocols from 'OriginSSLProtocols' in 'Origins' that have 'CustomOriginConfig' configurations.
        >>
}

rule cloudfront_no_deprecated_ssl_protocols_check when is_cfn_hook(%INPUT_DOCUMENT, %CLOUDFRONT_DISTRIBUTION_TYPE) {
    check(%INPUT_DOCUMENT.%CLOUDFRONT_DISTRIBUTION_TYPE.resourceProperties)
        <<
        [CT.CLOUDFRONT.PR.11]: Require an Amazon CloudFront distribution to use updated SSL protocols between edge locations and custom origins
            [FIX]: Remove deprecated SSL protocols from 'OriginSSLProtocols' in 'Origins' that have 'CustomOriginConfig' configurations.
        >>
}

#
# Parameterized Rules
#
rule check(cloudfront_distribution) {
    %cloudfront_distribution[
        filter_cloudfront_distribution_with_legacy_origins(this)
    ] {
        DistributionConfig {
            # Scenario 5
            CustomOrigin not exists
        }
    }

    %cloudfront_distribution[
        # Scenario 2
        filter_cloudfront_distribution_with_origins(this)
    ] {
        DistributionConfig {
            Origins [
                # Scenario 3 and 4
                CustomOriginConfig exists
                CustomOriginConfig is_struct
                filter_custom_origin_config(CustomOriginConfig)
            ] {
                CustomOriginConfig {
                    # Scenario 6, 7 and 8
                    OriginSSLProtocols exists
                    OriginSSLProtocols is_list
                    OriginSSLProtocols not empty
                    %UNSUPPORTED_ORIGIN_SSL_PROTOCOLS.* not in OriginSSLProtocols
                }
            }
        }
    }
}


rule filter_cloudfront_distribution_with_origins(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            Origins exists
            Origins is_list
            Origins not empty
        }
    }
}

rule filter_cloudfront_distribution_with_legacy_origins(cloudfront_distribution) {
    %cloudfront_distribution {
        DistributionConfig exists
        DistributionConfig is_struct

        DistributionConfig {
            CustomOrigin exists
        }
    }
}

rule filter_custom_origin_config(custom_origin_config) {
    %custom_origin_config {
        OriginProtocolPolicy exists
        OriginProtocolPolicy not in %OUT_OF_SCOPE_PROTOCOL_POLICIES
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

### CT\.CLOUDFRONT\.PR\.11 example templates<a name="ct-cloudfront-pr-11-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
            OriginSSLProtocols:
            - TLSv1
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  CachePolicy:
    Type: AWS::CloudFront::CachePolicy
    Properties:
      CachePolicyConfig:
        DefaultTTL: 20
        MaxTTL: 20
        MinTTL: 19
        Name:
          Fn::Sub: ${AWS::StackName}-example-cache-policy
ParametersInCacheKeyAndForwardedToOrigin:
          CookiesConfig:
            CookieBehavior: none
          EnableAcceptEncodingGzip: false
          HeadersConfig:
            HeaderBehavior: none
          QueryStringsConfig:
            QueryStringBehavior: none
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: false
        DefaultCacheBehavior:
          ViewerProtocolPolicy: https-only
          TargetOriginId: exampleOrigin
          CachePolicyId:
            Ref: CachePolicy
        Origins:
        - Id: exampleOrigin
          DomainName: example.com
          CustomOriginConfig:
            OriginProtocolPolicy: https-only
            OriginSSLProtocols:
            - SSLv3
```