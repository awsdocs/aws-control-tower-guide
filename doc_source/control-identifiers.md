# Resource identifiers for APIs and controls<a name="control-identifiers"></a>

The following list contains the `ControlARN` designations of the **Strongly recommended** and **Elective** controls that are part of AWS Control Tower, including the elective **Data residency** controls\. 

Each item in the list that follows serves as a link, which provides more information about these individual controls, given in the [Control reference](controls-reference.md)\.

## <a name="control-arns-for-elective-gr"></a>

**Designations for Elective controls**
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUDIT\_BUCKET\_ENCRYPTION\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-controls.html#log-archive-encryption-enabled)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUDIT\_BUCKET\_LOGGING\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-controls.html#log-archive-access-enabled)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUDIT\_BUCKET\_POLICY\_CHANGES\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-controls.html#log-archive-policy-changes)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUDIT\_BUCKET\_RETENTION\_POLICY](https://docs.aws.amazon.com/controltower/latest/userguide/elective-controls.html#log-archive-retention-policy)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_IAM\_USER\_MFA\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-controls.html#disallow-access-mfa) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_MFA\_ENABLED\_FOR\_IAM\_CONSOLE\_ACCESS](https://docs.aws.amazon.com/controltower/latest/userguide/elective-controls.html#disallow-console-access-mfa)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICT\_S3\_CROSS\_REGION\_REPLICATION](https://docs.aws.amazon.com/controltower/latest/userguide/elective-controls.html#disallow-s3-ccr)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICT\_S3\_DELETE\_WITHOUT\_MFA](https://docs.aws.amazon.com/controltower/latest/userguide/elective-controls.html#disallow-s3-delete-mfa)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_S3\_VERSIONING\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/elective-controls.html#disallow-s3-no-versioning)

## <a name="control-arns-for-dr-gr"></a>

**Designations for Data residency controls \(elective\)**
+ [arn:aws:controltower:REGION::control/AWS\-GR\_SUBNET\_AUTO\_ASSIGN\_PUBLIC\_IP\_DISABLED](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#subnet-auto-assign-public-ip-disabled) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_AUTOSCALING\_LAUNCH\_CONFIG\_PUBLIC\_IP\_DISABLED](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#autoscaling-launch-config-public-ip-disabled) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DISALLOW\_CROSS\_REGION\_NETWORKING](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#prevent-cross-region-networking)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DISALLOW\_VPC\_INTERNET\_ACCESS](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#disallow-vpc-internet-access) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DISALLOW\_VPN\_CONNECTIONS](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#prevent-vpn-connection) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DMS\_REPLICATION\_NOT\_PUBLIC](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#dms-replication-not-public) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EBS\_SNAPSHOT\_PUBLIC\_RESTORABLE\_CHECK](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#ebs-snapshot-public-restorable-check) 
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EC2\_INSTANCE\_NO\_PUBLIC\_IP](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#ec2-instance-no-public-ip)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EKS\_ENDPOINT\_NO\_PUBLIC\_ACCESS](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#eks-endpoint-no-public-access)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_ELASTICSEARCH\_IN\_VPC\_ONLY](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#elasticsearch-in-vpc-only)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EMR\_MASTER\_NO\_PUBLIC\_IP](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#emr-master-no-public-ip)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_LAMBDA\_FUNCTION\_PUBLIC\_ACCESS\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#lambda-function-public-access-prohibited)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_NO\_UNRESTRICTED\_ROUTE\_TO\_IGW](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#no-unrestricted-route-to-igw)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_REDSHIFT\_CLUSTER\_PUBLIC\_ACCESS\_CHECK](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#redshift-cluster-public-access-check)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_S3\_ACCOUNT\_LEVEL\_PUBLIC\_ACCESS\_BLOCKS\_PERIODIC](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#s3-account-level-public-access-blocks-periodic)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_SAGEMAKER\_NOTEBOOK\_NO\_DIRECT\_INTERNET\_ACCESS](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#sagemaker-notebook-no-direct-internet-access)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_SSM\_DOCUMENT\_NOT\_PUBLIC](https://docs.aws.amazon.com/controltower/latest/userguide/data-residency-controls.html#ssm-document-not-public)

## <a name="control-arns-for-sr-gr"></a>

**Designations for Strongly recommended controls**
+ [arn:aws:controltower:REGION::control/AWS\-GR\_ENCRYPTED\_VOLUMES](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#ebs-enable-encryption)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EBS\_OPTIMIZED\_INSTANCE](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#disallow-not-ebs-optimized)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_EC2\_VOLUME\_INUSE\_CHECK](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#disallow-unattached-ebs)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RDS\_INSTANCE\_PUBLIC\_ACCESS\_CHECK](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#disallow-rds-public-access)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RDS\_SNAPSHOTS\_PUBLIC\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#disallow-rds-snapshot-public-access)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RDS\_STORAGE\_ENCRYPTED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#disallow-rds-storage-unencrypted)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICTED\_COMMON\_PORTS](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#rdp-disallow-internet )
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICTED\_SSH](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#ssh-disallow-internet)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICT\_ROOT\_USER](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#disallow-root-auser-actions)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_RESTRICT\_ROOT\_USER\_ACCESS\_KEYS](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#disallow-root-access-keys)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_ROOT\_ACCOUNT\_MFA\_ENABLED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#enable-root-mfa)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_S3\_BUCKET\_PUBLIC\_READ\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#s3-disallow-public-read)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_S3\_BUCKET\_PUBLIC\_WRITE\_PROHIBITED](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#s3-disallow-public-write)
+ [arn:aws:controltower:REGION::control/AWS\-GR\_DETECT\_CLOUDTRAIL\_ENABLED\_ON\_MEMBER\_ACCOUNTS](https://docs.aws.amazon.com/controltower/latest/userguide/strongly-recommended-controls.html#ensure-cloudtrail-enabled-recommended)


## Controls that cannot be changed with the AWS Control Tower APIs<a name="cannot-change-with-gr-api"></a>

The following controls cannot be activated or deactivated by means of the AWS Control Tower APIs\. Except for the Region deny control, all of these are mandatory controls\. In general, mandatory controls cannot be deactivated\. The Region deny control must be changed in the console\.
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
