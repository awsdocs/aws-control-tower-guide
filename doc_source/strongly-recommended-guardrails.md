# Strongly Recommended Guardrails<a name="strongly-recommended-guardrails"></a>

Strongly recommended guardrails are based on best practices for well\-architected multi\-account environments\. These guardrails are not enabled by default, and can be disabled\. Following, you'll find a reference for each of the strongly recommended guardrails available in AWS Control Tower\.

**Topics**
+ [Disallow Creation of Access Keys for the Root User](#disallow-root-access-keys)
+ [Disallow Actions as a Root User](#disallow-root-auser-actions)
+ [Enable Encryption for Amazon EBS Volumes Attached to Amazon EC2 Instances](#ebs-enable-encryption)
+ [Disallow Internet Connection Through RDP](#rdp-disallow-internet)
+ [Disallow Internet Connection Through SSH](#ssh-disallow-internet)
+ [Enable MFA for the Root User](#enable-root-mfa)
+ [Disallow Public Read Access to Amazon S3 Buckets](#s3-disallow-public-read)
+ [Disallow Public Write Access to Amazon S3 Buckets](#s3-disallow-public-write)
+ [Disallow Amazon EBS Volumes That Are Unattached to An Amazon EC2 Instance](#disallow-unattached-ebs)
+ [Disallow Amazon EC2Instance Types That Are Not Amazon EBS\-Optimized](#disallow-not-ebs-optimized)
+ [Disallow Public Access to Amazon RDS Database Instances](#disallow-rds-public-access)
+ [Disallow Public Access to Amazon RDS Database Snapshots](#disallow-rds-snapshot-public-access)
+ [Disallow Amazon RDS Database Instances That Are Not Storage Encrypted](#disallow-rds-storage-unencrypted)

## Disallow Creation of Access Keys for the Root User<a name="disallow-root-access-keys"></a>

Secures your AWS accounts by disallowing creation of access keys for the root user\. We recommend that you instead create access keys for the IAM users with limited permissions to interact with your AWS account\. This is a preventive guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following SCP\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GRRESTRICTROOTUSERACCESSKEYS",
            "Effect": "Deny",
            "Action": "iam:CreateAccessKey",
            "Resource": [
                "*"
            ],
            "Condition": {
                "StringLike": {
                    "aws:PrincipalArn": [
                        "arn:aws:iam::*:root"
                    ]
                }
            }
        }
    ]
}
```

## Disallow Actions as a Root User<a name="disallow-root-auser-actions"></a>

Secures your AWS accounts by disallowing account access with root user credentials, which are credentials of the account owner that allow unrestricted access to all resources in the account\. Instead, we recommend that you create AWS Identity and Access Management \(IAM\) users for everyday interaction with your AWS account\. This is a preventive guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following SCP\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRRESTRICTROOTUSER",
      "Effect": "Deny",
      "Action": "*",
      "Resource": [
        "*"
      ],
      "Condition": {
        "StringLike": {
          "aws:PrincipalArn": [
            "arn:aws:iam::*:root"
          ]
        }
      }
    }
  ]
}
```

## Enable Encryption for Amazon EBS Volumes Attached to Amazon EC2 Instances<a name="ebs-enable-encryption"></a>

This guardrail detects whether encryption is enabled for Amazon EBS volumes attached to Amazon EC2 instances in your landing zone\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail isn't enabled on any OUs\.

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

## Disallow Internet Connection Through RDP<a name="rdp-disallow-internet"></a>

This guardrail detects whether internet connections are enabled to Amazon EC2 instances through services like Remote Desktop Protocol \(RDP\)\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

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

This guardrail detects whether any internet connections are allowed through remote services like the Secure Shell \(SSH\) protocol\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

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

## Enable MFA for the Root User<a name="enable-root-mfa"></a>

This guardrail detects whether multi\-factor authentication \(MFA\) is enabled for the root user of the master account\. MFA reduces vulnerability risks from weak authentication by adding an extra authentication code on top of a user name and password\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to require MFA for root access to accounts
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
  MaximumExecutionFrequency:
    Type: String
    Default: 24hours
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
  CheckForRootMfa:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether the root user of your AWS account requires multi-factor authentication for console sign-in.
      Source:
        Owner: AWS
        SourceIdentifier: ROOT_ACCOUNT_MFA_ENABLED
      MaximumExecutionFrequency:
        !FindInMap
          - Settings
          - FrequencyMap
          - !Ref MaximumExecutionFrequency
```

## Disallow Public Read Access to Amazon S3 Buckets<a name="s3-disallow-public-read"></a>

This guardrail detects whether public read access is allowed to Amazon S3 buckets\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

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

## Disallow Public Write Access to Amazon S3 Buckets<a name="s3-disallow-public-write"></a>

This guardrail detects whether public write access is allowed to Amazon S3 buckets\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

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

## Disallow Amazon EBS Volumes That Are Unattached to An Amazon EC2 Instance<a name="disallow-unattached-ebs"></a>

Detects whether an Amazon EBS volume persists independently from an Amazon EC2 instance\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check whether EBS volumes are attached to EC2 instances
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
  deleteOnTermination:
    Type: 'String'
    Default: 'None'
    Description: 'Check for Delete on termination'
Conditions:
  deleteOnTermination:
    Fn::Not:
    - Fn::Equals:
      - 'None'
      - Ref: deleteOnTermination
Resources:
  CheckForEc2VolumesInUse:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether EBS volumes are attached to EC2 instances
      InputParameters:
        deleteOnTermination:
          Fn::If:
            - deleteOnTermination
            - Ref: deleteOnTermination
            - Ref: AWS::NoValue
      Source:
        Owner: AWS
        SourceIdentifier: EC2_VOLUME_INUSE_CHECK
      Scope:
        ComplianceResourceTypes:
          - AWS::EC2::Volume
```

## Disallow Amazon EC2Instance Types That Are Not Amazon EBS\-Optimized<a name="disallow-not-ebs-optimized"></a>

Detects whether Amazon EC2 instances are launched without an Amazon EBS volume that is performance optimized\. Amazon EBS\-optimized volumes minimize contention between Amazon EBS I/O and other traffic from your instance\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check whether EBS optimization is enabled for your EC2 instances that can be EBS-optimized
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
Resources:
  CheckForEbsOptimizedInstance:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether EBS optimization is enabled for your EC2 instances that can be EBS-optimized
      Source:
        Owner: AWS
        SourceIdentifier: EBS_OPTIMIZED_INSTANCE
      Scope:
        ComplianceResourceTypes:
          - AWS::EC2::Instance
```

## Disallow Public Access to Amazon RDS Database Instances<a name="disallow-rds-public-access"></a>

Detects whether your Amazon RDS database instances have public access enabled\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check whether Amazon RDS instances are not publicly accessible.
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
Resources:
  CheckForRdsPublicAccess:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether the Amazon Relational Database Service (RDS) instances are not publicly accessible. The rule is non-compliant if the publiclyAccessible field is true in the instance configuration item.
      Source:
        Owner: AWS
        SourceIdentifier: RDS_INSTANCE_PUBLIC_ACCESS_CHECK
      Scope:
        ComplianceResourceTypes:
          - AWS::RDS::DBInstance
```

## Disallow Public Access to Amazon RDS Database Snapshots<a name="disallow-rds-snapshot-public-access"></a>

Detects whether your Amazon RDS database snapshots have public access enabled\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Checks if Amazon Relational Database Service (Amazon RDS) snapshots are public.
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
Resources:
  CheckForRdsStorageEncryption:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks if Amazon Relational Database Service (Amazon RDS) snapshots are public. The rule is non-compliant if any existing and new Amazon RDS snapshots are public.
      Source:
        Owner: AWS
        SourceIdentifier: RDS_SNAPSHOTS_PUBLIC_PROHIBITED
      Scope:
        ComplianceResourceTypes:
          - AWS::RDS::DBSnapshot
```

## Disallow Amazon RDS Database Instances That Are Not Storage Encrypted<a name="disallow-rds-storage-unencrypted"></a>

Detects whether your Amazon RDS database instances are not encrypted at rest, along with their automated backups, Read Replicas, and snapshots\. This guardrail does not change the status of the account\. This is a detective guardrail with strongly recommended guidance\. By default, this guardrail is not enabled\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rules to check whether storage encryption is enabled for your RDS DB instances
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
Resources:
  CheckForRdsStorageEncryption:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks whether storage encryption is enabled for your RDS DB instances.
      Source:
        Owner: AWS
        SourceIdentifier: RDS_STORAGE_ENCRYPTED
      Scope:
        ComplianceResourceTypes:
          - AWS::RDS::DBInstance
```