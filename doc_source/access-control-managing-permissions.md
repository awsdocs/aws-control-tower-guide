# Using Identity\-Based Policies \(IAM Policies\) for AWS Control Tower<a name="access-control-managing-permissions"></a>

This topic provides examples of identity\-based policies that demonstrate how an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\) and thereby grant permissions to perform operations on AWS Control Tower resources\. 

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available for you to manage access to your AWS Control Tower resources\. For more information, see [Overview of Managing Access Permissions to Your AWS Control Tower Resources](access-control-overview.md)\. 

The following shows an example of a permissions policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "*",
            "Resource": "*"
        }
    ]
}
```

The policy has one statement that grants permissions for all AWS actions on all resources in the account\. This is the permissions policy for administrator access in an AWS account\. This is the necessary level of permissions for an IAM entity that will set up a landing zone\.

The policy doesn't specify the `Principal` element because in an identity\-based policy you don't specify the principal who gets the permission\. When you attach policy to a user, the user is the implicit principal\. When you attach a permissions policy to an IAM role, the principal identified in the role's trust policy gets the permissions\. 

## Permissions Required to Use the AWS Control Tower Console<a name="additional-console-required-permissions"></a>

AWS Control Tower requires creation of three roles to set up a landing zone\. AWS Control Tower splits permissions into three roles as a best practice to restrict access to the minimal sets of actions and resources\.

### AWSControlTowerAdmin<a name="AWSControlTowerAdmin"></a>

This role provides AWS Control Tower with access to infrastructure critical to maintaining the landing zone\. The role requires an inline policy, and a managed policy attachment\.

Managed Policy: `AWSControlTowerServiceRolePolicy`

Inline Policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ec2:DescribeAvailabilityZones",
            "Resource": "*"
        }
    ]
}
```

### AWSControlTowerStackSetRole<a name="AWSControlTowerServiceRolePolicy"></a>

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

### AWSControlTowerCloudTrailRole<a name="AWSControlTowerCloudTrailRole"></a>

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