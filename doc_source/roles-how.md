# How AWS Control Tower works with roles to create and manage accounts<a name="roles-how"></a>

In general, roles are a part of identity and access management \(IAM\) in AWS\. For general information about IAM and roles in AWS, see [ the IAM roles topic in the *AWS IAM User Guide*](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)\.

For specific information about the roles required to use the AWS Control Tower console, see [Permissions Required to Use the AWS Control Tower Console](access-control-managing-permissions.md#additional-console-required-permissions)\.

## Roles and account creation<a name="roles-and-account-creation"></a>

AWS Control Tower creates a customer's account by calling the `CreateAccount` API of AWS Organizations\. When AWS Organizations creates this account, it creates a role within that account, which AWS Control Tower names by passing in a parameter to the API\. The name of the role is `AWSControlTowerExecution`\.

AWS Control Tower takes over the `AWSControlTowerExecution` role for all accounts created by Account Factory\. Using this role, AWS Control Tower *baselines* the account and applies mandatory \(and any other enabled\) controls, which results in creation of other roles\. These roles in turn are used by other services, such as AWS Config\.

**Note**  
To *baseline* an account is to set up its resources, which include [Account Factory templates](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory-considerations.html), sometimes referred to as *blueprints*, and controls\. The baselining process also sets up the centralized logging and security audit roles on the account, as part of deploying the templates\. AWS Control Tower baselines are contained in the roles that you apply to every enrolled account\.

For more information about accounts and resources, see [About AWS accounts in AWS Control Tower](accounts.md)\.

### <a name="awscontroltowerexecution"></a>

**The AWSControlTowerExecution role, explained**

The `AWSControlTowerExecution` role must be present in all enrolled accounts\. It allows AWS Control Tower to manage your individual accounts and report information about them to your Audit and Log Archive accounts\.

The `AWSControlTowerExecution` role can be added into an account in several ways, as follows:
+ For accounts in the Security OU \(sometimes called *core accounts*\), AWS Control Tower creates the role at the time of initial AWS Control Tower setup\.
+ For an Account Factory account created through the AWS Control Tower console, AWS Control Tower creates this role at the time of account creation\.
+ For a single account enrollment, we ask customers to manually create the role and then enroll the account in AWS Control Tower\.
+ When extending governance to an OU, AWS Control Tower uses the **StackSet\-AWSControlTowerExecutionRole** to create the role in all accounts in that OU\.

Purpose of the `AWSControlTowerExecution` role:
+ `AWSControlTowerExecution` allows you to create and enroll accounts, automatically, with scripts and Lambda functions\.
+ `AWSControlTowerExecution` helps you configure your organizations's logging, so that all the logs for every account are sent to the logging account\.
+ `AWSControlTowerExecution` allows you to enroll an individual account in AWS Control Tower\. First, you must add the `AWSControlTowerExecution` role to that account\. For steps on how to add the role, see [Manually add the required IAM role to an existing AWS account and enroll it](enroll-manually.md)\.

How the `AWSControlTowerExecution` role works with OUs:

The `AWSControlTowerExecution` role ensures that your selected AWS Control Tower controls apply automatically to every individual account, in each OU, in your organization, as well as to every new account you create in AWS Control Tower\. As a result:
+ You can provide compliance and security reports more easily, based on the auditing and logging features embodied by AWS Control Tower [controls](https://docs.aws.amazon.com/controltower/latest/userguide/guardrails.html)\.
+ Your security and compliance teams can verify that all requirements are met, and that no organizational drift has occurred\.

For more information about drift, see [Detect and resolve drift in AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/drift.html)\.

To summarize, the `AWSControlTowerExecution` role and its associated policy gives you flexible control of security and compliance across your entire organization\. Therefore, breaches of security or protocol are less likely to occur\.

## Optional conditions for your role trust relationships<a name="conditions-for-role-trust"></a>

You can impose conditions in your role trust policies, to restrict the accounts and resources that interact with certain roles in AWS Control Tower\. We strongly recommend that you restrict access to the `AWSControlTowerAdmin` role, because it allows wide access permissions\.

To help prevent an attacker from gaining access to your resources, manually edit your AWS Control Tower trust policy to add at least one `aws:SourceArn` or `aws:SourceAccount` conditional to the policy statement\. As a security best practice, we strongly recommend adding the `aws:SourceArn` condition, because it is more specific than `aws:SourceAccount`, limiting access to a specific account and a specific resource\.

If you don't know the full ARN of the resource, or if you are specifying multiple resources, you can use the `aws:SourceArn` condition with wildcards \(\*\) for the unknown portions of the ARN\. For example, `arn:aws:controltower:*:123456789012:*` works if you don't wish to specify a Region\.

The following example demonstrates the use of the `aws:SourceArn` IAM condition with your IAM role trust polices\. Add the condition in your trust relationship for the **AWSControlTowerAdmin** role, because the AWS Control Tower service principal interacts with it\. 

As shown in the example, the source ARN is of the format: `arn:aws:controltower:${HOME_REGION}:${CUSTOMER_AWSACCOUNT_id}:*`

Replace the strings `${HOME_REGION}` and `${CUSTOMER_AWSACCOUNT_id}` with your own home Region and account ID of the calling account\.

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
      "Action": "sts:AssumeRole",
      "Condition": {
        "ArnEquals": {
          "aws:SourceArn": "arn:aws:controltower:us-west-2:012345678901:*"
        }
      }
    }
  ]
}
```

In the example, the Source ARN designated as `arn:aws:controltower:us-west-2:012345678901:*` is the only ARN allowed to perform the `sts:AssumeRole` action\. In other words, only users who can sign in to the account ID `012345678901`, in the `us-west-2` Region, are allowed to perform actions that require this specific role and trust relationship for the AWS Control Tower service, designated as `controltower.amazonaws.com`\.

The next example shows the `aws:SourceAccount` and `aws:SourceArn` conditions applied to the role trust policy\.

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
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "aws:SourceAccount": "012345678901"
        },
        "StringLike": {
          "aws:SourceArn": "arn:aws:controltower:us-west-2:012345678901:*"
        }
      }
    }
  ]
}
```

