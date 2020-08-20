# Elective Guardrails<a name="elective-guardrails"></a>

Elective guardrails enable you to lock down or track attempts at performing commonly restricted actions in an AWS enterprise environment\. These guardrails are not enabled by default, and can be disabled\. Following, you'll find a reference for each of the elective guardrails available in AWS Control Tower\.

**Topics**
+ [Disallow Cross\-Region Replication for Amazon S3 Buckets](#disallow-s3-ccr)
+ [Disallow Delete Actions on Amazon S3 Buckets Without MFA](#disallow-s3-delete-mfa)
+ [Disallow Access to IAM Users Without MFA](#disallow-access-mfa)
+ [Disallow Console Access to IAM Users Without MFA](#disallow-console-access-mfa)
+ [Disallow Amazon S3 Buckets That Are Not Versioning Enabled](#disallow-s3-no-versioning)

## Disallow Cross\-Region Replication for Amazon S3 Buckets<a name="disallow-s3-ccr"></a>

Restricts the location of your Amazon S3 data to a single AWS Region by disabling any automatic, asynchronous copying of objects across buckets to other AWS Regions\. This is a preventive guardrail with elective guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRRESTRICTS3CROSSREGIONREPLICATION",
            "Effect": "Deny",
            "Action": [
                "s3:PutReplicationConfiguration"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

## Disallow Delete Actions on Amazon S3 Buckets Without MFA<a name="disallow-s3-delete-mfa"></a>

Protects your Amazon S3 buckets by requiring MFA for delete actions\. MFA adds an extra authentication code on top of a user name and password\. This is a preventive guardrail with elective guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRRESTRICTS3DELETEWITHOUTMFA",
            "Effect": "Deny",
            "Action": [
                "s3:DeleteObject",
                "s3:DeleteBucket"
            ],
            "Resource": [
                "*"
            ],
            "Condition": {
                "BoolIfExists": {
                    "aws:MultiFactorAuthPresent": [
                        "false"
                    ]
                }
            }
        }
    ]
}
```

## Disallow Access to IAM Users Without MFA<a name="disallow-access-mfa"></a>

Protects your account by requiring MFA for all IAM users in the account\. MFA adds an extra authentication code on top of a username and password\. This guardrail detects whether MFA is enabled\. This guardrail does not change the status of the account\. This is a detective guardrail with elective guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check whether the IAM users have MFA enabled
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
  MaximumExecutionFrequency:
    Type: String
    Default: 1hour
    Description: The frequency that you want AWS Config to run evaluations for the rule.
    AllowedValues:
    - 1hour
    - 3hours
    - 6hours
    - 12hours
    - 24hours
Mappings:
  Settings:
    FrequencyMap:
      1hour   : One_Hour
      3hours  : Three_Hours
      6hours  : Six_Hours
      12hours : Twelve_Hours
      24hours : TwentyFour_Hours
Resources:
  CheckForIAMUserMFA:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether the AWS Identity and Access Management users have multi-factor authentication (MFA) enabled. The rule is COMPLIANT if MFA is enabled.
      Source:
        Owner: AWS
        SourceIdentifier: IAM_USER_MFA_ENABLED
      MaximumExecutionFrequency:
        !FindInMap
          - Settings
          - FrequencyMap
          - !Ref MaximumExecutionFrequency
```

## Disallow Console Access to IAM Users Without MFA<a name="disallow-console-access-mfa"></a>

Protects your account by requiring MFA for all IAM users in the console\. MFA adds an extra authentication code on top of a username and password\. This guardrail detects whether MFA is enabled\. This guardrail does not change the status of the account\. This is a detective guardrail with elective guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check whether MFA is enabled for all AWS IAM users that use a console password.
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
  MaximumExecutionFrequency:
    Type: String
    Default: 1hour
    Description: The frequency that you want AWS Config to run evaluations for the rule.
    AllowedValues:
    - 1hour
    - 3hours
    - 6hours
    - 12hours
    - 24hours
Mappings:
  Settings:
    FrequencyMap:
      1hour   : One_Hour
      3hours  : Three_Hours
      6hours  : Six_Hours
      12hours : Twelve_Hours
      24hours : TwentyFour_Hours
Resources:
  CheckForIAMUserConsoleMFA:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether AWS Multi-Factor Authentication (MFA) is enabled for all AWS Identity and Access Management (IAM) users that use a console password. The rule is COMPLIANT if MFA is enabled.
      Source:
        Owner: AWS
        SourceIdentifier: MFA_ENABLED_FOR_IAM_CONSOLE_ACCESS
      MaximumExecutionFrequency:
        !FindInMap
          - Settings
          - FrequencyMap
          - !Ref MaximumExecutionFrequency
```

## Disallow Amazon S3 Buckets That Are Not Versioning Enabled<a name="disallow-s3-no-versioning"></a>

Detects whether your Amazon S3 buckets are not versioning enabled\. Versioning allows you to recover objects from accidental deletion or overwrite\. This guardrail does not change the status of the account\. This is a detective guardrail with elective guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check whether versioning is enabled for your S3 buckets.
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
Resources:
  CheckForS3VersioningEnabled:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether versioning is enabled for your S3 buckets.
      Source:
        Owner: AWS
        SourceIdentifier: S3_BUCKET_VERSIONING_ENABLED
      Scope:
        ComplianceResourceTypes:
          - AWS::S3::Bucket
```