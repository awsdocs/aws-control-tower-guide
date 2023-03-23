# Optionally configure AWS KMS keys<a name="configure-kms-keys"></a>

If you wish to encrypt and decrypt your resources with an AWS KMS encryption key, select the checkbox\. If you have existing keys, you'll be able to select them from identifiers displayed in a dropdown menu\. You can generate a new key by choosing **Create a key**\. You can add or change a KMS key any time you update your landing zone\.

When you select **Set up landing zone**, AWS Control Tower performs a pre\-check to validate your KMS key\. The key must meet these requirements:
+ Enabled
+ Symmetric
+ Not a multi\-Region key
+ Has correct permissions added to the policy
+ Key is in the management account

You may see an error banner if the key does not meet these requirements\. In that case, choose another key or generate a key\. Be sure to edit the key's permissions policy, as described in the next section\.

## Update the KMS key policy<a name="kms-key-policy-update"></a>

 Before you can update a KMS key policy, you must create a KMS key\. For more information, see [Creating a key policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-overview.html) in the *AWS Key Management Service Developer Guide*\. 

 To use a KMS key with AWS Control Tower, you must update the default KMS key policy by adding the minimum required permissions for AWS Config and AWS CloudTrail\. As a best practice, we recommend that you include the minimum required permissions in any policy\. When updating a KMS key policy, you can add permissions as a group in a single JSON statement or line by line\. 

 The procedure describes how to update the default KMS key policy in the AWS KMS console by adding policy statements that allow AWS Config and CloudTrail to use AWS KMS for encryption\. The policy statements require that you include the following information: 
+  **`YOUR-MANAGEMENT-ACCOUNT-ID`** – the ID of the management account in which AWS Control Tower will be set up\. 
+  **`YOUR-HOME-REGION`** – the home Region that you will select when setting up AWS Control Tower\. 
+  **`YOUR-KMS-KEY-ID`** – the KMS key ID that will be used with the policy\. 

**To update the KMS key policy**

1.  Open the AWS KMS console at [https://console.aws.amazon.com/kms](https://console.aws.amazon.com/kms)

1.  From the navigation pane, choose **Customer managed keys**\. 

1.  In the table, select the key that you want to edit\. 

1.  In the **Key policy** tab, make sure that you can view the key policy\. If you can't view the key policy, choose **Switch to policy view**\. 

1.  Choose **Edit**, and update the default KMS key policy by adding the following policy statements for AWS Config and CloudTrail\. 

    **AWS Config policy statement** 

   ```
   {
       "Sid": "Allow Config to use KMS for encryption",
       "Effect": "Allow",
       "Principal": {
           "Service": "config.amazonaws.com"
       },
       "Action": [
           "kms:Decrypt",
           "kms:GenerateDataKey"
       ],
       "Resource": "arn:aws:kms:YOUR-HOME-REGION:YOUR-MANAGEMENT-ACCOUNT-ID:key/YOUR-KMS-KEY-ID"
   }
   ```

    **CloudTrail policy statment** 

   ```
   {
       "Sid": "Allow CloudTrail to use KMS for encryption",
       "Effect": "Allow",
       "Principal": {
           "Service": "cloudtrail.amazonaws.com"
       },
       "Action": [
           "kms:GenerateDataKey*",
           "kms:Decrypt"
       ],
       "Resource": "arn:aws:kms:YOUR-HOME-REGION:YOUR-MANAGEMENT-ACCOUNT-ID:key/YOUR-KMS-KEY-ID",
       "Condition": {
           "StringEquals": {
               "aws:SourceArn": "arn:aws:cloudtrail:YOUR-HOME-REGION:YOUR-MANAGEMENT-ACCOUNT-ID:trail/aws-controltower-BaselineCloudTrail"
           },
           "StringLike": {
               "kms:EncryptionContext:aws:cloudtrail:arn": "arn:aws:cloudtrail:*:YOUR-MANAGEMENT-ACCOUNT-ID:trail/*"
           }
       }
   }
   ```

1.  Choose **Save changes**\. 

 ** Example KMS key policy ** 

 The following example policy shows what your KMS key policy might look like after you add the policy statements that grant AWS Config and CloudTrail the minimum required permissions\. The example policy doesn't include your default KMS key policy\. 

```
{
    "Version": "2012-10-17",
    "Id": "CustomKMSPolicy",
    "Statement": [
        {
        ... YOUR-EXISTING-POLICIES ...
        },
        {
            "Sid": "Allow Config to use KMS for encryption",
            "Effect": "Allow",
            "Principal": {
                "Service": "config.amazonaws.com"
            },
            "Action": [
                "kms:Decrypt",
                "kms:GenerateDataKey"
            ],
            "Resource": "arn:aws:kms:YOUR-HOME-REGION:YOUR-MANAGEMENT-ACCOUNT-ID:key/YOUR-KMS-KEY-ID"
        },
        {
            "Sid": "Allow CloudTrail to use KMS for encryption",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudtrail.amazonaws.com"
            },
            "Action": [
                "kms:GenerateDataKey*",
                "kms:Decrypt"
              ],
            "Resource": "arn:aws:kms:YOUR-HOME-REGION:YOUR-MANAGEMENT-ACCOUNT-ID:key/YOUR-KMS-KEY-ID",
            "Condition": {
                "StringEquals": {
                    "aws:SourceArn": "arn:aws:cloudtrail:YOUR-HOME-REGION:YOUR-MANAGEMENT-ACCOUNT-ID:trail/aws-controltower-BaselineCloudTrail"
                },
                "StringLike": {
                    "kms:EncryptionContext:aws:cloudtrail:arn": "arn:aws:cloudtrail:*:YOUR-MANAGEMENT-ACCOUNT-ID:trail/*"
                }
            }
        }
    ]
}
```

 To view other example policies, see the following pages: 
+  [Granting encrypt permissions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-kms-key-policy-for-cloudtrail.html#create-kms-key-policy-for-cloudtrail-encrypt) in the *AWS CloudTrail User Guide*\. 
+  [Required Permissions for the KMS Key When Using Service\-Linked RolesS3 Bucket Delivery\)](https://docs.aws.amazon.com/config/latest/developerguide/s3-kms-key-policy.html#required-permissions-s3-kms-key-using-servicelinkedrole) in the *AWS Config Developer Guide*\. 

**Protect against attackers**  
For more information about how to help protect against attackers when you grant permissions to other AWS service principals, see [Optional conditions for your role trust relationships](roles-how.md#conditions-for-role-trust)\. By adding certain conditions to your policies, you can help prevent a specific type of attack, known as a *confused deputy* attack, which occurs if an entity coerces a more\-privileged entity to perform an action, such as with cross\-service impersonation\. For general information about policy conditions, also see [Specifying conditions in a policy](access-control-overview.md#specifying-conditions)\.

The AWS Key Management Service \(AWS KMS\) allows you to create multi\-Region KMS keys and asymmetric keys; however, AWS Control Tower does not support multi\-Region keys or asymmetric keys\. AWS Control Tower performs a pre\-check of your existing keys\. You may see an error message if you select a multi\-Region key or an asymmetric key\. In that case, generate another key for use with AWS Control Tower resources\.

For more information about AWS KMS, see [ the AWS KMS Developer Guide\.](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)

Note that customer data in AWS Control Tower is encrypted at rest, by default, using SSE\-S3\.