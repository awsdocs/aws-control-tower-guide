# Root OU<a name="cfct-root-ou"></a>

 CfCT supports **Root** as a value for an organizationl unit \(OU\) under `organizational_units` in **manifest V2 version \(2021\-03\-15\)**\. 
+ If you choose the deployment method of `scp`, when you add Root under `organizational_units`, AWS Control Tower applies the policies to all of the OUs under the Root\. If you choose the deployment method of `stack_set`, when you add Root under `organizational_units`, CfCT deploys the stack sets in all the accounts under the Root that are enrolled in AWS Control Tower, except for the management account\.
+ As per AWS Control Tower best practices, the management account is intended only to manage member accounts and for billing purposes\. Do not run production workloads in the AWS Control Tower management account\.

  In accordance with best practices guidance, AWS Control Tower deployment puts the management account under the Root OU, so that it has full access and does not run additional resources\. For this reason, the **AWSControlTowerExecution** role is not deployed to the management account\.
+ We recommend that you follow these best practices for the management account\. If you have a specific use case that requires you to deploy stacksets in the management account, include **accounts** as a deployment target and specify the management account\. Otherwise, do not include **accounts** as a deployment target\. You must create the missing resources, including required IAM roles, in the management account\.

To deploy stacksets in the management account, include `accounts` as a deployment target and specify the management account\. Otherwise, do not include accounts as a deployment target\.

```
---
region: your-home-region
version: 2021-03-15

resources:

  …truncated…

    deployment_targets:
      organizational_units:
        - Root
```

**Note**  
The Root OU feature is supported only in the V2 version of the manifest file \(2021\-03\-15\)\. If you add **Root** as an OU under `organizational_units`, do not add any other OUs\.