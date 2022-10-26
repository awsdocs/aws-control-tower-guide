# Resource identifiers for APIs and guardrails<a name="control-identifiers"></a>

The following list contains the `ControlARN` designations of the **Strongly recommended** and **Elective** guardrails that are part of AWS Control Tower, including the elective **Data residency** guardrails\. 

Each item in the list that follows serves as a link, which provides more information about these individual guardrails, given in the [Guardrail reference](guardrails-reference.md)\.

## <a name="control-arns-for-elective-gr"></a>

**Designations for Elective guardrails**
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUDIT\_BUCKET\_ENCRYPTION\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-guardrails.html#log-archive-encryption-enabled)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUDIT\_BUCKET\_LOGGING\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-guardrails.html#log-archive-access-enabled)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUDIT\_BUCKET\_POLICY\_CHANGES\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-guardrails.html#log-archive-policy-changes)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUDIT\_BUCKET\_RETENTION\_POLICY](https://docs.aws.amazon.com/controltower/latest/userguide/elective-guardrails.html#log-archive-retention-policy)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_IAM\_USER\_MFA\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-guardrails.html#disallow-access-mfa) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_MFA\_ENABLED\_FOR\_IAM\_CONSOLE\_ACCESS](https://docs.aws.amazon.com/controltower/latest/userguide/elective-guardrails.html#disallow-console-access-mfa)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICT\_S3\_CROSS\_REGION\_REPLICATION](https://docs.aws.amazon.com/controltower/latest/userguide/elective-guardrails.html#disallow-s3-ccr)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICT\_S3\_DELETE\_WITHOUT\_MFA](https://docs.aws.amazon.com/controltower/latest/userguide/elective-guardrails.html#disallow-s3-delete-mfa)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_S3\_VERSIONING\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-guardrails.html#disallow-s3-no-versioning)

## <a name="control-arns-for-dr-gr"></a>

