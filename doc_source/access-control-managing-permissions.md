# Using identity\-based policies \(IAM policies\) for AWS Control Tower<a name="access-control-managing-permissions"></a>

This topic provides examples of identity\-based policies that demonstrate how an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\) and thereby grant permissions to perform operations on AWS Control Tower resources\. 

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available for you to manage access to your AWS Control Tower resources\. For more information, see [Overview of managing access permissions to your AWS Control Tower resources](access-control-overview.md)\. 

## Permissions Required to Use the AWS Control Tower Console<a name="additional-console-required-permissions"></a>

AWS Control Tower creates three roles automatically when you set up a landing zone\. All three roles are required to allow console access\. AWS Control Tower splits permissions into three roles as a best practice to restrict access to the minimal sets of actions and resources\.

**Three required roles**
+ [AWSControlTowerAdmin role](#AWSControlTowerAdmin)
+ [AWSControlTowerStackSetRole](#AWSControlTowerStackSetRole)
+ [AWSControlTowerCloudTrailRole](#AWSControlTowerCloudTrailRole)

We recommend that you restrict access to your role trust policies for these roles\. For more information, see [Optional conditions for your role trust relationships](roles-how.md#conditions-for-role-trust)\.

## AWSControlTowerAdmin role<a name="AWSControlTowerAdmin"></a>

This role provides AWS Control Tower with access to infrastructure critical to maintaining the landing zone\. The `AWSControlTowerAdmin` role requires an attached managed policy and a role trust policy for the IAM role\. A *role trust policy* is a resource\-based policy, specifying which principals can assume the role\.

** Managed Policy for this role: `AWSControlTowerServiceRolePolicy`**

## AWSControlTowerServiceRolePolicy<a name="AWSControlTowerServiceRolePolicy"></a>

The **AWSControlTowerServiceRolePolicy** AWS\-managed policy defines permissions to create and manage AWS Control Tower resources such as AWS CloudFormation stacksets and stack instances, AWS CloudTrail log files, a configuration aggregator for AWS Control Tower, as well as AWS Organizations accounts and organizational units \(OUs\) that are governed by AWS Control Tower\.

Updates to this managed policy are summarized in the table, [Managed policies for AWS Control Tower](#managed-policies-table)\.

Managed Policy Name: `AWSControlTowerServiceRolePolicy`

The JSON artifact for `AWSControlTowerServiceRolePolicy` is the following: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "cloudformation:CreateStack",
                "cloudformation:CreateStackInstances",
                "cloudformation:CreateStackSet",
                "cloudformation:DeleteStack",
                "cloudformation:DeleteStackInstances",
                "cloudformation:DeleteStackSet",
                "cloudformation:DescribeStackInstance",
                "cloudformation:DescribeStacks",
                "cloudformation:DescribeStackSet",
                "cloudformation:DescribeStackSetOperation",
                "cloudformation:ListStackInstances",
                "cloudformation:UpdateStack",
                "cloudformation:UpdateStackInstances",
                "cloudformation:UpdateStackSet"
            ],
            "Resource": [
                "arn:aws:cloudformation:*:*:type/resource/AWS-IAM-Role"
            ]
        },
        {
            "Effect": "Allow",   # needed for opt-in Regions
            "Action": [
                "account:EnableRegion",
                "account:ListRegions",
                "account:GetRegionOptStatus"
            ],
            "Resource": "*"
        },

        {
            "Effect": "Allow",
            "Action": [
                "cloudformation:CreateStack",
                "cloudformation:CreateStackInstances",
                "cloudformation:CreateStackSet",
                "cloudformation:DeleteStack",
                "cloudformation:DeleteStackInstances",
                "cloudformation:DeleteStackSet",
                "cloudformation:DescribeStackInstance",
                "cloudformation:DescribeStacks",
                "cloudformation:DescribeStackSet",
                "cloudformation:DescribeStackSetOperation",
                "cloudformation:GetTemplate",
                "cloudformation:ListStackInstances",
                "cloudformation:UpdateStack",
                "cloudformation:UpdateStackInstances",
                "cloudformation:UpdateStackSet"
            ],
            "Resource": [
                "arn:aws:cloudformation:*:*:stack/AWSControlTower*/*",
                "arn:aws:cloudformation:*:*:stack/StackSet-AWSControlTower*/*",
                "arn:aws:cloudformation:*:*:stackset/AWSControlTower*:*",
                "arn:aws:cloudformation:*:*:stackset-target/AWSControlTower*/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudtrail:CreateTrail",
                "cloudtrail:DeleteTrail",
                "cloudtrail:GetTrailStatus",
                "cloudtrail:StartLogging",
                "cloudtrail:StopLogging",
                "cloudtrail:UpdateTrail",
                "cloudtrail:PutEventSelectors", 
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:PutRetentionPolicy"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:aws-controltower/CloudTrailLogs:*",
                "arn:aws:cloudtrail:*:*:trail/aws-controltower*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::aws-controltower*/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "sts:AssumeRole"
            ],
            "Resource": [
                "arn:aws:iam::*:role/AWSControlTowerExecution"
                "arn:aws:iam::*:role/AWSControlTowerBlueprintAccess"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudtrail:DescribeTrails",
                "ec2:DescribeAvailabilityZones",
                "iam:ListRoles",
                "logs:CreateLogGroup",
                "logs:DescribeLogGroups",
                "organizations:CreateAccount",
                "organizations:DescribeAccount",
                "organizations:DescribeCreateAccountStatus",
                "organizations:DescribeOrganization",
                "organizations:DescribeOrganizationalUnit",
                "organizations:DescribePolicy",
                "organizations:ListAccounts",
                "organizations:ListAccountsForParent",
                "organizations:ListAWSServiceAccessForOrganization",
                "organizations:ListChildren",
                "organizations:ListOrganizationalUnitsForParent",
                "organizations:ListParents",
                "organizations:ListPoliciesForTarget",
                "organizations:ListTargetsForPolicy",
                "organizations:ListRoots",
                "organizations:MoveAccount",
                "servicecatalog:AssociatePrincipalWithPortfolio"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:GetRole",
                "iam:GetUser",
                "iam:ListAttachedRolePolicies",
                "iam:GetRolePolicy"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:PassRole"
            ],
            "Resource": [
                "arn:aws:iam::*:role/service-role/AWSControlTowerStackSetRole",
                "arn:aws:iam::*:role/service-role/AWSControlTowerCloudTrailRole",
                "arn:aws:iam::*:role/service-role/AWSControlTowerConfigAggregatorRoleForOrganizations"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "config:DeleteConfigurationAggregator",
                "config:PutConfigurationAggregator",
                "config:TagResource"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/aws-control-tower": "managed-by-control-tower"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "organizations:EnableAWSServiceAccess",
                "organizations:DisableAWSServiceAccess"
            ],
            "Resource": "*",
            "Condition": {
                "StringLike": {
                    "organizations:ServicePrincipal": [
                        "config.amazonaws.com",
                        "cloudtrail.amazonaws.com"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": "cloudtrail.amazonaws.com"
                }
            }
        }
    ]
}
```

Role trust policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "controltower.amazonaws.com"       
         ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

The inline policy is `AWSControlTowerAdminPolicy`:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": "ec2:DescribeAvailabilityZones",
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```

## AWSControlTowerStackSetRole<a name="AWSControlTowerStackSetRole"></a>

AWS CloudFormation assumes this role to deploy stack sets in accounts created by AWS Control Tower\. Inline Policy: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "sts:AssumeRole"
            ],
            "Resource": [
                "arn:aws:iam::*:role/AWSControlTowerExecution"
            ],
            "Effect": "Allow"
        }
    ]
}
```

## AWSControlTowerCloudTrailRole<a name="AWSControlTowerCloudTrailRole"></a>

AWS Control Tower enables CloudTrail as a best practice and provides this role to CloudTrail\. CloudTrail assumes this role to create and publish CloudTrail logs\. Inline Policy: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": "logs:CreateLogStream",
            "Resource": "arn:aws:logs:*:*:log-group:aws-controltower/CloudTrailLogs:*",
            "Effect": "Allow"
        },
        {
            "Action": "logs:PutLogEvents",
            "Resource": "arn:aws:logs:*:*:log-group:aws-controltower/CloudTrailLogs:*",
            "Effect": "Allow"
        }
    ]
}
```

