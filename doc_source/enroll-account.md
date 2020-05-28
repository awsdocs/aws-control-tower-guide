# Enrolling an Existing AWS Account in AWS Control Tower<a name="enroll-account"></a>

You can extend AWS Control Tower governance to individual, existing AWS accounts by enrolling them into an AWS Control Tower organizational unit \(OU\)\. Eligible accounts exist in *unregistered OUs that are part of the same AWS Organizations organization* as the AWS Control Tower OU\. 

Before you can enroll an existing AWS account into AWS Control Tower you must give permission for AWS Control Tower to manage, or *govern*, the account\. Specifically, AWS Control Tower requires permission to establish trusted access between AWS CloudFormation and AWS Organizations on your behalf, so that AWS CloudFormation can deploy your stack automatically to the accounts in your selected organization\.

 To learn more about trusted access and AWS CloudFormation StackSets, see [AWS CloudFormation StackSets and AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-cloudformation.html)\. When trusted access is enabled, AWS CloudFormation can create, update, or delete stacks across multiple accounts and AWS Regions with a single operation\. AWS Control Tower relies on this trust capability so it can apply roles and permissions to existing accounts before it moves them into a registered organizational unit and thereby brings them under governance\.

During the enrollment process, AWS Control Tower performs these actions:
+ Baselines the account, which includes deploying these stack sets:
  + ` AWSControlTowerBP-BASELINE-CLOUDTRAIL`
  + `AWSControlTowerBP-BASELINE-CLOUDWATCH`
  + `AWSControlTowerBP-BASELINE-CONFIG`
  + `AWSControlTowerBP-BASELINE-ROLES`
  + `AWSControlTowerBP-BASELINE-SERVICE-ROLES`

   It is a good idea to review the templates of these stack sets and make sure that they don’t conflict with your existing policies\.
+ Identifies the account through AWS Single Sign\-On or AWS Organizations\.
+ Places the account into the OU that you've specified\. Be sure to apply all SCPs that are applied in the current OU, so that your security posture remains consistent\.
+ Applies mandatory guardrails to the account by means of the SCPs that apply to the selected OU as a whole\.
+ Adds the AWS Config rules that apply the AWS Control Tower preventive guardrails to the account\.

**Note**  
Before enrolling, check that the existing account is not using the AWS default VPC\. AWS Control Tower removes the AWS default VPC and creates a new one\.

**Recommended: You can set up a two\-step approach to account enrollment**
+ First, use an AWS Config *conformance pack* to evaluate how your accounts may be affected by some AWS Control Tower guardrails\. To determine how enrollment into AWS Control Tower may affect your accounts, see [AWS Control Tower Detective Guardrails as an AWS Config Conformance Pack](http://aws.amazon.com/blogs/mt/aws-control-tower-detective-guardrails-as-an-aws-config-conformance-pack)\. 
+ Next, you may wish to enroll the account\. If the compliance results are satisfactory, the migration path is easier because you can enroll the account without unexpected consequences\.

**Note**  
The conformance pack also works in situations where the accounts are located in OUs registered by AWS Control Tower, but the workloads run within AWS Regions that don’t have AWS Control Tower support\. You can use the conformance pack to manage resources in accounts that exist in Regions where AWS Control Tower is not deployed\.

## Prerequisites for Enrollment<a name="enrollment-prerequisites"></a>

Before you can enroll an existing account in AWS Control Tower, the account must have the following roles, permissions, and trust relationships in place\.

Role Name: `AWSControlTowerExecution`

Role Permission: `AdministratorAccess` \(AWS managed policy\)

Role Trust Relationship:

```
        
        {
        "Version": "2012-10-17",
        "Statement": [
         {
        "Effect": "Allow",
        "Principal": {
        "AWS": "arn:aws:iam::Master Account ID:root"
             },
            "Action": "sts:AssumeRole",
            "Condition": {}
          }
          ]
         }
```

## <a name="enrollment-steps"></a>

After the **AdministratorAccess** permission is in place in your existing account, follow these steps to enroll the account:

**To enroll an individual account in AWS Control Tower**
+ Navigate to the AWS Control Tower Account Factory page and select **Enroll account**\.
+ Specify the current email address of the existing account you'd like to enroll in AWS Control Tower\.
+ Specify the first and last name of the account owner\.
+ Specify the organizational unit \(OU\) in which you'd like to enroll the account\.
+ Choose **Enroll account**\.

**Common Causes for Failure of Enrollment**
+ Your IAM principal lacks the necessary permissions to provision an account\. To enroll an existing account, the **AWSControlTowerExecution** role must be present in the account you're enrolling\.
+ AWS Security Token Service \(AWS STS\) is disabled in your AWS account in your home region\.

For more information about how AWS Control Tower works with roles when you're creating new accounts or enrolling existing accounts, see [How AWS Control Tower Works With Roles to Create and Manage Accounts ](how-control-tower-works.md#roles-how)\.

**Important**  
The account that you wish to enroll must exist in the same AWS Organizations organization as the AWS Control Tower master account\. The account that exists can be enrolled *only* into the same organization as the AWS Control Tower master account, in an OU that already is registered with AWS Control Tower\. Otherwise, enrollment will fail\.

## Automated Enrollment of AWS Organizations Accounts<a name="automated-account-enrollment"></a>

You can use the enrollment method described in a blog post called **[ Enroll existing AWS accounts into AWS Control Tower](http://aws.amazon.com/blogs/field-notes/enroll-existing-aws-accounts-into-aws-control-tower/)** to enroll your AWS Organizations accounts into AWS Control Tower with a programmatic process\.