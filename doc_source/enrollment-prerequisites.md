# Prerequisites for Enrollment<a name="enrollment-prerequisites"></a>

These prerequisites are required before you can enroll an account in AWS Control Tower:

1. To enroll an existing account, the **AWSControlTowerExecution** role must be present in the account you're enrolling\.

1. We recommend that the account should not have an AWS Config configuration recorder or delivery channel\. These may be deleted or modified through the AWS CLI before you can enroll an account\. Otherwise, see [Enroll accounts that have existing AWS Config resources](https://docs.aws.amazon.com/controltower/latest/userguide/existing-config-resources.html) for instructions on how you can modify your existing resources\.

1. The account that you wish to enroll must exist in the same AWS Organizations organization as the AWS Control Tower management account\. The account that exists can be enrolled *only* into the same organization as the AWS Control Tower management account, in an OU that already is registered with AWS Control Tower\. 

1. Before you can enroll an existing account in AWS Control Tower, the account must have the following roles, permissions, and trust relationships in place\. Otherwise, enrollment will fail\.

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
           "AWS": "arn:aws:iam::Management Account ID:root"
                },
               "Action": "sts:AssumeRole",
               "Condition": {}
             }
             ]
            }
   ```

To check other prerequisites for enrollment, see [Getting Started with AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-with-control-tower.html)\.

**Note**  
When you enroll an account into AWS Control Tower, your account is governed by the AWS CloudTrail trail for the AWS Control Tower organization\. If you have an existing deployment of a CloudTrail trail, you may see duplicate charges unless you delete the existing trail for the account before you enroll it in AWS Control Tower\.