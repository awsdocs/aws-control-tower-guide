# Amazon API Gateway controls<a name="api-gateway-rules"></a>

**Topics**
+ [\[CT\.APIGATEWAY\.PR\.1\] Require an Amazon API Gateway REST and WebSocket API to have logging activated](#ct-apigateway-pr-1-description)
+ [\[CT\.APIGATEWAY\.PR\.2\] Require an Amazon API Gateway REST API stage to have AWS X\-Ray tracing activated](#ct-apigateway-pr-2-description)
+ [\[CT\.APIGATEWAY\.PR\.3\] Require that an Amazon API Gateway REST API stage has encryption at rest configured for cache data](#ct-apigateway-pr-3-description)
+ [\[CT\.APIGATEWAY\.PR\.4\] Require an Amazon API Gateway V2 stage to have access logging activated](#ct-apigateway-pr-4-description)
+ [\[CT\.APIGATEWAY\.PR\.5\] Require Amazon API Gateway V2 Websocket and HTTP routes to specify an authorization type](#ct-apigateway-pr-5-description)

## \[CT\.APIGATEWAY\.PR\.1\] Require an Amazon API Gateway REST and WebSocket API to have logging activated<a name="ct-apigateway-pr-1-description"></a>

This control checks whether all methods in Amazon API Gateway stage have execution logging configured\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ApiGateway::Stage`
+ **AWS CloudFormation guard rule: ** [CT\.APIGATEWAY\.PR\.1 rule specification](#ct-apigateway-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.APIGATEWAY\.PR\.1 rule specification](#ct-apigateway-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.APIGATEWAY\.PR\.1 example templates](#ct-apigateway-pr-1-templates) 

**Explanation**

Amazon API Gateway REST or WebSocket API stages should have relevant logs enabled\. API Gateway REST and WebSocket API execution logging provides detailed records of requests made to API Gateway REST and WebSocket API stages\. The stages include API integration backend responses, Lambda authorizer responses, and the `requestId` for AWS integration endpoints\.

**Usage considerations**  
This control requires Amazon API Gateway stages to configure execution logging for all methods and resources \(`HttpMethod` of `*` and `ResourcePath` of `/*`\)\.

### Remediation for rule failure<a name="ct-apigateway-pr-1-remediation"></a>

Configure execution logging on Amazon API Gateway stages with a `MethodSetting` that sets `LoggingLevel` to `ERROR` or `INFO` for all methods \(`HttpMethod` of `*` and `ResourcePath` of `/*`\)\. Ensure that you do not set `LoggingLevel` to `OFF` for any method setting\.

The examples that follow show how to implement this remediation\.

#### Amazon API Gateway Stage \- Example<a name="ct-apigateway-pr-1-remediation-1"></a>

Amazon API Gateway stage configured with `error` level execution logging for all methods and resources\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ApiGatewayStage": {
        "Type": "AWS::ApiGateway::Stage",
        "Properties": {
            "StageName": "Sample",
            "Description": "Sample Stage",
            "RestApiId": {
                "Ref": "RestApi"
            },
            "DeploymentId": {
                "Ref": "Deployment"
            },
            "MethodSettings": [
                {
                    "ResourcePath": "/*",
                    "HttpMethod": "*",
                    "LoggingLevel": "ERROR"
                }
            ]
        }
    }
}
```

**YAML example**

```
ApiGatewayStage:
  Type: AWS::ApiGateway::Stage
  Properties:
    StageName: Sample
    Description: Sample Stage
    RestApiId: !Ref 'RestApi'
    DeploymentId: !Ref 'Deployment'
    MethodSettings:
      - ResourcePath: /*
        HttpMethod: '*'
        LoggingLevel: ERROR
```

### CT\.APIGATEWAY\.PR\.1 rule specification<a name="ct-apigateway-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   api_gw_v1_execution_logging_enabled_check
# 
# Description:
#   This control checks whether all methods in Amazon API Gateway stage have execution logging configured.
# 
# Reports on:
#   AWS::ApiGateway::Stage
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
#       And: The input document does not contain any API Gateway stage resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an API Gateway stage resource
#       And: In the stage resource, 'MethodSettings' is not present or is provided and is an empty list.
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an API Gateway stage resource
#       And: In the stage resource, Execution Logging is not configured for all HTTP Methods and API resources (In
#            'MethodSettings', 'LoggingLevel' is omitted, or not set to 'ERROR' or 'INFO', for 'HttpMethod' of '*' and
#            'ResourcePath' of '/*' )
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an API Gateway stage resource
#       And: In the stage resource, Execution Logging is configured for all HTTP Methods and API resources (In
#            'MethodSettings', 'LoggingLevel' is set to 'ERROR' or 'INFO', for 'HttpMethod' of '*' and
#            'ResourcePath' of '/*' )
#       And: 'LoggingLevel' has been set to 'OFF' for any other Method Setting
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an API Gateway stage resource
#       And: In the stage resource, Execution Logging is configured for all HTTP Methods and API resources (In
#            'MethodSettings', 'LoggingLevel' is set to 'ERROR' or 'INFO', for 'HttpMethod' of '*' and
#            'ResourcePath' of '/*' )
#       And: 'LoggingLevel' has not been provided or set to 'ERROR' or 'INFO' for all other Method Settings
#      Then: PASS

#
# Constants
#
let API_GW_STAGE_TYPE = "AWS::ApiGateway::Stage"
let INPUT_DOCUMENT = this
let VALID_LOG_LEVELS = [ "ERROR", "INFO" ]

#
# Assignments
#
let api_gateway_stages = Resources.*[ Type == %API_GW_STAGE_TYPE ]

#
# Primary Rules
#
rule api_gw_v1_execution_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                    %api_gateway_stages not empty {
    check(%api_gateway_stages.Properties)
        <<
        [CT.APIGATEWAY.PR.1]: Require an Amazon API Gateway REST and WebSocket API to have logging activated
        [FIX]: Configure execution logging on Amazon API Gateway stages with a 'MethodSetting' that sets 'LoggingLevel' to 'ERROR' or 'INFO' for all methods ('HttpMethod' of '*' and 'ResourcePath' of '/*'). Ensure that you do not set 'LoggingLevel' to 'OFF' for any method setting.
        >>
}

rule api_gw_v1_execution_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %API_GW_STAGE_TYPE) {
    check(%INPUT_DOCUMENT.%API_GW_STAGE_TYPE.resourceProperties)
        <<
        [CT.APIGATEWAY.PR.1]: Require an Amazon API Gateway REST and WebSocket API to have logging activated
        [FIX]: Configure execution logging on Amazon API Gateway stages with a 'MethodSetting' that sets 'LoggingLevel' to 'ERROR' or 'INFO' for all methods ('HttpMethod' of '*' and 'ResourcePath' of '/*'). Ensure that you do not set 'LoggingLevel' to 'OFF' for any method setting.
        >>
}

#
# Parameterized Rules
#
rule check(api_gateway_stage) {
    %api_gateway_stage {
        # Scenario 2
        MethodSettings exists
        MethodSettings is_list
        MethodSettings not empty

        # Scenario 3
        # At least one wildcard entry exists with valid logging enabled
        some MethodSettings[*] {
            HttpMethod exists
            ResourcePath exists
            LoggingLevel exists

            HttpMethod == "*"
            ResourcePath == "/*"
            LoggingLevel in %VALID_LOG_LEVELS
        }

        # Scenario 4, 5
        # When other methods explictly set/override logging settings, ensure that logging is not disabled
        MethodSettings[*] {
            when LoggingLevel exists {
                LoggingLevel in %VALID_LOG_LEVELS
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

### CT\.APIGATEWAY\.PR\.1 example templates<a name="ct-apigateway-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  RestApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: ExampleRestApi
  GetMethod:
    DependsOn: PutMethod
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: GET
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  PutMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: PUT
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  Deployment:
    DependsOn: GetMethod
    Type: 'AWS::ApiGateway::Deployment'
    Properties:
      RestApiId:
        Ref: RestApi
  ApiGatewayStage:
    Type: AWS::ApiGateway::Stage
    Properties:
      StageName: Example
      RestApiId:
        Ref: RestApi
      DeploymentId:
        Ref: Deployment
      MethodSettings:
      - ResourcePath: "/*"
        HttpMethod: "*"
        LoggingLevel: ERROR
      - ResourcePath: "/"
        HttpMethod: GET
        LoggingLevel: INFO
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RestApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: ExampleRestApi
  GetMethod:
    DependsOn: PutMethod
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: GET
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  PutMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: PUT
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  Deployment:
    DependsOn: GetMethod
    Type: 'AWS::ApiGateway::Deployment'
    Properties:
      RestApiId:
        Ref: RestApi
  ApiGatewayStage:
    Type: AWS::ApiGateway::Stage
    Properties:
      StageName: Example
      RestApiId:
        Ref: RestApi
      DeploymentId:
        Ref: Deployment
```

## \[CT\.APIGATEWAY\.PR\.2\] Require an Amazon API Gateway REST API stage to have AWS X\-Ray tracing activated<a name="ct-apigateway-pr-2-description"></a>

This control ensures that AWS X\-Ray tracing is enabled on Amazon API Gateway REST APIs\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ApiGateway::Stage`
+ **AWS CloudFormation guard rule: ** [CT\.APIGATEWAY\.PR\.2 rule specification](#ct-apigateway-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.APIGATEWAY\.PR\.2 rule specification](#ct-apigateway-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.APIGATEWAY\.PR\.2 example templates](#ct-apigateway-pr-2-templates) 

**Explanation**

AWS X\-Ray active tracing enables a more rapid response to performance changes in the underlying infrastructure\. Changes in performance could result in a lack of availability of the API\. X\-Ray active tracing provides real\-time metrics of user requests that flow through your API Gateway REST API operations and connected services\.

### Remediation for rule failure<a name="ct-apigateway-pr-2-remediation"></a>

Set `TracingEnabled` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon API Gateway Stage \- Example<a name="ct-apigateway-pr-2-remediation-1"></a>

Amazon API Gateway stage configured with AWS X\-Ray tracing enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ApiGatewayStage": {
        "Type": "AWS::ApiGateway::Stage",
        "Properties": {
            "StageName": "Sample",
            "Description": "Sample Stage",
            "TracingEnabled": true,
            "RestApiId": {
                "Ref": "RestApi"
            },
            "DeploymentId": {
                "Ref": "Deployment"
            }
        }
    }
}
```

**YAML example**

```
ApiGatewayStage:
  Type: AWS::ApiGateway::Stage
  Properties:
    StageName: Sample
    Description: Sample Stage
    TracingEnabled: true
    RestApiId: !Ref 'RestApi'
    DeploymentId: !Ref 'Deployment'
```

### CT\.APIGATEWAY\.PR\.2 rule specification<a name="ct-apigateway-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   api_gw_xray_enabled_check
# 
# Description:
#   This control ensures that AWS X-Ray tracing is enabled on Amazon API Gateway REST APIs.
# 
# Reports on:
#   AWS::ApiGateway::Stage
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
#       And: The input document does not contain any API Gateway stage resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an API Gateway stage resource
#       And: 'TracingEnabled' is not present on the API Gateway stage
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an API Gateway stage resource
#       And: 'TracingEnabled' is present on the API Gateway stage and is set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an API Gateway stage resource
#       And: 'TracingEnabled' is present on the API Gateway stage and is set to bool(true)
#      Then: PASS

#
# Constants
#
let API_GW_STAGE_TYPE = "AWS::ApiGateway::Stage"
let INPUT_DOCUMENT = this

#
# Assignments
#
let api_gateway_stages = Resources.*[ Type == %API_GW_STAGE_TYPE ]

#
# Primary Rules
#
rule api_gw_xray_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                    %api_gateway_stages not empty {
    check(%api_gateway_stages.Properties)
        <<
        [CT.APIGATEWAY.PR.2]: Require an Amazon API Gateway REST API stage to have AWS X-Ray tracing activated
        [FIX]: Set 'TracingEnabled' to 'true'.
        >>
}

rule api_gw_xray_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %API_GW_STAGE_TYPE) {
    check(%INPUT_DOCUMENT.%API_GW_STAGE_TYPE.resourceProperties)
        <<
        [CT.APIGATEWAY.PR.2]: Require an Amazon API Gateway REST API stage to have AWS X-Ray tracing activated
        [FIX]: Set 'TracingEnabled' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(api_gateway_stage) {
    %api_gateway_stage {
        # Scenario 2, 3, 4
        TracingEnabled exists
        TracingEnabled == true
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

### CT\.APIGATEWAY\.PR\.2 example templates<a name="ct-apigateway-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  RestApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: Testing
  GetMethod:
    DependsOn: PutMethod
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: GET
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  PutMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: PUT
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  Deployment:
    DependsOn: GetMethod
    Type: 'AWS::ApiGateway::Deployment'
    Properties:
      RestApiId:
        Ref: RestApi
  ApiGatewayStage:
    Type: AWS::ApiGateway::Stage
    Properties:
      StageName: Dev
      Description: Dev Stage
      TracingEnabled: true
      RestApiId:
        Ref: RestApi
      DeploymentId:
        Ref: Deployment
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RestApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: Testing
  GetMethod:
    DependsOn: PutMethod
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: GET
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  PutMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: PUT
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  Deployment:
    DependsOn: GetMethod
    Type: 'AWS::ApiGateway::Deployment'
    Properties:
      RestApiId:
        Ref: RestApi
  ApiGatewayStage:
    Type: AWS::ApiGateway::Stage
    Properties:
      StageName: Dev
      Description: Dev Stage
      TracingEnabled: false
      RestApiId:
        Ref: RestApi
      DeploymentId:
        Ref: Deployment
```

## \[CT\.APIGATEWAY\.PR\.3\] Require that an Amazon API Gateway REST API stage has encryption at rest configured for cache data<a name="ct-apigateway-pr-3-description"></a>

This control checks whether an Amazon API Gateway REST API stage that has caching enabled also encrypts the caches\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: ** `AWS::ApiGateway::Stage` 
+ **AWS CloudFormation guard rule: ** [CT\.APIGATEWAY\.PR\.3 rule specification](#ct-apigateway-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.APIGATEWAY\.PR\.3 rule specification](#ct-apigateway-pr-3-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.APIGATEWAY\.PR\.3 example templates](#ct-apigateway-pr-3-templates) 

**Explanation**

Encrypting data at rest reduces the risk that data stored on disk may be accessible by a user not authenticated to AWS\. It adds another set of access controls to limit unauthorized users' ability to obtain the data\. For example, API permissions are required to decrypt the data before it can be read\.

For an added layer of security, API Gateway REST API caches should be encrypted at rest\.

**Usage considerations**  
This control applies only to API Gateway stage resources with cache clustering enabled\.
Where cache clustering is enabled, this control requires cache encryption to be enabled for all resources and methods by specifying a `MethodSetting` entry with an `HttpMethod` of `*` and `ResourcePath` of `/*`\.

### Remediation for rule failure<a name="ct-apigateway-pr-3-remediation"></a>

Configure encryption on API Gateway caches with a `MethodSetting` that sets `CacheDataEncrypted` to true for all methods \(`HttpMethod` of `*` and `ResourcePath` of `/*`\)\. Ensure that you do not set `CacheDataEncrypted` to false for any method setting\.

The examples that follow show how to implement this remediation\.

#### API Gateway stage examples<a name="ct-apigateway-pr-3-remediation-1"></a>

This example shows the API Gateway stage configured to encrypt cache data for all methods \(`HttpMethod` of `*` and `ResourcePath` of `/*`\)\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ApiGatewayStage": {
        "Type": "AWS::ApiGateway::Stage",
        "Properties": {
            "StageName": "Dev",
            "Description": "Development Stage",
            "CacheClusterEnabled": true,
            "CacheClusterSize": 0.5,
            "RestApiId": {
                "Ref": "RestApi"
            },
            "DeploymentId": {
                "Ref": "Deployment"
            },
            "MethodSettings": [
                {
                    "ResourcePath": "/*",
                    "HttpMethod": "*",
                    "CacheDataEncrypted": true
                },
                {
                    "ResourcePath": "/",
                    "HttpMethod": "POST"
                }
            ]
        }
    }
}
```

**YAML example**

```
ApiGatewayStage:
  Type: AWS::ApiGateway::Stage
  Properties:
    StageName: Dev
    Description: Development Stage
    CacheClusterEnabled: true
    CacheClusterSize: 0.5
    RestApiId: !Ref 'RestApi'
    DeploymentId: !Ref 'Deployment'
    MethodSettings:
      - ResourcePath: /*
        HttpMethod: '*'
        CacheDataEncrypted: true
      - ResourcePath: /
        HttpMethod: POST
```

### CT\.APIGATEWAY\.PR\.3 rule specification<a name="ct-apigateway-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   api_gw_cache_encrypted_check
# 
# Description:
#   This rule checks whether Amazon API Gateway REST API stages that have caching enabled also encrypt the caches.
# 
# Reports on:
#   AWS::ApiGateway::Stage
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Amazon API Gateway stage resources
#     Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon API Gateway stage resource
#       And: 'CacheClusterEnabled' is not set, or is set to bool(false) on the API Gateway stage resource
#     Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon API Gateway stage resource
#       And: 'CacheClusterEnabled' is set to bool(true) on the API Gateway stage resource
#       And: In the Stage resource, 'MethodSettings' is not present or is provided and is an empty list.
#     Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon API Gateway stage resource
#       And: 'CacheClusterEnabled' is set to bool(true) on the API Gateway stage resource
#       And: In the stage resource, cache data encryption is not enabled for all HTTP methods and API resources (In
#           'MethodSettings', 'CacheDataEncrypted' is omitted or set to bool(false) for 'HttpMethod' of '*' and
#           'ResourcePath' of '/*' )
#     Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an API Gateway stage resource
#       And: 'CacheClusterEnabled' is set to bool(true) on the API Gateway stage resource
#       And: In the stage resource, cache data encryption is configured for all 'MethodSettings' (CacheDataEncrypted is
#           bool(true) for 'HttpMethod' of '*' and 'ResourcePath' of '/*')
#       And: 'CacheDataEncrypted' has been set to bool(false) for any other method settings
#     Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an API Gateway stage resource
#       And: 'CacheClusterEnabled' is set to bool(true) on the API Gateway stage resource
#       And: In the stage resource cache data encryption is configured for all 'MethodSettings' (CacheDataEncrypted is
#           bool(true) for 'HttpMethod' of '*' and 'ResourcePath' of '/*')
#       And: 'CacheDataEncrypted' has not been provided or set to bool(true) for all other method settings
#     Then: PASS

#
# Constants
#
let API_GW_STAGE_TYPE = "AWS::ApiGateway::Stage"
let INPUT_DOCUMENT = this

#
# Assignments
#
let api_gateway_stages = Resources.*[ Type == %API_GW_STAGE_TYPE ]

#
# Primary Rules
#
rule api_gw_cache_encrypted_check when is_cfn_template(%INPUT_DOCUMENT)
                                       %api_gateway_stages not empty {
    check(%api_gateway_stages.Properties)
        <<
        [CT.APIGATEWAY.PR.3]: Require that an Amazon API Gateway REST API stage has encryption at rest configured for cache data
        [FIX]: Configure encryption on API Gateway caches with a 'MethodSetting' that sets 'CacheDataEncrypted' to true for all methods ('HttpMethod' of '*' and 'ResourcePath' of '/*'). Ensure that you do not set 'CacheDataEncrypted' to false for any method setting.
        >>
}

rule api_gw_cache_encrypted_check when is_cfn_hook(%INPUT_DOCUMENT, %API_GW_STAGE_TYPE) {
    check(%INPUT_DOCUMENT.%API_GW_STAGE_TYPE.resourceProperties)
        <<
        [CT.APIGATEWAY.PR.3]: Require that an Amazon API Gateway REST API stage has encryption at rest configured for cache data
        [FIX]: Configure encryption on API Gateway caches with a 'MethodSetting' that sets 'CacheDataEncrypted' to true for all methods ('HttpMethod' of '*' and 'ResourcePath' of '/*'). Ensure that you do not set 'CacheDataEncrypted' to false for any method setting.
        >>
}

#
# Parameterized Rules
#
rule check(api_gateway_stage) {
    %api_gateway_stage [
        CacheClusterEnabled exists
        CacheClusterEnabled == true
    ] {
        # Scenario 2, 3, 4, 6
        cache_encrypted(this)
    }
}

rule cache_encrypted(api_gateway_stage) {
    %api_gateway_stage {
        MethodSettings exists
        MethodSettings is_list
        MethodSettings not empty

        some MethodSettings[*] {
            HttpMethod exists
            ResourcePath exists
            CacheDataEncrypted exists

            HttpMethod == "*"
            ResourcePath == "/*"
            CacheDataEncrypted == true
        }

        MethodSettings[*] {
            when CacheDataEncrypted exists {
                CacheDataEncrypted == true
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

### CT\.APIGATEWAY\.PR\.3 example templates<a name="ct-apigateway-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  RestApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: ExampleRestApi
  GetMethod:
    DependsOn: PutMethod
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: GET
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  PutMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: PUT
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  Deployment:
    DependsOn: GetMethod
    Type: 'AWS::ApiGateway::Deployment'
    Properties:
      RestApiId:
        Ref: RestApi
  ApiGatewayStage:
    Type: AWS::ApiGateway::Stage
    Properties:
      StageName: Example
      Description: Example Stage
      CacheClusterEnabled: true
      CacheClusterSize: 0.5
      RestApiId:
        Ref: RestApi
      DeploymentId:
        Ref: Deployment
      MethodSettings:
      - ResourcePath: "/*"
        HttpMethod: "*"
        CacheDataEncrypted: true
      - ResourcePath: "/"
        HttpMethod: "POST"
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RestApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: ExampleRestApi
  GetMethod:
    DependsOn: PutMethod
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: GET
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  PutMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: PUT
      RestApiId:
        Ref: RestApi
      ResourceId:
        Fn::GetAtt:
        - "RestApi"
        - "RootResourceId"
      AuthorizationType: NONE
      MethodResponses:
      - StatusCode: "200"
      Integration:
        Type: MOCK
  Deployment:
    DependsOn: GetMethod
    Type: 'AWS::ApiGateway::Deployment'
    Properties:
      RestApiId:
        Ref: RestApi
  ApiGatewayStage:
    Type: AWS::ApiGateway::Stage
    Properties:
      StageName: Example
      Description: Example Stage
      CacheClusterEnabled: true
      CacheClusterSize: 0.5
      RestApiId:
        Ref: RestApi
      DeploymentId:
        Ref: Deployment
      MethodSettings:
      - ResourcePath: "/*"
        HttpMethod: "*"
        CacheDataEncrypted: false
```

## \[CT\.APIGATEWAY\.PR\.4\] Require an Amazon API Gateway V2 stage to have access logging activated<a name="ct-apigateway-pr-4-description"></a>

This control checks whether Amazon API Gateway V2 stages have access logging enabled\. Access logging is supported for HTTP and WebSocket APIs\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ApiGatewayV2::Stage`
+ **AWS CloudFormation guard rule: ** [CT\.APIGATEWAY\.PR\.4 rule specification](#ct-apigateway-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.APIGATEWAY\.PR\.4 rule specification](#ct-apigateway-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.APIGATEWAY\.PR\.4 example templates](#ct-apigateway-pr-4-templates) 

**Explanation**

Access logging allows you to log who has called your API and how the caller gained access to the API\. You can create your own log group or choose an existing log group that could be managed by API Gateway\.

### Remediation for rule failure<a name="ct-apigateway-pr-4-remediation"></a>

Provide an `AccessLogSettings` configuration, setting `DestinationArn` to the ARN of an Amazon CloudWatch log group and `Format` to a single line log format configuration\.

The examples that follow show how to implement this remediation\.

#### Amazon API Gateway HTTP API Stage \- Example<a name="ct-apigateway-pr-4-remediation-1"></a>

Amazon API Gateway HTTP API stage configured to send API access logs to Amazon CloudWatch Logs\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "HttpApiStage": {
        "Type": "AWS::ApiGatewayV2::Stage",
        "Properties": {
            "StageName": "SampleStage",
            "Description": "Sample Stage",
            "ApiId": {
                "Ref": "HttpApi"
            },
            "AccessLogSettings": {
                "DestinationArn": {
                    "Fn::GetAtt": [
                        "LogGroup",
                        "Arn"
                    ]
                },
                "Format": "{\"requestId\":\"$context.requestId\", \"ip\": \"$context.identity.sourceIp\", \"user\":\"$context.identity.user\",\"requestTime\":\"$context.requestTime\"}"
            }
        }
    }
}
```

**YAML example**

```
HttpApiStage:
  Type: AWS::ApiGatewayV2::Stage
  Properties:
    StageName: SampleStage
    Description: Sample Stage
    ApiId: !Ref 'HttpApi'
    AccessLogSettings:
      DestinationArn: !GetAtt 'LogGroup.Arn'
      Format: '{"requestId":"$context.requestId", "ip": "$context.identity.sourceIp",
        "user":"$context.identity.user","requestTime":"$context.requestTime"}'
```

### CT\.APIGATEWAY\.PR\.4 rule specification<a name="ct-apigateway-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   api_gw_v2_access_logs_enabled_check
# 
# Description:
#   This control checks whether Amazon API Gateway V2 stages have access logging enabled. Access logging is supported for HTTP and WebSocket APIs.
# 
# Reports on:
#   AWS::ApiGatewayV2::Stage
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
#       And: The input document does not contain any APIGatewayV2 stage resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an APIGatewayV2 stage resource
#       And: 'AccessLogSettings' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an APIGatewayV2 stage resource
#       And: 'AccessLogSettings' has been provided
#       And: 'AccessLogSettings.DestinationArn' has not been provided, or has been provided as an empty string or
#            invalid local reference
#       And: 'AccessLogSettings.Format' is provided as a non-empty string
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an APIGatewayV2 stage resource
#       And: 'AccessLogSettings' has been provided
#       And: 'AccessLogSettings.DestinationArn' is provided as a non-empty string or valid local reference
#       And: 'AccessLogSettings.Format' has not been provided, or is an empty string
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an APIGatewayV2 stage resource
#       And: 'AccessLogSettings' has been provided
#       And: 'AccessLogSettings.DestinationArn' is provided as a non-empty string or valid local reference
#       And: 'AccessLogSettings.Format' is provided as a non-empty string
#      Then: PASS

#
# Constants
#
let API_GW_V2_STAGE_TYPE = "AWS::ApiGatewayV2::Stage"
let INPUT_DOCUMENT = this

#
# Assignments
#
let api_gateway_v2_stages = Resources.*[ Type == %API_GW_V2_STAGE_TYPE ]

#
# Primary Rules
#
rule api_gw_v2_access_logs_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                              %api_gateway_v2_stages not empty {
    check(%api_gateway_v2_stages.Properties)
        <<
        [CT.APIGATEWAY.PR.4]: Require an Amazon API Gateway V2 stage to have access logging activated
            [FIX]: Provide an 'AccessLogSettings' configuration, setting 'DestinationArn' to the ARN of an Amazon CloudWatch log group and 'Format' to a single line log format configuration.
        >>
}

rule api_gw_v2_access_logs_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %API_GW_V2_STAGE_TYPE) {
    check(%INPUT_DOCUMENT.%API_GW_V2_STAGE_TYPE.resourceProperties)
        <<
        [CT.APIGATEWAY.PR.4]: Require an Amazon API Gateway V2 stage to have access logging activated
            [FIX]: Provide an 'AccessLogSettings' configuration, setting 'DestinationArn' to the ARN of an Amazon CloudWatch log group and 'Format' to a single line log format configuration.
        >>
}

#
# Parameterized Rules
#
rule check(api_gateway_v2_stage) {
    %api_gateway_v2_stage {
        # Scenario 2
        AccessLogSettings exists
        AccessLogSettings is_struct

        AccessLogSettings {
            # Scenario 3
            DestinationArn exists
            check_is_string_and_not_empty(DestinationArn) or
            check_local_references(%INPUT_DOCUMENT, DestinationArn, "AWS::Logs::LogGroup")

            # Scenario 4, 5
            Format exists
            check_is_string_and_not_empty(Format)
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

### CT\.APIGATEWAY\.PR\.4 example templates<a name="ct-apigateway-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  LogGroup:
    Type: AWS::Logs::LogGroup
    Properties:
      RetentionInDays: 7
  HttpApi:
    Type: AWS::ApiGatewayV2::Api
    Properties:
      Name: ExampleApi
      ProtocolType: HTTP
  HttpApiStage:
    Type: 'AWS::ApiGatewayV2::Stage'
    Properties:
      StageName: ExampleStage
      Description: Example Stage
      ApiId:
        Ref: HttpApi
      AccessLogSettings:
        DestinationArn:
          Fn::GetAtt:
          - "LogGroup"
          - "Arn"
        Format: >-
          {"requestId":"$context.requestId", "ip": "$context.identity.sourceIp",
          "user":"$context.identity.user","requestTime":"$context.requestTime"}
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  HttpApi:
    Type: AWS::ApiGatewayV2::Api
    Properties:
      Name: ExampleApi
      ProtocolType: HTTP
  HttpApiStage:
    Type: 'AWS::ApiGatewayV2::Stage'
    Properties:
      StageName: ExampleStage
      Description: Example Stage
      ApiId:
        Ref: HttpApi
```

## \[CT\.APIGATEWAY\.PR\.5\] Require Amazon API Gateway V2 Websocket and HTTP routes to specify an authorization type<a name="ct-apigateway-pr-5-description"></a>

This control checks whether Amazon API Gateway V2 API routes have an authorization type set\.
+ **Control objective: **Use strong authentication
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::ApiGatewayV2::Route`, `AWS::ApiGatewayV2::ApiGatewayManagedOverrides`
+ **AWS CloudFormation guard rule: ** [CT\.APIGATEWAY\.PR\.5 rule specification](#ct-apigateway-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.APIGATEWAY\.PR\.5 rule specification](#ct-apigateway-pr-5-rule) 
+ For examples of PASS and FAIL AWS CloudFormation Templates related to this control, see: [CT\.APIGATEWAY\.PR\.5 example templates](#ct-apigateway-pr-5-templates) 

**Explanation**

API Gateway supports multiple mechanisms for controlling and managing access to your Websocket or HTTP API\. By specifying an authorization type, you can restrict access to your API, to allow only required users or processes\.

**Usage considerations**  
This control applies only to routes created by means of the `AWS::ApiGatewayV2::Route` resource, and to managed overrides that apply to HTTP API routes that are created through quick create\.
This control does not evaluate HTTP API routes imported using the `Body` or `BodyS3Location` properties of `AWS::ApiGatewayV2::API` resources\.

### Remediation for rule failure<a name="ct-apigateway-pr-5-remediation"></a>

For Amazon API Gateway V2 routes, set `AuthorizationType` to `AWS_IAM`, `JWT` or `CUSTOM`\. For Amazon API Gateway V2 managed route overrides with `AuthorizationType`, set `AuthorizationType` to `AWS_IAM`, `JWT` or `CUSTOM`\.

The examples that follow show how to implement this remediation\.

#### Amazon API Gateway V2 Route \- Example<a name="ct-apigateway-pr-5-remediation-1"></a>

Amazon API Gateway V2 route configured with AWS IAM authorization\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ApiGatewayV2Route": {
        "Type": "AWS::ApiGatewayV2::Route",
        "Properties": {
            "ApiId": {
                "Ref": "WebsocketApi"
            },
            "RouteKey": "$connect",
            "AuthorizationType": "AWS_IAM"
        }
    }
}
```

**YAML example**

```
ApiGatewayV2Route:
  Type: AWS::ApiGatewayV2::Route
  Properties:
    ApiId: !Ref 'WebsocketApi'
    RouteKey: $connect
    AuthorizationType: AWS_IAM
```

The examples that follow show how to implement this remediation\.

#### Amazon API Gateway V2 Managed Overrides \- Example<a name="ct-apigateway-pr-5-remediation-2"></a>

Amazon API Gateway V2 managed overrides configured with AWS IAM authorization\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "ApiGatewayManagedOverride": {
        "Type": "AWS::ApiGatewayV2::ApiGatewayManagedOverrides",
        "Properties": {
            "ApiId": {
                "Ref": "HttpApi"
            },
            "Route": {
                "AuthorizationType": "AWS_IAM"
            }
        }
    }
}
```

**YAML example**

```
ApiGatewayManagedOverride:
  Type: AWS::ApiGatewayV2::ApiGatewayManagedOverrides
  Properties:
    ApiId: !Ref 'HttpApi'
    Route:
      AuthorizationType: AWS_IAM
```

### CT\.APIGATEWAY\.PR\.5 rule specification<a name="ct-apigateway-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   api_gw_v2_authorization_type_configured_check
# 
# Description:
#   This control checks whether Amazon API Gateway V2 API routes have an authorization type set.
# 
# Reports on:
#   AWS::ApiGatewayV2::Route, AWS::ApiGatewayV2::ApiGatewayManagedOverrides
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
#       And: The input document does not contain any Amazon API Gateway V2 route or managed route overrides resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon API Gateway V2 managed route overrides resource
#       And: In 'Route', 'AuthorizationType' has not been provided
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon API Gateway V2 route resource
#       And: 'AuthorizationType' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon API Gateway V2 route or managed route overrides resource
#       And: 'AuthorizationType' has been provided and set to a value other than 'AWS_IAM', 'JWT' or 'CUSTOM'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon API Gateway V2 route or managed route overrides resource
#       And: 'AuthorizationType' has been provided and set to a value of 'AWS_IAM', 'JWT' or 'CUSTOM'
#      Then: PASS

#
# Constants
#
let API_GW_ROUTE_TYPE = "AWS::ApiGatewayV2::Route"
let API_GW_MANAGED_OVERRIDE_TYPE = "AWS::ApiGatewayV2::ApiGatewayManagedOverrides"
let ALLOWED_AUTHORIZATION_TYPES = ["AWS_IAM", "JWT", "CUSTOM"]
let INPUT_DOCUMENT = this

#
# Assignments
#
let api_route = Resources.*[ Type == %API_GW_ROUTE_TYPE ]
let api_override = Resources.*[ Type == %API_GW_MANAGED_OVERRIDE_TYPE ]

#
# Primary Rules
#
rule api_gw_v2_authorization_type_configured_check when is_cfn_template(%INPUT_DOCUMENT)
                                                        %api_route not empty {
    check_api_route(%api_route.Properties)
         <<
         [CT.APIGATEWAY.PR.5]: Require Amazon API Gateway V2 Websocket and HTTP routes to specify an authorization type
            [FIX]: For Amazon API Gateway V2 routes, set 'AuthorizationType' to 'AWS_IAM', 'JWT' or 'CUSTOM'. For Amazon API Gateway V2 managed route overrides with 'AuthorizationType', set 'AuthorizationType' to 'AWS_IAM', 'JWT' or 'CUSTOM'.
         >>
}

rule api_gw_v2_authorization_type_configured_check when is_cfn_template(%INPUT_DOCUMENT)
                                                        %api_override not empty {
    check_api_override(%api_override.Properties)
         <<
         [CT.APIGATEWAY.PR.5]: Require Amazon API Gateway V2 Websocket and HTTP routes to specify an authorization type
            [FIX]: For Amazon API Gateway V2 routes, set 'AuthorizationType' to 'AWS_IAM', 'JWT' or 'CUSTOM'. For Amazon API Gateway V2 managed route overrides with 'AuthorizationType', set 'AuthorizationType' to 'AWS_IAM', 'JWT' or 'CUSTOM'.
         >>
}

rule api_gw_v2_authorization_type_configured_check when is_cfn_hook(%INPUT_DOCUMENT, %API_GW_ROUTE_TYPE) {
    check_api_route(%INPUT_DOCUMENT.%API_GW_ROUTE_TYPE.resourceProperties)
         <<
         [CT.APIGATEWAY.PR.5]: Require Amazon API Gateway V2 Websocket and HTTP routes to specify an authorization type
            [FIX]: For Amazon API Gateway V2 routes, set 'AuthorizationType' to 'AWS_IAM', 'JWT' or 'CUSTOM'. For Amazon API Gateway V2 managed route overrides with 'AuthorizationType', set 'AuthorizationType' to 'AWS_IAM', 'JWT' or 'CUSTOM'.
         >>
}

rule api_gw_v2_authorization_type_configured_check when is_cfn_hook(%INPUT_DOCUMENT, %API_GW_MANAGED_OVERRIDE_TYPE) {
    check_api_override(%INPUT_DOCUMENT.%API_GW_MANAGED_OVERRIDE_TYPE.resourceProperties)
         <<
         [CT.APIGATEWAY.PR.5]: Require Amazon API Gateway V2 Websocket and HTTP routes to specify an authorization type
            [FIX]: For Amazon API Gateway V2 routes, set 'AuthorizationType' to 'AWS_IAM', 'JWT' or 'CUSTOM'. For Amazon API Gateway V2 managed route overrides with 'AuthorizationType', set 'AuthorizationType' to 'AWS_IAM', 'JWT' or 'CUSTOM'.
         >>
}

#
# Parameterized Rules
#
rule check_api_route(api_route) {
    %api_route {
        # Scenario 3
        AuthorizationType exists

        # Scenario 4 and 5
        AuthorizationType in %ALLOWED_AUTHORIZATION_TYPES
    }
}

rule check_api_override(api_override) {
    %api_override [
        # Scenario 2
        Route exists
        Route is_struct
        Route {
            AuthorizationType exists
        }
    ]{
        check_api_route(Route)
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

### CT\.APIGATEWAY\.PR\.5 example templates<a name="ct-apigateway-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ApiGatewayV2Route:
    Type: AWS::ApiGatewayV2::Route
    Properties:
      ApiId: a1bcdef2gh
      RouteKey: $connect
      AuthorizationType: AWS_IAM
```

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  ApiGatewayManagedOverride:
    Type: AWS::ApiGatewayV2::ApiGatewayManagedOverrides
    Properties:
      ApiId: a1bcdef2gh
      Route:
        AuthorizationType: AWS_IAM
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ApiGatewayV2Route:
    Type: AWS::ApiGatewayV2::Route
    Properties:
      ApiId: a1bcdef2gh
      RouteKey: $connect
      AuthorizationType: NONE
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  ApiGatewayManagedOverride:
    Type: AWS::ApiGatewayV2::ApiGatewayManagedOverrides
    Properties:
      ApiId: a1bcdef2gh
      Route:
        AuthorizationType: NONE
```