# Required roles<a name="aft-required-roles"></a>

In general, roles and policies are part of identity and access management \(IAM\) in AWS\. Refer to the [https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) for more information\.

AFT creates multiple IAM roles and policies in the AFT management and AWS Control Tower management accounts to support the operations of the AFT pipeline\. These roles are created based on the least privilege access model, which restricts permission to the minimally required sets of actions and resources for each role and policy\. These roles and policies are assigned an AWS tag `key:value` pair, as ` managed_by:AFT` for identification\. 

Besides these IAM roles, AFT creates three essential roles:
+ the `AWSAFTAdmin` role
+ the `AWSAFTExecution` role
+ the `AWSAFTService` role

These roles are explained in the following sections\.

**The AWSAFTAdmin role, explained**

When you deploy AFT, the `AWSAFTAdmin` role is created in the AFT management account\. This role allows the AFT pipeline to assume the `AWSAFTExecution` role in AWS Control Tower and AFT provisioned accounts, thereby to perform actions related to account provisioning and customizations\.

Here is the inline policy \(JSON artifact\) attached to the `AWSAFTAdmin` role: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": [
                "arn:aws:iam::*:role/AWSAFTExecution",
                "arn:aws:iam::*:role/AWSAFTService"
            ]
        }
    ]
}
```

The following JSON artifact shows the trust relationship for the `AWSAFTAdmin` role\. The placeholder number `012345678901` is replaced by the AFT management account ID number\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::012345678901:root"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**The AWSAFTExecution role, explained**

When you deploy AFT, the `AWSAFTExecution` role is created in the AFT management and AWS Control Tower management accounts\. Later, the AFT pipeline creates the `AWSAFTExecution` role in each AFT provisioned account during the AFT account provisioning stage\.

 AFT utilizes the `AWSControlTowerExecution` role initially, to create the `AWSAFTExecution` role in specified accounts\. The `AWSAFTExecution` role allows the AFT pipeline to run the steps that are performed during the AFT framework's provisioning and provisioning customizations stages, for AFT provisioned accounts and for shared accounts\.

**Distinct roles help you limit scope**  
As a best practice, keep the customization permissions separate from the permissions allowed during your initial deployment of resources\. Remember that the `AWSAFTService` role is intended for account provisioning, and the `AWSAFTExecution` role is intended for account customization\. This separation limits the scope of permissions that are allowed during each phase of the pipeline\. This distinction is especially important if you are customizing the AWS Control Tower shared accounts, because the shared accounts may contain sensitive information, such as billing details or user information\.

Permissions for `AWSAFTExecution` role: **AdministratorAccess** – an AWS managed policy 

The following JSON artifact shows the IAM policy \(trust relationship\) attached to the `AWSAFTExecution` role\. The placeholder number `012345678901` is replaced by the AFT management account ID number\.

Trust policy for `AWSAFTExecution`

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::012345678901:role/AWSAFTAdmin"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**The AWSAFTService role, explained**

The `AWSAFTService` role deploys AFT resources in all enrolled and managed accounts, including the shared accounts and management account\. Resources formerly were deployed by the `AWSAFTExecution` role only\.

The `AWSAFTService` role is intended for use by the service infrastructure to deploy resources during the provisioning stage, and the `AWSAFTExecution` role is intended to be used only to deploy customizations\. By assuming the roles in this way, you can maintain more granular access control during the each stage\.

Permissions for `AWSAFTService` role: **AdministratorAccess** – an AWS managed policy 

The following JSON artifact shows the IAM policy \(trust relationship\) attached to the `AWSAFTService` role\. The placeholder number `012345678901` is replaced by the AFT management account ID number\.

Trust policy for `AWSAFTService`

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::012345678901:role/AWSAFTAdmin"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```