# Account Factory guidance<a name="af-guidance"></a>

 You can encounter issues when using Account Factory to provision a new account in AWS Control Tower\. For information about how to troubleshoot these issues, see the section [New Account Provisioning Failed](troubleshooting.md#account-provisioning-failed) in [Troubleshooting](https://docs.aws.amazon.com/controltower/latest/userguide/troubleshooting.html) of the *AWS Control Tower User Guide*\. 

 We recommend that you create federated users or IAM roles instead of IAM users\. Federated users and IAM roles provide you with temporary credentials\. IAM users have long\-term credentials that can be difficult to manage\. For more information, see [IAM identities \(users, user groups, and roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\. 

 If you're authenticated as an IAM user or IAM Identity Center user when provisioning a new account in Account Factory or when using the *Enroll account* feature AWS Control Tower, verify that your user has access to your AWS Service Catalog portfolio\. Otherwise, you might receive an error message from Service Catalog\. For more information, see [No Launch Paths Found Error](troubleshooting.md#no-launch-paths-found) in [the Troubleshooting section](https://docs.aws.amazon.com/controltower/latest/userguide/troubleshooting.html) of the *AWS Control Tower User Guide*\. 

**Note**  
 Up to five accounts can be provisioned at a time\. 