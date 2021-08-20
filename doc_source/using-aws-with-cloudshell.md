# Using AWS CloudShell to work with AWS Control Tower<a name="using-aws-with-cloudshell"></a>

AWS CloudShell is a browser\-based, pre\-authenticated shell that you can launch directly from the AWS Management Console\. There's no need to download or install command line tools\. You can run AWS CLI commands for AWS Control Tower and other AWS services from your preferred shell \(Bash, PowerShell or Z shell\)\. 

When you [launch AWS CloudShell from the AWS Management Console](https://docs.aws.amazon.com/cloudshell/latest/userguide/working-with-cloudshell.html#launch-options), the AWS credentials you used to sign in to the console are available in a new shell session\. You can skip entering your configuring credentials when you interact with AWS Control Tower and other AWS services, and you'll be using AWS CLI version 2, which is pre\-installed on the shell's compute environment\.You're pre\-authenticated with AWS CloudShell\.

## Obtaining IAM permissions for AWS CloudShell<a name="cloudshell-permissions"></a>

AWS Identity and Access Management provides access management resources that allow administrators to grant permissions to IAM users for access to AWS CloudShell\.

The quickest way for an administrator to grant access to users is through an AWS managed policy\. An [AWS managed policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) is a standalone policy that's created and administered by AWS\. The following AWS managed policy for CloudShell can be attached to IAM identities:
+ `AWSCloudShellFullAccess`: Grants permission to use AWS CloudShell with full access to all features\.

 If you want to limit the scope of actions that an IAM user can perform with AWS CloudShell, you can create a custom policy that uses the `AWSCloudShellFullAccess` managed policy as a template\. For more information about limiting the actions that are available to users in CloudShell, see [Managing AWS CloudShell access and usage with IAM policies](https://docs.aws.amazon.com/cloudshell/latest/userguide/sec-auth-with-identities.html) in the *AWS CloudShell User Guide*\.

**Note**  
Your IAM identity also requires a policy that grants permission to make calls to AWS Control Tower\. For more information, see [Permissions required to use the AWS Control Tower console](https://docs.aws.amazon.com/controltower/latest/userguide/access-control-managing-permissions.html#additional-console-required-permissions)\.

## Interacting with AWS Control Tower using AWS CloudShell<a name="cshell-examples"></a>

After you launch AWS CloudShell from the AWS Management Console, you can immediately start to interact with AWS Control Tower from the command line interface\. AWS CLI commands work in the standard way in CloudShell\.

**Note**  
When using AWS CLI in AWS CloudShell, you don't need to download or install any additional resources\. You're already authenticated within the shell, so you don't need to configure credentials before making calls\.<a name="launch-cloudshell"></a>

## Launch AWS CloudShell<a name="launch-cloudshell"></a>
+ From the AWS Management Console, you can launch CloudShell by choosing the following options available on the navigation bar:
  +  Choose the CloudShell icon\. 
  + Start typing "cloudshell" in Search box and then choose the CloudShell option\.

  Now that you've started CloudShell, you can enter any AWS CLI commands you require to work with AWS Control Tower\. For example, you can check your AWS Config status\.<a name="cloudshell-and-controltower"></a>

## Using AWS CloudShell to help set up AWS Control Tower<a name="cloudshell-and-controltower"></a>

Before performing these procedures, unless it's otherwise indicated, you must be signed in to the AWS Management Console in the home Region for your landing zone, and you must be signed in as an IAM user with administrative permissions for the management account that contains your landing zone\.

1. Here's how you can use AWS Config CLI commands in AWS CloudShell to determine the status of your configuration recorder and delivery channel before you start to configure your AWS Control Tower landing zone\.

   **Check your AWS Config status**

**View commands:**
   + `aws configservice describe-delivery-channels`
   + `aws configservice describe-delivery-channel-status`
   + `aws configservice describe-configuration-recorders`
   + `The normal response is something like "name": "default"`

1. If you have an existing AWS Config recorder or delivery channel that you need to delete before you set up your AWS Control Tower landing zone, here are some commands you can enter:

   **Manage your pre\-existing AWS Config resources**

**Delete commands:**
   + `aws configservice stop-configuration-recorder --configuration-recorder-name NAME-FROM-DESCRIBE-OUTPUT`
   + `aws configservice delete-delivery-channel --delivery-channel-name NAME-FROM-DESCRIBE-OUTPUT`
   + `aws configservice delete-configuration-recorder --configuration-recorder-name NAME-FROM-DESCRIBE-OUTPUT`
**Important**  
Do not delete the AWS Control Tower resources for AWS Config\. Loss of these resources can cause AWS Control Tower to enter an inconsistent state\.

**For more information, see the AWS Config documentation**
   +  [Managing the Configuration Recorder \(AWS CLI\)](https://docs.aws.amazon.com/config/latest/developerguide/stop-start-recorder.html#managing-recorder_cli)
   +   [Managing the Delivery Channel](https://docs.aws.amazon.com/config/latest/developerguide/manage-delivery-channel.html)

1. This example shows AWS CLI commands you'd enter from AWS CloudShell to enable or disable trusted access for AWS Organizations\. For AWS Control Tower you do not need to exable or disable trusted access for AWS Organizations, it is just an example\. However, you may need to enable or disable trusted access for other AWS services if you're automating or customizing actions in AWS Control Tower\.

**Enable or disable trusted service access**
   + `aws organizations enable-aws-service-access`
   + `aws organizations disable-aws-service-access`<a name="cloudshell-and-s3"></a>

## Create an Amazon S3 bucket with AWS CloudShell<a name="cloudshell-and-s3"></a>

In the following example, you can use AWS CloudShell to create an Amazon S3 bucket and then use the **PutObject** method to add a code file as an object in that bucket\.

1. To create a bucket in a specified AWS Region, enter the following command in the CloudShell command line:

   ```
   aws s3api create-bucket - -bucket insert-unique-bucket-name-here - -region us-east-1
   ```

   If the call is successful, the command line displays a response from the service similar to the following output:

   ```
   {
       "Location": "/insert-unique-bucket-name-here"
   }
   ```
**Note**  
If you don't adhere to the [rules for naming buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) \(using only lowercase letters, for example\), the following error is displayed: An error occurred \(InvalidBucketName\) when calling the CreateBucket operation: The specified bucket is not valid\.

1. To upload a file and add it as an object to the bucket that was just created, call the PutObject method: 

   ```
   aws s3api put-object - -bucket insert-unique-bucket-name-here - -key add_prog - -body add_prog.py
   ```

   If the object is uploaded successfully to the Amazon S3 bucket, the command line displays a response from the service similar to the following output:

   ```
   {
              "ETag": "\"ab123c1:w:wad4a567d8bfd9a1234ebeea56\""}
   ```

   The `ETag` is the hash of the object that's been stored\. It can be used to [check the integrity of the object uploaded to Amazon S3](https://aws.amazon.com/premiumsupport/knowledge-center/data-integrity-s3/)\.