## AWSControlTowerBlueprintAccess role requirements<a name="AWSControlTowerBlueprintAccess"></a>

AWS Control Tower requires you to create the `AWSControlTowerBlueprintAccess` role in the designated blueprint hub account, within the same organization\.

**Role name**

The role name must be `AWSControlTowerBlueprintAccess`\.

**Role trust policy**

The role must be set up to trust the following principals:
+ The principal that uses AWS Control Tower in the management account\.
+ The `AWSControlTowerAdmin` role in the management account\.

The following example shows a least\-privilege trust policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::ManagementAccountId:role/AWSControlTowerAdmin", 
                    "arn:aws:iam::ManagementAccountId:role/YourControlTowerUserRole"
                ]
            },
            "Action": "sts:AssumeRole",
            "Condition": {}
        }
    ]
}
```

**Role permissions**

You are required to attach the managed policy **AWSServiceCatalogAdminFullAccess** to the role\.

## Managed policies for AWS Control Tower<a name="managed-policies-table"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\. 


| Change | Description | Date | 
| --- | --- | --- | 
|  [AWSControlTowerServiceRolePolicy](#AWSControlTowerServiceRolePolicy) – Update to an existing policy  |  AWS Control Tower added new permissions that allow AWS Control Tower to make calls to the `EnableRegion`, `ListRegions`, and `GetRegionOptStatus` APIs implemented by the AWS Account Management service, to make the opt\-in AWS Regions available for customer accounts in the landing zone \(Management account, Log archive account, Audit account, OU member accounts\)\. This change is needed so that customers can have the option to expand Region governance by AWS Control Tower into the opt\-in Regions\.  | April 6, 2023 | 
|  [AWSControlTowerServiceRolePolicy](#AWSControlTowerServiceRolePolicy) – Update to an existing policy  |  AWS Control Tower added new permissions that allow AWS Control Tower to assume the `AWSControlTowerBlueprintAccess` role in the blueprint \(hub\) account, which is a dedicated account in an organization, containing pre\-defined blueprints stored in one or more Service Catalog Products\. AWS Control Tower assumes the `AWSControlTowerBlueprintAccess` role to perform three tasks: create a Service Catalog Portfolio, add the requested blueprint Product, and share the Portfolio to a requested member account at account provisioning time\. This change is needed so that customers can provision customized accounts through AWS Control Tower Account Factory\.  | October 28, 2022 | 
|  [AWSControlTowerServiceRolePolicy](#AWSControlTowerServiceRolePolicy) – Update to an existing policy  |  AWS Control Tower added new permissions that allow customers to set up organization\-level AWS CloudTrail trails, starting in landing zone version 3\.0\. The organization\-based CloudTrail feature requires customers to have trusted access enabled for the CloudTrail service, and the IAM user or role must have permission to create an organization\-level trail in the management account\.  | June 20, 2022 | 
|  [AWSControlTowerServiceRolePolicy](#AWSControlTowerServiceRolePolicy) – Update to an existing policy  |  AWS Control Tower added new permissions that allow customers to use KMS key encryption\. The KMS feature allows customers to provide their own KMS key to encrypt their CloudTrail logs\. Customers also can change the KMS key during landing zone update or repair\. When updating the KMS key, AWS CloudFormation needs permissions to call the AWS CloudTrail `PutEventSelector` API\. The change to the policy is to allow the **AWSControlTowerAdmin** role to call the AWS CloudTrail `PutEventSelector` API\.  | July 28, 2021 | 
|  AWS Control Tower started tracking changes  |  AWS Control Tower started tracking changes for its AWS managed policies\.  | May 27, 2021 | 