The example illustrates the `aws:SourceArn` condition statement, with an added `aws:SourceAccount` condition statement\. For more information, see [Prevent cross\-service impersonation](prevent-confused-deputy.md)\.

 For general information about permission policies in AWS Control Tower see [Manage access to resources](access-control-manage-access-intro.md)\.

**Recommendations:**

We recommend that you add conditions to the roles that AWS Control Tower creates, because those roles are directly assumed by other AWS services\. For more information, see the example for **AWSControlTowerAdmin**, shown previously in this section\. For the AWS Config recorder role, we recommend adding the `aws:SourceArn` condition, specifying the Config recorder ARN as the permitted source ARN\.

For roles such as **AWSControlTowerExecution** or the [ roles that can be assumed by the AWS Control Tower Audit account in all managed accounts](#stacksets-and-roles), we recommend that you add the `aws:PrincipalOrgID` condition to the trust policy for these roles, which validates that the principal accessing the resource belongs to an account in the correct AWS organization\. Do not add the `aws:SourceArn` condition statement, because it will not work as expected\.

**Note**  
In case of drift, it is possible that an AWS Control Tower role may be reset under certain circumstances\. It is recommended that you re\-check the roles periodically, if you have customized them\.

## AWS Control Tower ConfigRecorderRole<a name="aws-controltower-configrecorderrole"></a>

AWS Control Tower deploys this role as a resource in the log archive account, the audit account, and in each account created by Account Factory\. The role can be assumed by AWS Config, as shown in the role trust relationship artifact, given later in this section\. This role is over 1000 lines long, because it allows multiple actions by many AWS services\. The role grants permission to AWS Config to record configurations and deliver them to the delivery channels\.

**Note**  
When you create this IAM role, you give AWS Control Tower permission to manage the AWS Config resources as defined in the permissions policy for this role\. The first time that AWS Control Tower uses this role, AWS Config might create a new [service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in your account\. That role grants AWS Config access to other AWS resources that are required to complete your original AWS Control Tower request\.  
To learn more about how AWS Config or other services create and use service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html)\. Look for the services that have **Yes **in the **Service\-Linked Role** column to indicate that they support using service\-linked roles\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.  
For a definition of AWS service\-linked role, see [AWS service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/iam-term-service-linked-role)\.

Role name: `aws-controltower-ConfigRecorderRole`

Deployed in these accounts: Log archive, Audit, Account factory accounts

Assumed by: AWS Config

You can view the details and JSON artifacts of the AWS managed policies for this role\.
+ [ ReadOnlyAccess](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html#awsmp_readonlyaccess)
+ To view the complete JSON policy artifact for the AWS Control Tower ConfigRecorderRole, see [AWS\_ConfigRole](https://docs.aws.amazon.com/config/latest/developerguide/security-iam-awsmanpol.html#security-iam-awsmanpol-AWS_ConfigRole)\.

**Role trust relationship**

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "config.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

## How AWS Control Tower aggregates AWS Config rules in unmanaged OUs and accounts<a name="config-role-for-organizations"></a>

The AWS Control Tower management account creates an organization\-level aggregator, which assists in detecting external AWS Config rules, so that AWS Control Tower does not need to gain access to unmanaged accounts\. The AWS Control Tower console shows you how many externally created AWS Config rules you have for a given account\. You can view details about those external rules in the **External Config Rule Compliance** tab of the **Account details** page\.

To create the aggregator, AWS Control Tower adds a role with the permissions required to describe an organization and list the accounts under it\. The `AWSControlTowerConfigAggregatorRoleForOrganizations` role requires the `AWSConfigRoleForOrganizations` managed policy and a trust relationship with `config.amazonaws.com`\.

Here is the IAM policy \(JSON artifact\) attached to the role:

```
{
    "Version": "2012-10-17",
      "Statement": [
       {
        "Effect": "Allow",
        "Action": [
          "organizations:ListAccounts",
          "organizations:DescribeOrganization",
          "organizations:ListAWSServiceAccessForOrganization"
         ],
       "Resource": "*"
      }
    ]
  }
```

Here is the `AWSControlTowerConfigAggregatorRoleForOrganizations` trust relationship:

```
{
    "Version": "2012-10-17",
      "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
        "Service": "config.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
      }
    ]
  }
}
```

To deploy this functionality in the management account, the following permissions are added in the managed policy `AWSControlTowerServiceRolePolicy`, which is used by the `AWSControlTowerAdmin` role when it creates the AWS Config aggregator:

```
{
  "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
          "config:PutConfigurationAggregator",
          "config:DeleteConfigurationAggregator",
          "iam:PassRole"
          ],
        "Resource": [
          "arn:aws:iam:::role/service-role/AWSControlTowerConfigAggregatorRoleForOrganizations",
          "arn:aws:config:::config-aggregator/"
          ]
        },
      {
        "Effect": "Allow",
        "Action": "organizations:EnableAWSServiceAccess",
        "Resource": "*"
      }
    ]
}
```

New resources created: `AWSControlTowerConfigAggregatorRoleForOrganizations` and `aws-controltower-ConfigAggregatorForOrganizations`

When you are ready, you can enroll accounts individually, or enroll them as a group by registering an OU\. When you've enrolled an account, if you create a rule in AWS Config, AWS Control Tower detects the new rule\.  The aggregator shows the number of external rules and provides a link to the AWS Config console where you can view the details of each external rule for your account\. Use the information in the AWS Config console and the AWS Control Tower console to determine whether you have the appropriate controls enabled for the account\.

## Programmatic roles and trust relationships for the AWS Control Tower audit account<a name="stacksets-and-roles"></a>

You can sign into the audit account and assume a role to review other accounts programmatically\. The audit account does not allow you to log in to other accounts manually\.

The audit account gives you programmatic access to other accounts, by means of some roles that are granted to AWS Lambda functions only\. For security purposes, these roles have *trust relationships* with other roles, which means that the conditions under which the roles can be utilized are strictly defined\.

The AWS Control Tower stack set `StackSet-AWSControlTowerBP-BASELINE-ROLES` creates these programmatic\-only, cross\-account roles in the audit account:
+ **aws\-controltower\-AdministratorExecutionRole**
+ **aws\-controltower\-AuditAdministratorRole**
+ **aws\-controltower\-ReadOnlyExecutionRole**
+ **aws\-controltower\-AuditReadOnlyRole**

`ReadOnlyExecutionRole:` Note that this role allows the audit account to read objects in Amazon S3 buckets across the entire organization \(in contrast to the `SecurityAudit` policy, which allows for metadata access only\)\.

**aws\-controltower\-AdministratorExecutionRole:**
+ Has administrator permissions
+ Cannot be assumed from the console
+ Can be assumed only by a role in the audit account – the `aws-controltower-AuditAdministratorRole` 

The following artifact shows the trust relationship for `aws-controltower-AdministratorExecutionRole`\. The placeholder number `012345678901` will be replaced by the `Audit_acct_ID` number for your audit account\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::012345678901:role/aws-controltower-AuditAdministratorRole"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**aws\-controltower\-AuditAdministratorRole:**
+ Can be assumed by the AWS Lambda service only 
+ Has permission to perform read \(Get\) and write \(Put\) operations on Amazon S3 objects with names that start with the string **log**

**Attached policies:**

1\. **AWSLambdaExecute** – AWS managed policy

2\. **AssumeRole\-aws\-controltower\-AuditAdministratorRole** – inline policy – Created by AWS Control Tower, artifact follows\.

```
{
  "Version": "2012-10-17",
  "Statement": [
	{
	"Action": [
		 "sts:AssumeRole"
		 ],
	"Resource": [
		 "arn:aws:iam::*:role/aws-controltower-AdministratorExecutionRole"
		 ],
	"Effect": "Allow"
	}
   ]
}
```

The following artifact shows the trust relationship for `aws-controltower-AuditAdministratorRole`:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**aws\-controltower\-ReadOnlyExecutionRole:**
+ Cannot be assumed from the console
+ Can be assumed only by another role in the audit account – the `AuditReadOnlyRole`

The following artifact shows the trust relationship for `aws-controltower-ReadOnlyExecutionRole`\. The placeholder number `012345678901` will be replaced by the `Audit_acct_ID` number for your audit account\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::012345678901:role/aws-controltower-AuditReadOnlyRole "
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**aws\-controltower\-AuditReadOnlyRole:**
+ Can be assumed by the AWS Lambda service only
+ Has permission to perform read \(Get\) and write \(Put\) operations on Amazon S3 objects with names that start with the string **log**

**Attached policies:**

1\. **AWSLambdaExecute** – AWS managed policy

2\. **AssumeRole\-aws\-controltower\-AuditReadOnlyRole** – inline policy – Created by AWS Control Tower, artifact follows\.

```
{
  "Version": "2012-10-17",
  "Statement": [
	{
	"Action": [
		"sts:AssumeRole"
	],
	"Resource": [
		"arn:aws:iam::*:role/aws-controltower-ReadOnlyExecutionRole"
	],
	"Effect": "Allow"
   }
  ]
}
```

The following artifact shows the trust relationship for `aws-controltower-AuditAdministratorRole`:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

## Automated Account Provisioning With IAM Roles<a name="automated-provisioning"></a>

To configure Account Factory accounts in a more automated way, you can create Lambda functions in the AWS Control Tower management account, which [assumes the **AWSControlTowerExecution** role ](https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-assume-iam-role/) in the member account\. Then, using the role, the management account performs the desired configuration steps in each member account\.

 If you're provisioning accounts using Lambda functions, the identity that will perform this work must have the following IAM permissions policy, in addition to `AWSServiceCatalogEndUserFullAccess`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AWSControlTowerAccountFactoryAccess",
            "Effect": "Allow",
            "Action": [
                "sso:GetProfile",
                "sso:CreateProfile",
                "sso:UpdateProfile",
                "sso:AssociateProfile",
                "sso:CreateApplicationInstance",
                "sso:GetSSOStatus",
                "sso:GetTrust",
                "sso:CreateTrust",
                "sso:UpdateTrust",
                "sso:GetPeregrineStatus",
                "sso:GetApplicationInstance",
                "sso:ListDirectoryAssociations",
                "sso:ListPermissionSets",
                "sso:GetPermissionSet",
                "sso:ProvisionApplicationInstanceForAWSAccount",
                "sso:ProvisionApplicationProfileForAWSAccountInstance",
                "sso:ProvisionSAMLProvider",
                "sso:ListProfileAssociations",
                "sso-directory:ListMembersInGroup",
                "sso-directory:AddMemberToGroup",
                "sso-directory:SearchGroups",
                "sso-directory:SearchGroupsWithGroupName",
                "sso-directory:SearchUsers",
                "sso-directory:CreateUser",
                "sso-directory:DescribeGroups",
                "sso-directory:DescribeDirectory",
                "sso-directory:GetUserPoolInfo",
                "controltower:CreateManagedAccount",
                "controltower:DescribeManagedAccount",
                "controltower:DeregisterManagedAccount",
                "s3:GetObject",
                "organizations:describeOrganization",
                "sso:DescribeRegisteredRegions"
            ],
            "Resource": "*"
        }
    ]
}
```