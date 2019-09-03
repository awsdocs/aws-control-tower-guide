# Mandatory Guardrails<a name="mandatory-guardrails"></a>

Mandatory guardrails are enabled by default when you set up your landing zone and can't be disabled\. Following, you'll find a reference for each of the mandatory guardrails available in AWS Control Tower\.

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

This guardrail detects whether public read access is enabled to the Amazon S3 buckets in the log archive shared account\. This guardrail does not change the status of the account\. This is a detective guardrail with mandatory guidance\. By default, this guardrail is enabled on the **Core** OU\.

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

This guardrail detects whether public write access is enabled to the Amazon S3 buckets in the log archive shared account\. This guardrail does not change the status of the account\. This is a detective guardrail with mandatory guidance\. By default, this guardrail is enabled on the **Core** OU\.

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

This guardrail enables integrity validation for the CloudTrail log file in all accounts and OUs\. It protects the integrity of account activity logs using CloudTrail log file validation, which creates a digitally signed digest file that contains a hash of each log that CloudTrail writes to Amazon S3\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

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

### Disallow Changes to IAM Roles Set Up by AWS Control Tower<a name="iam-disallow-changes"></a>

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

### Disallow Changes to Lambda Functions Set Up by AWS Control Tower<a name="lambda-disallow-changes"></a>

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

### Disallow Changes to Amazon SNS Set Up by AWS Control Tower<a name="sns-disallow-changes"></a>

This guardrail disallows changes to Amazon SNS set up by AWS Control Tower\. It protects the integrity of Amazon SNS notification settings for your landing zone\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRSNSTOPICPOLICY",
      "Effect": "Deny",
      "Action": [
        "sns:AddPermission",
        "sns:CreateTopic",
        "sns:DeleteTopic",
        "sns:RemovePermission",
        "sns:SetTopicAttributes"
      ],
      "Resource": [
        "arn:aws:sns:*:*:aws-controltower-*"
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

### Disallow Changes to Amazon SNS Subscriptions Set Up by AWS Control Tower<a name="sns-subscriptions-disallow-changes"></a>

This guardrail disallows changes to Amazon SNS subscriptions set up by AWS Control Tower\. It protects the integrity of Amazon SNS subscriptions settings for your landing zone\. This is a preventive guardrail with mandatory guidance\. By default, this guardrail is enabled in all OUs\.

The artifact for this guardrail is the following SCP\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRSNSSUBSCRIPTIONPOLICY",
      "Effect": "Deny",
      "Action": [
        "sns:Subscribe",
        "sns:Unsubscribe"
      ],
      "Resource": [
        "arn:aws:sns:*:*:aws-controltower-SecurityNotifications"
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