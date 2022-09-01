# Optionally configure AWS KMS keys<a name="configure-kms-keys"></a>

If you wish to encrypt and decrypt your resources with an AWS KMS encryption key, select the checkbox\. If you have existing keys, you'll be able to select them from identifiers displayed in a dropdown menu\. You can generate a new key by choosing **Create a key**\. You can add or change a KMS key any time you update your landing zone\.

When you select **Set up landing zone**, AWS Control Tower performs a pre\-check to validate your KMS key\. The key must meet these requirements:
+ Enabled
+ Symmetric
+ Not a multi\-Region key
+ Has correct permissions added to the policy
+ Key is in the management account

You may see an error banner if the key does not meet these requirements\. In that case, choose another key or generate a key\. Be sure to edit the key's permissions policy, as described in the next section\.

## To make the key's policy update<a name="kms-key-policy-update"></a>

To use a KMS key with AWS Control Tower, you must make a specific policy update to the key\. At minimum, the KMS key must have permissions that allow AWS CloudTrail and AWS Config to use the chosen KMS key\.

**Make the required policy update**

1. Navigate to the AWS KMS console at [https://console.aws.amazon.com/kms](https://console.aws.amazon.com/kms)

1. Select **Customer managed keys** on the left

1. In the table, select the key you wish to edit, or select **Create a key** from the upper right

1. Under the section called **Key policy**, make sure you can see the policy and edit it\. You may need to select **Switch to policy view** on the right\.

You can copy and paste the following example policy statement\. Alternatively, for an existing key, you can ensure that your KMS key has these minimum permissions by adding them to your own existing policy\. You can add these lines as a group in a single JSON statement, or if you prefer, you can incorporate them line by line into your policy's other statements\.

```
{
  "Sid": "Allow CloudTrail and AWS Config to encrypt/decrypt logs",
  "Effect": "Allow",
  "Principal": {
    "Service": [
      "cloudtrail.amazonaws.com",
      "config.amazonaws.com"
   ]
  },
   "Action": [
      "kms:GenerateDataKey",
      "kms:Decrypt"
   ],
    "Resource": "*"
}
```

The AWS Key Management Service \(AWS KMS\) allows you to create multi\-Region KMS keys and asymmetric keys; however, AWS Control Tower does not support multi\-Region keys or asymmetric keys\. AWS Control Tower performs a pre\-check of your existing keys\. You may see an error message if you select a multi\-Region key or an asymmetric key\. In that case, generate another key for use with AWS Control Tower resources\.

For more information about AWS KMS, see [ the AWS KMS Developer Guide\.](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)

Note that customer data in AWS Control Tower is encrypted at rest, by default, using SSE\-S3\.