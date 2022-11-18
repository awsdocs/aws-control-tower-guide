# Elective controls<a name="elective-controls"></a>

Elective controls enable you to lock down or track attempts at performing commonly restricted actions in an AWS enterprise environment\. These controls are not enabled by default, and can be disabled\. Following, you'll find a reference for the elective controls available in AWS Control Tower\. The elective controls specifically for data residency are collected into a separate section, [Controls that enhance data residency protection](data-residency-controls.md)\. 

**Topics**
+ [Disallow Changes to Encryption Configuration for Amazon S3 Buckets \[Previously: Enable Encryption at Rest for Log Archive\]](#log-archive-encryption-enabled)
+ [Disallow Changes to Logging Configuration for Amazon S3 Buckets \[Previously: Enable Access Logging for Log Archive\]](#log-archive-access-enabled)
+ [Disallow Changes to Bucket Policy for Amazon S3 Buckets \[Previously: Disallow Policy Changes to Log Archive\]](#log-archive-policy-changes)
+ [Disallow Changes to Lifecycle Configuration for Amazon S3 Buckets \[Previously: Set a Retention Policy for Log Archive\]](#log-archive-retention-policy)
+ [Disallow Changes to Replication Configuration for Amazon S3 Buckets](#disallow-s3-ccr)
+ [Disallow Delete Actions on Amazon S3 Buckets Without MFA](#disallow-s3-delete-mfa)
+ [Detect Whether MFA is Enabled for AWS IAM Users](#disallow-access-mfa)
+ [Detect Whether MFA is Enabled for AWS IAM Users of the AWS Console](#disallow-console-access-mfa)
+ [Detect Whether Versioning for Amazon S3 Buckets is Enabled](#disallow-s3-no-versioning)
+ [Controls that enhance data residency protection](data-residency-controls.md)

## Disallow Changes to Encryption Configuration for Amazon S3 Buckets \[Previously: Enable Encryption at Rest for Log Archive\]<a name="log-archive-encryption-enabled"></a>

This control disallows changes to encryption for all Amazon S3 buckets\. This is a preventive control with elective guidance\. By default, this control is not enabled\.

The artifact for this control is the following service control policy \(SCP\)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRAUDITBUCKETENCRYPTIONENABLED",
            "Effect": "Deny",
            "Action": [
                "s3:PutEncryptionConfiguration"
            ],
            "Resource": ["*"],
            "Condition": {
                "ArnNotLike": {
                    "aws:PrincipalARN":"arn:aws:iam::*:role/AWSControlTowerExecution"
                }
            }
        }
    ]
}
```

## Disallow Changes to Logging Configuration for Amazon S3 Buckets \[Previously: Enable Access Logging for Log Archive\]<a name="log-archive-access-enabled"></a>

This control disallows changes to logging configuration for all Amazon S3 buckets\.  This is a preventive control with elective guidance\. By default, this control is not enabled\.

The artifact for this control is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRAUDITBUCKETLOGGINGENABLED",
            "Effect": "Deny",
            "Action": [
                "s3:PutBucketLogging"
            ],
            "Resource": ["*"],
            "Condition": {
                "ArnNotLike": {
                    "aws:PrincipalARN":"arn:aws:iam::*:role/AWSControlTowerExecution"
                }
            }
        }
    ]
}
```

## Disallow Changes to Bucket Policy for Amazon S3 Buckets \[Previously: Disallow Policy Changes to Log Archive\]<a name="log-archive-policy-changes"></a>

This control disallows changes to bucket policy for all Amazon S3 buckets\. This is a preventive control with elective guidance\. By default, this control is not enabled\.

The artifact for this control is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRAUDITBUCKETPOLICYCHANGESPROHIBITED",
            "Effect": "Deny",
            "Action": [
                "s3:PutBucketPolicy"
            ],
            "Resource": ["*"],
            "Condition": {
                "ArnNotLike": {
                    "aws:PrincipalARN":"arn:aws:iam::*:role/AWSControlTowerExecution"
                }
            }
        }
    ]
}
```

## Disallow Changes to Lifecycle Configuration for Amazon S3 Buckets \[Previously: Set a Retention Policy for Log Archive\]<a name="log-archive-retention-policy"></a>

This control disallows lifecycle configuration changes for all Amazon S3 buckets\.  This is a preventive control with elective guidance\. By default, this control is not enabled\.

The artifact for this control is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRAUDITBUCKETRETENTIONPOLICY",
            "Effect": "Deny",
            "Action": [
                "s3:PutLifecycleConfiguration"
            ],
            "Resource": ["*"], 
            "Condition": {
                "ArnNotLike": {
                    "aws:PrincipalARN":"arn:aws:iam::*:role/AWSControlTowerExecution"
                }
            }
        }
    ]
}
```

## Disallow Changes to Replication Configuration for Amazon S3 Buckets<a name="disallow-s3-ccr"></a>

Prevents changes to the way your Amazon S3 buckets have been set up to handle replication within Regions or across Regions\. For example, if you set up your buckets with single\-region replication, to restrict the location of your Amazon S3 data to a single AWS Region \(thereby disabling any automatic, asynchronous copying of objects across buckets to other AWS Regions\), then this control prevents that replication setting from being changed\. This is a preventive control with elective guidance\. By default, this control is not enabled\.

The artifact for this control is the following SCP\.

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

Protects your Amazon S3 buckets by requiring MFA for delete actions\. MFA requires an extra authentication code after the user name and password are successful\. This is a preventive control with elective guidance\. By default, this control is not enabled\.

The artifact for this control is the following SCP\.

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

## Detect Whether MFA is Enabled for AWS IAM Users<a name="disallow-access-mfa"></a>

This control detects whether MFA is enabled for AWS IAMusers\. You can protect your account by requiring MFA for all AWS IAM users in the account\. MFA requires an additional authentication code after the user name and password are successful\. This control does not change the status of the account\. This is a detective control with elective guidance\. By default, this control is not enabled\.

The artifact for this control is the following AWS Config rule\.

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

## Detect Whether MFA is Enabled for AWS IAM Users of the AWS Console<a name="disallow-console-access-mfa"></a>

Protects your account by requiring MFA for all AWS IAM users in the console\. MFA reduces vulnerability risks from weak authentication by requiring an additional authentication code after the user name and password are successful\. This control detects whether MFA is enabled\. This control does not change the status of the account\. This is a detective control with elective guidance\. By default, this control is not enabled\.

The artifact for this control is the following AWS Config rule\.

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

## Detect Whether Versioning for Amazon S3 Buckets is Enabled<a name="disallow-s3-no-versioning"></a>

Detects whether your Amazon S3 buckets are enabled for versioning\. Versioning allows you to recover objects from accidental deletion or overwrite\. This control does not change the status of the account\. This is a detective control with elective guidance\. By default, this control is not enabled\.

The artifact for this control is the following AWS Config rule\.

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