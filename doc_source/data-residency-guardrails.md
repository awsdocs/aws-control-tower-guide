# Guardrails that enhance data residency protection<a name="data-residency-guardrails"></a>

These elective guardrails complement your enterprise's data residency posture\. By applying these guardrails together, you can set up your multi\-account environment to help detect and inhibit the purposeful or accidental creation, sharing, or copying of data, outside of your selected AWS Region or Regions\.

These guardrails take effect at the OU level, and they apply to all member accounts within the OU\.

**Topics**
+ [Deny access to AWS based on the requested AWS Region](#primary-region-deny-policy)
+ [Disallow internet access for an Amazon VPC instance managed by a customer](#disallow-vpc-internet-access)
+ [Disallow Amazon Virtual Private Network \(VPN\) connections](#prevent-vpn-connection)
+ [Disallow cross\-region networking for Amazon EC2, Amazon CloudFront, and AWS Global Accelerator](#prevent-cross-region-networking)
+ [Detect whether public IP addresses for Amazon EC2 autoscaling are enabled through launch configurations](#autoscaling-launch-config-public-ip-disabled)
+ [Detect whether replication instances for AWS Database Migration Service are public](#dms-replication-not-public)
+ [Detect whether Amazon EBS snapshots are restorable by all AWS accounts](#ebs-snapshot-public-restorable-check)
+ [Detect whether any Amazon EC2 instance has an associated public IPv4 address](#ec2-instance-no-public-ip)
+ [Detect whether Amazon S3 settings to block public access are set as true for the account](#s3-account-level-public-access-blocks-periodic)
+ [Detects whether an Amazon EKS endpoint is blocked from public access](#eks-endpoint-no-public-access)
+ [Detect whether an Amazon OpenSearch Service domain is in Amazon VPC](#elasticsearch-in-vpc-only)
+ [Detect whether any Amazon EMR cluster master nodes have public IP addresses](#emr-master-no-public-ip)
+ [Detect whether the AWS Lambda function policy attached to the Lambda resource blocks public access](#lambda-function-public-access-prohibited)
+ [Detect whether public routes exist in the route table for an Internet Gateway \(IGW\)](#no-unrestricted-route-to-igw)
+ [Detect whether Amazon Redshift clusters are blocked from public access](#redshift-cluster-public-access-check)
+ [Detect whether an Amazon SageMaker notebook instance allows direct internet access](#sagemaker-notebook-no-direct-internet-access)
+ [Detect whether any Amazon VPC subnets are assigned a public IP address](#subnet-auto-assign-public-ip-disabled)
+ [Detect whether AWS Systems Manager documents owned by the account are public](#ssm-document-not-public)

## Deny access to AWS based on the requested AWS Region<a name="primary-region-deny-policy"></a>

*This guardrail is commonly referred to as the Region deny guardrail\.*

This guardrail disallows access to unlisted operations in global and regional services outside of the specified Regions\. That includes all Regions where AWS Control Tower is not available, as well as all Regions not selected for governance in the **Landing zone settings** page\. Actions are allowed as usual in Regions with **Governed** status\.

This is an elective guardrail with preventive guidance\. It is the primary guardrail associated with the **Region deny** action\. For more information, see [Configure the Region deny guardrail](region-deny.md)\.

The format for this guardrail is based on the following SCP\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRREGIONDENY",
      "Effect": "Deny",
      "NotAction": [
        "a4b:*",
        "acm:*",
        "aws-marketplace-management:*",
        "aws-marketplace:*",
        "aws-portal:*",
        "budgets:*",
        "ce:*",
        "chime:*",
        "cloudfront:*",
        "config:*",
        "cur:*",
        "directconnect:*",
        "ec2:DescribeRegions",
        "ec2:DescribeTransitGateways",
        "ec2:DescribeVpnGateways",
        "fms:*",
        "globalaccelerator:*",
        "health:*",
        "iam:*",
        "importexport:*",
        "kms:*",
        "mobileanalytics:*",
        "networkmanager:*",
        "organizations:*",
        "pricing:*",
        "route53:*",
        "route53domains:*",
        "s3:GetBucketPublicAccessBlock",
        "s3:ListAllMyBuckets",
        "s3:GetBucketLocation",
        "shield:*",
        "sts:*",
        "support:*",
        "trustedadvisor:*",
        "waf-regional:*",
        "waf:*",
        "wafv2:*",
        "access-analyzer:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": []
        },
        "ArnNotLike": {
          "aws:PrincipalARN": [
            "arn:aws:iam::*:role/AWSControlTowerExecution"
          ]
        }
      }
    }
  ]
}
```

Based on this example SCP format, AWS Control Tower adds your governed Regions into the `aws:RequestedRegion` statement\. You cannot exclude your home Region\. Actions not listed in the SCP are not permitted\.

## Disallow internet access for an Amazon VPC instance managed by a customer<a name="disallow-vpc-internet-access"></a>

This guardrail disallows internet access for an Amazon Virtual Private Cloud \(VPC\) instance managed by a customer, rather than by an AWS service\.

**Important**  
If you provision Account Factory accounts with VPC internet access settings enabled, that Account Factory setting overrides this guardrail\. To avoid enabling internet access for newly provisioned accounts, you must change the setting in Account Factory\. For more information, see [Walkthrough: Configuring AWS Control Tower Without a VPC](configure-without-vpc.md)\.
+ This guardrail does not apply to VPCs managed by AWS services\.
+ Existing VPCs that have internet access retain their internet access\. It applies to new instances only\. After this guardrail is applied, access cannot be changed\.

This is a preventive guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\. 

The artifact for this guardrail is the following service control policy \(SCP\)\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRDISALLOWVPCINTERNETACCESS",
      "Effect": "Deny",
      "Action": [
        "ec2:CreateInternetGateway",
        "ec2:AttachInternetGateway",
        "ec2:CreateEgressOnlyInternetGateway",
        "ec2:CreateDefaultVpc",
        "ec2:CreateDefaultSubnet",
        "ec2:CreateCarrierGateway"
      ],
      "Resource": [
        "*"
      ],
      "Condition": {
        "ArnNotLike": {
          "aws:PrincipalArn": [
            "arn:aws:iam::*:role/AWSControlTowerExecution"
          ]
        }
      }
    }
  ]
}
```

## Disallow Amazon Virtual Private Network \(VPN\) connections<a name="prevent-vpn-connection"></a>

This guardrail prevents Virtual Private Network \(VPN\) connections \(Site\-to\-Site VPN and Client VPN\) to an Amazon Virtual Private Cloud \(VPC\)\. 

**Note**  
Existing VPCs that have internet access retain their internet access\.

This is a preventive guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\. 

The artifact for this guardrail is the following service control policy \(SCP\)\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRDISALLOWVPNCONNECTIONS",
      "Effect": "Deny",
      "Action": [
        "ec2:CreateVPNGateway",
        "ec2:AttachVPNGateway",
        "ec2:CreateCustomerGateway",
        "ec2:CreateVpnConnection",
        "ec2:ModifyVpnConnection",
        "ec2:CreateClientVpnEndpoint",
        "ec2:ModifyClientVpnEndpoint",
        "ec2:AssociateClientVpnTargetNetwork",
        "ec2:AuthorizeClientVpnIngress"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```

## Disallow cross\-region networking for Amazon EC2, Amazon CloudFront, and AWS Global Accelerator<a name="prevent-cross-region-networking"></a>

This guardrail prevents configuring cross\-region networking connections from Amazon EC2, Amazon CloudFront, and AWS Global Accelerator services\. It prevents VPC peering and transit gateway peering\.

**Note**  
This guardrail prevents Amazon EC2 VPC peering and Amazon EC2 transit gateway peering within a single Region, as well as across Regions\. For this reason, this guardrail may affect certain workloads in addition to your data residency posture\.

This is a preventive guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\. 

The artifact for this guardrail is the following service control policy \(SCP\)\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GRDISALLOWCROSSREGIONNETWORKING",
      "Effect": "Deny",
      "Action": [
        "ec2:CreateVpcPeeringConnection",
        "ec2:AcceptVpcPeeringConnection",
        "ec2:CreateTransitGatewayPeeringAttachment",
        "ec2:AcceptTransitGatewayPeeringAttachment",
        "cloudfront:CreateDistribution",
        "cloudfront:UpdateDistribution",
        "globalaccelerator:Create*",
        "globalaccelerator:Update*"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```

## Detect whether public IP addresses for Amazon EC2 autoscaling are enabled through launch configurations<a name="autoscaling-launch-config-public-ip-disabled"></a>

This guardrail detects whether Amazon EC2 Auto Scaling groups have public IP addresses enabled through launch configurations\. 

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if the launch configuration for an autoscaling group sets the value of the field `AssociatePublicIpAddress` set as **True**\. 

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether public IP addresses for Amazon EC2 Auto Scaling are enabled through launch configurations
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
Resources:
  AutoscalingLaunchConfigPublicIpDisabled:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether Amazon EC2 Auto Scaling groups have public IP addresses enabled through launch configurations. This rule is NON_COMPLIANT if the launch configuration for an Auto Scaling group has the value of the field AssociatePublicIpAddress set as True.
      Scope:
        ComplianceResourceTypes:
          - AWS::AutoScaling::LaunchConfiguration
      Source:
        Owner: AWS
        SourceIdentifier: AUTOSCALING_LAUNCH_CONFIG_PUBLIC_IP_DISABLED
```

## Detect whether replication instances for AWS Database Migration Service are public<a name="dms-replication-not-public"></a>

This guardrail detects whether AWS Database Migration Service replication instances are public\. 

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if the value of the `PubliclyAccessible` field is set as **True**\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether replication instances for AWS Database Migration Service are public
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
  MaximumExecutionFrequency:
    Type: String
    Default: 24hours
    Description: The frequency at which AWS Config will run evaluations for the rule.
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
  DmsReplicationNotPublic:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether AWS Database Migration Service replication instances are public. The rule is NON_COMPLIANT if the value of the PubliclyAccessible field is set as True.
      Source:
        Owner: AWS
        SourceIdentifier: DMS_REPLICATION_NOT_PUBLIC
      MaximumExecutionFrequency:
        !FindInMap
        - Settings
        - FrequencyMap
        - !Ref MaximumExecutionFrequency
```

## Detect whether Amazon EBS snapshots are restorable by all AWS accounts<a name="ebs-snapshot-public-restorable-check"></a>

This guardrail detects whether all AWS accounts have access to restore Amazon EBS snapshots\. 

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if any snapshots have the `RestorableByUserIds` field set to the value **All**\. In that case, the Amazon EBS snapshots are public\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether Amazon EBS snapshots are restorable by all AWS accounts
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
  MaximumExecutionFrequency:
    Type: String
    Default: 24hours
    Description: The frequency at which AWS Config will run evaluations for the rule.
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
  EbsSnapshotPublicRestorableCheck:
    Type: AWS::Config::ConfigRule
 
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether all AWS accounts have access to restore Amazon EBS snapshots. The rule is NON_COMPLIANT if any snapshots have the RestorableByUserIds field set to the value All. In that case, the Amazon EBS snapshots are public.
      Source:
        Owner: AWS
        SourceIdentifier: EBS_SNAPSHOT_PUBLIC_RESTORABLE_CHECK
      MaximumExecutionFrequency:
        !FindInMap
        - Settings
        - FrequencyMap
        - !Ref MaximumExecutionFrequency
```

## Detect whether any Amazon EC2 instance has an associated public IPv4 address<a name="ec2-instance-no-public-ip"></a>

This guardrail detects whether an Amazon Elastic Compute Cloud \(Amazon EC2\) instance has an associated public IPv4 address\. This guardrail applies only to IPv4 addresses\.

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\. 

**In the console:**
+ The rule shows **Non\-compliant** status if the public IP field is present in the Amazon EC2 instance configuration item\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether any Amazon EC2 instance has an associated public IPv4 address
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
Resources:
  Ec2InstanceNoPublicIp:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether an Amazon Elastic Compute Cloud (Amazon EC2) instance has an associated public IPv4 address. The rule is NON_COMPLIANT if the public IP field is present in the Amazon EC2 instance configuration item.
      Scope:
        ComplianceResourceTypes:
        - AWS::EC2::Instance
      Source:
        Owner: AWS
        SourceIdentifier: EC2_INSTANCE_NO_PUBLIC_IP
```

## Detect whether Amazon S3 settings to block public access are set as true for the account<a name="s3-account-level-public-access-blocks-periodic"></a>

This guardrail periodically detects whether the required Amazon S3 settings to block public access are configured as true for the account, rather than for a bucket or an access point\.

**In the console:**
+ The rule shows **Non\-compliant** status if at least one of the settings is false\.

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to check whether Amazon S3 settings to block public access are set as true for the account.
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
  PublicAccessBlockSetting:
    Type: 'String'
    Default: 'True'
  MaximumExecutionFrequency:
    Type: String
    Default: 24hours
    Description: The frequency at which AWS Config will run evaluations for the rule.
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
  CheckForS3PublicAccessBlock:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Checks the Amazon S3 settings to block public access are set as true for the account. The rule is non-compliant if at-least one of the settings is false.
      Source:
        Owner: AWS
        SourceIdentifier: S3_ACCOUNT_LEVEL_PUBLIC_ACCESS_BLOCKS_PERIODIC
      Scope:
        ComplianceResourceTypes:
          - AWS::S3::AccountPublicAccessBlock
      InputParameters:
        IgnorePublicAcls: !Ref PublicAccessBlockSetting
        BlockPublicPolicy: !Ref PublicAccessBlockSetting
        BlockPublicAcls: !Ref PublicAccessBlockSetting
        RestrictPublicBuckets: !Ref PublicAccessBlockSetting
      MaximumExecutionFrequency:
        !FindInMap
        - Settings
        - FrequencyMap
        - !Ref MaximumExecutionFrequency
```

## Detects whether an Amazon EKS endpoint is blocked from public access<a name="eks-endpoint-no-public-access"></a>

This guardrail detects whether an Amazon Elastic Kubernetes Service \(Amazon EKS\) endpoint is blocked from public access\. 

 This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if the endpoint is publicly accessible\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether an Amazon EKS endpoint is blocked from public access
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
  MaximumExecutionFrequency:
    Type: String
    Default: 24hours
    Description: The frequency at which AWS Config will run evaluations for the rule.
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
 EKSEndpointNoPublicAccess:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether an Amazon Elastic Kubernetes Service (Amazon EKS) endpoint is publicly accessible. The rule is NON_COMPLIANT if the endpoint is publicly accessible.
      Source:
        Owner: AWS
        SourceIdentifier: EKS_ENDPOINT_NO_PUBLIC_ACCESS
      MaximumExecutionFrequency:
        !FindInMap
        - Settings
        - FrequencyMap
        - !Ref MaximumExecutionFrequency
```

## Detect whether an Amazon OpenSearch Service domain is in Amazon VPC<a name="elasticsearch-in-vpc-only"></a>

This guardrail detects whether an Amazon OpenSearch Service domain is in Amazon VPC\. 

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if the OpenSearch Service domain endpoint is public\.

The artifact for this guardrail is the following AWS Config rule\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether an Amazon OpenSearch Service domain is in Amazon VPC
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
  MaximumExecutionFrequency:
    Type: String
    Default: 24hours
    Description: The frequency at which AWS Config will run evaluations for the rule.
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
  ElasticsearchInVpcOnly:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether Amazon OpenSearch Service domains are in Amazon Virtual Private Cloud (Amazon VPC). The rule is NON_COMPLIANT if the OpenSearch Service domain endpoint is public.
      Source:
        Owner: AWS
        SourceIdentifier: ELASTICSEARCH_IN_VPC_ONLY
      MaximumExecutionFrequency:
        !FindInMap
        - Settings
        - FrequencyMap
        - !Ref MaximumExecutionFrequency
```

## Detect whether any Amazon EMR cluster master nodes have public IP addresses<a name="emr-master-no-public-ip"></a>

This guardrail detects whether any Amazon EMR cluster master nodes have public IP addresses\.

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs

**In the console:**
+ The rule shows **Non\-compliant** status if a master node has a public IP address\.
+ This guardrail checks clusters that are in RUNNING or WAITING state\.

The artifact for this guardrail is the following AWS Config rule\. 

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether any Amazon EMR cluster master nodes have public IP addresses
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
  MaximumExecutionFrequency:
    Type: String
    Default: 24hours
    Description: The frequency at which AWS Config will run evaluations for the rule.
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
  EmrMasterNoPublicIp:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether any Amazon Elastic MapReduce (EMR) cluster master nodes have public IP addresses. The rule is NON_COMPLIANT if a master node has a public IP. This guardrail checks clusters that are in RUNNING or WAITING state.
      Source:
        Owner: AWS
        SourceIdentifier: EMR_MASTER_NO_PUBLIC_IP
      MaximumExecutionFrequency:
        !FindInMap
        - Settings
        - FrequencyMap
        - !Ref MaximumExecutionFrequency
```

## Detect whether the AWS Lambda function policy attached to the Lambda resource blocks public access<a name="lambda-function-public-access-prohibited"></a>

This guardrail detects whether the AWS Lambda function policy attached to the Lambda resource blocks public access\. 

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if the Lambda function policy allows public access\. 

The artifact for this guardrail is the following AWS Config rule\. 

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether the AWS Lambda function policy attached to the Lambda resource blocks public access
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
Resources:
  LambdaFunctionPublicAccessProhibited:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether the AWS Lambda function policy attached to the Lambda resource prohibits public access. The rule is NON_COMPLIANT if the Lambda function policy allows public access.
      Scope:
        ComplianceResourceTypes:
        - AWS::Lambda::Function
      Source:
        Owner: AWS
        SourceIdentifier: LAMBDA_FUNCTION_PUBLIC_ACCESS_PROHIBITED
```

## Detect whether public routes exist in the route table for an Internet Gateway \(IGW\)<a name="no-unrestricted-route-to-igw"></a>

This guardrail detects whether public routes exist in the route table associated with an Internet Gateway \(IGW\)\.

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if a route has a destination CIDR block of `0.0.0.0/0` or `::/0` or if a destination CIDR block does not match the rule parameter\.

The artifact for this guardrail is the following AWS Config rule\. 

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether public routes exist in the route table for an Internet Gateway (IGW)
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
Resources:
  NoUnrestrictedRouteToIgw:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether public routes exist in the route table associated with an Internet Gateway (IGW). The rule is NON_COMPLIANT if a route has a destination CIDR block of '0.0.0.0/0' or '::/0' or if a destination CIDR block does not match the rule parameter.
      Scope:
        ComplianceResourceTypes:
        - AWS::EC2::RouteTable
      Source:
        Owner: AWS
        SourceIdentifier: NO_UNRESTRICTED_ROUTE_TO_IGW
```

## Detect whether Amazon Redshift clusters are blocked from public access<a name="redshift-cluster-public-access-check"></a>

This guardrail detects whether Amazon Redshift clusters are blocked from public access\.

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if the `publiclyAccessible` field is set to **True** in the cluster configuration item\.

The artifact for this guardrail is the following AWS Config rule\. 

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether Amazon Redshift clusters are blocked from public access
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
Resources:
  RedshiftClusterPublicAccessCheck:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether Amazon Redshift clusters are blocked from public access. The rule is NON_COMPLIANT if the publiclyAccessible field is true in the cluster configuration item.
      Scope:
        ComplianceResourceTypes:
        - AWS::Redshift::Cluster
      Source:
        Owner: AWS
        SourceIdentifier: REDSHIFT_CLUSTER_PUBLIC_ACCESS_CHECK
```

## Detect whether an Amazon SageMaker notebook instance allows direct internet access<a name="sagemaker-notebook-no-direct-internet-access"></a>

This guardrail detects whether an Amazon SageMaker notebook instance allows direct internet access\.

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if Amazon SageMaker notebook instances allow direct internet access\. 

The artifact for this guardrail is the following AWS Config rule\. 

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether an Amazon SageMaker notebook instance allows direct internet access
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
  MaximumExecutionFrequency:
    Type: String
    Default: 24hours
    Description: The frequency at which AWS Config will run evaluations for the rule.
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
  SagemakerNotebookNoDirectInternetAccess:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether direct internet access is allowed for an Amazon SageMaker notebook instance. The rule is NON_COMPLIANT if Amazon SageMaker notebook instances allow direct internet access.
      Source:
        Owner: AWS
        SourceIdentifier: SAGEMAKER_NOTEBOOK_NO_DIRECT_INTERNET_ACCESS
      MaximumExecutionFrequency:
        !FindInMap
        - Settings
        - FrequencyMap
        - !Ref MaximumExecutionFrequency
```

## Detect whether any Amazon VPC subnets are assigned a public IP address<a name="subnet-auto-assign-public-ip-disabled"></a>

This guardrail detects whether Amazon Virtual Private Cloud \(Amazon VPC\) subnets are assigned a public IP address\.

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**
+ The rule shows **Non\-compliant** status if the Amazon VPC has subnets that are assigned a public IP address\.

The artifact for this guardrail is the following AWS Config rule\. 

```
AWSTemplateFormatVersion: 2010-09-09
Description: Detect whether any Amazon VPC subnets are assigned a public IP address
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
Resources:
  SubnetAutoAssignPublicIpDisabled:
    Type: AWS::Config::ConfigRule
 
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether Amazon Virtual Private Cloud (Amazon VPC) subnets are assigned a public IP address. The rule is NON_COMPLIANT if Amazon VPC has subnets that are assigned a public IP address.
      Scope:
        ComplianceResourceTypes:
        - AWS::EC2::Subnet
      Source:
        Owner: AWS
        SourceIdentifier: SUBNET_AUTO_ASSIGN_PUBLIC_IP_DISABLED
```

## Detect whether AWS Systems Manager documents owned by the account are public<a name="ssm-document-not-public"></a>

This guardrail detects whether AWS Systems Manager documents owned by the account are public\.

This is a detective guardrail with elective guidance\. By default, this guardrail isn't enabled on any OUs\.

**In the console:**  
The rule shows **Non\-compliant** status if any documents with owner 'Self' are public\.

The artifact for this guardrail is the following AWS Config rule\. 

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure AWS Config rule to detect whether AWS Systems Manager documents owned by the account are public
 
Parameters:
  ConfigRuleName:
    Type: 'String'
    Description: 'Name for the Config rule'
 
  MaximumExecutionFrequency:
    Type: String
    Default: 24hours
    Description: The frequency at which AWS Config will run evaluations for the rule.
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
  SsmDocumentNotPublic:
    Type: AWS::Config::ConfigRule
    Properties:
      ConfigRuleName: !Sub ${ConfigRuleName}
      Description: Detects whether AWS Systems Manager (SSM) documents owned by the account are public. This rule is NON_COMPLIANT if any documents with owner 'Self' are public.
      Source:
        Owner: AWS
        SourceIdentifier: SSM_DOCUMENT_NOT_PUBLIC
      MaximumExecutionFrequency:
        !FindInMap
        - Settings
        - FrequencyMap
        - !Ref MaximumExecutionFrequency
```
