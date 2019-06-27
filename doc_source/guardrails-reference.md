# Guardrail Reference<a name="guardrails-reference"></a>

The following sections include a reference for each of the guardrails available in AWS Control Tower\. Each guardrail reference includes the details, artifacts, additional information, and considerations to keep in mind when enabling a specific guardrail on a OU in your landing zone\.

**Topics**
+ [Enable Encryption at Rest for Log Archive](#log-archive-encryption-enabled)
+ [Enable Access Logging for Log Archive](#log-archive-access-enabled)
+ [Disallow Policy Changes to Log Archive](#log-archive-policy-changes)
+ [Disallow Public Read Access to Log Archive](#log-archive-public-read)
+ [Disallow Public Write Access to Log Archive](#log-archive-public-write)
+ [Set a Retention Policy for Log Archive](#log-archive-retention-policy)
+ [Disallow Configuration Changes to CloudTrail](#cloudtrail-configuration-changes)
+ [Integrate CloudTrail Events with CloudWatch Logs](#cloudtrail-integrate-events-logs)
+ [Enable CloudTrail in All Available Regions](#cloudtrail-enable-region)
+ [Enable Integrity Validation for CloudTrail Log File](#cloudtrail-enable-validation)
+ [Disallow Changes to CloudWatch Set Up by AWS Control Tower](#cloudwatch-disallow-changes)
+ [Disallow Changes to AWS Config Aggregation Set Up by AWS Control Tower](#cloudwatch-disallow-config-changes)
+ [Disallow Configuration Changes to AWS Config](#config-disallow-changes)
+ [Enable AWS Config in All Available Regions](#config-enable-regions)
+ [Disallow Changes to AWS Config Rules Set Up by AWS Control Tower](#config-disallow-changes)
+ [Enable Encryption for Amazon EBS Volumes Attached to Amazon EC2 Instances](#ebs-enable-encryption)
+ [Disallow Changes to IAM Roles Set Up by AWS Control Tower](#iam-disallow-changes)
+ [Disallow Changes to Lambda Functions Set Up by AWS Control Tower](#lambda-disallow-changes)
+ [Disallow Internet Connection Through RDP](#rdp-disallow-internet)
+ [Disallow Internet Connection Through SSH](#ssh-disallow-internet)

## Enable Encryption at Rest for Log Archive<a name="log-archive-encryption-enabled"></a>

This guardrail enables encryption at rest for the Amazon S3 buckets in the log archive account\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled on the **Core** OU\.

The artifact for this guardrail is the following service control policy \(SCP\)\.

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

## Enable Access Logging for Log Archive<a name="log-archive-access-enabled"></a>

This guardrail enables access logging for the log archive shared account\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled on the **Core** OU\.

The artifact for this guardrail is the following SCP\.

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

## Disallow Policy Changes to Log Archive<a name="log-archive-policy-changes"></a>

This guardrail disallows any policy changes from occurring in the log archive shared account\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled on the **Core** OU\.

The artifact for this guardrail is the following SCP\.

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

## Disallow Public Read Access to Log Archive<a name="log-archive-public-read"></a>

This guardrail disallows public read access to the Amazon S3 buckets in the log archive shared account\. This is a detective guardrail with mandatory guidance\. By default, this guardrail is enabled on the **Core** OU\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check that your S3 buckets do not allow public access
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
Resources:
  CheckForS3PublicRead:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks that your S3 buckets do not allow public read access. If an S3 bucket policy or bucket ACL allows public read access, the bucket is noncompliant.
      Source:
        Owner: AWS
        SourceIdentifier: S3_BUCKET_PUBLIC_READ_PROHIBITED
      Scope:
        ComplianceResourceTypes:
          - AWS::S3::Bucket
```

## Disallow Public Write Access to Log Archive<a name="log-archive-public-write"></a>

This guardrail disallows public write access to the Amazon S3 buckets in the log archive shared account\. This is a detective guardrail with mandatory guidance\. By default, this guardrail is enabled on the **Core** OU\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check that your S3 buckets do not allow public access
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
Resources:
  CheckForS3PublicWrite:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks that your S3 buckets do not allow public write access. If an S3 bucket policy or bucket ACL allows public write access, the bucket is noncompliant.
      Source:
        Owner: AWS
        SourceIdentifier: S3_BUCKET_PUBLIC_WRITE_PROHIBITED
      Scope:
        ComplianceResourceTypes:
          - AWS::S3::Bucket
```

## Set a Retention Policy for Log Archive<a name="log-archive-retention-policy"></a>

This guardrail sets a retention policy on the logs in the log archive shared account of 365 days\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled on the **Core** OU\.

The artifact for this guardrail is the following SCP\.

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

## Disallow Configuration Changes to CloudTrail<a name="cloudtrail-configuration-changes"></a>

This guardrail prevents configuration changes to CloudTrail in your landing zone\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled on all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRCLOUDTRAILENABLED",
            "Effect": "Deny",
            "Action": [
                "cloudtrail:DeleteTrail",
                "cloudtrail:PutEventSelectors",
                "cloudtrail:StopLogging",
                "cloudtrail:UpdateTrail"
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

## Integrate CloudTrail Events with CloudWatch Logs<a name="cloudtrail-integrate-events-logs"></a>

This guardrail performs real\-time analysis of activity data by sending CloudTrail events to CloudWatch Logs log files\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled on all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRCLOUDTRAILENABLED",
            "Effect": "Deny",
            "Action": [
                "cloudtrail:DeleteTrail",
                "cloudtrail:PutEventSelectors",
                "cloudtrail:StopLogging",
                "cloudtrail:UpdateTrail"
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

## Enable CloudTrail in All Available Regions<a name="cloudtrail-enable-region"></a>

This guardrail enables CloudTrail in all available AWS Regions\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRCLOUDTRAILENABLED",
            "Effect": "Deny",
            "Action": [
                "cloudtrail:DeleteTrail",
                "cloudtrail:PutEventSelectors",
                "cloudtrail:StopLogging",
                "cloudtrail:UpdateTrail"
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

## Enable Integrity Validation for CloudTrail Log File<a name="cloudtrail-enable-validation"></a>

This guardrail enables integrity validation for the CloudTrail log file in all accounts and OUs\. This protects the integrity of account activity logs using CloudTrail log file validation, which creates a digitally signed digest file that contains a hash of each log that CloudTrail writes to Amazon S3\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRCLOUDTRAILENABLED",
            "Effect": "Deny",
            "Action": [
                "cloudtrail:DeleteTrail",
                "cloudtrail:PutEventSelectors",
                "cloudtrail:StopLogging",
                "cloudtrail:UpdateTrail"
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

## Disallow Changes to CloudWatch Set Up by AWS Control Tower<a name="cloudwatch-disallow-changes"></a>

This guardrail disallows changes to CloudWatch as it was configured by AWS Control Tower when you set up your landing zone\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRCLOUDWATCHEVENTPOLICY",
      "Effect": "Deny",
      "Action": [
        "events:PutRule",
        "events:PutTargets",
        "events:RemoveTargets",
        "events:DisableRule",
        "events:DeleteRule"
      ],
      "Resource": [
        "arn:aws:events:*:*:rule/aws-controltower-*"
      ],
      "Condition": {
        "ArnNotLike": {
          "aws:PrincipalARN": "arn:aws:iam::*:role/AWSControlTowerExecution"
        }
      }
    }
  ]
}
```

## Disallow Changes to AWS Config Aggregation Set Up by AWS Control Tower<a name="cloudwatch-disallow-config-changes"></a>

This guardrail disallows changes to the AWS Config aggregation settings made by AWS Control Tower to collect configuration and compliance data when you set up your landing zone\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRCONFIGRULETAGSPOLICY",
      "Effect": "Deny",
      "Action": [
        "config:TagResource",
        "config:UntagResource"
      ],
      "Resource": ["*"],
      "Condition": {
        "ArnNotLike": {
          "aws:PrincipalARN": "arn:aws:iam::*:role/AWSControlTowerExecution"
        },
        "ForAllValues:StringEquals": {
          "aws:TagKeys": "aws-control-tower"
        }
      }
    }
  ]
}
```

## Disallow Configuration Changes to AWS Config<a name="config-disallow-changes"></a>

This guardrail disallows configuration changes to AWS Config\. It ensures that AWS Config records resource configurations in a consistent manner by disallowing AWS Config settings changes\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRCONFIGENABLED",
            "Effect": "Deny",
            "Action": [
                "config:DeleteConfigurationRecorder",
                "config:DeleteDeliveryChannel",
                "config:DeleteRetentionConfiguration",
                "config:PutConfigurationRecorder",
                "config:PutDeliveryChannel",
                "config:PutRetentionConfiguration",
                "config:StopConfigurationRecorder"
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

## Enable AWS Config in All Available Regions<a name="config-enable-regions"></a>

This guardrail enables AWS Config in all available AWS Regions\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRCONFIGENABLED",
            "Effect": "Deny",
            "Action": [
                "config:DeleteConfigurationRecorder",
                "config:DeleteDeliveryChannel",
                "config:DeleteRetentionConfiguration",
                "config:PutConfigurationRecorder",
                "config:PutDeliveryChannel",
                "config:PutRetentionConfiguration",
                "config:StopConfigurationRecorder"
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

## Disallow Changes to AWS Config Rules Set Up by AWS Control Tower<a name="config-disallow-changes"></a>

This guardrail disallows changes to AWS Config Rules that were implemented by AWS Control Tower when the landing zone was set up\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRCONFIGRULEPOLICY",
      "Effect": "Deny",
      "Action": [
        "config:PutConfigRule",
        "config:DeleteConfigRule",
        "config:DeleteEvaluationResults",
        "config:DeleteConfigurationAggregator",
        "config:PutConfigurationAggregator"
      ],
      "Resource": ["*"],
      "Condition": {
        "ArnNotLike": {
          "aws:PrincipalARN": "arn:aws:iam::*:role/AWSControlTowerExecution"
        },
        "StringEquals": {
          "aws:ResourceTag/aws-control-tower": "managed-by-control-tower"
        }
      }
    }
  ]
}
```

## Enable Encryption for Amazon EBS Volumes Attached to Amazon EC2 Instances<a name="ebs-enable-encryption"></a>

This guardrail enables encryption for Amazon EBS volumes attached to Amazon EC2 instances in your landing zone\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail isn't enabled on any OUs\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check for encryption of all storage volumes attached to compute
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
Resources:
  CheckForEncryptedVolumes:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether EBS volumes that are in an attached state are encrypted.
      Source:
        Owner: AWS
        SourceIdentifier: ENCRYPTED_VOLUMES
      Scope:
        ComplianceResourceTypes:
          - AWS::EC2::Volume
```

## Disallow Changes to IAM Roles Set Up by AWS Control Tower<a name="iam-disallow-changes"></a>

This guardrail disallows changes to the IAM roles that were created by AWS Control Tower when the landing zone was set up\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRIAMROLEPOLICY",
      "Effect": "Deny",
      "Action": [
        "iam:AttachRolePolicy",
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:DeleteRolePermissionsBoundary",
        "iam:DeleteRolePolicy",
        "iam:DetachRolePolicy",
        "iam:PutRolePermissionsBoundary",
        "iam:PutRolePolicy",
        "iam:UpdateAssumeRolePolicy",
        "iam:UpdateRole",
        "iam:UpdateRoleDescription"
      ],
      "Resource": [
        "arn:aws:iam::*:role/aws-controltower-*",
        "arn:aws:iam::*:role/*AWSControlTower*"
      ],
      "Condition": {
        "ArnNotLike": {
          "aws:PrincipalARN":"arn:aws:iam::*:role/AWSControlTowerExecution"
        }
      }
    }
  ]
}
```

## Disallow Changes to Lambda Functions Set Up by AWS Control Tower<a name="lambda-disallow-changes"></a>

This guardrail disallows changes to Lambda functions set up by AWS Control Tower\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRLAMBDAFUNCTIONPOLICY",
      "Effect": "Deny",
      "Action": [
        "lambda:AddPermission",
        "lambda:CreateEventSourceMapping",
        "lambda:CreateFunction",
        "lambda:DeleteEventSourceMapping",
        "lambda:DeleteFunction",
        "lambda:DeleteFunctionConcurrency",
        "lambda:PutFunctionConcurrency",
        "lambda:RemovePermission",
        "lambda:UpdateEventSourceMapping",
        "lambda:UpdateFunctionCode",
        "lambda:UpdateFunctionConfiguration"
      ],
      "Resource": [
        "arn:aws:lambda:*:*:function:aws-controltower-*"
      ],
      "Condition": {
        "ArnNotLike": {
          "aws:PrincipalARN":"arn:aws:iam::*:role/AWSControlTowerExecution"
        }
      }
    }
  ]
}
```

## Disallow Internet Connection Through RDP<a name="rdp-disallow-internet"></a>

This guardrail disallows internet connections to Amazon EC2 instances through services like Remote Desktop Protocol \(RDP\)\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check whether security groups that are in use disallow unrestricted incoming TCP traffic to the specified ports.
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
  blockedPort1:
    Type: String
    Default: '20'
    Description: Blocked TCP port number.
  blockedPort2:
    Type: String
    Default: '21'
    Description: Blocked TCP port number.
  blockedPort3:
    Type: String
    Default: '3389'
    Description: Blocked TCP port number.
  blockedPort4:
    Type: String
    Default: '3306'
    Description: Blocked TCP port number.
  blockedPort5:
    Type: String
    Default: '4333'
    Description: Blocked TCP port number.
Conditions:
  blockedPort1:
    Fn::Not:
    - Fn::Equals:
      - ''
      - Ref: blockedPort1
  blockedPort2:
    Fn::Not:
    - Fn::Equals:
      - ''
      - Ref: blockedPort2
  blockedPort3:
    Fn::Not:
    - Fn::Equals:
      - ''
      - Ref: blockedPort3
  blockedPort4:
    Fn::Not:
    - Fn::Equals:
      - ''
      - Ref: blockedPort4
  blockedPort5:
    Fn::Not:
    - Fn::Equals:
      - ''
      - Ref: blockedPort5
Resources:
  CheckForRestrictedCommonPortsPolicy:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether security groups that are in use disallow unrestricted incoming TCP traffic to the specified ports.
      InputParameters:
        blockedPort1:
          Fn::If:
          - blockedPort1
          - Ref: blockedPort1
          - Ref: AWS::NoValue
        blockedPort2:
          Fn::If:
          - blockedPort2
          - Ref: blockedPort2
          - Ref: AWS::NoValue
        blockedPort3:
          Fn::If:
          - blockedPort3
          - Ref: blockedPort3
          - Ref: AWS::NoValue
        blockedPort4:
          Fn::If:
          - blockedPort4
          - Ref: blockedPort4
          - Ref: AWS::NoValue
        blockedPort5:
          Fn::If:
          - blockedPort5
          - Ref: blockedPort5
          - Ref: AWS::NoValue
      Scope:
        ComplianceResourceTypes:
        - AWS::EC2::SecurityGroup
      Source:
        Owner: AWS
        SourceIdentifier: RESTRICTED_INCOMING_TRAFFIC
```

## Disallow Internet Connection Through SSH<a name="ssh-disallow-internet"></a>

This guardrail disallows any internet connections through remote services like the Secure Shell \(SSH\) protocol\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check whether security groups that are in use disallow SSH
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
Resources:
  CheckForRestrictedSshPolicy:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether security groups that are in use disallow unrestricted incoming SSH traffic.
      Scope:
        ComplianceResourceTypes:
        - AWS::EC2::SecurityGroup
      Source:
        Owner: AWS
        SourceIdentifier: INCOMING_SSH_DISABLED
```