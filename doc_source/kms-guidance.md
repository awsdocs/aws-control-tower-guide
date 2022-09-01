# Guidance for KMS keys<a name="kms-guidance"></a>

AWS Control Tower works with AWS Key Management Service \(KMS\)\. Optionally, if you wish to encrypt and decrypt your AWS Control Tower resources with an encryption key that you manage, you can generate and configure AWS KMS keys\. You can add or change a KMS key any time you update your landing zone\. As a best practice, we recommend using your own KMS keys and changing them from time to time\.

The AWS Key Management Service \(KMS\) allows you to create multi\-Region KMS keys and asymmetric keys; however, AWS Control Tower does not support multi\-Region keys or asymmetric keys\. AWS Control Tower performs a pre\-check of your existing keys\. You may see an error message if you select a multi\-Region key or an asymmetric key\. In that case, generate another key for use with AWS Control Tower resources\.

*For customers who operate an AWS CloudHSM cluster:* Create a custom key store associated with your CloudHSM cluster\. Then you can create a KMS key, which resides in the CloudHSM custom key store you created\. You can add this KMS key to AWS Control Tower\.

You must make a specific update to the permissions policy of a KMS key to make it work with AWS Control Tower\. For details, refer to the section called [To make the key's policy update](configure-kms-keys.md#kms-key-policy-update)\.