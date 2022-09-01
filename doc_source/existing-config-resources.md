# Enroll accounts that have existing AWS Config resources<a name="existing-config-resources"></a>

This topic provides a step\-by\-step approach for how to enroll accounts that have existing AWS Config resources\. For examples of how to check your existing resources, see [Example AWS Config CLI commands for resource status](enroll-account.md#example-config-cli-commands)\.

**Note**  
If you plan to bring existing AWS accounts into AWS Control Tower as **Audit** and **Log archive** accounts, and if those accounts have existing AWS Config resources, you must delete the existing AWS Config resources before you can enroll the accounts into AWS Control Tower\.

**Examples of AWS Config resources**

Here are some types of AWS Config resources that your account could have already\. These resources may need to be modified so that you can enroll your account into AWS Control Tower\.
+ AWS Config recorder
+ AWS Config delivery channel
+ AWS Config aggregation authorization

**Assumptions**
+ Your account is not enrolled with AWS Control Tower already\.
+ Your account has at least one pre\-existing AWS Config resource in at least one of the AWS Control Tower Regions governed by the management account\.

For a blog that describes an automated approach to enrolling accounts with existing AWS Config resources, see [Automate enrollment of accounts with existing AWS Config resources into AWS Control Tower](http://aws.amazon.com/blogs/mt/automate-enrollment-of-accounts-with-existing-aws-config-resources-into-aws-control-tower/)\. You'll be able to submit a single support ticket for all of the accounts you wish to enroll, as described in [Step 1: Contact customer support with a ticket, to add the account to the AWS Control Tower allow list](#existing-config-step-1), which follows\.

**Limitations**
+ The account can be enrolled only by using the AWS Control Tower workflow for extending governance\.
+ If the resources are modified and create drift on the account, AWS Control Tower does not update the resources\.
+ AWS Config resources in Regions that are not governed by AWS Control Tower are not changed\.

**This process has 5 main steps\.**

1. Add the account to the AWS Control Tower allow list\.

1. Create a new IAM role in the account\.

1. Modify pre\-existing AWS Config resources\.

1. Create AWS Config resources in AWS Regions where they don't exist\.

1. Enroll the account with AWS Control Tower\.

**Before you proceed, consider the following expectations regarding this process\.**
+ AWS Control Tower does not create any AWS Config resources in this account\.
+ After enrollment, AWS Control Tower guardrails automatically protect the AWS Config resources you created, including the new IAM role\.
+ If any changes are made to the AWS Config resources after enrollment, those resources must be updated to align with AWS Control Tower settings before you can re\-enroll the account\.

## Step 1: Contact customer support with a ticket, to add the account to the AWS Control Tower allow list<a name="existing-config-step-1"></a>

**Include this phrase in your ticket subject line:**

*Enroll accounts that have existing AWS Config resources into AWS Control Tower*

**Include the following details in the body of your ticket:**
+ Management account number
+ Account numbers of member accounts that have existing AWS Config resources
+ Your selected home Region for AWS Control Tower setup

## Step 2: Create a new IAM role in the member account<a name="existing-config-step-2"></a>

1. Open the AWS CloudFormation console for the member account\.

1. Create a new stack using the following template

   ```
   AWSTemplateFormatVersion: 2010-09-09
   Description: Configure AWS Config
       
   Resources:
     CustomerCreatedConfigRecorderRole:
       Type: AWS::IAM::Role
       Properties:
         RoleName: aws-controltower-ConfigRecorderRole-customer-created
         AssumeRolePolicyDocument:
           Version: 2012-10-17
           Statement:
             - Effect: Allow
               Principal:
                 Service:
                   - config.amazonaws.com
               Action:
                 - sts:AssumeRole
         Path: /
         ManagedPolicyArns:
           - arn:aws:iam::aws:policy/service-role/AWS_ConfigRole
           - arn:aws:iam::aws:policy/ReadOnlyAccess
   ```

1. Provide the name for the stack as **CustomerCreatedConfigRecorderRoleForControlTower**

1. Create the stack\.

## Step 3: Identify the AWS Regions with pre\-existing resources<a name="existing-config-step-3"></a>

For each governed Region \(AWS Control Tower governed\) in the account, identify and note the Regions that have at least one of the existing AWS Config resource example types shown previously\.

## Step 4: Identify the AWS Regions without any AWS Config resources<a name="existing-config-step-4"></a>

For each governed Region \(AWS Control Tower governed\) in the account, identify and note the Regions in which there are no AWS Config resources of the example types shown previously\.

## Step 5: Modify the existing resources in each AWS Region<a name="existing-config-step-5"></a>

For this step, the following information is needed about your AWS Control Tower setup\.
+ `LOGGING_ACCOUNT` \- the Logging account ID
+ `AUDIT_ACCOUNT` \- the Audit account ID
+ `IAM_ROLE_ARN` \- the IAM role ARN created in Step 1
+ `ORGANIZATION_ID` \- the organization ID for the management account
+ `MEMBER_ACCOUNT_NUMBER` \- the member account that is being modified
+ `HOME_REGION` \- the home Region for AWS Control Tower setup\.

 Modify each existing resource by following the instructions given in sections 5a through 5c, which follow\.

## Step 5a\. AWS Config recorder resources<a name="modify-config-recorder-resources-step-5a"></a>

Only one AWS Config recorder can exist per AWS Region\. If another exists, modify the settings as shown\.
+ **Name:** DON'T CHANGE
+ **RoleARN:**` IAM_ROLE_ARN`
  + **RecordingGroup:**
  + **AllSupported:** true
  + **IncludeGlobalResourceTypes:** `GLOBAL_RESOURCE_RECORDING`
  + **ResourceTypes:** Empty

This modification can be made through the AWS CLI using the following command\. Replace the string `RECORDER_NAME` with the existing AWS Config recorder name\.

```
aws configservice put-configuration-recorder --configuration-recorder  name=RECORDER_NAME,roleARN=arn:aws:iam::MEMBER_ACCOUNT_NUMBER:role/aws-controltower-ConfigRecorderRole-customer-created --recording-group allSupported=true,includeGlobalResourceTypes=GLOBAL_RESOURCE_RECORDING --region CURRENT_REGION
```

## Step 5b\. Modify AWS Config delivery channel resources<a name="modify-config-delivery-channel-step-5"></a>

Only one AWS Config delivery channel can exist per Region\. If another exists, modify the settings as shown\.
+ **Name:** DON’T CHANGE
+ **ConfigSnapshotDeliveryProperties:** TwentyFour\_Hours
+ **S3BucketName:** The logging bucket name from the AWS Control Tower logging account

  `aws-controltower-logs-LOGGING_ACCOUNT-HOME_REGION`
+ **S3KeyPrefix: ***ORGANIZATION\_ID*
+ **SnsTopicARN: **The SNS topic ARN from the audit account, with the following format:

  `arn:aws:sns:CURRENT_REGION:AUDIT_ACCOUNT:aws-controltower-AllConfigNotifications`

This modification can be made through the AWS CLI using the following command\. Replace the string `DELIVERY_CHANNEL_NAME` with the existing AWS Config recorder name\.

```
aws configservice put-delivery-channel --delivery-channel name=DELIVERY_CHANNEL_NAME, s3BucketName=aws-controltower-logs-LOGGING_ACCOUNT_ID-HOME_REGION, s3KeyPrefix="ORGANIZATION_ID", configSnapshotDeliveryProperties={deliveryFrequency=TwentyFour_Hours}, snsTopicARN=arn:aws:sns:CURRENT_REGION:AUDIT_ACCOUNT:aws-controltower-AllConfigNotifications --region CURRENT_REGION
```

## Step 5c\. Modify AWS Config aggregation authorization resources<a name="modify-config-aggregator-auth-step-5c"></a>

Multiple aggregation authorizations can exist per Region\. AWS Control Tower requires an aggregation authorization that specifies the audit account as the authorized account, and has the home Region for AWS Control Tower as the authorized Region\. If it doesn’t exist, create a new one with the following settings:
+ **AuthorizedAccountId: **The Audit account ID
+ **AuthorizedAwsRegion:** The home Region for the AWS Control Tower setup

This modification can be made through the AWS CLI using the following command:

 `aws configservice put-aggregation-authorization --authorized-account-id AUDIT_ACCOUNT_ID --authorized-aws-region HOME_REGION --region CURRENT_REGION` 

## Step 6: Create resources where they don’t exist, in Regions governed by AWS Control Tower<a name="existing-config-step-6"></a>

1. Navigate to the management account’s AWS CloudFormation console\.

1. Create a new StackSet with the name **CustomerCreatedConfigResourcesForControlTower**\.

1. Copy and update the following template:

   ```
   AWSTemplateFormatVersion: 2010-09-09
   Description: Configure AWS Config
   Resources:
     CustomerCreatedConfigRecorder:
       Type: AWS::Config::ConfigurationRecorder
       Properties:
         Name: aws-controltower-BaselineConfigRecorder-customer-created
         RoleARN: !Sub arn:aws:iam::${AWS::AccountId}:role/aws-controltower-ConfigRecorderRole-customer-created
         RecordingGroup:
           AllSupported: true
           IncludeGlobalResourceTypes: GLOBAL_RESOURCE_RECORDING
           ResourceTypes: []
     CustomerCreatedConfigDeliveryChannel:
       Type: AWS::Config::DeliveryChannel
       Properties:
         Name: aws-controltower-BaselineConfigDeliveryChannel-customer-created
         ConfigSnapshotDeliveryProperties:
           DeliveryFrequency: TwentyFour_Hours
         S3BucketName: aws-controltower-logs-LOGGING_ACCOUNT-HOME_REGION
         S3KeyPrefix: ORGANIZATION_ID
         SnsTopicARN: !Sub arn:aws:sns:${AWS::Region}:AUDIT_ACCOUNT:aws-controltower-AllConfigNotifications
     CustomerCreatedAggregationAuthorization:
       Type: "AWS::Config::AggregationAuthorization"
       Properties:
         AuthorizedAccountId: AUDIT_ACCOUNT
         AuthorizedAwsRegion: HOME_REGION
   ```

**Update the template with required fields:**

   1. In the **S3BucketName** field, replace the *LOGGING\_ACCOUNT\_ID* and *HOME\_REGION*

   1. In the **S3KeyPrefix** field, replace the *ORGANIZATION\_ID*

   1. In the **SnsTopicARN** field, replace the *AUDIT\_ACCOUNT*

   1. In the **AuthorizedAccountId** field, replace the *AUDIT\_ACCOUNT*

   1. In the **AuthorizedAwsRegion** field, replace the *HOME\_REGION*

1. During deployment on the AWS CloudFormation console, add the member account number\.

1. Add the AWS Regions that were identified in Step 4\.

1. Deploy the stack set\.

## Step 7: Register the OU with AWS Control Tower<a name="existing-config-step-7"></a>

In the AWS Control Tower dashboard, register the OU\.

**Note**  
The **Enroll account** workflow will not succeed for this task\. You must choose **Register OU** or **Re\-register OU**\.