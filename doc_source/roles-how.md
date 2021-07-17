# How AWS Control Tower works with roles to create and manage accounts<a name="roles-how"></a>

In general, roles are a part of identity and access management \(IAM\) in AWS\. Refer to [Permissions Required to Use the AWS Control Tower Console](access-control-managing-permissions.md#additional-console-required-permissions) for information about the roles required by AWS Control Tower\. For general information about IAM and roles in AWS, see [ the IAM roles topic in the *AWS IAM User Guide*](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)\.

**Roles and account creation**

AWS Control Tower creates a customer's account by calling the `CreateAccount` API of AWS Organizations\. When AWS Organizations creates this account, it creates a role within that account, which AWS Control Tower names by passing in a parameter to the API\. The name of the role is `AWSControlTowerExecution`\.

AWS Control Tower takes over the `AWSControlTowerExecution` role for all accounts created by Account Factory\. Using this role, AWS Control Tower *baselines* the account and applies mandatory \(and any other enabled\) guardrails, which results in creation of other roles\. These roles in turn are used by other services, such as AWS Config\.

**Note**  
To baseline an account is to set up its blueprints and guardrails\. The baselining process also sets up the centralized logging and security audit roles on the account, as part of deploying the blueprints\. AWS Control Tower baselines are contained in the roles that you apply to every enrolled account\. 

## <a name="awscontroltowerexecution"></a>

**The AWSControlTowerExecution role, explained**

The `AWSControlTowerExecution` role allows AWS Control Tower to manage your individual accounts and report information about them to your audit and logging accounts\.
+ `AWSControlTowerExecution` allows auditing by the AWS Control Tower audit account\.
+ `AWSControlTowerExecution` helps you configure your organizations's logging, so that all the logs for every account are sent to the logging account\.
+ To enroll an individual account in AWS Control Tower you must add the `AWSControlTowerExecution` role to that account\.

After you’ve completed setting up accounts, `AWSControlTowerExecution` ensures that your selected AWS Control Tower guardrails apply automatically to every individual account in your organization, as well as to every new account you create in AWS Control Tower\. Therefore, you can provide compliance and security reports with ease, based on the auditing and logging features embodied by AWS Control Tower guardrails\. Your security and compliance teams can verify that all requirements are met, and that no organizational drift has occurred\. For more information about drift, see [the AWS Control Tower User Guide](https://docs.aws.amazon.com/controltower/latest/userguide/drift.html)\. 

To summarize, the `AWSControlTowerExecution` role and its associated policy gives you flexible control of security and compliance across your entire organization\. Therefore, breaches of security are less likely to occur\.

## How AWS Control Tower aggregates unmanaged OUs and accounts<a name="config-role-for-organizations"></a>

The AWS Control Tower management account creates an organization\-level aggregator, which assists in detecting external AWS Config rules, so that AWS Control Tower does not need to gain access to unmanaged accounts\. The AWS Control Tower console shows you how many externally created AWS Config rules you have for a given account, and links you to the AWS Config console, where you can view details about those external rules\. 

To create the aggregator, AWS Control Tower adds a role with the permissions required to describe an organization and list the accounts under it\. The `AWSControlTowerConfigAggregatorRoleForOrganizations` role requires the `AWSConfigRoleForOrganizations` managed policy and a trust relationship with `config.amazonaws.com`\.

Here is the artifact for the role:

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

When you are ready, you can enroll accounts individually, or enroll them as a group by registering an OU\. When you've enrolled an account, if you create a rule in AWS Config, AWS Control Tower detects the new rule and updates the account's list of enabled guardrails in the AWS Control Tower console to show that you have an external rule\. The aggregator provides the number of external rules and provides a link to the AWS Config console where you can view the details of each external rule for your account\. Use the information in the AWS Config console and the AWS Control Tower console to determine whether you have the appropriate guardrails enabled for the account\.

**Note**  
To link directly from the AWS Control Tower console to your aggregated list of AWS Config rules, configure your AWS Config console with the Config Recorder and Delivery Channel in the home Region of your management account\.

## Programmatic roles and trust relationships for the AWS Control Tower audit account<a name="stacksets-and-roles"></a>

You can log into the audit account to review other accounts programmatically\. The audit account does not allow you to log in to other accounts manually\.

The audit account gives you programmatic access to other accounts, by means of some roles that are granted to AWS Lambda functions only\. For security purposes, these roles have *trust relationships* with other roles, which means that the conditions under which the roles can be utilized are strictly defined\.

The AWS Control Tower stack set `StackSet-AWSControlTowerBP-BASELINE-ROLES` creates these programmatic\-only, cross\-account roles in the audit account:
+ **aws\-controltower\-AdministratorExecutionRole**
+ **aws\-controltower\-AuditAdministratorRole**
+ **aws\-controltower\-ReadOnlyExecutionRole**
+ **aws\-controltower\-AuditReadOnlyRole**

`ReadOnlyExecutionRole:` Note that this role allows the audit account to read objects in S3 buckets across the entire organization \(in contrast to the `SecurityAudit` policy, which allows for metadata access only\)\.

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
+ Has permission to perform read \(Get\) and write \(Put\) operations on S3 objects with names that start with the string **log**

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
+ Has permission to perform read \(Get\) and write \(Put\) operations on S3 objects with names that start with the string **log**

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