**Designations for Data residency guardrails \(elective\)**
+ [arn:aws:controltower:REGION::control/AWS\-GR\_SUBNET\_AUTO\_ASSIGN\_PUBLIC\_IP\_DISABLED](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#subnet-auto-assign-public-ip-disabled) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUTOSCALING\_LAUNCH\_CONFIG\_PUBLIC\_IP\_DISABLED](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#autoscaling-launch-config-public-ip-disabled) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DISALLOW\_CROSS\_REGION\_NETWORKING](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#prevent-cross-region-networking)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DISALLOW\_VPC\_INTERNET\_ACCESS](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#disallow-vpc-internet-access) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DISALLOW\_VPN\_CONNECTIONS](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#prevent-vpn-connection) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DMS\_REPLICATION\_NOT\_PUBLIC](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#dms-replication-not-public) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EBS\_SNAPSHOT\_PUBLIC\_RESTORABLE\_CHECK](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#ebs-snapshot-public-restorable-check) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EC2\_INSTANCE\_NO\_PUBLIC\_IP](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#ec2-instance-no-public-ip)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EKS\_ENDPOINT\_NO\_PUBLIC\_ACCESS](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#eks-endpoint-no-public-access)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_ELASTICSEARCH\_IN\_VPC\_ONLY](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#elasticsearch-in-vpc-only)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EMR\_MASTER\_NO\_PUBLIC\_IP](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#emr-master-no-public-ip)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_LAMBDA\_FUNCTION\_PUBLIC\_ACCESS\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#lambda-function-public-access-prohibited)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_NO\_UNRESTRICTED\_ROUTE\_TO\_IGW](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#no-unrestricted-route-to-igw)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_REDSHIFT\_CLUSTER\_PUBLIC\_ACCESS\_CHECK](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#redshift-cluster-public-access-check)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_S3\_ACCOUNT\_LEVEL\_PUBLIC\_ACCESS\_BLOCKS\_PERIODIC](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#s3-account-level-public-access-blocks-periodic)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_SAGEMAKER\_NOTEBOOK\_NO\_DIRECT\_INTERNET\_ACCESS](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#sagemaker-notebook-no-direct-internet-access)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_SSM\_DOCUMENT\_NOT\_PUBLIC](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-guardrails.html#ssm-document-not-public)

## <a name="control-arns-for-sr-gr"></a>

**Designations for Strongly recommended guardrails**
+ [arn:aws:controltower:REGION::control/AWS\-GR\_ENCRYPTED\_VOLUMES](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#ebs-enable-encryption)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EBS\_OPTIMIZED\_INSTANCE](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#disallow-not-ebs-optimized)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EC2\_VOLUME\_INUSE\_CHECK](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#disallow-unattached-ebs)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RDS\_INSTANCE\_PUBLIC\_ACCESS\_CHECK](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#disallow-rds-public-access)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RDS\_SNAPSHOTS\_PUBLIC\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#disallow-rds-snapshot-public-access)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RDS\_STORAGE\_ENCRYPTED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#disallow-rds-storage-unencrypted)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICTED\_COMMON\_PORTS](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#rdp-disallow-internet )
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICTED\_SSH](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#ssh-disallow-internet)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICT\_ROOT\_USER](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#disallow-root-auser-actions)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICT\_ROOT\_USER\_ACCESS\_KEYS](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#disallow-root-access-keys)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_ROOT\_ACCOUNT\_MFA\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#enable-root-mfa)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_S3\_BUCKET\_PUBLIC\_READ\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#s3-disallow-public-read)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_S3\_BUCKET\_PUBLIC\_WRITE\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#s3-disallow-public-write)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DETECT\_CLOUDTRAIL\_ENABLED\_ON\_MEMBER\_ACCOUNTS](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-guardrails.html#ensure-cloudtrail-enabled-recommended)

## Guardrails that cannot be changed with the AWS Control Tower guardrail APIs<a name="cannot-change-with-gr-api"></a>

The following guardrails cannot be activated or deactivated by means of the AWS Control Tower guardrail APIs\. Except for the Region deny guardrail, all of these are mandatory guardrails\. In general, mandatory guardrails cannot be deactivated\. The Region deny guardrail must be changed in the console\.
+ AWS\-GR\_REGION\_DENY
+ AWS\-GR\_AUDIT\_BUCKET\_DELETION\_PROHIBITED
+ AWS\-GR\_AUDIT\_BUCKET\_PUBLIC\_READ\_PROHIBITED
+ AWS\-GR\_AUDIT\_BUCKET\_PUBLIC\_WRITE\_PROHIBITED
+ AWS\-GR\_CLOUDTRAIL\_CHANGE\_PROHIBITED
+ AWS\-GR\_CLOUDTRAIL\_CLOUDWATCH\_LOGS\_ENABLED
+ AWS\-GR\_CLOUDTRAIL\_ENABLED
+ AWS\-GR\_CLOUDTRAIL\_VALIDATION\_ENABLED
+ AWS\-GR\_CLOUDWATCH\_EVENTS\_CHANGE\_PROHIBITED
+ AWS\-GR\_CONFIG\_AGGREGATION\_AUTHORIZATION\_POLICY
+ AWS\-GR\_CONFIG\_AGGREGATION\_CHANGE\_PROHIBITED
+ AWS\-GR\_CONFIG\_CHANGE\_PROHIBITED
+ AWS\-GR\_CONFIG\_ENABLED
+ AWS\-GR\_CONFIG\_RULE\_CHANGE\_PROHIBITED
+ AWS\-GR\_CT\_AUDIT\_BUCKET\_ENCRYPTION\_CHANGES\_PROHIBITED
+ AWS\-GR\_CT\_AUDIT\_BUCKET\_LIFECYCLE\_CONFIGURATION\_CHANGES\_PROHIBITED
+ AWS\-GR\_CT\_AUDIT\_BUCKET\_LOGGING\_CONFIGURATION\_CHANGES\_PROHIBITED
+ AWS\-GR\_CT\_AUDIT\_BUCKET\_POLICY\_CHANGES\_PROHIBITED
+ AWS\-GR\_IAM\_ROLE\_CHANGE\_PROHIBITED
+ AWS\-GR\_LAMBDA\_CHANGE\_PROHIBITED
+ AWS\-GR\_LOG\_GROUP\_POLICY
+ AWS\-GR\_SNS\_CHANGE\_PROHIBITED
+ AWS\-GR\_SNS\_SUBSCRIPTION\_CHANGE\_PROHIBITED
+ AWS\-GR\_ENSURE\_CLOUDTRAIL\_ENABLED\_ON\_SHARED\_ACCOUNTS

## Find identifiers for OUs<a name="identifiers-for-ous"></a>

For more information about how to find the resource identifier for an OU and its resources, see [Resource types defined by AWS Organizations](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awsorganizations.html#awsorganizations-resources-for-iam-policies)\.

To learn more about how to get information from an OU, see [the AWS Organizations API Reference](https://docs.aws.amazon.com/organizations/latest/APIReference/API_DescribeOrganizationalUnit.html)\.
